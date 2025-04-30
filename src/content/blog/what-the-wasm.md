---
title: "What the Wasm"
pubDate: "May 20, 2024"
draft: false
---

WebAssembly has been the new hotness for what feels like ages. The
creator of Docker [tweeted that Docker wouldn't have been necessary if
WebAssembly existed in
2008](https://twitter.com/solomonstre/status/1111004913222324225). People
have made talks speculating that it will eat the entire programming
model. Almost every language has been compiled to WebAssembly. So why
are people not using WebAssembly over 7 years after its release?

# Standardization Is Slow

First and most obviously, WebAssembly has been very slow to
standardize. Lots of key features like garbage collection, 64 bit
memory, threads, etc. have taken several years to nail down. This is
overall a very good thing. We don't want a repeat of the infamous 2
week development cycle for JavaScript. The standardization process is
slow because there are lots of very smart people thinking very
carefully about WebAssembly's design.

I will say though, that standardization is by its nature quite
slow. When a language is mature with a large userbase, this slowness
is much appreciated because it's important to not break existing
code. But WebAssembly is not widely used. So slowness just means
frustration for people who do want to use it but are blocked on
proposed features. You're going to lose more users in the beginning by
being too slow than by breaking code.

Of course, that's easier said in a dinky little dev tool than in a
language that will literally be run on billions of devices across the
world where one mistake will be forever immortalized. So maybe
standardization is not so bad.

# The Goalposts Changed

The more key issue here is that WebAssembly has expanded in scope to
the detriment of its original usecase. Let me explain. When
WebAssembly first came out of
[asm.js](https://en.wikipedia.org/wiki/Asm.js), it was billed as a
faster, simpler compilation target for the browser. You could run
Unreal in the browser! You could compile native code and run it on a
web page!

This felt like the main goal for a while. Build a language that works
well in the browser that most programming languages can target. But at
some point the goal shifted to building a universal, secure runtime,
akin to the Java Virtual Machine. You could build once, run anywhere,
as the old Java slogan goes.

One of the earliest versions of this idea shows up in Gary Bernhardt's
talk, [The Birth and Death of
JavaScript](https://www.destroyallsoftware.com/talks/the-birth-and-death-of-javascript). Gary
paints a vision where all code gets compiled to asm.js, even compilers
or operating systems. This is done because the browser's sandbox
elimintes the need for operating system memory isolation and system
call overhead, while also providing easy emulation and cross platform
capabilities. Gary paints this as semi-science fiction, but really,
this has become more or less accurate.

Eventually this became the top line goal of WebAssembly[^1]. All of the
focus appears to be on WASI, the WebAssembly System Interface, and now
the Wasm Component model, with the Wasm Interface Type (WIT)
language. These are really cool projects! You should check them out
because they're very fascinating work done by very smart people. But
they're **massive** in scope. They're trying to build a universal
interoperability standard, so Rust can talk to Python who can talk to
Go. They're trying to build essentially a brand new platform agnostic
system call interface for I/O, files, sockets, and so on. Oh and
they're trying to add innovations like capabilities based
security. Genuinely incredible stuff!

[^1]: You can tell it wasn't in the beginning because, well, why call
    it Web-Assembly when the goal is beyond the web? Why not call it
    UniAssembly or something?

This is all great for the universal compilation target. But it's not
really necessary or ideal for the browser. Do you want to run WASI in
the browser and mock out an entire filesystem? Probably not. Do you
need 5 different languages to play nice in the browser? Ehh
debatable. However, because this has become the top line goal, the
things that would be nice for the browser are stuck in stasis or a
half-hearted state of neglect. There's still no direct DOM
access. There's still not great integration with bundlers. Compiling
still sucks. You're still restricted to essentially a program that
passes in raw data and passes out raw data, no side effects,
nothing. Yes, there are ways around these limitations, but they are
still workarounds.

As I've already [detailed in a previous
post](https://uptointerpretation.com/posts/webassembly-woes/), this
halfheartedness towards the browser is readily apparent if you try to
do anything non-trivial. I tried to get my Rust code to call some C
code. Getting the compilation took a bunch of finagling of Rust's
toolchain with a C cross compiler (thank god for `zig cc`). Then I ran
into interop issues where apparently the C ABI is broken on
`wasm32-unknown-unknown`, so either I could move to
`wasm32-unknown-emscripten`, which is barely maintained, or try
`wasm32-wasi`. But then I'd have to add a whole WASI shim to the
browser, because why would the browser implement an entire operating
system? Oh and a good WASI shim that runs in the browser doesn't
really exist either.

What might be nice is if we could create a `wasm32-web` target that
could be independent of WASI and build out all of the features that
web developers actually need to write WebAssembly in the browser. But
of course, if WASI were to become mature and usable in the browser,
then we'd end up in a fragmented ecosystem.

# Underconsidered UX

Another problem is that while a lot of stuff is possible for
WebAssembly in the browser, it's really really painful to use. As I
mentioned before, it was really hard to cross compile the C code to
WebAssembly. Often times you have to literally clone random GitHub
projects to get the right libraries or fiddle with clang flags. And
hooking that up into a consistent build with JavaScript is not great
either. Lots of bundlers don't understand WebAssembly, or worse, they
half understand it and do some weird magic that doesn't work.

The documentation for this stuff is also not great. Lin Clark makes
some fanastic illustrations for the high level conceptual parts of
WebAssembly, but I'd love more detail on the nitty gritty. Most of my
knowledge comes from reading random blog posts that are often
inaccurate or outdated.

This is also at the language level. Rust is arguably the best language
right now for compiling to WebAssembly and it's still not great in
many ways. You can't easily tell if a crate can be compiled to
Wasm. This is especially tricky since it's often not the actual crate
that's the issue, but instead some random transitive dependency like
[socket2](https://github.com/rust-lang/socket2/issues/268). There's
the aforementioned 3 targets that each behave slightly
different. `wasm-pack`, the defacto build system for Rust Wasm in the
browser, doesn't work for the WASI or Emscripten targets.

# Always Bet On JavaScript

When WebAssembly first came out, there were these flashy posts about
how soon we could all be rid of JavaScript, how we could be writing
our front ends in Rust or Python or COBOL. That's not come to pass in
the slightest. I've always assumed that it's because WebAssembly
hasn't been mature enough to adopt. However maybe instead of
WebAssembly being too immature, maybe it's a problem of JavaScript
being too good. At this point, JavaScript has been fully
revolutionized to be a good UI language. We have lots of really
impressive frameworks like React, Svelte, Solid, etc. There's great
tooling, with bundlers, linters, package managers.

Maybe the designers of WebAssembly, consciously or not, recognized
that JavaScript was becoming a little too entrenched for Wasm to
compete for the browser. Maybe that's why they moved towards the
universal compilation target goal. After all, when users would be
using WebAssembly, they wouldn't actually be writing WebAssembly. They
would be writing Rust, Python, etc. And each of these languages would
have to develop its own browser based libraries with its own set of UI
libraries and components. Would these individual languages be able to
beat JavaScript, the language with a 20 year head start that has
co-evolved with browsers and the Web? Probably not.

# Conclusion

There's a very high chance that in 3-4 years all of this is
solved. The component model and WASI become mature, more people start
writing WebAssembly, which in turn brings more investment into tooling
and browser compatibility. We get DOM access and proper interop with
JS via WIT definitions.

I'm really excited for that future. But I'm also a little more
measured than before. Because it's been 7 years already. I started
learning WebAssembly in 2019. It was immature, frustrating, and not
that capable. It's now 2024. It's still immature, frustrating, and not
that capable. And now part of me wonders if it'll ever change. Part of
me wonders if having committees toil away for this long was really the
right move. Part of me wonders if moving away from browsers to a more
ambitious goal was really necessary before shipping a feature complete
Wasm. Well, I guess we'll see.
