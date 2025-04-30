---
title: "Surface Area"
pubDate: "Dec 11, 2021"
draft: false
---

There's been a lot of ink spilled about how packages are evil and npm
will bring about the end of days. I've responded to these critiques
[before](https://uptointerpretation.com/posts/we-get-it/), but I
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
those 20 lines might cause 20 bugs or 200 bugs. Not to mention, I can
write a naive implemention in 20 lines, that will eventually balloon
to 500 lines once I figure out all the edgecases.

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

What a lot of people miss when they make arguments against packages is
that realistically, their code is not going to be better than the
package's code. We all like to imagine that we write bug free, perfect
code, but that's just not based in reality. Sure, in some cases the
package code is truly not good and you should roll your own. But in
many cases the opposite is true. And do we really want fifty buggy,
insecure JSON parsers? I don't.

This is maybe where some people step in and say "ugh this is what's
wrong with programmers these days. You can totally write safe code,
you just need to be careful." I'm not going to try to convince these
people otherwise, because it takes a lot of self confidence to make
such a claim and any argument I make will not remove that
confidence. If you believe that, good for you and godspeed.

For the rest of us, let's keep it practical: Use packages, not too
many and not too few.
