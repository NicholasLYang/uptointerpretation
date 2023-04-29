---
title: "Principles of IDEs"
date: 2023-04-26T23:16:56-04:00
draft: true
---

Here are some principles of IDEs that I think are worth codifying:

- The principle of least navigation

Navigating in an IDE is annoying. In text, sure, you may know all the
fancy navigation shortcuts like C-a M-< M-f and so on, but it's still
a non-zero amount of typing. And when it's navigating in a file
system, that's even more typing and searching.

An IDE should minimize the amount of time spent navigating. That can
mean automatically importing a function while the user is typing a
call-site, so that they don't have to navigate to the top of the file
to reach the imports, or it can mean using goto definition to avoid
having to figure out in which file a class is defined.

Naviagation can also mean moving blocks of code around, like if you
need to flip an if statement.

You should aim to keep your users on a linear path of typing, kinda
like how modern CPU-friendly code avoids branches and jumps.

- The principle of using to declare

My example of importing a function while the user is typing also
highlights another principle, that IDEs should allow usages to create
declarations. If a variable is not defined, an IDE should not just
give an error message and give up. Unless the user is very confused,
there are only a few possibilities: the variable is misspelled; the
variable is not defined yet and a definition needs to be added; the
variable is defined later on. Each of these can and should be solved
by the IDE. The name can be fixed; the definition automatically
generated or moved up. Likewise, IntelliJ's Rust plugin just added the
ability to add a new enum variant if it's not defined.

- The principle of least memorization

A description of programming is that it's about [holding a bunch of
concepts in your head at
once](https://heeris.id.au/2013/this-is-why-you-shouldnt-interrupt-a-programmer/). That's
true, but I do believe that IDEs make it so you have to hold *less* in
your head. A very common feature in IDEs these days is to show a
function's parameters and their types in a tooltip as you're typing a
function call. That way, you can know the correct order and types,
thereby saving you from having to remember it.

The oldest trick in the book to avoid memorization is windowing. By
having multiple windows open, you can read off of one window while
typing in the other. But windowing is a very brute-force solution. For
one, it takes up a lot of space. Yes, you could in theory create a 9x9
grid of windows, but that's the next problem, each window makes it
harder to read the code. And it's annoying when your cursor is in the
wrong window and you accidentally mess up the wrong code.

This principle is underused in my view. A lot of writing code is
reading code, and there can certainly be ways to limit how much
memorization is required in this reading. Whether that's a small
tooltip view into the function definition, or the ability to
automatically open up a worktree to reference the main branch, I think
there's room for improvement here.

- The principle of visualization

If there's one unsung feature of modern IDEs, I'd say it's syntax
highlighting. If you've ever tried to implement syntax highlighting,
you may be surprised at how hard it is to get it truly good. For the
longest time, syntax highlighters relied on regular expressions. This
meant you could only color in broad swaths; identifiers are green,
keywords are yellow, and so on. That wasn't good enough to let users
distinguish between a variable and a function, or to highlight a
method that is imported versus not imported, or to give a method call
a specific color. Syntax highlighting inside strings is also quite
helpful, whether for interpolations or regexes.

Of course, syntax highlighting is also about what you don't
highlight. Most modern IDEs avoid overwhelming the user with a
potpourri of color, instead choosing to highlight only a few key
elements.
