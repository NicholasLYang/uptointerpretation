---
title: "Saber Notes: Name Table, Parameters & Local Variables"
pubDate: "Apr 13, 2020"
draft: false
---

I've been working on my language,
[Saber](https://github.com/nicholaslyang/saber) a lot recently. I
figured I should keep some notes on implementation in case someone
finds them useful, even if that's only future-me. Saber is a scripting
language intended to be a lightweight row-polymorphic
psuedo-functional language that compiles to WebAssembly. Think OCaml
meets the best parts of ES6. Of course right now I'm just trying to
get basic functionality finished. The compiler is written in Rust.

Before this recent spurt of work in Saber, I had successfully gotten
compilation of basic functions with arithmetic from Saber code to
WebAssembly. That may not seem like much but it required getting the
whole typed AST to WASM intermediate representation pipeline working,
as well as an emitter. Emitting was especially daunting, as it
involved basically staring at raw bytecode and debugging
it. Eventually I realized that it isn't so bad. Although that's
probably the Stockholm Syndrome talking.

I came back to the compiler after taking a long break, due to working
on two compilers: a ChocoPy to RISC-V compiler written in Java for my
compilers class, and a separate ChocoPy one in Rust just for fun. I
haven't finished those, but I learned some neat little tricks in the
process that I decided to transfer to Saber.

# Name Table

I started with creating a name table, basically a table that stores
names such as "int" or "foo", swapping them out for an id. I used one
in my Rust ChocoPy compiler and really liked how it made wrangling
with ASTs a lot easier. Instead of worrying about the lifetimes of the
strings, I was able to deal with nice, simple `usize`'s. I could have
in theory used `Arc`'s as well, like Saber does for types, but there's
something about using ids that's cleaner for this[^1]. Some people
call this table a symbol table, but as you'll see in a future post,
that means something else in Saber.

[^1]: Some may argue that this is a hack to get around Rust's strict
    lifetime rules. Sure, but it's a nice hack.

The name table implementation was pretty straightforward: Create a map
from names to ids and a counter; as the lexer gets names/identifiers,
put them into the table if they don't exist already; spit out a token
with the id. The only issue I encountered was that I really needed a
bidirectional map. Luckily, Rust has a crate for that[^2].

[^2]: I know, I could have implemented it myself, but momentum is the
name of the game with side projects)

There was some minor code involved moving the table from lexer to
parser to typechecker but nothing crazy.

# Multiple Parameters

I designed Saber to have a slightly unconventional attitude towards
parameters. Basically, functions in Saber only take one
parameter. However tuples and records are first class values and
easily destructurable using patterns, patterns being stuff like `(a,
b, c)` or `{ field1, field2 }`. Therefore you can do stuff like:

```
let add = \(a, b) => a + b
let args = (10, 11);
add(args);
```
or
```
add(10, 11);
```

The `(a, b)` in `add`'s definition is a pattern which destructures the
args.

Function calls still have parens around them but that's mostly because
I don't want to deal with parsing a Ruby-esque `foo a, b` just yet.

