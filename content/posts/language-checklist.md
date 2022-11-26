---
title: "Language Checklist"
date: 2022-10-06T22:30:50-04:00
draft: false 
---

If you're creating a programming language, here's what I think you should have:

- Error messages with source code locations, snippets and explanations

- A language server

- A go-to documention source that most libraries use

- A linter and formatter

- A new project generator

- An online playground

- A way to manage different versions of the language

- A C interop

- A package manager

- A build tool that works with packages

- A nice selection of libraries, either bundled with the language or in the ecosystem that handle:
  - Data structures
  - Serialization
  - Web servers
  - GUIs
  - Databases
  - Authentication
  - Error handling
  - Regexes
  - Cryptography
  - Testing

- A way to handle errors gracefully and without too much boilerplate.

- A beginner’s guide. Ideally one that is not just a list of language features.

- Searchable documentation.

- Online spaces where people can ask for help like Discord, reddit, etc.

- A way to handle uninitialized values. Ideally type safe.


# Caveats and Explanations

Of course, creating all of these things is far beyond the abilities of
a single person. Don't feel bad if you can't do it all. It
requires a significant amount of investment.

And of course, this is all my opinion, not some ordained list of
requirements. Feel free to disagree. There's also a bunch of stuff I
probably missed.

With some of these, people may push back and claim these are optional
or even undesirable features (package management, typesafe
null). I’d disagree. Often times this opinion that these tools are not
necessary comes out of particular situations, whether that’s operating
in a company-wide monorepo, a tendency to write code completely from
scratch, or simply idiosyncratic ideological preferences. For a
language to extend beyond those who are in these particular
situations, it should provide these features.

In a sense this is a self fulfilling prophecy, because if a language
neglects or dismisses a user's needs, the user will not join that
language community. What's left is a community that is formed of the
people who do not have these needs and therefore see no reason for
them to be added.

What amazes me about this list is the paucity of languages that
fulfill these requirements. How many languages have a standard site
for documentation? How many of them make it easy to search the
language documentation? Maybe it's entitled to expect so much of
projects that are essentially volunteer efforts, but I strongly
believe these aspects would provide an incredible return on
investment.

P.S. This list may seem overtly pro-Rust in that it cites various
features that the Rust ecosystem has. That is true, but I will note
that Rust still needs improvement as well: docs.rs, while great for
API documentation, is not great for the walkthrough, linear based
documentation that I find useful. Rust also lacks good GUI and
authentication libraries. That said, I will admit that I am looking at
this from a very biased place. I'd love to see what people of other
language communities have as their language checklist.
