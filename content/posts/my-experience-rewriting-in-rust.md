---
title: "My Experience Rewriting in Rust"
date: 2022-09-18T17:37:45-04:00
draft: true
---

A lot of people are rewriting in Rust. I spent some time at a company
who did a rewrite and learned a bunch of lessons. I wanted to publish
a post on these lessons, but I didn't get to do it while I was at the
company. Here's my chance:

1. Why rewrite in Rust?

Ask yourself why you are rewriting in Rust. Is it for genuine
technical reasons? Or are you just getting to that boring part of the
technical problem? It's often true that the early stages where you're
figuring out architecture and writing a bunch of code are way more
satisfying than the next stage where all the architecture decisions
have been made and all that's left is a bunch of work. You feel really
productive writing a new parser from scratch, adding all this code,
handling the general cases, setting up infrastruture. Except if you
don't need a parser from scratch, then you're just retreading covered
ground.

Are you being attracted by the new shiny? It's easy to pretend that as
a senior developer you don't fall for the new shiny, but in reality
senior developers are also better at finding plausible sounding
reasons to use the new shiny. As someone who likes Rust, I'll freely
admit that it's more fun to write than say, JavaScript. That said, if
I had a nice existing codebase in JavaScript, I wouldn't just throw it
away to write Rust. I understand that it's tempting to go with the
language that you like to write, but that's not enough.

Are you rewriting for performance or for safety? They're not mutually
exclusive, but often times people are rewriting from a generally safe
high level language (JS, Ruby, Python), or they're rewriting from an
unsafe, low level language (C, C++). If it's performance, is there any
way to get better performance with your existing language? How long
would that take? Would that take longer than rewriting in Rust? If
it's safety, then how critical is your software? Is it a big deal if
there's a memory leak? Many developers seem to think that any bug is a
horrible horrible problem that can never occur. Sometimes that's true,
but bugs are also a factor of life. In many cases I'd rather have
shipped software with some bugs versus a hypothetical piece of
software with no bugs.

2. Have you validated your product?

By doing a rewrite, you have commited to a long process before you go
to market. If you're going to invest this amount of time into a
rewrite, you should be certain that this product is something that
people will actually use. More than that, you should be certain that
this product fulfills a pressing, painful need. If you haven't, then
do that before you rewrite.

You should also validate the benefits of your rewrite. If you're
rewriting for performance, validate that performance is important for
users. This isn't just asking people "do you want a faster X", because
they'll always say yes. Nobody wants a slower tool. It's instead
ensuring that slowness is what's preventing other people from using
your tool. It's ensuring that speed is a sufficient reason for people
leaving your competitors. The same is true with safety.

Sometimes your product can be over-validated. If your product is
gaining users like mad and has dozens of feature requests, it may not
be in your interest to stop and rewrite. Perhaps you can build these
new features in the new language.

3. How much should you rewrite?

Are you trying to do a complete rewrite? You probably should not. It
will take a long long time. Unless you maintain and expand the old
codebase, you won't be able to ship anything until the new codebase is
done. Clean rewrites are always a tempting proposition. But again,
it's like sprinting through territory you've already covered. Yes,
you're moving fast, but you're also 100 miles behind. [Joel Spolsky was
right](https://www.joelonsoftware.com/2000/04/06/things-you-should-never-do-part-i/).

With smaller rewrites, what is your end state? What is the smallest
amount of code you need	in order to ship? Can you make that smaller?

Can you write a tiny, unimportant part of your codebase in Rust and
spread it from there? Can you write tooling in Rust? In which case
you're not rewriting, you're just writing new tools in a different
language. This is a good prelude to a rewrite.

Is there a part of your codebase that above all else desperately needs
performance or safety? Maybe just rewrite that.

4. How long is it going to take?

I'll answer this for you. It will take a while. People always seem to
think that rewriting will take less time than the first time
around. In the best case that's true, but it's more like 50-70% of the
time, which is still a while. In the worst case that is certainly not
true because you end up getting bogged down with [second system
effect](https://en.wikipedia.org/wiki/Second-system_effect). Do
**not** see a rewrite as an opportunity to "get it right" and thus
spend an unbounded amount of time. That is how you end up letting
perfect be the enemy of good.

How long it's going to take is directly tied to how much code you need
to write. If there's good libraries that can be used as jumping off
points, then maybe it'll take less time. However, that is *only* true
if you **use** the libraries. I've been at places where they rejected
many wonderful pre-built libraries to write stuff from scratch. They
lost a lot of time reinventing the wheel.

One other aspect that is underrated is the length of time it takes to
really learn Rust idiomatically. It only takes a few weeks to learn
enough Rust to write it. It takes quite a lot longer to learn enough
Rust to write it well. What ends up happening is the code that you
wrote with 2 months of Rust experience you end up wanting to refactor
with 6 months of Rust experience. You probably shouldn't do that
though. Because...


5. Where are you going to waste time?

Rust is a really easy language to refactor. The static type system
makes it quite easy to change a few types and just chase the
errors. This does mean that it's quite easy to get caught up
refactoring and refactoring and refactoring. Remember, refactoring
doesn't get you users. Also don't chase idiomatic. Rust is new
enough that there may not be one single answer.

There's a lot of other ways to waste time. You could realize there's
no good library for this and now you have to write it from
scratch. For instance authentication in Rust is still very early. You
could delude yourself into believing that you are such a fantastic
programmer and no library out there is as good as the one you write
yourself. That very well could be the case, but remember, your job is
to write the product, not the dependencies of the product.

Your team will likely develop different styles and techniques as they
learn Rust. That's okay. It will also mean that they will be tempted
to refactor code that is not theirs. Resist that urge.

6. Ownership and Rewriting

Another problem that will arise is dealing with Rust ownership through
an interop barrier.

7. Recruiting

Common advice given for tech companies is that if your stack is in
XYZ, you should have at least one XYZ guru on your team. That's valid,
but you should recognize that a Rust guru is hard to find. It's okay
if you don't have one.
