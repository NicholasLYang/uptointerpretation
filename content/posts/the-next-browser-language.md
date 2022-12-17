---
title: "The Next Browser Based Language"
date: 2022-12-15T01:04:56-05:00
draft: true
---

Let's say you're writing some front-end code. How many options are
there for programming languages? I'd say there's three camps. There's
plain old JavaScript, there's languages that compile to WebAssembly,
and there's languages that compile to JavaScript.

Plain old JavaScript requires the least tooling, at the cost of being
rather frustrating to debug and even more annoying to read. It
can be a great, low friction option to start, but beyond some
obsession around "minimalism", I don't see many benefits.

Languages that compile to WebAssembly, while growing, are still very
new. They often come with massive binary sizes since most languages
need to ship an additional runtime. Interop is still a pipe
dream. Just because two languages compile to WebAssembly doesn't mean
they can necessarily talk to each other. And the languages still need
to catch up on decades of JavaScript libraries for the DOM. Don't get
me wrong, there's still a time and a place for WebAssembly. If you
want to run compute heavy native code in the browser, WebAssembly is
perfect.

# TypeScript

Which leaves the languages that compile to JavaScript. Of them,
there's one giant and the rest. The giant, of course, is
TypeScript. TypeScript is a wonderful language that has significantly
improved developer experience. It adds a layer of safety, facilitates
better tooling, and at minimal buy-in cost. The TypeScript team has
accomplished a remarkable feat considering the state of the ecosystem
and the challenges associated with type-checking JavaScript.

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
the language. The developers had to understand how TypeScript felt as
a language. And rejecting soundness meant that developers could use
TypeScript with existing JS codebases. It also meant that developers
could escape the frustration of types with a single `any` type.

This is worth an entire essay of its own, but TypeScript is probably
the first language to be developer-experience forward. The language
did not add any runtime constructs. It did not affect
performance. Instead it added a type system, and more importantly, it
taught a community that did not use types, that did not have high
quality tooling[^1], that did not have a culture of correctness, to
adopt these values.

[^1]: Recall that TypeScript came out in 2012.

# The Next Browser Language

All of this is to say that TypeScript made some trade-offs 10 years
ago that had a massive effect on the language. And now, with that time
passed, I think it's time for new languages with a new set of
trade-offs. Specifically, I want a language that has soundness, that
has type inference, and that has quick compilation speeds. That's
great and all, but what are the corresponding trade-offs that come
with these choices? Well, to have soundness, the language should have
explicit runtime type checks. This does mean more overhead for using
JavaScript libraries in this new language. The language will not try
to type check the various JavaScript patterns, but instead treat
existing JavaScript code as something to interoperate with.

Why do I want this? Well first, I want this because I like writing
languages that have sound and relatively simple type systems. I want a
language that works well in the browser and works within the existing
web ecosystem. Too often languages that compile to WebAssembly attempt
to ignore the rest of the web ecosystem. They want to build pixel
based native UI in the browser. I think that's a wonderful goal, but
it is not my goal. I want to use this language to build normal,
everyday websites. I don't want a pure functional language and I do
want a language that has conventional C-style syntax (sorry Elm!). And
I want a language that exemplifies my ideas in [tooling for
tooling](/posts/tooling-for-tooling/).

And why do I think this is the right time for it? There's the obvious
answer of the present being the second-best time to start a language,
the first-best being 10 years ago. But also I think the JavaScript
community has evolved a lot in the last ten years. People learned
TypeScript and with it they started to get comfortable listening to
the compiler and modeling their data in types. They started playing
around with languages like Rust and Swift and Kotlin. Not to say that
people would have rejected a type-safe language ten years ago, but it
would have been a lot harder.

# ReScript/ReasonML

Some of you may be thinking that this language sounds awfully like
ReScript/ReasonML. And yes, there is some overlap. However, my
language would ideally have explicit runtime type checks for JS code,
and traits. Runtime type checks would make the interop story very
nice. You could use any JavaScript library with relative
ease. Likewise, I believe traits are a better fit for users. They map
onto other language features like Java interfaces and C++
concepts. They facilitate features like easy printing of any type via
a Display trait. This may seem superficial, but it's usability quirks
like "how do I print this???" or "why is + used for integer addition
and +. used for float addition?" that put people off from
languages. While I do like OCaml, I don't necessarily think it's a
fantastic language for front-end. Furthermore, I'd like to remove some
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

As for runtime checks, I believe they would worth the overhead. We
already do plenty of schema validation in JavaScript.

# WebAssembly

As for WebAssembly, I am still very optimistic about its
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
increase performance.

# Conclusion

If you've noticed, the previous two sections are essentially taking
ideas from systems languages such as unsafety and hardware
acceleration, and applying them to a browser based language. Unsafety
may seem like an odd one. JavaScript is not exactly unsafe in the same
way C is unsafe. But what a lot of people don't realize is that safety
extends beyond security. Safety is the ability to use a value without
worrying if it's actually null. Safety is the ability to model the
domains of your values as types and know that they will be
correct. Safety is the ability to have mutability without introducing
bugs or confusion. Likewise, hetereogenous compilation is not
something necessarily thought about in the browser world. I suspect
it's time for that to change.

I'm still sketching out this language. I've been playing around with
an implementation called
[vicuna](https://github.com/nicholaslyang/vicuna), but it's still very
very early. Feel free to email me at nick@nicholasyang.com with any
thoughts or feedback!
