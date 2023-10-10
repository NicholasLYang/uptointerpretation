---
title: "How Big Could Rust Be?"
date: 2023-09-29T13:37:45-04:00
draft: false
---

Rust is in an interesting stage as a programming language. It's gotten
a lot of mindshare, and a decent amount of adoption. It's not at the
point where people are learning Rust by the droves, but there are a
respectable amount of people who write Rust. As for jobs, Rust
positions are not ubiquitous, though they are increasing in
number. Basically, Rust is past the threshold of only being used by
programming language nerds, and onto the stage of being a language
that real companies depend on, i.e. an up and coming mainstream
language.

Which begs the question, where is Rust going? It could level out in
growth, and end up a respectable, albeit niche language. Perhaps on
the order of Ruby; used in various companies, but not actively growing.

It could keep growing and end up a somewhat popular language, maybe on
par with PHP or C#. Both of which are languages with massive
communities, however they're not truly ubiquitous

Or it could end up at the top, with the languages that are so popular
they're almost impossible to avoid. The A-listers of Java, Python,
JavaScript, C++. Rust could become so common that people
learn it in college, that companies hire Rust developers by the
thousands, that entire ecosystems are built with Rust.

This may sound absurd. I don't think it is.

# What Is Rust Replacing?

If you're like me and get most of your programming news from Hacker
News, Twitter and reddit, you'd be forgiven for thinking most people
are writing JavaScript and Python. And yes, a lot are. But they're
just the tip of the iceberg. Underneath is a massive, hidden
undercurrent of C and C++. Almost everything you use is supported by
this code. It's device drivers, browsers, text editors, network
protocol parsers, compilers, and so on. Heck, a lot of the
foundational Python libraries rely on tightly optimized C and C++
code.

In 10 years, a significant portion of this code will be in
Rust. Perhaps not all; Zig, Go, and other languages will certainly
take a portion. Nonetheless I anticipate Rust will take a large
slice.

Why not C/C++? Well, there's the standard answer of security issues,
memory bugs, etc. Those should not be underlooked. Bugs cost money, as
do security issues. Furthermore, there's the issue of hiring. Before
Rust became popular, the general sentiment was that it would attract
ex-C++ users and ex-functional programming users, since those were the
two main lineages behind Rust. And indeed both groups were
attracted. But a third group emerged of ex-scripting language users,
people who wrote Ruby and JavaScript.

What people didn't realize was that there were a large contingent of
people who wanted to write systems level code but for whatever reason
did not want to write C or C++. You can create all sorts of
uncharitable interpretations for this phenomenon, but the reality
still stands. Personally, I fall into this camp. I had dabbled with C
and C++, but I never wanted to commit to writing it full time. Rust
made tackling low level problems a lot more palatable.

From a hiring perspective, this opens up jobs to a wider group than
before. Heck, you might not need to even hire, as you can retrain your
existing JavaScript or Python developers to use Rust. This is not
apparent currently, because Rust is still relatively niche, but as the
language gains in popularity due to the previous factors, there will
be an additional advantage to using Rust.

To recap, Rust lets you avoid memory bugs and security issues, while
being palatable to a wide group of developers.

# Why Not Rust?

Indeed, that is a question worth asking. And in some cases there are
quite reasonable answers. Rust doesn't support all of the same targets
that C or C++ does. It doesn't quite have the ultra-low level, super
performant capabilities of C or C++. The tooling story is good, but
incomplete. The borrow checker does limit some common programming
patterns. These issues can all be solved with time, but as of today
they are still very much concerns with Rust.

