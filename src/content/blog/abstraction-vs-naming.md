---
title: "Abstraction vs Naming"
pubDate: "May 20, 2025"
draft: false
---


Abstraction is often stated as a benefit. But it comes at the cost of
naming. Let's take a monad. This is not a monad tutorial, so let's
just say monads are things that have two methods, `bind`, and
`return`.

One such example is a list. In a list, `return` refers to
creating a list with a single element, while `bind` refers to applying
a function to each element in the list, where the function returns a
list of items, and then returning the flattened list of all these
outputs.

The problem is that these are clearly terrible names for these
functions. `return` should probably be `create` or `new`, and `bind`
should be `flat_map` or
[`smoosh_map`](https://developer.chrome.com/blog/smooshgate). Calling
the function `bind` makes it not clear at all what it does.

And really that's inherent to the definition of a monad. When I said a
monad is something that has `bind` and `return`, that wasn't me trying
to avoid defining a monad. That *is* the definition of a
monad[^1]. Anything that has these methods and fulfills their type
signatures can be called a monad.

[^1]: Well, plus or minus some laws.

Other than that, there's nothing that inherently connects the
different monads to each other. In a list monad `bind` flattens lists
into a single list. In a IO monad `bind` sequences IO. And in each of
these cases, `bind` and `return` are terrible names for what the monad
actually does.

Another benefit of monads is that it comes with extra syntax. Except,
the syntax also doesn't really match up with what the monad does. Like
how does this:

```
arr >>= f
```

Intuitively mean that I am flat mapping `f` over `arr`? It doesn't!

Ditto with

```
do
  x <- arr
  f x
```

I understand the temptation, because do notation makes what's usually
a hairy bunch of nested functions into a nice sequential bunch of
pseudo statements, but it just does not make sense visually.

I think there's two reasonable options here to make this better:

1. Just name methods properly and forget about abstraction. Name it
   `flat_map` with a list and `and_then` with a result type.

2. Name methods properly and provide the abstract aliases for when you
   need the abstraction.

The first is nice in that you don't create duplicate names for
methods, but the second lets you have your abstract cake and eat it
too. But really, is it worth it? I'll let you decide.
