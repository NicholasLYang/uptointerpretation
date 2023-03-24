---
title: "Don't Underestimate the Amateurs"
date: 2023-02-23T11:43:59-05:00
draft: false
---

I was reading HN like a dutiful tech employee, when I stumbled upon a
classic HN scene: a laudatory Rust post followed by a whole host of
reactionary anti-Rust comments. In these comments was an inadvertently
good question: Why is "(re)written in Rust" a good selling point?

My first instinct was to respond with the typical arguments: Rust is
safer; Rust has a nicer build system; Rust is faster. But after some
thought, I actually wanted to respond with the answer that most people
hold in contempt: Rust is fun!

Yes, Rust is fun to write. It's more fun than C++ or Java or even
JavaScript. It's fun for a variety of reasons, such as
being expression-based, very good tooling, nice community,
packages ([yes!
packages!](https://uptointerpretation.com/posts/surface-area/)). Whatever
the reasons, it is fun. And when a language is fun, when you want to
build with it, you build with it.

This is where some people sneer and say that this is foolish, that we
should not pick our tools because of such frivolous reasons, that we
should use "the right tool for the right job", that that's no reason
to rewrite or reinvent the wheel.

My only response is:

> Hello everybody out there using minix -

> I'm doing a (free) operating system (just a hobby, won't be big and
> professional like gnu) for 386(486) AT clones. This has been brewing
> since april, and is starting to get ready. I'd like any feedback on
> things people like/dislike in minix, as my OS resembles it somewhat
> (same physical layout of the file-system (due to practical reasons)
> among other things).

> I've currently ported bash(1.08) and gcc(1.40), and things seem to work.
> This implies that I'll get something practical within a few months, and
> I'd like to know what features most people would want. Any suggestions
> are welcome, but I won't promise I'll implement them :-)

> Linus (torv...@kruuna.helsinki.fi)

> PS. Yes - it's free of any minix code, and it has a multi-threaded fs.
> It is NOT protable (uses 386 task switching etc), and it probably never
> will support anything other than AT-harddisks, as that's all I have :-(.

Yep, that's the original Linux release email. Remember, when Linus was
doing this, he was a college student writing an amateur operating
system for fun. This was not a grand project meant to become the most
used OS in the world. In fact, I bet if that was the intention, it
would have never succeeded (*cough* GNU Hurd *cough*).

All of this is well known. Worse is better, New Jersey style, etc. But
what's forgotten in the equation is why amateurs decide to build: They
build because it's fun. Therefore if your project is fun, if it uses a
language that people like to write, if it doesn't take itself too
seriously, and if it has a culture, i.e. a branding, of fun, then it
has a possibility of really taking off.

Sure, a lot of these projects won't make it. But that's true with
serious, "professional" projects too. In fact there's a lot of
drawbacks with being professional, or at least what a team may deem
professional. I remember talking with someone who built an extremely
popular open source tool. They mentioned that when they were building
this tool, they had a lot of free time, so when a bug report came in,
they'd fix it immediately, and ship a patch within a couple
hours. That super fast feedback loop gained them lots of trust and
adoption. Is it the most professional system? No. A feedback loop
measured in hours or even minutes implies little to no code review.

This, as with all things, is a trade-off. Being professional makes you
stable, but it can also make you boring. In the early stages of a
project, boring is a lot more dangerous than unstable. A new project
is inherently unstable. Who knows if you'll stick around? The people
who want boring, stable technology are not going to use your project
even if you do all the fancy stability stuff like RFCs, semantic
versioning, etc. You're just too new. The people who will use your
product will be the early adopters who are cool with breaking changes,
who might even investigate bugs themselves and open a PR. Stability
occurs when those early adopters convince their coworkers and boss to
use your project, an issue occurs, they start sweating, and then they
come back to you asking that hey maybe you could obey semantic
versioning and write a few more tests please? When that starts
happening, you should celebrate! That's a sign that people are using
your code and that the stakes are now real.

Basically, stability has a time and a place---and that place is after
gaining users. But before that, you should own your
instability. Trying to act all professional and stable is like a young
filmmaker who has a shoestring budget but insists on trying to shoot
like the big Hollywood folks. It alienates the indie film crowds, and
the big budget crowds don't even know you exist. It's taking oneself
too seriously.

Instead you should aim to be fun. Be a fun project to contribute
towards by eliminating process and putting trust in people. So what if
a pull request isn't perfect? So what if the code breaks? So what if
Rust isn't the best language to use for the project? If you can make a
project that appears to contributors, then you're already doing well.

And appealing to contributors will also appeal to users. First,
because a lot of early adoptors are also potential contributors. And
second, because more contributors means you get closer to Linus's law,
i.e. "given enough eyeballs, all bugs are shallow". If you can make an
environment where people have fun learning and programming, then no
task is insurmountable.
