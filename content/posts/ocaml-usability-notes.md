---
title: "OCaml Usability Notes"
date: 2025-03-24T19:26:34-04:00
draft: true
---

I've started learning OCaml. I've tried learning it previously, so
it's not like I'm a complete beginner, but I'm still very new to the
language. Being a beginner is sort of nice, because it means you get
to experience all the little usability quirks and pain points that get
paved over once you gain familiarity. Here's some that I've
encountered. I'll rate them on a scale of *nit*, *issue*, and
*problem*. Also *praise* for stuff that I find cool.

I want to emphasize, this is **not** to hate on OCaml and say any
other language is better. The point is to collect constructive
criticism that can hopefully be used to improve the language.

## Docs Search

*nit*

I'm using Base, which is an alternate standard library for OCaml
written by Jane Street. It has [API
documentation](https://ocaml.org/p/base/v0.15.0/doc/Base/index.html)
on [ocaml.org](https://ocaml.org), which I suppose is OCaml's version
of [docs.rs](https://docs.rs). This is great! It's nicely designed and
fairly simple to navigate.

My main complaint is that the docs search doesn't have a keyboard
shortcut. In docs.rs, it's `s`, while on most JavaScript docs sites
it's `Command-K`.

## Syntax Errors

*issue*

OCaml doesn't produce the best diagnostics for syntax errors. In fact,
as far as I can tell, it just says `Syntax Error` with no explanation,
or suggestions. It does show you the line, but no surrounding lines,
which isn't great because sometimes the actual problem is in the
preceding line.

## Dune Projects

*nit*

`dune` is OCaml's popular common build system. It overall seems quite nice!

One small issue I have is that dune works on projects, which are
closer to cargo workspaces or npm workspaces, i.e. multi-package
codebases. Dune does *not* have a mode for working on a single
package. Nor does it appear to have a way of specifying which package
is the entrypoint for a project. So you can't create a setup where you
automatically know what file to actually execute.

At the very least dune should be able to spit out which executables I
can run in a project.

## Formatter

*nit*

Why do we need a `.ocamlformat` file to activate formatting? That
feels unnecessary.

## Named Arguments

*praise*

Named arguments are very nice. I wish Rust had them. Ditto optional arguments.

## Dune Config

*nit*

Dune config is stored in a `dune` or `dune-project` file as
s-expressions, pretty straightforward. However, there's no file
extension, so editors don't know what the file format is and can't add
syntax highlighting automatically. Bazel also has this problem with
its `BUILD` files.

Also, it's not always clear how to extend config, since there's no
explicit schema or structure. I know config languages are all flawed,
so this isn't a huge deal, but it is a little frustrating that I can't
tell how to add a new `ppx` rewriter.

## Tooling Performance

*praise*

Everything's so zippy! I love it.

## Package Management

*problem*

Does OCaml not have package management with lockfiles and local
dependencies? It seems like `opam` installs everything globally? There
appears to be some sort of virtual environments with `opam switch`,
but they're tied to compiler versions, not projects.

Perhaps I'm mistaken, but I'm getting the impression OCaml has the Go
problem where it's really meant to work with monorepos where most
dependencies are vendored.

## People

*praise*

So far people seem quite nice in the community! I've already asked for
help in a few places and they've been quite responsive and helpful.

## Usage in Docs

*issue*

I'm seeing a pattern where tools like `ocamldoc` or `menhir` start
their docs with a "Usage" section that's just a big ol' list of
command line options. That's not helpful at all. It's in fact quite
intimidating.

## Docs Formats

As mentioned before, OCaml has a lovely `ocamldoc` format that is
great for APIs. But it doesn't seem to have an equivalent for
`mdBook`, which Rust uses for more tutorial style walkthroughs.

## Docs Quality

The docs for an average library feel quite sparse. Very few comments.

## Namespaces

For a language that prides itself in explicitness, it's really
annoying that OCaml doesn't directly import modules, but instead
requires you to "open" their parent module. This means you can't
easily find the location of a module.

## Incremental Compilation

OCaml's compiler is quite fast, but I don't think it has incremental
recompilation? That feels like a serious issue.

## Signatures

Type signatures with no names given to the parameters are so
unhelpful. Yes, it's a `report` type, but what the hell does it
actually do?

## Switches

Local switches are giving me Python virtual environment
flashbacks. People claim they're as good as lockfiles but in practice
they're way more tedious and easy to get wrong. Like how the heck do
you upgrade a switch's compiler? Having a switch tied to a compiler
version just feels unnecessary.

Also stuff is just slow. The ecosystem seems rather unstable too. Like
I have to manually pass an environment variable that was misnamed in a
recent opam/ocaml release.

## Writing Code

*praise*

OCaml is actually delightful to write. It's a pretty simple language
overall so you pick it up fast. And writing interface files does have
its charm. Not having to think about lifetimes is also quite nice.

## Early Return

OCaml desperately needs an early return/Try mechanism. Otherwise it's
quite tedious writing code. Similarly, the standard library/Core
doesn't seem to have enough niceties like `ok_or`, `ok_or_else`,
etc. I have to wonder how much of that is limited by lack of traits.

## Syntactic Sugar

Rust has various quality-of-life features that I'm missing. Like being
able to write bytes as a string prefixed with `b` is quite nice. Or
being able to write an incomplete function with `todo!` that will
still compile.

Or being able to unnest pattern matches with `let...else` or `if
let`. As far as I can tell OCaml doesn't have many ways to do pattern
matching other than `match`

## Early Return/Error Handling

So much of errors is returning early. OCaml has exceptions, but idk,
they seem a little icky compared to your standard sum type result. I'd
much rather be able to do an early return. Otherwise it's really easy
to end up nesting.

## Constructor Function Punning

You can't pun a constructor as a function? That's lame.

## Tooling Versions

So it seems like the OCaml language server is not backwards
compatible, so if you want to run 5.2.0 instead of 5.3.0, then you
need the OCaml LSP for 5.2.0. Buuuut you need to install that as a
regular package, which in turn screws up your package versions for the
actual code you need to write. Because I guess you can't have multiple
versions of the same package in a single switch?

First, tooling should *not* be a regular package, it should be
compiled into a fully static binary (or at least just rely in
libc). Second, even if it's a regular package, it should have its own
namespace for tooling.

## Generality and Syntax

Functional programming tends to lean towards general, abstract
solutions. This can be great, but it also creates some usability
problems. Like take `bind`. It generalizes a bunch of different
common code patterns into a single operator. But that means it doesn't
quite have the right syntax or name for any of them. Like in the
`Result.t` scenario, bind allows you to simulate early returns:

```
parse content
 |> Result.bind ~f:(fun ast -> typecheck ast)
 |> Result.bind ~f:(fun typed_ast -> codegen typed_ast)
```

(Yes I know this can be shortened, but this is more clear to my eyes)

What this code does is take the output of `parse content`, which is a
`Result.t`, i.e. either an `Ok 'a` or an `Error 'b`, and if it's `Ok`,
it calls the `fun ast -> typecheck ast` function. That next function
also returns a `Result.t`.

This is a nice pattern! It allows you to chain fallible
operations. But the naming is not ideal. `bind` really doesn't tell us
anything about chaining fallible operations. If I were to read this
without knowing any functional programming, I'd assume it's doing some
sort of setting of variables or connecting to some data structure.

## Where's The Function

One issue with functions mostly being organized by module is that
conversion functions can be defined on either the type being converted
or the resulting type.

## Testing

`ppx_expect` is sooo nice. Inline snapshots that don't require a lot
of effort!

## Match Syntax

I don't love how `match` doesn't use parentheses for its branches, so
you can't nest matches without wrapping the entire match expression in
parens. Basically whitespace formatting doesn't play well with nested
matches.

## Warnings

Why are warnings these opaque numbers that you have to memorize? Why
do you disable them using these numbers? It's just so unnecessary.

Also OCaml should have a proper linter versus an opt in warning system
where everyone can pick and choose random warnings to care about.

## Standard Library

The OCaml standard library needs some love. For one, the naming
conventions are...odd. Like what's the module for handling files? Is
it `file`, `fs`, `filesystem`, `f`? Nope, it's `In_channel` and
`Out_channel`. Do these modules even explain that they are used for
files? Nope.

Second, there's multiple alternative standard libraries. While they do
seem to have improvements over the main one, I'm just not a huge fan
of multiple options here.

## OCaml Manual

Maybe make the manual easier to contribute towards? It's currently a
weird LaTeX compiled to HTML setup. First, people aren't likely to
know LaTeX, and second, the toolchain to build the manual is
hilariously annoying to setup.

Plus the manual has no search function.
