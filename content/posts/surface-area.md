---
title: "Surface Area"
date: 2020-11-01T21:52:01-05:00
draft: true
---

There's been a lot of ink spilled about how packages are evil and npm
will bring about the end of days. I've responded to these critiques
[before](https://horriblyunderqualified.com/posts/we-get-it/), but I
realized there's more to say. While I don't disagree that large
dependencies cause issues ranging from security holes to serious
single points of failure, I think it's important that we recognize why
packages are so popular and helpful. Any purported solution will need
to satisfy these constraints and not just condescendingly claim that
these benefits don't matter.

Let's talk about surface area. We can think of projects as
planets. When we write code, we need to defend the project/planet from
various attacks, whether these are bugs or actual security
holes. The more surface area, the more we have to defend.

How does surface area increase? It increases with amount of code. The
more code, the more potential bugs. I'll caution that amount of code
isn't measured in literal lines of code, but more in terms of what
your code handles. If you're writing a server that handles HTTP and
Gopher (I have no clue why), then you'll get HTTP bugs and Gopher
bugs.

I specify what your code handles because it's quite easy to look at a
new feature and say "oh but that's only 20 more lines". Sure, but
those 20 lines might cause 20 bugs. Not to mention, I can write a
naive implemention in 20 lines, that will eventually balloon to 500
lines once I figure out all the edgecases.

Some of you may be ahead of me and are already responding that using a
library doesn't reduce surface area, that it just hides it. And if you
were writing libraries for one single person, you'd be right. However,
libraries are not written for one person. They're written for hundreds
of people. And when that happens, the surface area gets condensed. If
100 people wrote their own HTTP parsing code, they'd have 100 planets
with their surface area open to bugs. Instead by using one library, we
have 1/100 of the surface area in one place. And now many of the bugs
can be fought off quite easily. We have more users, more stakeholders,
and therefore more potential fixers. [Linus's
law](https://en.wikipedia.org/wiki/Linus%27s_law) comes into effect.


