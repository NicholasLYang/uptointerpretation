---
title: "How to Think About Compiling"
date: 2021-01-15T19:56:07-05:00
draft: true
---

I've wanted to write a compiler for a while, about 3.5 years at this
point. I struggled with it for a while because I didn't know how to
even approach the problems involved in writing a compiler. I mean, how
the heck do you take a nice, elegant language like Java and turn it
into bytecode or assembly? Maybe I'm just not that smart, but it took
me a lot of effort to figure out this process.

Here's what I've learned over my last few compiler projects. I wish I
could have learned this stuff from a compiler book, but instead I
learned from trial & error & suffering.

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
run. The classic example of this is saving registers.

When I code generate, I use a few WebAssembly globals as
"registers". In particular, I use global #0 as a general purpose
register, often to store the result of a pointer to the heap. Global
#0 explicitly has no saving. If you call an `generate_expr`, then you have no guarantees that global #0 will stay the same.

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
them into structs, then let our compiler do the rest.
