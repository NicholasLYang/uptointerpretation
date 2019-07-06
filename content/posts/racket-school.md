---
title: "Racket School"
date: 2018-07-14T23:21:37-06:00
draft: true
---

I'm waiting for my flight back from Racket School 2018, so I figured I
might as well recommend the event while it's still fresh. Basically,
Racket School is a week long conference/workshop about Racket, the
programming language. It's run by the creators of Racket: Matthias
Felleisen, Robby Findler, and Matthew Flatt. For anybody who learned
Racket in school, that may seem a little odd–"Racket, you mean that
language I used in Intro CS five million years ago?" But in reality
Racket is far far more than just another Lisp in the Scheme
family. It's a programming language built around designing and
building other languages.

What does this mean? Basically, Racket has a lot of first class
support for designing small, domain-specific-languages (DSLs). Racket
practices a paradigm called language oriented programming. The way a
Racket programmer attacks a problem is by designing a small DSL to
solve it. It's a very intriguing approach to programming and actually
more common than you'd think. For instance, Ruby on Rails essentially
uses a DSL to define model associations and routing. Or JSX, which is
a DSL for designing component hierarchies.

At Racket School they basically indoctrinate you into the cult of
Racket. They do this by teaching you various uses of Racket, centered
around a particular theme. Last year it was designing the semantics of
programming languages. This year, it was more focused on the general
design of programming languages. This meant that they taught us topics
such as:

- Macros/compile time functions. Granted, pretty familiar to most Lisp
  programmers, but Racket has some neat ways of designing macros
  (error handling actually works!).
- How to use macros to create small languages with different features.
- How modules can be languages and vice versa. You pretty much import
  a language in Racket, which means that Racket projects often have
  multiple files in different languages (that sounds confusing, but
  trust me, it isn't).
 - How to write a parser and add non S-expression syntax to a language
   using a language called brag. Essentially you can write Backus-Naur
   and it works perfectly. Also includes some ergonomics that other
   parser generators like bison/yacc don't have (in my opinion).
 - Writing a type checker using a language called turnstile. Turnstile
   allows you to write essentially the type rules and have it
   typecheck. For instance, this is valid turnstile:

    [(_ f e ...) ⇐ t ≫
    [⊢ e ≫ e- ⇒ tin] ...
    [⊢ f ≫ f- ⇐ (-> tin ... t) ]
    ----------------
    [⊢ (#%app f- e- ...)]]
Granted, it's a little APL-esque, but it's still really cool.

 - How to implement Prolog in Racket. Okay....to be quite honest I got
   very lost on that one. But it was still very neat.

The best part of this? They sponsor students (both undergrad and grad)
to come. They will reimburse flights and give you free housing. And
since I have little to no experience in programming languages, and am
only an undergrad, yet managed to get funded, I'm pretty sure most to
all of the grad/undergrad kids on this list could get funded.

But why should you go? Well recently, I had found myself kind of
spinning my wheels with programming languages. I had made some decent
progress in parsing...but that's kind of like making a really really
good first sentence in your essay. Great, but you now need to write
the rest of the paper. A lot of PLT is really intimidating, with a lot
of dense theory involved. This past week gave me a really good view of
the way forward. In a gist, Racket School is a really good way to get
an excellent high level view of how to develop programming languages.

_TL;DR_: Go to Racket School, they will fund you and you will learn so
much your head will explode with lambdas.

Best,
Nicholas
