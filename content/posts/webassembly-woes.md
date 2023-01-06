---
title: "Webassembly Woes"
date: 2023-01-01T18:02:35-05:00
draft: false
---

I've been playing around with a new language called
[Vicuna](https://github.com/nicholaslyang/vicuna). It's very much in
the early stages, just a simple parser that compiles to JS. One thing
I like to do when I write a compiler is to build a playground so that
I can inspect the various compiler stages and test everything in an
interactive fashion.

To build this playground, I decided to compile my compiler to
WebAssembly. That way everything could run in browser in a tight loop.

I had done this before at my previous job, where it was a breeze. I
used `wasm-pack` to build the compiler into a library that could
interact with JS, and then hooked it up to a React app.

However, this time I had decided to use tree-sitter as my
parser. Tree-sitter is a wonderful parser generator that I've written
about before. It generates parsers from a grammar specification. These
parsers are written in C and generally compiled alongside the Rust code.

Turns out this whole process gets a lot harder when you compile
everything to WebAssembly. First there's the issue of which
WebAssembly backend to use. There's currently three ones that I know
about: `wasm32-unknown-unknown`, `wasm32-unknown-emscripten`, and
`wasm32-wasi`.

Starting from the top, `wasm32-unknown-unknown` is a backend that is
pretty barebones. It doesn't have a lot of facilities for interacting
with the outer world. That said it's what works with tools like
`wasm-pack` so I decided to go with it. And of course, I'm rewarded
with a nice large build error:

```
The following warnings were emitted during compilation:

warning: In file included from /Users/nicholas/projects/vicuna/tree-sitter-vicuna/src/parser.c:1:
warning: /Users/nicholas/projects/vicuna/tree-sitter-vicuna/src/tree_sitter/parser.h:10:10: fatal error: 'stdlib.h' file not found
warning: #include <stdlib.h>
warning:          ^~~~~~~~~~
warning: 1 error generated.

error: failed to run custom build command for `vicuna v0.1.0 (/Users/nicholas/projects/vicuna)`

...

error occurred: Command "clang" "-O0" "-ffunction-sections" "-fdata-sections" "-fPIC" "-g" "-fno-omit-frame-pointer" "--target=wasm32-unknown-unknown" "-I" "/Users/nicholas/projects/vicuna/tree-sitter-vicuna/src" "-Wall" "-Wextra" "-o" "/Users/nicholas/projects/vicuna/target/wasm32-unknown-unknown/debug/build/vicuna-4cf7e78393ef5ba4/out/parser.o" "-c" "/Users/nicholas/projects/vicuna/tree-sitter-vicuna/src/parser.c" with args "clang" did not execute successfully (status code exit status: 1).
```

Great. This error makes some sense because tree-sitter's parser tries
to use the C standard library, and `wasm32-unknown-unknown` requires a
special C standard library, which is not provided by the standard
clang toolchain I was using. I tried various methods of including the
C standard library which didn't work, such as
[wasm-stdlib-hack](https://github.com/guybedford/wasm-stdlib-hack)
likely because I'm not familiar with C at all.

Moving on, I tried `wasm32-unknown-emscripten`. And to my delight, it
worked! The code built with no issues. This is because emscripten
provides the C stdlib by default. Even better, emscripten outputs a JS
file that I can just include in my webpage. However...this JS file is
not nice standard ES modules with `export` and `import`, but old
school CommonJS. Upon [asking about how to have
`wasm32-unknown-emscripten` emit
ES6](https://users.rust-lang.org/t/emitting-es6-module-for-wasm32-unknown-emscripten/84684),
I'm informed that the `wasm32-unknown-emscripten` backend is no longer
used[^1] and that also there's an ABI incompatibility with Rust and C for
`wasm32-unknown-unknown`.

[^1]: Side note but it's really frustrating how quickly the tech world
    goes from "this is in beta don't use it" to "you have to use this
    don't use the old version". There should be an period of overlap
    where both are acceptable.

Okay...that's unfortunate, but silver lining, `wasm32-wasi` should
work! And WASI should be the right fit because I need the C stdlib to
work and WASI provides all the syscall-like functionality that stdlib
would require. So let's compile! And yay...we get the same error:

```
The following warnings were emitted during compilation:

warning: In file included from /Users/nicholas/projects/vicuna/tree-sitter-vicuna/src/parser.c:1:
warning: /Users/nicholas/projects/vicuna/tree-sitter-vicuna/src/tree_sitter/parser.h:10:10: fatal error: 'stdlib.h' file not found
warning: #include <stdlib.h>
warning:          ^~~~~~~~~~
warning: 1 error generated.

error: failed to run custom build command for `vicuna v0.1.0 (/Users/nicholas/projects/vicuna)`
```

This is where I gave up for a couple weeks. Revisiting this issue
while doing some programming over the holidays, I decided to do what
any good [magpie
developer](https://blog.codinghorror.com/the-magpie-developer/) would
do and add another programming language. Namely, I decided to use
zig's C toolchain that offers [easy cross
compilation](https://andrewkelley.me/post/zig-cc-powerful-drop-in-replacement-gcc-clang.html). And...it
works! The toolchain compiles. In fact it compiles for both
`wasm32-wasi` and `wasm32-unknown-unknown`!

Okay great! Let's try using `wasm32-wasi` since that appears to be the
anointed "modern" backend. Okay, it appears that for the browser we
need a polyfill for WASI. Looking around,
[`wasmer.js`](https://github.com/wasmerio/wasmer-js) is a good
candidate. First try, I get a `Buffer is not defined` bug, which
usually indicates the library only works on Node. Reading an issue,
turns out I have to polyfill `Buffer` and add it to the `globalThis`
object. Not ideal, but okay. And then I get the following:

```
Uncaught (in promise) Error: Failed to instantiate WASI: RuntimeError: JsValue(TypeError: import object field 'env' is not an Object
```

Weird. Because I'm lazy, I'm not going to chase down that path just
yet. Instead let's try using `wasm-pack` with
`wasm32-unknown-unknown`. Generate a Wasm stub with `wasm-pack new`,
then build with `CC="zig cc" wasm-pack build`, and then import the
newly generated `pkg/` in my React app. There's some unimportant
errors around importing Wasm, and then I get:

```
 Failed to resolve import "env" from "../vicuna-wasm/pkg/vicuna_wasm_bg.wasm". Does the file exist?
```

These are oddly similar errors and from [Googling
around](https://stackoverflow.com/questions/54598317/rust-wasm-module-not-found-error-cant-resolve-env-in),
it appears that they're caused by the Rust code using `extern "C"` and
linking to C code, namely the `tree-sitter` parser.

# Where I'm At

At this point, we've caught up to the present. I'm still stuck on
these various issues and cannot get this damn compiler to build to
Wasm. There's three approaches I could take:

- Use `wasm32-unknown-emscripten` and either figure out how to
  integrate the old style JS into my bundling setup, or figure out how
  to emit ES modules.
- Use `wasm-pack` and `wasm32-unknown-unknown` and figure out this env issue.
- USe `wasm32-wasi` and `@wasmer/wasi`, and figure out this env issue.

These are all feasible, but they all beg the question: **Why is this so
damn hard?**

# Why Is This Hard

There's a few reasons. The first is that WebAssembly needs a special
sysroot, basically a bunch of libraries like stdlib and stdio. With
`wasm32-unknown-emscripten` that's fine because emscripten provides
this sysroot. With the other compile targets you need to provide it
yourself. Which...frankly is just annoying. I don't know why it took
Zig for someone to create a C compiler that bundles sysroots for
various targets. Really there should be a C compiler that has
something akin to Rustup where you can install targets with sysroots.

The second is that there is far too much churn and outdated
information around Rust and WebAssembly. I don't want to be switching
between three different compile targets because there's one that
compiles without any trouble (`wasm32-unknown-emscripten`), there's
one that has somewhat decent tooling (`wasm32-unknown-unknown`), and
there's one that is actually recommended as the "modern" solution
(`wasm32-wasi`).

Third, the tooling is simply not there. The truth is that none of this
should require special tooling. I should be able to run `cargo build
--target wasm32-wasi` and have it figure out the C code. I should be
able to generate JS bindings for `wasm32-wasi`. WebAssembly should be
as easy to use as native. Heck, I should be able to import a
Rust file in my JS code.

# This Isn't Hard, You Just Suck

That may be true. But please understand that this is a very condensed
version of events. I spent a lot of time reading various docs, posting
online, and trying various different strategies. I also omitted some
of the less interesting errors that I received.

And even if someone more knowledgeable than me can get this to work,
well that's not great. Getting a Rust + C codebase to compile to
WebAssembly should not require intricate knowledge of C flags and
sysroots and WebAssembly internals. It should be doable by a junior
developer.

# What I Want From WebAssembly

If WebAssembly is supposed to be a universal runtime, it needs tools
that make compiling non-trivial codebases to WebAssembly a feasible
process. It needs documentation on the different compile targets and
how to get everything working. And it needs people who can build these
tools, write this documentation, and respond to questions.

Because if we do not solve these usability problems, WebAssembly will
run the risk of being too damn hard to use.

I'd love for the WebAssembly Community Group (CG) )to create a
usability sub-group that focuses on solving people's issues with
WebAssembly and increasing user adoption. It'd be really nice if the
sub-group could work with language implementors to help build better
tooling for Wasm. Right now it appears that the CG is focused
primarily on the pure semantics and featureset of Wasm. That's great,
but it won't matter a bit if Wasm has fixed-width SIMD or branch
hinting if the toolchains are too difficult to use.

# What I Want From Rust And C

I know it's not Rust's job to compile C, but if C compilation can get
simpler, that would open up a lot of cases where Rust can start to
replace or live alongside C. I love that Zig has fixed this problem to
a degree but it shouldn't require installing Zig to build my C code.

And sorry, I don't care that I could have "just" used some incantation
of `-I /usr/lib -L /usr/root -B foobar -QS 10` and so on. Building C
should be simple. There's no inherent benefit to making your build
system arcane.

I also would love more work invested in `rustwasm`, `wasm-pack`
specifically. I know that's not a simple task, with developer time in
such short supply. I should probably put my money where my mouth is
and contribute to this effort. I'll do my best to try.

# What I Want From Myself

Stop trying to use WebAssembly and just have a backend that compiles
the code dude. You just yak-shaved a nice-to-have feature for your
compiler into a whiny blog post.

Hope this was helpful, feel free to reach out if you have any comments
or feedback.
