---
title: "The Next Browser Language"
pubDate: "Dec 27, 2022"
draft: false
---

Let's say you're writing some front-end code. How many options are
there for programming languages? I'd say there's three camps. There's
plain old JavaScript, there's languages that compile to WebAssembly,
and there's languages that compile to JavaScript.

Plain old JavaScript requires the least tooling, at the cost of being
rather frustrating to debug and even more annoying to read. It
can be a great, low friction option to start, but beyond some
obsession around "minimalism"[^1], I don't see many benefits.

[^1]: Which I know is popular among various groups online, but I need
    more than a vague notion of minimalism as a reason.

Languages that compile to WebAssembly, while growing, are still very
new. They often come with massive binary sizes since most languages
need to ship an additional runtime. Interop is still a pipe
dream. Just because two languages compile to WebAssembly doesn't mean
they can necessarily talk to each other. And the languages still need
to catch up on decades of JavaScript libraries for the DOM. There's
nothing like React or Svelte for WebAssembly. Don't get me wrong,
there's still a time and a place for WebAssembly. If you want to run
compute heavy native code in the browser, WebAssembly is
perfect. Otherwise, I can't say I'd recommend it for everyday
front-end development.

Which leaves the languages that compile to JavaScript. Barring the one
elephant in the room, which I'll address shortly, they've become a
little unsung. Languages such as ClojureScript, Elm, ReScript, Dart,
etc. all have respectable communities but I wouldn't say they're
necessarily expanding in market share[^2]. This is a shame, as
languages that compile to JavaScript are probably the most effective
way to get a good programming language experience in the browser. They
allow you to access all the nice features that JavaScript doesn't have
like static types, strong typing, immutability, macros, etc. They
still give you access to JS and the extensive JS ecosystem, hopefully
through bindings. And they don't require shipping a large runtime.

[^2]: Nor does that seem to be their goal, in fairness.

Because of WebAssembly, I suspect there's a reticence to compile to
JS, since Wasm is the anointed compilation target for the browser. I
disagree with this sentiment. We need more languages that compile to
JavaScript. In this post, I'd like to outline what I want out of these
future languages.

# TypeScript

But first, we need to discuss the elephant in the room of languages
that compile to JS. The elephant, of course, is TypeScript. TypeScript
is a wonderful language that has significantly improved developer
experience. It adds a layer of safety, facilitates better tooling, and
at minimal buy-in cost. The TypeScript team has accomplished a
remarkable feat considering the state of the ecosystem and the
challenges associated with type-checking JavaScript.

However, there are some fair criticisms of TypeScript. Usually they
boil down to performance and soundness. What's important to note is
that both of these criticisms are not unknown to the TypeScript
team. These issues are due to explicit trade-offs that the TypeScript
team made when they began development. In my view, these trade-offs
were the correct ones to make at the time to effectively execute.

With that said, performance is arguably the most commonly cited issue
with TypeScript.  TypeScript is implemented in itself and that
implementation is quite complex. The type system at this point is
practically a mini programming language. This leads to quite slow
type-checking times.

The second issue is soundness. This is less of a commonly cited issue
and more something grumbled about by programming language
nerds. Basically, because TypeScript has various escape hatches like
the `allowJs` config option, the `any` type, and intersection types,
the type system cannot ensure that the code is type-safe. Put bluntly,
you can write TypeScript and get runtime bugs. On top of this,
TypeScript lacks type inference beyond very simple cases. You have to
explicitly annotate types in a lot of places.

Again, both of these were the result of calculated
trade-offs. Bootstrapping the compiler was essential for dogfooding
TypeScript. The developers had to understand how TypeScript felt as a
language. More specifically, they had to experience writing a large
JavaScript codebase, then incrementally adopting types within this
codebase. And rejecting soundness meant that developers could
incrementally adopt TypeScript in existing JS codebases. It also meant
that developers could escape the frustration of types with a single
`any` type.

This is worth an entire essay of its own, but TypeScript is probably
the first language to be purely about developer-experience and not
about semantics. The language did not add any runtime constructs. It
did not affect performance. Instead it added a type system, and more
importantly, it taught a community that did not use types, that did
not have high quality tooling[^3], that did not have a culture of
correctness, to adopt these values. That is an incredible feat.

[^3]: Recall that TypeScript came out in 2012. Babel and Webpack came
    out in 2014.

# The Next Browser Language

All of this is to say that TypeScript made some trade-offs 10 years
ago that had a massive effect on the language. And now, with that time
passed, I think it's time for new languages with a new set of
trade-offs. Specifically, I want a language that has soundness, that
has type inference, and that has quick compilation speeds.

That's great and all, but what are the corresponding trade-offs that
come with these choices? Well, to have soundness, the language will
not try to type check the various JavaScript patterns, but instead be
a separate language that compiles to JavaScript with a simpler type
system. It will treat existing JavaScript code as foreign code to
interoperate with. It will have explicit runtime type checks for
JavaScript code. And it will be implemented in a different, native
language.

Why do I want this? Well first, I want this because I like writing
languages that have sound and relatively simple type systems[^4]. I want a
language that works well in the browser and works within the existing
web ecosystem. Too often languages that compile to WebAssembly attempt
to ignore the rest of the web ecosystem. They want to build pixel
based native UI in the browser. I think that's a wonderful goal, but
it is not my goal. I want to use this language to build normal,
everyday websites. I don't want a pure functional language and I do
want a language that has conventional C-style syntax (sorry Elm!). And
I want a language that exemplifies my ideas in [tooling for
tooling](/posts/tooling-for-tooling/).

[^4]: Okay type systems are almost never simple, but at least simpler
    than TypeScript's type system.

