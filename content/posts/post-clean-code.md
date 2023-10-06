---
title: "Post-Clean Code"
date: 2023-09-19T00:04:36-04:00
draft: false
---

I've noticed that there's been a push-back on some commonly cited best
practices for programming. If you've read programming advice of a
certain era, the Code Complete, Clean Code, Design Patterns one,
you've probably heard that you should split up your functions into
small chunks, no more than 10 lines. Or that you should write code
that only has one return. Or that you should use a Factory pattern to
create a Strategy that uses a Visitor.

I have no doubt that many people still follow these rules and get
great results. But it's interesting seeing the general opinion shift
back against these rules. I have a few hypotheses how and why this is
happening:

1. The tradeoffs were not acknowledged.

I remember reading and believing that functions should be nice and
short, that anything long should be refactored into smaller
pieces. But then a few things happened. First, I read some code that
obeyed these conventions and realized hey, this kinda sucks. I have to
jump to five different definitions to understand even half of what's
going on. Maybe it's just me, but it's a lot easier to read code when
you can actually read it linearly from top to bottom.

The second thing that happened was that I read and wrote some code
that had really long functions and...it was fine? The code would not
have been materially improved by adding more functions or more
indirection.

This isn't to say that you should go out there and write 500 line
functions. But it's sometimes forgotten in the codification of these
rules that most of them are tradeoffs. Shorter functions may make it
easier to understand them as individual abstractions, but they also
increase the fragmentation of your code. Longer functions may be more
complex, but they're compact and linear. Moreover, abstraction is not
an inherently good thing. It can create simple interfaces, but it can
also obscure the actual intent of the code.

It also doesn't help that usually this advice goes from a long, nuanced
book full of examples to a second-hand recollection in a tweet to folk
knowledge, or worse, a particularly naggy linter rule.

2. The people giving the advice changed.

If you look at the people giving advice in that era, they were mostly
people who worked as consultants. Which kind of made sense. They were
professional advice givers writing books of advice on programming. But
first of all, professional advice givers quickly stop being
professional programmers. Oh sure they were probably great programmers
back in the day, but one's advice quickly becomes stale without keeping
one's programming skills sharp. And second, they were almost always
people running big projects at established companies.

Nowadays there's a lot more diversity. You don't need to be a full-on
consultant to give people programming advice. You can stream on
Twitch, you can write blog posts, you can tweet out your tips. This
means there's a lot of different programmers giving advice. Everyone
from game developers to startup devs to big tech devs all giving
advice. Naturally, this advice often conflicts. That's okay! Turns out
you need different advice for different situations, instead of one
single source declaring that *this* is how you should write code
for all programmers everywhere.

On the flip side, this does mean that the barrier to entry for
becoming a programming thought leader is a lot lower. Or is it? A lot
of the early thought leaders wrote books, which is a lot harder than a
blog post, but it's not like they were inherently better programmers
for having written a book. Today, while you may be able to get your
thoughts out, it's much harder for them to be noticed and
elevated. Plus many modern thought leaders are involved in open
source, which allows you to judge for yourself if their code is
actually clean.

3. Languages have gotten better (and worse).

Leo White said something interesting in Signals & Threads about how he
started to notice when two variables were in scope with the same type,
as that usually meant there could be a bug. I don't know if I could go
that far, but I have noticed that I feel a lot more comfortable
writing large functions in Rust. Generally the types constrain what I
can do, especially if the variables are moved and therefore
unavailable later on in the function. Pattern matching also means that
I've effectively broken up a function into multiple, mutually
exclusive branches, which can take the place of a function quite
nicely.

On the flip side, because of Rust's borrow checker, it's sometimes a
lot easier to just write things in a longer, less abstracted
manner. Otherwise I tend to end up in lifetime soup, or worse, closure
types soup. Or the code doesn't even work. That's not a good thing,
but it does have an inevitable effect on my code.

There's also a lot of code that just feels like a workaround or
unnecessarily defensive due to the constraints of the
language. Visitor patterns can become enums and pattern matching.
Strategy patterns can be closures or enums. Booleans don't have to be
prefixed with `is-` if you have type checking.

4. It's just fashion.

Also it's important to recognize that a lot of coding practices are
just fashion. There was a period where we really cared about what you
named your error variable in your `try/catch` statement, much like
there was a period where everybody was wearing raw denim and good-year
welted boots. Fashions come and go. They seem eternal and sensible in
the moment, then outdated and foolish in hindsight. Perhaps we can
recognize this with our next round of advice and not take it too
seriously.
