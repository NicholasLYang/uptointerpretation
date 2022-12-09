---
title: "How to Think About Compiling"
date: 2022-12-08T16:56:07-05:00
draft: false
---

I've wanted to write a compiler for a while, about 5 years at this
point. I struggled with it for a while because I didn't know how to
even approach the problems involved in writing a compiler. I mean, how
the heck do you take a nice, elegant language like Java and turn it
into bytecode or assembly? Maybe I'm just not that smart, but it took
me a lot of effort to figure out this process.

Here's what I've learned over my last few compiler projects. I wish I
could have learned this stuff from a compiler book, but instead I
learned from trial & error & suffering.

# Saber

All of the examples are taken from my compiler for
[Saber](https://github.com/nicholaslyang/saber), a garbage collected
OCaml-esque language. It has structs, integers, floats, first class
functions, garbage collection, and it kind of looks like
JavaScript. It's also very much incomplete
and not a production language.

Saber compiles to WebAssembly, which is a stack language with a
bytecode format. Basically, WebAssembly holds values on a
stack. Instructions can pop values off the stack and/or push them on
the stack. There are functions, integers, floats, and linear memory (a
heap).

# Combining Concepts

A lot of compilation is combining concepts. You'll take stuff like
objects, functions, arrays and compile them down to a simple concept:
pointers.

This isn't a revelation. If you understand high level languages versus
low level languages, you understand this.

Sometimes there's tricks on how to combine things. If you're designing
the target, as in the case of bytecode interpreters or intermediate
representations, you can help yourself by adding precisely the right
concept in the target such that you can combine multiple features into
one. Maybe you can add first class array support and compile strings,
arrays and structs to arrays.

# Split Concepts

On the flip side however, sometimes you need to split concepts
up. Let's take functions as an example. In a lot of languages these
days, functions are first class citizens. They can be passed as
values, they can enclose other values, etc. This makes compilation
slightly tricky as it's not always clear which function is being
called:

```
let fn = if is_cool {
  () => {
    print("I'm cool!");
  }
} else {
  () => {
    print("I'm not cool!");
  }
}
```

In WebAssembly, you can implement first class functions via calling
into a table of potential functions with an index computed
dynamically. This is great, but it's a separate feature from regular
functions that requires some setup. I'd prefer to not use it if
possible.

Therefore, in my typechecking pass, I check if I can resolve the
function statically and if so, I emit an `ExprT::DirectCall`. If not,
I emit an `ExprT::IndirectCall`. By splitting the two concepts, I can
make my function calls a little more efficient and my code generation
a lot nicer.

If you're familiar with dynamic versus static dispatch, this should
sound familiar. In languages such as C++ this distinction is in the
source language, but even if it isn't, you can still use it for
compilation.

# Pre and Post Conditions

Code generation at times can feel kinda like time travel. Since you
know what happens in the next few lines of code, you can leave
yourself hints in the past.

However, with hints, it's important that hints don't squash each
other. If you remember to leave some important data in register 1, you
have to make sure it doesn't get overwritten by some other important
data in register 1.

The way we do this is via pre and post conditions. When we compile, we
create certain agreements about the data before and after some code is
run. The classic example of this is saving registers. Saving registers
is the idea that you need to store the value of certain machine
registers on the stack before calling a function, because the function
is also allowed to use the registers and will likely overwrite your
values.

When I code generate, I use a few WebAssembly globals as
"registers". In particular, I use global #0 as a general purpose
register, often to store a pointer to the heap. Global
#0 explicitly has no guarantees around saving. If you're generate an
if statement and you call into `generate_expr` to generate the code
for the condition, there are no guarantees that global #0 is saved.

This is great when you just need a variable to duplicate stack values
(WASM doesn't have a dup instruction for...reasons). However it falls
apart in certain situations. With indirect function calls, we need a
global that's saved. Basically, indirect function calls require
generating the code that evaluates to a function value, then
generating the code that evaluates to the arguments, then doing an
indirect call. The problem here is that a function value is actually a
struct with two fields: an index into a function table and a pointer
to the function's environment. The pointer to the function's
environment is the first argument, while the index goes after the
arguments.

If you imagine the stack when we do a call indirect, it should look like:

```
<pointer to env>
<arg 1
...
<arg n>
<function index>
```

However, to do this, we need to preserve the pointer to the function
value struct so that we can use it after the argument generation to
access the index.

How do we do that? Well we use global #1! Unlike global #0, global #1
has strict rules about being preserved. Whenever you use global #1,
you are expected to save its previous value and reset to that after
using it.

Basically there's an explicit post-condition here. Global #1 must be
the same as it was before your current usage.

# Desugaring

If the explanation above sounded messy, it's because it was. Closures
are kind of a pain in the ass. That said, they were a lot easier than
they could have been.

Closures refer to the process by which functions keep access to
variables that are in scope, even if the function is moved out of that
context. For instance:

```
let foo = () => {
  let a = 10;
  let bar = () => {
    printInt(a);
  }
  return bar;
}

let fn = foo();
fn();
```

In this case, `fn()` should print `10`. However that requires that we
retain access to `a` even though the scope in which it's defined,
i.e. foo's scope, is no longer in existence.

The (somewhat) standard way we accomplish this is by giving functions
a first parameter that is a pointer to the function's environment,
basically a struct of values that we need to keep around (or *enclose*
to use proper terminology). Then, when a function is defined, we
initialize the struct to contain the necessary values, in this case
`a`, and bind it to the function.

This is further complicated when you have even more nesting:

```
let foo = () => {
  let a = 10;
  let bar = () => {
    let baz = () => {
	  printInt(a);
	}
	return baz;
  }
  return bar;
}

let fn = foo()();
fn();
```

With multiple levels of nesting, we have a few problems. If we're
initializing the function environment for `baz`, we need `a`. But `a`
isn't defined in `bar`, it's defined in `foo`! Therefore `bar` needs
access to `a` so that we can initialize `baz`.

To solve this, we have two options: Either we include `a` in each
function environment, i.e. `bar` has `a`, then `baz` has `a`. Or we
create a linked list between environments so that if we need to get
`a`, we can simply look at `baz`'s environment, which has a pointer to
`bar`'s environment, which then has `a`.

The problem with including `a` in each function environment can be
explained with another example:

```
let a = 1;
let b = 2;
let c = 3;
let d = 4;
let foo = () => {
  let bar = () => {
    printInt(a);
	printInt(b);
  }
  let baz = () => {
    printInt(c);
	printInt(d);
  }
}
```

Since `bar` needs access to `a` and `b`, we need to put them in
`foo`'s function environment. But since `baz` needs `c` and `d`, those
also need to go into `foo`'s environment.

You can kinda visualize this as a pyramid. `foo`'s environment must
contain all the necessary values that both `bar` and `baz` require. If
`bar` and `baz` have functions inside them that require values, well
`foo`'s gonna have to have them too.

Instead, let's do the linked list idea. But writing a linked list in
WebAssembly is annoying. Not to mention we have to garbage collect it,
which means type tags and all these other messy aspects.

But wait! We've already implemented all of these messy aspects in our
regular struct code. Therefore, we can just take our closures, desugar
them into structs, then let our compiler do the rest. We can also move
all of the functions out of this nested situation and put them at the
top level.

Taking our first example, we can put all of the functions at the top
level, and replace the function values with the structs:

```
let foo_env = FooEnv {};
let bar_fn = (env) => {
  printInt(env.a);
}
let foo_fn = (env) => {
  let a = 10;
  let bar_env = BarEnv { parent: env, a };
  return bar_env
}

let fn = foo_fn(foo_env);
bar_fn(fn);
```

Technically there's some scope trickery here because all the global
functions can call each other, regardless of their declaration order,
but that's hard to demonstrate in pseudocode.

From here, we can trust our code for compiling structs and top level
functions to work.

# Intermediate Representations

As I've mentioned before, intermediate representations can make your
life a lot easier. When I started out writing my compiler, I was
hesitant to add too many IRs. I felt that it added unnecessary
complexity by having yet another data structure to worry about. I was
worried about the performance implications of having these various
translation steps. I also made the assumption that each successive IR
had to be closer to the target language.

Turns out none of these are really valid concerns. Yes, you can have
too many IRs, but most compilers have at least 3-4. IRs may slow down
your compiler, but you shouldn't assume that off the bat. Besides,
performance doesn't matter if the compiler doesn't work.

As for the IR having to be closer to the target language, sure, in
some ways it should be. But part of the purpose of an IR is to
introduce structure. What does that mean?

Backing up a little, I'd say there's two main things a compiler should
do: deduce information about the code's structure (parsing, type
checking, control flow analysis), and translate (lowering, code
generation). In many cases what we do is deduce the structure and then
translate. We parse and then build an AST. We type-check and then
compile.

However, there is sometimes the opposite where we need to translate to
deduce structure. This is where an IR comes in. Let's say we want to
do some control flow analysis. Control flow analysis is pretty
important for type checking, specifically for
non-nullability. Non-nullability is the principle that any value
cannot be null by default. If a value can be null, it needs to be
explicitly marked, often with a wrapper like `Option<Foo>`. To have
non-nullability, a compiler needs to ensure that all paths in a
function return a value.

In theory you could do this in the type-checking pass. But there's a
couple issues. For one, type-checking is done on an AST, which means
that evaluation order of code is not linear, i.e. you cannot just read
a list of operations that are executed in order. This makes checking
control flow kinda frustrating because you need to add bookkeeping to
remember if you've seen a return, if that return was inside an if
expression, etc. Second, we're already doing a lot in the
type-checking pass. We're checking the types, figuring out scopes for
closures, and now doing control flow analysis?  From a code
perspective this seems very messy. Again, you could totally do this,
but personally it gives me a headache.

What's the answer? An IR! In this case we want an IR that puts the
structure of control flow front and center. This requires a few basic
structures. We have a basic block with instructions. Instructions can
use the result of previous instructions via their index. Instructions
are always executed from top to bottom in order. Basically there is no
control flow inside a block. To have control flow, we provide some
operations: you can conditionally go to a different block;
unconditionally go to a different block (more on this later); and you
can return[^1] from a block. Blocks can return a value. Functions are
made up of blocks with one entry block.

```
------ Block 0 -------
0: i32.var 0      // Load variable #0 (v#0)
1: i32.const 4    // Constant 4
2: i32.add #0 #1  // Add v#0 and 4
3. i32.var 1      // Load variable #1 (v#1)
4. i32.eq #2 #3   // Check if v#0 == v#1
5: if #4 1 2      // If v#0 == v#1, go to block 1 otherwise go to block 2.
----------------------
```

[^1]: Reading this, I should probably have some instruction for
    general return from a function.

If you're wondering, this is totally cribbed from Professor Adrian
Sampson's Advanced Compiler's
[course](https://www.cs.cornell.edu/courses/cs6120/2020fa/self-guided/). It's
also a sort of bastardized [single-static
assignment](https://en.wikipedia.org/wiki/Static_single-assignment_form)
to a degree.

Control flow analysis on this IR is pretty nice! You can check that a
block always resolves to a value pretty easily by going down the block
and verifying that it always has a return instruction. If there are
instructions after a return, you can mark them as dead.

Beyond that, the IR makes other forms of translation easier. All of
the complicated types are lowered down to 32 bit signed ints, 32 bit
signed floats, and pointers. In reality, pointers are 32 bit integers
too, but having them be separate makes for a clearer translation.

IRs are also easier for debugging. While I "love" debugging pure
WebAssembly[^2], it's certainly a lot nicer to have a format that I
can print out into clean blocks and read straight through.

[^2]: Especially when your bytecode emitting is incorrect so tools
    like wasm2wat don't work and you end up reading raw hex.

This is just one IR example. There's a whole bunch of different types
like continuation passing style, bytecode, etc. I recommend learning
more IRs so you can keep them in your toolbox.

# Final Advice

To recap, if you're writing a compilation step and you're stuck, I'd
say there's a few key questions you can ask yourself:

- What's the simplest example I can find?

Literally the simplest example. I started my code generator with integer
arithmetic.

- What does this simplest example look like in my target language?

Integer arithmetic in WebAssembly may seem trivial but you need to set
up the bytecode format for a module, add a type definition, add a
function definition, then the code body. It's not the simplest.

- How can I generate this simplest example in code?

- How can I represent the target language in code?

Maybe a little dumb, but compiling to WebAssembly became a lot less
daunting once I had just typed out all of the language as Rust data
structures. It became a question of moving data around and indexing
it, something that we've all done.

- How can I make this feature more simple?

I made the mistake of having really fancy features from the
start. Functions would be closures, they'd have pattern matching with
tuples and structs. All of that was too much. I ended up starting with
a top level function that takes in regular, ordered arguments.

- Can I move the previous representation to be closer?

I had a heck of a time figuring out if a function call was direct or
indirect (basically statically determined or dynamically determined)
in my WebAssembly code generation. As I mentioned above, I resolved
this in my typechecking pass. By doing so, I moved my typed AST
representation closer to WebAssembly and farther away from its
previous representation, the untyped AST.

A lot of compiler design is playing around with the closeness of each
compilation step from the other steps.

- Can I make an intermediate representation that makes this easier?

Of course, if you can't get it to work, you can always create an IR.

- What invariants should be in the IR?

Having the invariant that a block is always executed from the top down
with no internal control flow in my IR made compiling and reasoning
about it way easier.

- What knowledge do I need to have to compile this code?

- How can I get this knowledge prior to this step?

If you need a lot of knowledge and therefore a lot of bookkeeping, you
probably should introduce an IR and gather the knowledge there.

# Thanks

Thanks for reading! I'm not an expert here and likely a lot of this is
common knowledge already. If there are inaccuracies, omissions or
additions that you'd like to talk about, feel free to email me at
nick@nicholasyang.com!
