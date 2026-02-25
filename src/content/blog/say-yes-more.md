---
title: "Say Yes More (In Open Source)"
pubDate: "Jun 22, 2025"
draft: true
---

I saw [a tweet](https://x.com/mitchellh/status/1936515706469921027)
that exemplified a divide I've noticed in programming languages and
open source projects as a whole. Some projects say yes, others say
no. That's a massive overgeneralization but it really does act as a
good barometer. Some projects are willing to accept new people and
listen to them. Others may pay lip service to new contributors, new
ideas, new features, but don't actually say yes enough.

Of course, there are some cases where you need to say no. If you're
writing a programming language and someone requests that you add a
coffee machine API, perhaps you don't need to do that. But there's a
lot of situations where you can say yes.

Like take the example I linked above. The user was suggesting a slight
modification to the Alacritty logo. That's not a very risky area for
change. Why not say yes? You can make it configurable if you really
need. Or you can agree, with the condition that the person work on it
themselves, and you provide feedback. It's extremely low stakes.

If you say yes, you've gained a contributor. You've made them feel
encouraged and supported. If you reject it immediately, you don't have
a contributor. In fact, you might not even have a user.

It's these contributions on the margins that matter. They're often the
entrypoint to more central work. A contributor doesn't start by
rewriting the type checker. They start with a documentation fix, maybe
a small bug that they noticed.

A common response is that dealing with these little tiny requests
takes up precious maintainer time. I'd argue that's missing the forest
for the trees. Don't think of small requests or PRs as taking up
maintainer time. Think of it as training. You don't get mad if you
have to review an intern's code multiple times. You're investing in
their development.

Another response is that your project requires *vision*, and that
requires turning down ideas. Well, sure, in the critical cases I
agree. When I worked on Turborepo, we had this idea of not becoming
Bazel. Bazel is an excellent build system, but it lies on the complete
other end of the spectrum in terms of complexity. We would filter
ideas to avoid too much complexity. But there's a lot of cases where
that's not a problem. If someone wants to add some docs or fix a bug,
that's not adding complexity! And if there's a feature that's juuuuust
on the border, well maybe we can squeeze it in.
