---
title: "Lambdas in C"
date: 2018-08-03T01:35:20-04:00
draft: true
---

Every so often I stumble upon an attempt to improve C. More often than
not, in these proposed improvements is a rather interesting feature:
lambdas. What (I assume) the writer generally means by this is some
treatment of functions as first class values. After all, anonymous
functions in of themselves would not be particularly useful in C. But
if they could be passed as arguments or returned from functions, well
that's extremely powerful. And really, C already has function
pointers, so shouldn't it just be a matter of extending where
functions can be defined, maybe adding some special syntax, and so on,
right?

Except...that that's completely wrong. You see, C has this remarkable
characteristic in that the call stack in C is, well, a stack. You call
a function, any stack allocated variables are initialized, then when
the function is done, the call frame is popped from the stack and all
the variables are deinitialized. This is why you can't define variable
length arrays that are stack allocated; the compiler needs to know the
size of the stack frame at compile time. 

Now, this seems kind of obvious. A call stack should be a stack, after
all, why name it a call *stack*? Except in most languages the call
stack is not a call stack. Take Python for instance. Let's say you
define a function that returns a function:

```
def foo():
	a = 10
	def bar():
		print(a)
	return bar
```

Then what will the following give?

```
baz = foo()
baz()
```

If you answered `10`, you're correct! You see, the function that was
returned out of `foo` keeps track of the variables that it could see
when it was defined. In this case, that is `a`. Note that `foo` has
returned, so logically, the stack frame containing `a` should be gone
by now, and `a` deinitialized. But because the closure was moved
outside of its calling context, `a` persists. So when will `a` be
freed? Well, that's a very good question. I suppose the naive answer
is whenever the function that encloses `baz` returns. But it's pretty
easy to make an example where it never closes. Make
