---
title: "What Do You Want (Instead)?"
date: 2019-02-26T17:33:26-05:00
draft: false
---

A lot of virtual ink has been spilled about dependency hell and
left-pad and how NPM is hell incarnate. Medium posts moan about how
NPM is insecure, is full of bad packages and is emblematic of
developers' laziness. They're not wrong. However, when one raises an
issue with a service, there is always the inevitable follow up: what
do you want instead? There's no clear answer to this question for
package management. Do we want to go to the bad old days of adding
script tags and polluting our global namespace? Do we want to switch
to large catch-all libraries a la Boost or jQuery? What do you want?

One common answer to this question is to point to other package
distributions that appear to not have these problems. Take your pick
between Bundler, Cargo, Composer, Pip, etc. Except, there's a few
flaws with this. First, these registries and respective package
managers have their own problems. Pip has the inane global
installation by default and non determinism. Cargo is beginning to
have the same problems as NPM in dependency size, especially
compounded by Rust's compile times. I love Rust, but downloading and
compiling packages can take a bit. Second, there's the issue of
scale. While it is nice to compare to other languages, NPM is a little
unique in the sheer size of the ecosystem. Due to several factors
which quite frankly I don't feel like explaining (fragmentation,
churn, diversity of usage), the ecosystem has the perfect storm of
repetition and scale. Granted, Python is also rather diverse in its
usages, but ask anybody who has had to install Tensorflow and they'll
tell you Python dependency management needs work as well.

Which brings me to my main point: the problems of NPM are problems
that every package manager either faces or will face. I.e. NPM is not
unique. The reason NPM gets all the coverage is because it's reached
these problems faster than any other ecosystem. But the fundamental
problems of massive dependencies; tiny, yet powerful packages and
massive attack surfaces will be problems that everybody will need to
address.

Why? Well first, because packages **work** dammit. They're an
effective way of reusing software at a much more granular level than
the massive swiss army knife libraries of yore. Try to find a newish
language that isn't moving to the package model. Even Go, the grumpy
grampa yelling "get off my lawn" of 21st century languages, is moving
to packages. This isn't surprising at all. When we design our own
projects, do we rely on monolithic objects that provide all the
functions we need? No, we split them up into granular, modular
abstractions.

Now, there's always the straw man argument of "what about the is-odd
package??" Well of *course* we shouldn't make packages for checking
the parity of a number. That shouldn't have to be said. But there's a
lot of useful cases in between is-odd and jQuery in which packages
solve a real problem.

The next point that people often make is, why not write the code
yourself? And sure, in the trivial cases that's true. You can write a
decent set of array/string/object primitives instead of using
lodash. You can write a delay Promise function yourself. But people
don't just avoid writing code due to laziness. They avoid it because
of *specialization*. A commonly used package will probably have less
bugs (cf [Linus' Law](https://en.wikipedia.org/wiki/Linus's_Law)), be
faster than the naive implementation, and handle more cases. Not to
mention, they'll get bugfixes for free. I can't count the number of
times I've fixed a bug by bumping a version number.

Another interesting point people make is that the reason behind NPM's
issues is JavaScript's lack of standard library. I don't disregard
this completely, there's probably some amount of packages (*cough*
Lodash *cough*) which depend on JS's poor standard library. But if you
look at the most depended upon packages, beside Lodash most of the
packages wouldn't fall under a standard library. Request, chalk,
react, express, commander, or moment don't really appear to be
standard library material (okay maybe moment/request, but Rust for
instance does not provide either functionality in its stdlib).

And besides, large standard libraries don't exactly seem great
either. They're tied to the release cycle of the language, meaning if
there's a bug in the standard library, or worse, a security hole, you
need to beg and plead users to upgrade their language. Meanwhile, a
package means that even if a user doesn't upgrade their existing
projects, future projects will automatically use the newer
package. And tools like NPM and GitHub are starting to warn people
automatically about their dependency security holes.

This isn't to say packages are perfect or that we should just ignore
the issues with packages. I'm just sick of people hating on NPM as if
there's something unique about it that makes it bad and I'm sick of
people hating on packages without proposing a better option. If you
want to go back to script tags and globals be my guest. I'll be here
with my million pound node_modules.
