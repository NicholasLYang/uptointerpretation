---
title: "Speed and Iteration"
pubDate: "Feb 20, 2018"
draft: false
---

I've become interested in programming languages recently. This
interest stemmed from Bob Nystrom's wonderful book [Crafting
Interpreters](http://www.craftinginterpreters.com/). Reading the book,
with all the wonderful diagrams and simple code snippets, has given me
the idea of writing my own language. However, what kind of language
should I make? Right now it feels like the programming world is
bursting at the seams with new languages. Golang, Kotlin, Swift, and
Rust have all appeared within the past few years. JavaScript, while
not new, has had a recent explosion of transpilers and extensions,
such as ECMAScript 6 and TypeScript.

However, I've noticed one trait that each of these languages have yet
to embrace: speed and iteration. Sure, each language partially
encourages these qualities; Golang is dead easy to learn; Kotlin
interops with Java and has amazing tooling. But they don't make this
the primary focus of the language.

What do I mean by primary focus? I mean a language with the following
features

- A good REPL
- Easy browser integration
- Package management built in from the beginning
- Good API documentation with examples
- Code scaffolding and boilerplate management
- Good tooling

Now granted, these are not easy features. Package management is not a
trivial task. Neither is browser integration. But there's more than a
few good reasons to put in the work.

First, these features will make your language attractive. And not
attractive in the high level intellectual way that Haskell or OCaml
are "interesting", but worth it in the rapid fire, move fast and break
things kind of way. I shouldn't have to compile from source just to
get the language working. I shouldn't have to fumble with awkward
packages that break if you look at them. I shouldn't have to link
system libraries that may or may not exist. It's okay for your
language to be hard to understand. It's not okay for languages to be
hard to write. Part of the reason JavaScript was so damn popular was
that it could be run on literally any internet connected
computer. What I want is a language that literally can be run on a
webpage with a script tag to a CDN. Hell, with the latest progress
with WebAssembly, I wouldn't be surprised to see a language with a
toolchain completely in the browser.

Second, you want to take advantage of the network effect as much as
possible. Sure, theoretically you could build a bunch of really nice
standard libraries that have every potential (common) function your
users need. But if you make the language easy to write, and make the
package mangement intuitive and robust, you *don't need* a standard
library. You just need people writing code who are willing to publish
packages. Okay, sure, that's not easy either. But if you can get a few
insane suckers to start writing code and wrap up their packages,
that's a start. Sure, bug ridden, of questionable quality, incomplete
packages. But that's where the iteration factor takes place. Just as
long as people are writing code in your language, it'll get better.

I could also claim that your language needs to be easy to understand,
easy to learn. But there is something to be said for sacrificing ease
of use for some benefits. Haskell sacrifices ease of use for purity
and concurrency. Rust sacrifices it for memory safety. How much you
want to go in that direction is up to you. But what shouldn't be up
for debate is that your language should be easy to write.
