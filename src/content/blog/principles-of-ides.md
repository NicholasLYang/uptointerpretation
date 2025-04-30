---
title: "Principles of IDEs"
pubDate: "May 4, 2023"
draft: false
---

Here are some principles of IDEs that I think are worth codifying:

# The principle of least navigation

Navigating in an IDE is annoying. In text, sure, you may know all the
fancy navigation shortcuts like C-a M-< M-f and so on, but it's still
a non-zero amount of typing. And when it's navigating in a file
system, that's even more typing and searching.

An IDE should minimize the amount of time spent navigating. That can
mean automatically importing a function while the user is typing a
call-site, so that they don't have to navigate to the top of the file
to reach the imports, or it can mean using goto definition to avoid
having to figure out in which file a class is defined.

Navigation can also mean moving blocks of code around, like if you
need to flip an if statement, it should be doable via a code action
and not just manual manipulation.

You should aim to keep your users on a linear path of typing, kinda
like how modern CPU-friendly code avoids branches and jumps.

# The principle of using to declare

My example of importing a function while the user is typing also
highlights another principle, that IDEs should allow usages to create
declarations. If a variable is not defined, an IDE should not just
give an error message and give up. Unless the user is very confused,
there are only a few possibilities: the variable is misspelled; the
variable is not defined yet and a definition needs to be added; the
variable is defined later on. Each of these can and should be solved
by the IDE. The name can be fixed or the definition automatically
generated.

IntelliJ's Rust plugin just added the ability to add a new enum
variant if it's not defined, but really this should be the case with
anything that can be declared. An IDE should be able to add a
parameter in the case of an undefined variable. It should be able to
add a type variable if one isn't in scope.

