---
title: "Explaining Redux"
date: 2018-02-26T13:31:22-05:00
draft: true
---

Redux is one of those concepts that is so damn hard to explain, and
even harder to understand. For me at least, React was a lot easier to
understand at a high level. I mean, React at a high level is basically
just objects minus inheritance plus one way data flow. Or function
composition with some enclosed state if you're into functional
programming. Sure, there's a lot more to it once you get into the
nitty gritty, but really that's all you need to know to start writing
React code.

Redux however, is a lot trickier to explain. Because first, you have
to understand *why* you need Redux. And [you might
not!](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)
Second, you need to understand the terminology. I mean, reading about
Redux is just a whole slew of "actions, reducers, action creators,
store, middleware", blah blah blah. It's pretty overwhelming. And
third there's just so many ways to use Redux. There's
[ducks](https://github.com/erikras/ducks-modular-redux), there's the
[Redux documentation](https://redux.js.org/basics/reducers). It's just
a whole mess of different styles.

The goal of this post is to explain Redux in a simple, high level
way. I'm not going to tell you how to write your reducers, or action
creators or really anything code specific. I just want to give a clear
mental image for the Redux workflow. Let's get started!

Let's imagine you're a kid. You get cash, either from an allowance
from your parents, or from odd jobs like mowing lawns or delivering
papers. Because you're a kid, you don't have a bank account, you keep
the cash on you and spend it at places like the grocery store, or the
movie theater. In this case, the cash is the local state. You are the
component. You're not doing anything fancy with it. You're basically
just spending it in a few different places. You get the cash from a
few sources and you spend it in a few places. The cash never leaves
your pocket except for when you decide to spend it. This is perfectly
fine.

But let's say you grow up and get a job. Your job no longer pays you
in person. They pay you through a bank account. In this model, you
don't actually have the cash on you. Instead, you decide to place the
cash into a separate *store* of value. When you need physical cash,
you ask the bank account for some money. When you want to deposit
money, you write a check, which the bank receives, and adjusts your
balance accordingly. By using a check, you have a few benefits. First,
you can easily record the cash flow. You can see your income and your
spending. If there's a discrepancy, you can isolate where and how it
happened. In Redux, these checks are called *actions*. Actions are
basically ways of telling the store that you want to change the
state. The bank account is called the *store* and keeps the state. The
bank itself is called the *reducer* and responds to actions by
adjusting the store accordingly (there's a little more to that, but
I'll discuss it later).

Moving on, there's a few more benefits. For instance, if you have more
than one person using the cash, e.g. a family member, then you don't
have to physically hand that person cash. Instead, you can connect
them to the bank account, and they can withdraw as needed.

Likewise, there's an idea of middleware. Let's say you want to get
bank alerts on your phone. What you can do is ask the bank to send you
notices whenever someone takes out money. This is called a
*middleware*. A middleware intercepts the actions and applies a
function to it, in this case a logging function.