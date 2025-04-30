---
title: "My Usual Crates"
pubDate: "Oct 13, 2024"
draft: true
---

I'm a habitual project starter. I usually write Rust since that's the
language I know best. I've found that I use the same crates again and
again. These will probably be pretty obvious to experienced Rust
users, but I figure it's worth writing down.

**anyhow**: Rust error handling can be quite tedious. Anyhow makes it
easier. Is it the "right" way to handle errors? Probably not. It
essentially turns everything into a blank `anyhow::Error`, which isn't
great for documenting error states. But in a pinch for a small
project, it's fantastic. **thiserror** is the other option, but I
think using it prematurely is too tedious and not worth it.

**camino**: Rust paths are not UTF-8, and that's *probably* the right
choice, but I generally don't need non-UTF-8 paths. And the cost of
supporting non-UTF-8 paths is that you lose all of the benefits of
being able to treat a path as a string, such as the nice [`str`
APIs](https://doc.rust-lang.org/std/primitive.str.html) that Rust
provides. Plus, in some cases, non-UTF-8 paths can introduce more
usability issues, such as the [Makefile
problem](https://wiki.mercurial-scm.org/EncodingStrategy#The_.22makefile_problem.22). Read
the [camino docs](https://docs.rs/camino/latest/camino/) for a better
explanation. As an alternative, you could use
[**bstr**](https://docs.rs/bstr/latest/bstr/) which lets you use
strings that may not be UTF-8. However, that doesn't mean you can just
use bstr and have everything work. Many of bstr's APIs are
[effectively
doing](https://docs.rs/bstr/latest/bstr/#differences-with-standard-strings)
`to_string_lossy`. That's a reasonable tradeoff in some cases, but
maybe not in others.

**clap**: I build a lot of CLIs. Even if the project isn't meant to be
a CLI long term, having one is a great way to play with your
code. Clap parses your CLI args. You can generate the args with a
builder pattern, i.e. method calls on a variable, or a derive pattern,
i.e. macro calls on a struct definition. I generally use a
derive. Yes, yes, clap is quite large and can inflate your binary. But
it's worth it just to automate the tedium of parsing args. Parsing
arguments is a game of edge cases. I'll happily pay the binary tax
instead when I'm getting started.

**axum**: This is my go-to simple HTTP server library. It works well,
although sometimes the handler type errors are a mess. You can add a
debug handler macro to make the errors nicer. I just keep those on
permanently.

**itertools**: All the random iterator methods you need. I like
`sorted`, which sorts an iterator.

**tempfile**: Great for spinning up test files.

**colored**: Colors are really useful for creating visual hierarchy in
CLIs. They just feel nice too. Of course, your UI should be usable
without color too, but you can still add bold/italic for
emphasis. Some people recommend **termcolor** and it does seem nice,
but colored is super simple.

**cargo-dist**: Excellent way to distribute binaries of your
project. I love the "compile to GitHub Actions" model where it takes
your config in Cargo.toml and produces an action. Definitely has its
pain points for more complicated build setups, but for a starter
project, wonderfully simple.
