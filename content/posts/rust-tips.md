---
title: "Rust Tips"
date: 2024-05-04T15:52:00-04:00
draft: true
---

Here's some tips about writing Rust.

## Traits

A lot of people seem to come into Rust with this idea that traits are
a more "professional" and therefore should be used. At least I had
that view. Now, I'm a little more skeptical of traits. I wouldn't
recommend defining a lot of them unless you really need the
abstraction. Using them or implementing them? Sure, that's extremely
common. But defining them just for the sake of abstraction is a bit
silly.


