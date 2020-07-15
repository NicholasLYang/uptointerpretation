---
title: "Saber Notes: Strings"
date: 2020-06-28T00:50:51-04:00
draft: false
---

I've been working on my language,
[Saber](https://github.com/nicholaslyang/saber) a lot recently. I
figured I should keep some notes on implementation in case someone
finds them useful, even if that's only future-me. Saber is a scripting
language intended to be a lightweight row-polymorphic
psuedo-functional language that compiles to WebAssembly. Think OCaml
meets the best parts of ES6. Of course right now I'm just trying to
get basic functionality finished. The compiler is written in Rust.

It's been a while since I've written one of these. I've been working
on Saber on and off, but one major development has been
strings. Strings were an interesting challenge because they required
me to share memory with JS.

I should quickly recap what I've done since the last post. I managed
to write a basic allocator in pure WebAssembly. From there, I got
structs working. The allocator is just some inlined code that checks
if we have enough memory, returns a pointer if so, and otherwise grows
the memory.

After that I wanted strings, since hey, I could get an official hello
world program.

The actual code generation for strings wasn't terrible. I decided to
do pascal style where the first 4 bytes are the length of the
string. I guess if the user has a lot of small strings it won't be
that great. Ah well.

I also make sure to have the strings align on 32 bits. That way I can
write 4 bytes at a time on the string.

One small hack that I used was to have my allocator code "return" a
pointer to the memory in the first global variable. I'm kinda using
globals like registers for now.

After that though, I got stuck on printing the strings. How could I
print characters without too much trouble? It took a bit and emailing
someone for help, but I eventually figured out how to share memory
between JS and WASM. Essentially I create a WASM memory object in JS,
then import it to WASM. My printString function is written in JS and
takes in a pointer. This pointer points to the string in the WASM
memory. Since the memory object is still available in JS, I can loop
through it as a UInt8Array.

This makes things waaay easier since I can do memory manipulation in
JS. Plus JS comes with a TextEncoder class. All I needed to do is copy
the string into a new array, run it through the TextEncoder and print
it.

Of course this is hella slow since I need to copy the string each
time. I could probably cache it somehow. Unfortunately slow interop is
a general WebAssembly problem. Hopefully they'll have a fix sometime
soon.

With this, I can finally run hello world!

```
let hello_world = \() => {
  printString("hello world!");
}
```