Anyways turns out this is terrible for implementing. Either I need to
implement
[boxing](https://stackoverflow.com/questions/13055/what-is-boxing-and-unboxing-and-what-are-the-trade-offs)
for records/tuples, then pass in a pointer (or however close to that I
can get in WASM), or I have to find a way to destructure. I haven't
figured out a total solution for implementing, but what I came up with
was essentially desugaring to regular multiple parameters.

I've been using
[desugaring](https://en.wikipedia.org/wiki/Syntactic_sugar) a lot in
Saber. What that means is that I'll take a complicated feature like
destructuring and convert it into a simpler feature such as
assignments and field accesses. Where I've been desugaring has been in
the typechecker. This is a pretty natural place to do it, as I'm
already exchanging one AST for another (untyped to typed) and I have
enough contextual information to say, generate new variable names
without worrying about
[shadowing](https://en.wikipedia.org/wiki/Variable_shadowing).

This is a long winded way of saying that I've been working on
desugaring patterns into simpler, easier to compile code. For
assignment statements, this involves introducing multiple
bindings. For functions, this just means taking the pattern and
flattening it into a list of regular old parameters. This:

```
({ a, b }, c) => ....
```
Becomes:
```
(a, b, c) => ...
```
I still need to work on getting arguments in function calls to be wrangled accordingly, but the flattening and function calls with regular tuples works.

As for code generating parameters, I added two fields to the
CodeGenerator struct: `var_indices` and
`local_variables`. `var_indices` is a HashMap from var names to local
indices, while `local_variables` is a vector of `WasmType`, my enum
for WebAssembly types. WebAssembly treats arguments as local variables
that happen to be set on function call (well, kinda, we'll get into
this later), hence the naming. If you're wondering about scope, I
decided to ignore that concern at that time.

As the code generator read through the function params, now desugared
to a list of names with their corresponding types, we'd add them to
these data structures. Then on var usage, we'd put the index in an
`OpCode::GetLocal(index)`.

# Type Promotions

Next up was type promotions. Saber has ints and floats, mostly because
I like having a data type that obeys *some* algebraic laws (*cough*
associativity *cough*). However I don't want Saber to nag people about
adding an int and a float. Unfortunately WebAssembly does nag people
about this, so we need to add a specific conversion function.

Pop quiz, which type would you expect `f32.convert_s/i32` to take in
and which type would you expect to have it spit out? If you answered
take in 32-bit float, spit out 32-bit int, you'd be wrong. Takes in
signed 32-bit int, spits out float. Kinda confusing[^3], huh?

[^3]: Quite possible you don't find this confusing. But I do, since it
    contradicts left-to-right ordering..

Anyways yadda yadda, on code generating binary operations I check if
the type of the binary operation is not the type of one side of the
operation, and if so I pop on a conversion operation. This works cause
I already have type promotion in the typechecker, i.e. `10 + 1.2`
already gets typed as float even if `10` is technically an int.

# If Stmts

In a "wtf past me" moment, I realized I never bothered to write
the parsing for if statements. Fortunately it's dumb easy. I decided
to go with the Rust version of non mandatory parentheses around the
condition, mandatory brackets around the branches. I.e. you can do:

```
if blah {
 ...
} else {
 ...
}
```
But not

```
if (foo)
  ...
```

I don't use the single line no-block if statements that much anyways.

I also added the special case in call expressions where a single
argument gets passed to the function as itself, not as a one element tuple.

# Typechecking Recursive Functions

Another thing I implemented in my ChocoPy compiler and earmarked for
Saber: recursive functions! Typing functions is a lot of set up, as
you need to create the new environment, add in the parameters with
their types to this env, set up the return type so that if you come
across a return statement you can accurately typecheck it, and
finally, something I forgot to do previously, you need to add the
function itself to the environment. Otherwise typechecking recursive
functions becomes impossible.

## Digression On Saber Functions

Saber, like any self respecting language these days, has anonymous
functions. What's unique about Saber is that it *only* has anonymous
functions. If you want a named function, you simply bind it with a
let. Therefore this is the only way to define a function in Saber:

```
let foo = \() => {
 ...
}
```

This stems from my noticing that every language eventually adds
anonymous functions, so why bother having two ways of creating a
function? Plus I hate the whole `fn/fun/func/function` debate. I'm
waiting for a language to use `fu`.

What I realized while code generating however was that while I had
only one type of function from a user facing perspective, I really
wanted to treat function bindings, e.g. `let foo = \() => { ... }`
differently from anonymous functions like `map(\a => a + 1)`,
especially come code gen time. Well, I say that but really I still
don't know how I'm going to treat anonymous functions. Probably
indices into a WASM table. But I digress[^4]. Function bindings are a
lot simpler. Code generate the function, then when referenced in the
right scope, call it directly. It's kinda the classic virtual versus
static dispatch problem that you run into with object oriented
languages; you have function calls where you know which function will
be called at compile time, and you have function calls where the
destination is unknown.

Long story short, I took my `Expr::Function` and split it into
`Expr::Function`[^5] and `Stmt::Function`.

[^4]: In my digression nonetheless.
[^5]: Probably should be renamed to `Expr::Lambda`

## Back To Recursion

Anyways I wanted one function for typechecking both `Expr::Function` and
`Stmt::Function`. However that's tricky as in the function binding
case, we have to add the function name into the scope before
typechecking the body. Easy! You just add the function name into the
scope, then call the shared function for typechecking
functions. Buuuut, to get the types for the function so that you can
insert it into the scope, you need to typecheck the parameters,
convert the return type annotation into an actual type, etc. Which
kinda defeats the purpose of having a shared function, if you're just
gonna do all the work outside of it.

So what did I do? Did I come up with some brilliant solution that was
tasteful, magically clean and elegant? Nope. I just wrote the setup
code before calling the shared function. Sometimes you just gotta
write the damn code and move on.

In the future this might be easier as I'm planning to allow anonymous
functions to call themselves with `this`.

Also on second thought I need to add functions to environments in
their own pass already as Saber functions will probably be hoisted in
their scope. That way you can do:

```
let bar = \() => foo()
let foo = \() => { ... }
```

Which is expected of every language created post-1970.

# Local Variables

Since I got parameters working, local variables seemed like a
cinch. After all, aren't parameters just fancy local variables? Turns
out that's not entirely the case.

WebAssembly is described as a stack based VM and that is accurate to
how it evaluates code, but I'd argue that a better description is an
index based VM. Everything from local variables to functions to tables
are described by indices. WASM documentation defines what are called
"index spaces", essentially the various indices you'll need to
remember in order to write WebAssembly.

My first thought upon reading about index spaces was basically, "oh,
okay, so each section has an array of items into which I can
index. That's my index space. Cool!" Thus the function section
contains the function indices, the global section contains the global
indices, etc. But where's the local variables index?

Reading through the documentation, I found the answer: it's in
code. Huh, a little weird, but it makes sense. The code section
declares function bodies, inside which there is a list of local
variables. Now since I was thinking about things in terms of index
spaces, my natural inclination was that this section that contains
local variables contains the entire local variables index. This means
parameters as well. As I've mentioned before, parameters are just
fancy local variables that get set on function call. To access or
mutate them, you use `get_local` and `set_local` , just like
locals.

You can probably guess that this was not the case. This mistake was
pretty nefarious, as I only realized it when also testing my
conversion/type promotion code. After all, adding my parameters into
the locals section in code body just meant that my function had more
local variables than necessary. But if my local variables were all the
same type, no errors would pop up (WASM doesn't care if you use all of
your locals). Only when I started having locals that were floats with
my parameters as ints did the error show up. And they'd show up as
errors where my local variables were mysteriously the wrong type.

Debugging this error was pretty painful. There's two main sources of
errors: misimplementation and misinterpretation. Misimplementation
errors mean you messed up some implementation detail but you have the
right mental model. Misinterpretation is the precise opposite: you
have everything right, you're just thinking about it
wrong. Misinterpretation errors are pretty hard to fix, as you need to
get a whiff of the right mental model. Luckily I had some help in the
form of wat2wasm. You see, WASM has a text format, WAT using
s-expressions. It has some nice features like named local
variables. This is pretty handy for testing as you can write a sample
WAT program, compile it to WASM and compare the binary format.

I compiled it, stared at some bytecode (hexl-mode in emacs helps a lot
with that) and eventually figured out that the locals entry in the
code body should only contain, well, locals. That may seem obvious but
honestly I don't blame myself for assuming that everything in an index
space would be in the same place.

What's also kind of weird is that these local entries consist of a
count and type, so you can have two integers in a row that are stored
in one entry. I guess it makes sense but it makes counting indices a
lot harder. Ah well, WASM isn't meant to be read anyways.

# Fin

That's all for now. I'll start again with tweaking my return stmt code
generation, control flow and function calls, culminating in
successfully compiling factorial!