This principle is similar to the pull based semantics of [modern query
oriented compilers](https://www.youtube.com/watch?v=wSdV1M7n4gQ),
where compilation is defined as a query about a piece of code, and the
compiler pulls in additional data and definitions to satisfy that
query. A user should be able to define a usage of a symbol and *then*
declare that symbol.

# The principle of least memorization

A description of programming is that it's about [holding a bunch of
concepts in your head at
once](https://heeris.id.au/2013/this-is-why-you-shouldnt-interrupt-a-programmer/). That's
true, but I do believe that IDEs should make it so you have to hold
*less* in your head. A very common feature in IDEs these days is to
show a function's parameters and their types in a tooltip as you're
typing a function call. That way, you can see the correct order and
types without having to memorize it.

The oldest trick in the book to avoid memorization is windowing. By
having multiple windows open, you can read off of one window while
typing in the other. But windowing is a very brute-force solution. For
one, it takes up a lot of space. Yes, you could in theory create a 9x9
grid of windows, but that's the next problem, each window makes it
harder to read the code. You quickly lose track of where the cursor
is, and the window in which you can read code becomes smaller and
smaller with each window.

This principle is underused in my view. A lot of writing code is
reading code, and there can certainly be ways to limit how much
memorization is required in this reading. Whether that's a small
tooltip view into the function definition, or the ability to
automatically open up a worktree to reference the main branch, I think
there's room for improvement here.

# The principle of least documentation reading

I was really surprised to learn that kdy1, the creator of swc,
[doesn't read
documentation](https://twitter.com/kdy1dev/status/1563385180219658240). How
could he use libraries without docs? But after some thought, I do see
how that's feasible these days. Between tools like Copilot or TabNine,
and an ergonomic, rigorous compiler like rustc, you can usually figure
out an API without too much trouble. I've definitely eschewed reading
docs and used goto definition and some autocomplete to figure out
libraries.

That isn't to say I'm anywhere close to kdy1. I still depend on
documentation very very frequently. And while documentation is getting
nicer to navigate with tools like Docsearch and conventions like
[Command-K to search](https://maggieappleton.com/command-bar), it's
still not as efficient as using an IDE. If an IDE can reduce the
amount of time spent reading documentation, whether that's by
integrating docs into the IDE or by adding more tools like Copilot, I
could see that speeding up development workflows significantly.

Note that documentation isn't just the official text on a library's
website, but also its GitHub issues, pull requests, StackOverflow
questions, even Twitter threads. If I could spend less time trawling
through various sources and more time programming, that would be
fantastic.

# The principle of visualization

If there's one unsung feature of modern IDEs, I'd say it's syntax
highlighting. If you've ever tried to implement syntax highlighting,
you may be surprised at how hard it is to get it truly good. For the
longest time, syntax highlighters relied on regular expressions. This
meant you could only color in broad swaths; identifiers are green,
keywords are yellow, and so on. That wasn't good enough to let users
distinguish between a variable and a function, or to highlight a
method that is imported versus not imported, or to give a method call
a specific color. Syntax highlighting inside strings is also quite
helpful, whether for interpolations or regexes. All of these are
necessary pieces for good syntax highlighting. Indeed, you could argue
that syntax highlighting facilitates certain language
features. Without highlighting, features like regular expressions
or string formatting become extremely hard to write correctly.

Of course, syntax highlighting is also about what you don't
highlight. Most modern IDEs avoid overwhelming the user with a
potpourri of color, instead choosing to highlight only a few key
elements.

Lack of highlighting can also function as a very effective indication
that your code is incorrect. Of course, it can also simply indicate
that your IDE is still processing the code and can't highlight it yet.

Part of the goal of an IDE is to provide feedback in a manner that's
ambient and not textual. We can interpret colors faster than we can
read an error message, and for most programmers, the error messages
are really just keys into a dictionary of common bugs that we recall,
so the actual text of the error message is not always relevant.

# The principle of learning

This is more an aspirational principle, but I'd love for IDEs to help
with learning and reading. Specifically, it'd be nice for them to
persist this reading and learning in a manner that's
comprehensible. Whether that's comments that are not embedded in the
code, but are more notes for a specific user, or a visual history of
how the user navigated the codebase, or pull requests that also
function as code walkthroughs, there are a lot of features that IDEs
could add to reify and share the technical knowledge that's hidden in
programmers' minds. After all, the real value of a programmer is
arguably this knowledge of the codebase.

Alongside that, I think there's opportunities for IDEs that are solely
focused on reading code, an Integrated Reading Environment, if you
will. IDEs tend to run at the limits of most computers' performance,
but if you can make the codebase immutable, then the IDEs don't have
to spend computation updating their information. What could they do
instead? Perhaps laws like Linus' law, i.e. "given enough eyeballs,
all bugs are shallow", can be pushed even further with better
tooling. If more people can read the codebase, if we can get more
eyeballs, then perhaps more bugs can be solved.

# Principle of Feature Discovery

IDEs have truly incredible features. Take rust-analyzer's [structural
search and
replace](https://rust-analyzer.github.io/manual.html#structural-search-and-replace). It
allows you to define syntax rewriting rules in a simple manner. I've
wanted this for a while, and yet it took me ages to learn about
it. Why? Because IDEs aren't able to teach users. The best we've
gotten is a few popup menus on how to use a specific feature. Indeed,
most programmers seem to learn IDE features from watching other
programmers.

There should be a way for an IDE to ambiently watch how you write code
and suggest ways to make it faster. Maybe it can snapshot your code
and notice that hey, you inverted an if statement but you didn't use
the invert-if-statement code action. Next time you put a cursor on an
if statement, bring up a tooltip that you can invert it with xyz
shortcut.

# Principle of Customization

I started out using emacs (fun fact, I'm actually a second-generation
emacs user). However, as I started to write more and more code, I
realized that the tooling of IDEs really made my life a lot easier
than it did to know how to capitalize a group of text with `C-x
C-u`. But I do miss the flexibility of emacs sometimes. Like how it's
hard to create custom code actions. Or completely revamp how your IDE
thinks about text or keybindings. Oh sure, you can use plugins, but as
any former emacs user knows, emacs keybinding plugins are quite awful
because the abstraction almost always leaks. That's because the core
of the IDE is rarely that flexible.

Now, I'm not saying we should embed a lisp interpreter inside an IDE
and make all the IDE actions secretly execute lisp functions, because
that's ridiculous on multiple levels. But I'm very excited about tools
like [ast-grep](https://github.com/ast-grep/ast-grep) because they
offer a little bit of that customization back. I'd be interested to
see other ideas like WebAssembly plugins.

# Conclusion

That's all I have for now. I'd love to hear about any other principles
you have in mind. Feel free to reach out at nick@nicholasyang.com
