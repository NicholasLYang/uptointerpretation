---
title: "Surviving CSO"
date: 2019-08-03T00:00:38-07:00
draft: true
---

Computer Systems Organization, or CSO, is the third class in NYU CAS's
computer science program. I've noticed that a lot of people have
trouble in CSO. This is for good reasons. The class covers a lot of
ground, from the C language to systems architecture to
concurrency. This is especially tricky because students face a massive
paradigm shift from the relatively high level bubble of Python and
Java with its managed memory and forgiving error messages, to the raw,
bare metal of C and x86 assembly. It's kind of like going from driving
a 2019 Mercedes to a Model T.

I was lucky enough to come into the class with some C experience. I've
also learned some more useful techniques for systems development after
I took CSO. I hope that by writing up a quick guide for preparing and
surviving CSO, future students can be more prepared, get better grades
and be happier people. Well, at least the first two. The last one
might require [incremental lifestyle changes sustained over
time](https://www.youtube.com/watch?v=TbwlC2B-BIg).

This guide is applicable whether you have an entire summer to prepare,
a few weeks to practice or even if you're in the middle of the course
and struggling.

Quick aside, if you're in CSO already and worried that you're going to
fail, don't panic. You are almost certainly not the only person in
this situation. In fact, there's a decent chance a lot of people are
in the same boat. CSO generally has a reasonable curve to it. Not that
you should rely on the curve to pass. Now let's get to work:

## Tooling

Memorize these 3 flags: `-Wall -Wpedantic -Wextra`. Flags are ways of
modifying your compiler and how it views your code. Like how you can
flip a switch on some cars and go into sports mode. These three flags
basically turn up your compiler's standards for code quality. The
compiler will now warn you about potential issues in your code. 

One side effect is that when you run your code, you may get a gigantic
boatload of errors. **That's fine**. The compiler is your friend,
who's warning you about potential issues in your code. Better the
compiler than the professor's test suite.

You can either manually call the compiler with these flags. That means
entering something like `gcc -Wall -Wpedantic -Wextra main.c` into the
command line. Or you can [add it to your
Makefile](http://www.cs.colby.edu/maxwell/courses/tutorials/maketutor/).

Install [valgrind](http://www.valgrind.org/). It's extremely
useful for finding places where your code is messing up memory
management. Consider running your code with it automatically.

Finally, learn to use the terminal if you haven't already. If you're
using Windows, either install a Linux VM, dual boot Linux or use the
subsystem. Usually your TA has you set up a Linux VM to run your
code. That's perfectly fine, although personally I run the VM headless
and [SSH into
it](https://stackoverflow.com/questions/5906441/how-to-ssh-to-a-virtualbox-guest-externally-through-a-host). That
way I don't have to interact with the VM except via
Terminal. Seriously consider learning git. Version control allows you
to save various versions of your code, making it easier to store and
roll back your code changes. Maybe learn some vim or emacs, personally
I use emacs.

You don't have to set up all of these things immediately. I'd get the
basics down, learn some C, then tinker with your setup more. Don't
feel like you have to get everything perfect immediately.

## C

Find yourself a good source to learn or relearn C. I'm not up to date
on the best books for C. Personally I used The C Programming Language
by Kernighan & Ritchie but they teach a very old fashioned style with
terrible variable names (like atoi) and really optimistic uses of
memory. I'd look at [some lists
online](https://stackoverflow.com/questions/562303/the-definitive-c-book-guide-and-list).

The areas you need to be solid in are pointers and memory layout. C is
very tricky in that its model of memory has very little
abstraction. Languages like Java or Python abstract away pointers with
the idea of references. C has no such concept. The most important fact
you need to keep in mind when writing C is that **everything is a
value**. Pointers? They're just values that happen to store locations
in memory. Arrays? They're just a pointer to a place in memory (well,
kinda). The `a[i]` syntax? That's just syntactic sugar for `*(a + i)`
Strings? They're just arrays of characters. So if you add 1 to a
pointer, you are simply changing the value of the pointer, not where
it's pointing.

Debugging in C is very different due to how C handles (or doesn't
handle) errors. Java will generally spit out nice stack traces which
you can just follow to find your problems. In C, the most common error
you'll get is a segfault. Segfaults do NOT give stack traces and they
basically immediately halt the program. The way I debug segfaults
personally is to print out markers at various parts in my code, then
see which markers get printed before my code segfaults. For instance,
if you have

```
printf("1\n");
char* x = NULL;
printf("2\n");
*x = 5;
printf("3\n");
```

It's totally okay if you don't understand this. Come back to it once
you've learned some C. In most cases this will print out:

```
1
2   
Segmentation fault: 11
```

This will allow me to say "huh, I guess my error is in between 2 and
3". In fact, you can kinda [binary
search](https://en.wikipedia.org/wiki/Binary_search_algorithm) your
code by putting one marker in the middle of your code, then if it gets
printed out, putting a new marker between the old one and the end of
your code, otherwise putting a new marker between the beginning of
your code and the old marker, etc.

However contrary to popular belief, segfaults are not the worst errors
in C. The worst errors in C are the *silent* ones. These are errors
that don't manifest until you happen to run a particular input or
until a particular thread wins. The only way to really ensure that
your code works 100% is to test it yourself. Now, entire books have
been written on proper, thorough testing. But a quick gist is to
always test for bad input. Passing in a pointer? Make it NULL, 0, -1,
anything you want. Giving in integers? What if they're really large?
What if they're negative? Write code that runs through these
situations.

Under no circumstances should you **ever** submit code that you have
not compiled and run. Never assume your code will work. Test and
verify.


## Assembly

Assembly is probably my weakest area, so I can't offer too much
advice. I guess if I had to give some, it'd be that assembly is just
another language. You can run it, you can generate it, etc. A good way
to read assembly is to write some C, compile it with the `-S` flag and
look at the assembly. The [Godbolt compiler
explorer](https://godbolt.org/) is a nice interactive tool for this as
well.


## Misc

Start the labs early. I definitely had to use some grace days to
submit some of my labs. Even if just the thought of the labs stresses
you out, just try to take the smallest peek at the problem
statement. Even if it's just looking at the README and jotting down a
couple notes, that'll go a long long way.

Depending on your professor and their views on collaboration, you
might want to work with a partner. Even if your professor doesn't look
kindly on collaborating on code, you can still talk to each other
about techniques and ideas.

All the standard advice for hard courses applies. Talk to your
professor. Go to tutoring. Go to office hours. Find a study buddy.

Buy a print copy of the textbook. Yeah, it's likely you won't read it,
but it's even less likely that you'd read the PDF version. You can't
flip through a PDF and print books don't have Facebook or Reddit on
them to distract you. A good trick is to find international editions
of the book. I usually try to find the Indian version, as it's
significantly cheaper (under 20 dollars) and the same exact material.

This is extremely optional and not recommended unless you have the
free time, but I'd consider learning a little Rust. Rust is a very
interesting systems language that enforces a lot of rules about memory
at compile time. It's kind of like working with an extremely anal
programmer who forces you to follow the rules with memory. If you can
pick up some Rust, I guarantee you'll get better at thinking about
memory and how to manage it. Plus it's just a fun language.

Congratulate yourself: you've read this advice and are now on the path
to passing CSO! Good luck!




