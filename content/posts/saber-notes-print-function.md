---
title: "Saber Notes: Print Function"
date: 2020-04-12T22:23:53-04:00
draft: true
---

I've been working on my language,
[Saber](https://github.com/nicholaslyang/saber) a lot recently. I
figured I should keep some notes on implementation in case someone
finds them useful, even if that's only future-me.

# Pre-print

Before the print stuff, I finished a nice chunk of code generation
work, implementing local variables, function calls, basic control flow
and comparison operators. This allowed me to succesfully compile
factorial, which is a pretty cool accomplishment.

Then I started on some bigger architectural refactors in order to
prepare myself for more involved features. First I implemented a
proper symbol table. Up until now I'd been relying on a vector of
scopes in the typechecker that acts as your classic [cactus
stack](https://en.wikipedia.org/wiki/Parent_pointer_tree) for
scopes. Scopes were a struct with a hashmap of names to types and a
parent field of type `Option<usize>` which indexes into the
vector. Using indices as essentially pseudo-pointers is a little messy
but the vector allowed me to avoid complicated lifetimes by ensuring
that the scopes lifetimes were just tied to the typechecker. I futzed
around with some different symbol table implementations before settle
with basically the same scopes format but hidden in a `SymbolTable`
struct. Instead of the hashmap being from names to types, I changed it
to be an enum that had different variants for functions and vars. This
allowed me to give each function an index *before* code
gen. Convenient.

After that, I started on a parser refactor to get actual error
messages with row/col numbers. Oooh fancy. I settled on a data
structure for representing source code locations, changed the AST
representation and followed the stream of Rust error messages to fix
everything. Overall it wasn't so painful. I might do a post digging
into the representation and its tradeoffs. What helped was that
location info is so small---basically 2 pairs of `usize`---that I was
fine with making the structure `Copy` so lifetime issues were
negligible.

In the future I want a way to actually get a source snippet with
errors but that'll wait.

After that I decided to get back to actual feature work, as it's
pretty cool seeing my language get new abilities. There's a whole
bunch of big ones to tackle like strings, first class functions,
structs, etc. but print seemed like a good start. So far I had been
relying on returning out to the JS script running my WebAssembly for
I/O. I read up on 

