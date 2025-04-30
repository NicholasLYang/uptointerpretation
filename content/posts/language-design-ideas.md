---
title: "Language Design Ideas"
date: 2023-03-05T23:09:16-05:00
draft: true
---

Here are some things I'd think about if I were designing a language.

- Compilation unit. Tradeoffs of large vs small

- Reactivity. How can you change data and let listeners subscribe to
  those changes?

- Mutability. How can you give mutability without creating bugs?
  - Just provide mutability. What most languages do and it is terrible
  - Just don't provide mutability. What pure functional languages do.
  - Provide limited mutability. What Rust/OCaml does.

- How can you provide an error handling story that has simple, data
  structure oriented foundations, but also the ergonomics of
  exceptions?
