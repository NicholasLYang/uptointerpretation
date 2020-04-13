---
title: "Thoughts on Compilers in Rust"
date: 2020-02-22T23:34:38-05:00
draft: true
---

I've been writing a compiler in Rust for a while now. It's started and
stopped a lot, and frankly I'm not super satisfied with my
progress. But I have learned a few things about compilers and about
Rust in the process. Namely I've learned that the two don't always mix
as well as I'd like. It's quite weird. There's so much in Rust that's
suited to compilers. Algebraic data types, pattern matching,
relatively functional/expression-based semantics but with the option
of imperative programming, and so on. But there's one fundamental
aspect that makes writing a compiler in Rust so damn annoying: borrow
checking. Which, awkward, is Rust's shtick.

You see, compilers are all about data representation. And for a decent
part of a compiler, that source representation is a tree
structure. Which is not bad for Rust. In fact it's great! Every node
in a tree has one and only one owner. Great for borrow checking. But
when we add types, stuff gets tricky. Let's say we have a tuple
expression: `(10, "foo")`. The tree for this kind looks like
`Expr::Tuple(Expr::Primary(Literal::Int(10)),
Expr::Primary(Literal::String("foo")))` Not too bad. But what happens
when we add types? Well we convert the `Expr::Primary` into `TypedExpr::Primary`:
```
  TypedExpr::Primary(Literal::Int(10), Type::Int)
  TypedExpr::Primary(Literal::String("foo"), Type::String)
```

What happens when we convert the `Expr::Tuple` into a
`TypedExpr::Tuple`? Well we need to come up with a type for the
tuple. This is tricky, because ideally we'd like to reuse the types
from the `TypedExpr::Primary` nodes. After all, if we clone the types,
this could lead to rapid performance issues. Imagine typing `(((1, 2),
3, 4), 5)`. Each nested tuple will clone the types of its children and
create a new type. But if we reuse the types, we quickly get on the
bad side of the Rust compiler.
