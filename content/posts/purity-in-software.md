---
title: "Purity in Software"
date: 2022-10-26T01:23:58-04:00
draft: true
---

In a previous post I talked about how people have this irrational
dislike of dependencies. Sure, dependencies are not great. But they're
also inevitable. You need to run code that is not yours. It's just a
matter of whether it's going to be managed in an ad-hoc, poorly
versioned fashion, or in a versioned, somewhat stable fashion.

And it's not like people don't know this. If you asked any halfway
reasonable programmer, they'd tell you that sure, dependencies are
part of a healthy programming diet. So then why are there languages
and runtimes and frameworks that have absolutely refused to provide
good dependency support? That insist upon solutions that do not work?
Go is of course the classic example. Go started out saying
"dependencies? Just vendor it all in one single folder on your
computer" and eventually got to "okay fine, here's Go modules". Deno
likewise started out as a clean break, and now they're adding npm
support.

I suspect the answer is ideology. People are ideologically opposed to
dependencies and therefore feel the need to make them difficult, in
order to penalize those who dare use them.

Where does this ideology come from? In short, a fear of the
unsolvable. Many programmers are uncomfortable with unsolvable
problems. When encountering an unsolvable problem, there's two main
approaches: try to solve an approximate solution, or refute the
problem entirely. The latter is surprisingly appealing. Think of the
person who instead of grappling with the risk of nuclear war, pushes
it aside as an impossibility. Think of the person who dismissed COVID
in the beginning. Denial is a powerful tool.

As programmers, we pretend to live in a world of clean abstractions
and elegant designs. We pretend that you just need a simple, elegant
language; that paths are Unicode; that everything behaves like a Unix
machine; but the truth is far from it.
