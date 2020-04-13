---
title: "C Is Cool"
date: 2018-03-06T16:09:56-05:00
draft: false
---

I've been using C for my classes and I can't help but be really really
happy about that. I've played with C before. Well, actually, C was my
first language. I tried learning it as a stepping stone to Objective C
and iOS development, but I quickly gave it up after getting to
pointers and arrays. Granted, I was like 11. After that, I've played
with C in various places. First, I had to use C++ in an internship,
where I got to play around with performance optimization and
profiling. Then, last year because I was bored with using Java in my
classes, I started to write some data structures in C. Just basic
stuff like linked lists, binary trees, maybe a hash table. I found it
really intriguing and a lot of fun to play with. Oh sure, segfaults
were annoying and the whole lack of decent typing or safety led to
some less than safe code. But overall it was a good experience.

Now, this semester, I'm in Computer Systems Organization, a class with
a lot of C programming. And honestly I'm having the time of my life. C
is just such a *cool* language. Oh sure, it doesn't have the fancy
features of Python/Ruby, the metaprogramming of Lisp or the libraries
of JavaScript. But it's just so damn minimal. I've been trying to
learn about compilers and language development (albeit at a glacial
pace, I do have other things in my life) and C is just such an
intriguing language from a design standpoint.

Like take lexical scoping. C doesn't allow you to declare functions in
functions. The best you can get are function pointers. But as a
result, functions and their lexical scope map cleanly onto stack
frames. You don't have to worry about closures or about resolving
scope dynamically. Hell, you can't call functions before they're
declared, forcing you to use header files. This means that the map
from code to implementation is just about the simplest thing. You can
resolve scope just by looking at the code. And that's so damn cool.

Or how you can count memory allocations. High level languages are nice
and all, but you often run into awkward situations where one line of
code is mysteriously and disproportionately slowing down your
program. In C? Not so much. Oh sure, malloc is amortized and can be
slower than you expect. But generally when you're writing C, you know
what you're getting. In a language like Ruby or Python or JavaScript,
I can't tell you how many bytes I've allocated. Hell, I probably
wouldn't even get within 100 bytes of the right answer. But in C?
I could probably tell you down to plus or minus 2 bytes.

Of course, there's plenty of annoying issues, not to mention the whole
concept of memory safety. But it's probably the most fun I've had just
programming, barring messing around with Lisps. Now if only I could
find a good project to write in C...
