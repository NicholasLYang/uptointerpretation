---
title: "The Community Is The Language"
pubDate: "Nov 13, 2024"
draft: false
---

Why do people use JavaScript even though it's a poorly designed
programming language? Why is PHP still kicking? Why does OCaml have a
smaller community than Go? It's because of the stuff beyond the
literal language. It's because JavaScript has a great package
ecosystem. It's because PHP has a vast community of users. It's
because Go makes it so damn easy to get work done. It's because of the
people who steer the language.

When most programmers judge a programming language, they judge the
holistic experience of using the language, whether that's getting
started with the language, asking for help, or even hiring developers
who write the language. They care about how it *feels* to use the
language in all aspects.

What's frustrating is that this is not how many programming language
designers view languages. This makes sense. A programming language
designer tends to be someone who likes to nerd out about semantics
and types and compilers. It's understandable that they'd focus on
those aspects when comparing languages.

But this difference in focus leads to a fundamental disconnect between
designers and users. A designer may say that two languages have
the same feature, but neglect to mention how it *feels* to use the
feature in either language. I can implement borrow checking in my
language in a way that produces confounding error messages, overly
technical language, and requires a working knowledge of linear type
systems. And yes, both my language and Rust will "have a borrow
checker", but that totally misses the point.

Part of the issue here is that designers play a very different role
than that of most software engineers. They're like dictionary
authors. They know every little facet of the language, down to the
most obscure details. But is writing a dictionary comparable to
writing a novel? Of course not!

This is why Go is such a brilliant success. Go is a language designed
by people who do not care about programming language theory. I mean
this in the nicest way. The core language of Go could have been
invented in the 80's. There's classic mistakes like null pointers or
the absence of generics. But they bundled this core language with an
excellent formatter, some simple tooling, and very clear
conventions. Writing Go is a very comfortable experience. You can take
a problem and map it onto Go code without much effort. You can also
build, test, and ship that code with ease. Yes, the language is quite
limiting. Yes, the type system sucks. But Go *feels* great to
write.

## Two Groups

For the longest time, there were main groups of programming languages:
those created by programming language theorists and those created by
programming language hackers. Languages created by the first group
have fancy theoretical features like type inference, generics, macros,
but very little in the way of ecosystem, tooling, or documentation,
think Haskell or OCaml. Languages created by the second group would be
easy to use, nice syntax, good libraries, but not much theory behind
them, think Python or Ruby.

Of course, both communities would object to these categorizations. And
yes, OCaml and Haskell do have tooling and documentation; Python and
Ruby do have theory in them. But the degree is extremely different.

Theory based languages would regularly encourage single letter
variable names and type signatures as documentation. The tooling would
consist of an emacs plugin and a half-broken build system. Yes, it
worked for some, but for many it did not.

The communities were also often quite idiosyncratic. A main way to get
help on Haskell was to frequent the IRC channel. Yes, the people on
there were quite helpful, but IRC is both a particularly temporary
medium, and not exactly friendly to people who are not old school
Linux nerds. There was also the issue of terminology. The common joke
was that if you asked what a monad was---monads being a key part of
Haskell---you'd get an answer like "monads are just monoids in the
category of endofunctors". While that's obviously an exaggeration,
functional programming communities really do love their math
terminology. Instructional material would spend a lot of time
explaining how this code was really an Applicative Functor, and how
you could generalize this code to work on any such Applicative, which,
yes, is neat, but doesn't make it any easier to write a web server[^1].

[^1]: Cue someone explaining how I'm wrong because servers are really
    Applicative so you can use the `>>=<<!>` operator to easily do
    multithreading.

While the hacker based languages like Ruby or JavaScript would at best
have a rudimentary type system with no inference, and at worst
complete, fully dynamicism. Safety would be provided via a runtime and
constant, incessant testing of even the simplest behavior. Ever wonder
why the Ruby community fell in love with Test Driven Development? Well
when your language can override methods at runtime and anything can be
an object even `nil`, and all operators can be overloaded, well,
testing everything in sight seems like a good plan.

## Rust

However, there is an exception. Rust is a language designed by a rare
combination: the programming language theorists and the programming
language hackers combined. It has all of these neat little details and
fancy programming language concepts like traits, generics, macros,
etc. But it also has a syntax that is familiar, nice error message,
carefully designed tooling, and a broad ecosystem. It manages to take
the powerful tools designed by theorists and put them into a usable
package.

The personnel here was truly key. Having people with PhDs in
programming languages like Niko Matsakis or Felix Klock, while also
having developer tooling gurus like Yehuda Katz or Steve Klabnik or
Esteban Kuber, well that creates a language that has a really
compelling combination of features.

In other words, Rust is like you combined JSConf with PLDI. You got
the theory and the UX. In retrospect, it's not so surprising that such
a language came out of Mozilla. You needed people who taking on really
challenging engineering projects, and people who were steeped in
theory. You needed a place that is building systems level projects
like browsers and also hiring PhDs. Rust only exists because of the
unique combination of people who built it.

## Open Minded Communities

I mentioned tooling as the main contribution from Ruby and JavaScript
to Rust, but that's not exactly it. I don't know precisely how to
describe it, but both communities have this sense of open minded
casualness to them. Like people are generally very willing to try
different ideas or build new stuff. This could be Ruby's extremely
flexible syntax, or JavaScript's wacky, ever changing ecosystem. Yes,
this comes at some risk. Ruby is quite painful to parse and there's
always something breaking in the JavaScript world. But it also comes
with some great benefits. The JavaScript world is constantly
innovating. It's not a surprise to me that JavaScript eclipsed Python
in tooling. A tool like esbuild or SWC only comes out of a willingness
to reinvent the wheel and to try something unusual.

Perhaps it's because JavaScript was forced onto so many people. Since
it was the only language available in the browser, everyone, from kids
playing around with their own site to begrudging compiler engineers,
were put into the same community. And because that community was so
broad, JavaScript itself evolved, first through ES6, which drastically
improved the language, and then through tooling and further language
developments.

To reiterate, a diverse community results in new ideas and innovation.

## Not Just Rust

At this point you must be thinking gosh, this person is such a Rust
fanboy. And yes, I do like my Rust. But Rust isn't the only language
that has this combination of theory and practice. TypeScript is a
fascinating example of theorists and hackers working together to adapt
an existing language. You could argue C# is another great
example. Kotlin and Swift have some real solid theoretical foundations
too.

And really, this combination will become more and more
common. Existing languages are evolving with the times. OCaml, for
instance, has been investing into packages and documentation. I still
wish the promise of ReasonML had come through; OCaml with good syntax
and easy package management was truly wonderful. As there's more
cross-pollination between these groups, as spaces open up where
programming language theorists can chat with developer tooling
hackers, we'll start to see some truly fantastic new languages. I
can't wait.
