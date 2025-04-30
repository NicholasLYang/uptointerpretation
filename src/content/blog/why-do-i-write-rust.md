---
title: "Why Do I Write Rust?"
pubDate: "Oct 29, 2023"
draft: true
---

There's a lot of reasons why someone may write Rust. They could like
the performance. They could enjoy the type system. Or maybe it's just
their job. Here's why I write it.

When I was first learning programming, I heard about these crazy
programming languages like Haskell or OCaml. All the really smart kids
were using them and talking about functional programming and how it
was like math and super cool. Well hey, I like math, I want to be one
of the smart kids. So I tried to learn functional programming. Mostly
I tried to learn Haskell.

## Haskell

I used a few different resources like [Learn You A Haskell for Great
Good](http://learnyouahaskell.com/) and [Haskell Programming From
First Principles](https://haskellbook.com/). And I learned a lot of
really cool stuff like currying, type classes, monads, lambda
calculus, and so on. But there'd always be a point where I'd want to
do more, like write a real meaty project in Haskell. In my view,
that's really where a language shifts from something you're learning
to something you know. If you can write a non-trivial project in it,
then you can claim to know it. When I'd try to make that shift, things
would fall apart[^1].

[^1]: Important note, these are my experiences circa 2016-2017. I
    expect that things have improved massively since then.

The first issue that I'd encounter is with documentation. The Haskell
libraries that I used would feel like abandoned relics. Even the most
basic stuff like finding a tutorial on building a simple web
application was unnecessarily difficult. And after that tutorial,
you'd be told to just look at the API documentation, which was
generally a big confusing set of type signatures declaring that a
server was really an IO monad combined with a State Applicative and a
List Functor. Which is great if you have the experience to (f)map your
existing knowledge onto the library. But if you're not comfortable
doing that, or if you're trying to learn for the first time, then it's
not ideal. Want to add authentiation to your app? Go read a different
language's explanation of JWT auth, then come back and apply it to
Haskell[^2].

[^2]: This is likely significantly easier with tools like Copilot or
    ChatGPT.

The second issue came with tooling. I can't tell you how many times I
switched between cabal and stack, desperately trying to get my project
to build. I'd try to install the package versions listed in the
tutorial only for the build system to throw up its hands and fail.

If I wanted nice editor tooling, I'd have to configure a bunch of
emacs packages, and even then stuff like go-to definition was finicky
at best. This was especially important when I was learning, because
good tooling made it a lot easier to understand what the heck the code
was doing. I could find where a function was defined; I could get
inline type hints; I could get the types of the function parameters as
I wrote out the function call.

The third came with community. The Haskell community seems quite nice!
I don't have any issues with the people itself. The problem came with
finding them. From my experience, the best place to get help with
Haskell was on IRC. This meant that I had to spin up an IRC account
(sorry, I don't have the nerd cred to say I was on IRC already) and
either engage with people or wait until someone else asks the same
question. That's a huge barrier to entry!

I'd try repeatedly to get into Haskell and always feel a little guilty
about never succeeding. My best accomplishment was writing a parser in
it, albeit a very non-idiomatic, awkwardly written parser.

## Rust

Around this time I was also learning Rust. And don't get me wrong,
Rust had its challenges too. However as a whole, it was significantly
easier. Instead of searching high and low for documentation, there
were standard places for documentation like docs.rs or GitHub. There
was a culture of writing high quality starter books that would walk
you through the process, start to finish.

For tooling, `cargo` just worked out of the box. No messing with cabal
or stack. No figuring out weird dependency issues. And there were
plenty of active forums for Rust developers.

Rust also felt more familiar. It had mutability and statements and
brackets. I didn't have to do an incantation to increment a loop
variable. I didn't have to think of things entirely in terms of its
highest abstractions. Eventually, yes, I began to model a lot more of
my problems using Rust idioms, but in the beginning I could just write
code.

Going to Haskell felt like wandering into a country where the language
had no cognates, no overlap with my native tongue, while going to Rust
felt like learning a close cousin language. I could leverage my
existing knowledge and build off of it.

# Takeaways

I don't mean to imply that languages should all be similar. There's
value in having languages that are different, that stretch the
boundaries of convention[^3]. We need languages that are inherently
more experimental. But there is a cost in terms of who uses the
language and for what.

[^3]: Indeed Rust itself has influences from Haskell

And even if a language is challenging to learn, there is so much more
than just the core language. There's tooling, there's documentation,
there's the community. Rust itself is a challenging language. The
borrow checker is extremely foreign from any other mainstream
language. Traits, macros, and lifetimes are all very confusing. But
that's it. The only hard part about Rust is the language. Everything
else is made to be as simple as possible.