Otherwise, there's the issue of inertia and ideology. A lot of people
are sticking with C/C++ because that is what is taught and therefore
that is what they know. Moreover, once they have learned C/C++, they
have become accustomed to its mental model, and see Rust as an affront
to that model. Much like the programmers of yore [who balked at
writing compiled languages instead of
assembly](http://www.catb.org/jargon/html/story-of-mel.html), they
have become used to a certain degree of freedom in their
programming. Rust fundamentally challenges the ideology of C++, and
that can create discomfort.

But as time passes, as generations come and go, more and more
programmers will come of age with Rust as their initial context for
systems level programming. They won't see it as restrictive because
it'll be natural to them.

Indeed the biggest challenge to Rust is not C, C++, Go or any
established language. It's the next language---the language that
leapfrogs Rust by being a better version of it. Maybe it has a better,
easier borrow checker. Maybe it's faster to compile. That is the main
obstacle towards Rust becoming a true A-list language[^1].

[^1]: Even then, it's quite unlikely that a new language will suddenly
    leapfrog Rust, given that Rust has a 15 year head start.

# Beginner Friendly

This belief that Rust will win the long-term game stems from a key
observation: Rust is **really really** beginner friendly. This may
seem counterintuitive, since the borrow checker is frustrating and
there are a lot of things to learn, but language complexity is only
part of the story. Rust comes with simple, excellent tooling. You can
jump into almost any project and build it with `cargo build`. There's
a built in formatter and linter that helps steer your code in the
right direction. You don't have to fiddle with compiler flags or
Makefiles. The incidental complexity of setting up a tool is much more
frustrating than the inherent complexity of a language.

And the error messages! Rust has fantastic error messages. They're
often so good that you can solve your issue simply by reading the
message and applying the recommended solution. You can learn the
borrow checker simply by writing Rust and fighting through the
compiler messages (although I wouldn't recommend it). The difference
between a good error message and a bad one is a long detour through
StackOverflow and GitHub Issues versus a quick fix that keeps your
flow state.

Packages are also massively helpful for beginners. Instead of rolling
my own implementation, I can use a package written by a more qualified
developer. Suddenly something like creating a SQL linter is within
reach of a novice Rust developer. You can import a SQL parser, import
an error printing library, and you're off to the races! C++ has some
packaging options, but nothing on this level.

Rust's strong culture of documentation means you can jump into almost
any library and figure it out. I still remember trying to learn
Haskell and having to discern documentation from a function type
signature. Or having to dive into the code to understand a JavaScript
library. There's still some rough edges here, but the ecosystem is by
and large excellent at documenting libraries.

One other undersung aspect is syntax. I know it's gauche to discuss
syntax as meaningful in programming language circles, but it truly is
important. That is how beginners first interact with your language and
interpret its meaning. Rust, by staying relatively close to C/C++
syntax, made it much more familiar to users than other languages with
more esoteric syntax. I know that I've encountered OCaml or Haskell
code that is so syntactically foreign that I struggle to understand
it.

# Other Languages

Rust can and will replace other languages too. We're seeing a trend of
JavaScript developer tools written in Rust, such as linters,
compilers, build tools, etc. This is partially for performance, yes,
but also because Rust is a fantastic cross-platform language. Turns
out, when you're building for 6 different targets (x86/aarch64 *
macOS/Linux/Windows), you want a language that has proper abstractions
around operating system APIs, file systems, encodings, etc.

Likewise, Rust could replace a lot of functional programming
languages. It offers a similarly sophisticated type system, and
similar safety guarantees, but with better ecosystem, better escape
hatches and better performance.

Some critics have cited the complexity of borrow checking and
lifetimes as a reason for OCaml or Haskell over Rust. And those are
valid critiques in many cases. However, the better tooling story still
makes Rust a more compelling option for many developers. OCaml is
starting to improve its tooling---I'm quite excited actually to see it
go through a renaissance---so if it reaches a point where it's
comparable to Rust, I could see it siphoning back some users from
Rust.

And really, it's not that Rust is per se better than these other
languages. That's completely subjective and there are certainly
arguments against Rust. But Rust is *good enough* for a lot of
people. It has the performance and low level capabilities that the
C/C++ community desires, the type system and mutability control for
the functional programming community, the ease of use for the
scripting language community. By choosing Rust, there is something for
everyone.

# Conclusion

Rust will likely become a massively popular language in the next 10
years. It will eat into a significant chunk of the C/C++ market, as
well as other languages. It will do so because of advantages in
safety, hiring, beginner friendliness, tooling, cross-platform support
and so on.

If you came away from this post thinking it's just the ramblings of a
Rust fanboy, then I don't blame you. The goal of this post wasn't to
provide an even-handed perspective on Rust. Rust has plenty of flaws
like slow compilation, borrow checking limitations, the orphan rule,
and so on. But the benefits of Rust, especially compared to the flaws
of existing languages, outweight these limitations.