And why do I think this is the right time for it? There's the obvious
answer of the present being the second-best time to start a language,
the first-best being 10 years ago. But also I think the JavaScript
community has evolved a lot in the last ten years. People learned
TypeScript and with it they started to get comfortable listening to
the compiler and modeling their data in types. They started playing
around with languages like Rust and Swift and Kotlin. They started to
appreciate good tooling. Not to say that people would have rejected a
type-safe language ten years ago, but it would have been a lot harder
to gain adoption.

# ReScript/ReasonML

Some of you may be thinking that this language sounds awfully like
ReScript/ReasonML. And yes, there is some overlap. However, my
language would ideally have explicit runtime type checks for JS code,
and traits. Runtime type checks would make the interop story very
nice. You could use any JavaScript library with relative
ease. Likewise, I believe traits are a better fit for users. They map
onto other language features like Java interfaces and C++
concepts. They facilitate features like easy printing of any type via
a Display trait. This may seem superficial, but it eliminates
preventable usability quirks like "how do I print this???" or "why
is + used for integer addition and +. used for float addition?" that
put people off from languages. Furthermore, I'd like to remove some
extra stuff like objects, linked lists, polymorphic variants, etc. I
was also not impressed with ReScript's developer experience and error
messages when I tried it last.

That said, I'm not ruling out the possibility that ReScript is the
correct path forward. I need to give it another shot, as my
experiences are a few years out of date. Perhaps with the split from
OCaml, the language has sufficiently evolved into a good front-end
option.

# Type Safety

With this language, I would like a more systematic approach towards
type safety. Specifically, I would like to take Rust's approach of
unsafe blocks for the JavaScript interop. Basically, to call
JavaScript, you need to wrap the code in an unsafe block. This acts as
an explicit signifier that this code needs to be read more
carefully. The goal then would be to implement bindings on top of
these unsafe accesses for JavaScript libraries. At first, this process
would be manual, but hopefully the equivalent of bindgen and cxx would
come into existence.

Using the concept of unsafe blocks in JavaScript may seem like an odd
choice. JavaScript is not exactly unsafe in the same way C is
unsafe. But what a lot of people don't realize is that safety extends
beyond security. Safety is the ability to use a value without worrying
if it's actually null. Safety is the ability to model the domains of
your values as types and know that they will be correct. Safety is the
ability to have mutability without introducing bugs or
confusion. JavaScript, due to its dynamic features, is fundamentally
unsafe. Rust's concept of unsafe blocks allows users to have their
safe zone, but also interact with the large corpus of unsafe code. The
same should be true with browser based languages.

As for runtime checks, I believe they would worth the overhead. We
already do plenty of schema validation in JavaScript. It's just done
with ad-hoc mechanisms like
[zod](https://github.com/colinhacks/zod). They will probably either
take the form of being able to derive an automatic conversion to a
language type that errors at runtime, or the ability to pattern match
on a JavaScript value.

# WebAssembly

With WebAssembly, I am still very optimistic about its
future. Proposals have taken a while to be released, but hopefully
that is just an indication of the quality of the work. However, I
don't necessarily see WebAssembly taking over as the general purpose
runtime of the browser. Perhaps this will change, but the way I see
WebAssembly is actually more of a hardware accelerator. When the user
needs powerful compute with hardware-friendly characteristics like
fixed width integers and static function calls, they use WebAssembly,
much like when a user needs parallel computation, they go to the
GPU. In this model, I see a potential for a language that embraces
hetereogenous compilation, where some of the code is compiled to
JavaScript, and some is compiled to WebAssembly. This could be done
explicitly by the user, automatically via profiling, or even
just-in-time. Hopefully by controlling both the JS and the Wasm code,
the compiler can minimize trips over the boundary and therefore
increase performance. There could even be a mechanism to send some
code to WebGPU.

Hopefully with this model, computationally intensive programs like
machine learning models, video games, and rendering software can be
written with relative ease.

This concept of compiling for both WebAssembly and for JavaScript can
be made visible in the language. I'd want explicit integer and float
types, and perhaps an explicit indexing type like Rust's `usize`. That
way if the code were to be compiled to WebAssembly, it could take
advantage of WebAssembly's fixed width integers. Perhaps another
possibility is to create a subset of the language that compiles nicely
down to Wasm, that restricts dynamic features such as closures or
garbage collection. To interact with that subset, you'd need another
`unsafe`-style block, perhaps a `strict` block, or alternatively the
subset could interact with the outer code with a `dynamic`
block. These are all hypotheticals but I believe they're worth
exploring.

# Implementation

The language will probably be implemented in Rust. This is mostly
because I'm a fan of Rust and I believe the combination of algebraic
data types, relatively fast code, limited but available mutability,
and decent libraries is a good fit for a compiler.

If WebAssembly evolves sufficiently such that the performance is close
to native, I would consider bootstrapping the compiler with a subset
of the language that compiles to fast WebAssembly. But a Rust compiler
would probably be more than sufficient for quite a few years.

# Conclusion

If you've noticed, the Type Safety and WebAssembly sections are
essentially taking ideas from systems languages such as unsafety and
hardware acceleration, and applying them to a browser based
language. This is by design. Some of the most interesting programming
languages work is being done at the systems level. All I wish is that
these ideas make it into the browser.

I've titled this post "The Next Browser Language", but I want to be
clear, it's not a singular language. My hope is that there will be
multiple languages that attempt these ideas and more. My goal here is
more to inspire some people to keep innovating in the browser
languages space. As for my personal attempt, I'm still sketching out
this language. I've been playing around with an implementation called
[vicuna](https://github.com/nicholaslyang/vicuna), but it's still very
very early.

Feel free to email me at nick@nicholasyang.com with any thoughts or
feedback!
