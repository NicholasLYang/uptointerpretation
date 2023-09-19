---
title: "Code Crush Syndrome"
date: 2023-08-22T10:18:54-04:00
draft: false
---

*Aside: Sorry, this is a morbid post, so feel free to skip if you're
not into medical stuff. I, for one, rewatch way too much House MD (S6E22),
hence this post*

I've been working at Vercel on porting Turborepo from Go to Rust. In
this process, I've learned a lot. One particular lesson that I've
found particularly illuminating concerns the dangers of dead code.

The analogy that comes to mind is of crush syndrome. Say your leg is
stuck under a fallen tree. Because your muscles are being crushed
beneath the tree, they are cut off from circulation and therefore from
oxygen. This causes them to die and become necrotic[^1]. This in turn
poses a serious problem, because if someone were to come along and
lift the tree up to rescue you, your body would be flooded with all of
the toxins that get produced when muscles break down. These toxins
would then fry your kidneys and possibly kill you. This is called
Crush Syndrome.

[^1]: Do not Google this if you're squeamish

Okay, gross, but what does this have to do with dead code? Well dead
code is like this crushed leg. It's sitting there, not being used,
slowly breaking down. When it's reconnected back into the program, it
immediately floods the program with bugs and outdated assumptions,
whether that's the outdated date format that you changed everywhere
else in the program, or something more subtle like broken concurrency
invariant.

And yes, you should consider any dead code to be actively
decomposing. Unless it's being run, you can't be sure. Requirements
change, libraries get updated, etc. Static analysis can keep the code
in somewhat better shape, i.e. not totally obviously broken, but
nowhere close to actually correct.

Okay, but what about tests? Tests are great for keeping dead code
alive; you can think of them as a life support machine for your
crushed leg. They'll keep the code going, but only just barely. After
all, your tests can become outdated themselves. And unless these tests
somehow connect your code to the larger codebase, they still don't
account for the bugs that come from interactions between different
modules in the codebase. As Dijkstra said: "Program testing can be
used to show the presence of bugs, but never to show their absence!"
Tests can limit your bug exposure but you'll never truly know until
the code is connected back into the program.

This thought process informed how we planned our porting efforts at
Vercel. While it was tempting to tackle the port in large chunks, such
as a full rewrite or a few large pull requests, we knew that doing so
would create a lot of dead code. This would be code that is sitting in
a pull request or gated behind a feature flag, unused by users,
accumulating bugs. By the time we'd ship the new code, we'd likely end
up dealing with an onslaught of bugs and changed behavior. Therefore
we tried to move code over in smaller pieces, and wire those pieces
into the existing system, even if it meant using creating complicated
interop schemes or slower performance.

At a certain point we did have to start accumulating large quantities
of dead code, especially as we got to the core functionality of
Turborepo. However, we were able to still exercise the code by having
it run alongside the existing Go code in parallel, almost like
plugging an additional kidney[^2] into your body. We'd compare the
outputs to make sure the Rust code was working properly.

[^2]: Which is actually how kidney transplants work! They don't take
    out the old kidneys.

It's funny to say, but in some ways, the most dangerous code is the
code that is not run.
