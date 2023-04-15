---
title: "Things I Used to Care About"
date: 2023-03-21T09:15:24-04:00
draft: true
---

Do you ever look back and marvel at how much fashion has changed? In
2016 all the cool kids were wearing selvedge jeans, Clarks desert
boots or maybe Stan Smith sneakers. Oh and they were writing ES6
with React classes, CSS in JS and Redux. Y'know, the cool front-end
dev kids. Now it's all kids wearing light-washed high-waisted
bell-bottom (!) jeans with some chunky sneakers, writing Solid with
Tailwind and TypeScript. It's like Abe Simpson said, I used to be with
"it", but then they changed what "it" was.

What's funny about these trends is that in the present, it's quite
easy to see them as eternal truths. Plenty of people bought into the
idea that good-year welted boots and selvedge jeans were timeless
classics that would never go out of fashion. Likewise there were a lot
of trends around best practices in programming that we all thought
were going to always be true. Such as...

# Don't Shadow Variables

Take a look at this Java code.

```
String user = "Nick";
String user = "Nicholas";
System.out.println("My name is " + user);
```

What does this print? Trick question, it gives a compile error. We've
defined two variables with the name `user` in the same scope. Because
they have the same name, we cannot access the first one after the
second one is defined, thereby shadowing the first variable. This was
generally considered to be a bad practice. Many compilers, such as
`javac`, would return an error upon encountering shadowing.

However, if you've written any Rust, you'll know that shadowing is
actually really common and basically encouraged in Rust. An extremely
prevalent pattern is:

```
let user: Option<String> = get_user();
if let Some(user) = user {
  println!("The user is {}", user);
}
```

What this code is doing is taking a variable `user` that is of type
`Option<String>` and printing it if the `user` is `Some`, i.e. if the
`user` variable has a value inside it[^1]. The way it does so is by using
a `if let` expression, which allows you to take an expression and
match it against a pattern, in this case `Some(user)`. The pattern
creates bindings, i.e. new variables, in this case a `user` variable
that is the `String` value that must exist if this pattern is matched.

[^1]: Quick background, in Rust you have what are called non-nullable
    types, where they cannot have the `null` value by default. To
    express a `null` value, you need to use an `Option` wrapper, which
    is what's called a sum type. If you're not familiar with this
    concept, I'd recommend reading the Rust documentation.

Basically, inside the `if let` block, we are shadowing the `user` that
is of type `Option<String>` with the `user` of type `String`. And it's
fine! It's fine because Rust's borrow checking rules ensure that you
don't want to use the first `user`. It's also fine because Rust offers
very robust typing and most editors provide inline type hints, which
allow you to see the types of your variables live. You can see that
the first `user` is an `Option<String>` and the second `user` is a
`String`.

You may say that pattern matching is materially different than
defining variables and therefore shadowing is fine in that
context. Okay, sure. How about this?

```
let index = get_index();
a[index] = 10;
let index = index + 1;
```

We're shadowing `index` here. But it's pretty clear what we mean. We
have an index that we use in one context, and then we create a new
index after that. Of course, we could use mutability for this,
something like:

```
let mut index = getIndex();
a[index] = 10;
index = index + 1;
```

But in general it's not recommended to use mutability in Rust. If your
variable has a bunch of methods on it, you are now permitting the
mutable ones to be called, which may not be what you want. Shadowing
allows us to mimick it without resorting to mutability.

Another case where shadowing is nice is when you want to unwrap a
variable with the `?` operator:

```
let lhs_ty = lhs_ty?;
let rhs_ty = rhs_ty?;
```

If I couldn't shadow, I'd have to come up with some awkward names like

```
let lhs_ty = lhs_ty_result?;
let rhs_ty = rhs_ty_result?;
```

Which isn't the worst, but it's getting weirdly close to [Hungarian
notation](https://en.wikipedia.org/wiki/Hungarian_notation).

So why is shadowing suddenly kosher? Well for one, I don't think it
was ever *that* big of a deal. But it is true that when dealing with
mutability, shadowing does become a little messier. When you have
multiple variables that can be mutated with the same name, mutating
the wrong one can cause very confusing bugs.

Similarly, I suspect that in Rust, you generally have a lot fewer
variables and objects in scope. Nested functions are not encouraged
and generally most values are moved after one or two uses.

# Formatting

I started programming seriously right before formatters became super
popular. I remember my mentors harping on me carefully aligning my
arguments to a function call, and religiously following the 80
character rule. They always stressed that I should *not* follow my
editor's default formatting (which, to be fair, was limited to just
awkward auto-indentation).

Now, I can't remember the last time I had a discussion around
formatting beyond "hey you forgot to run rustfmt/gofmt/prettier". It's
also interesting how my opinions on formatting have totally vanished
too. I don't care as long as the lines are not super long and the code
is legible. For a while I had debates with people who stubbornly hated
on whatever formatting rules, but I suspect even the most opinionated
of developers have begrudgingly embraced formatters.

Even when I worked on a formatter at Rome, we eventually concluded
that there was no point to changing any of Prettier's choices. It
would just cause large diffs when adopting our formatter and no
material benefit.

# (Many) ESLint Rules

If you want some culture shock, open up an ESLint setup
from 2016. Remember when everybody was convinced that you *had* to use
`const` everywhere, that you *had* to use arrow functions, that
mutability was the source of all evil? Or that you *had* to name your
error variables `err` and not *gasp* `e`??? Yeah...times have
changed. Some people probably do still care about these rules, but for
me, I can't say I do.

This isn't to say that linters are bad or out of vogue. But I think
there was a period where we were really fanatical about lint rules and
now, hopefully, we've backed off a little.

The rational often cited was that linters enforced standardization and
therefore less mental overhead when it came to reading the code. I do
think that's somewhat believable, not necessarily to the point where
you have to make everything an arrow function or everything const. In
fact, that standardization erased some nuances within the context, say
that a variable is indeed intended to be mutated and is not a constant
(`const` is mutable despite its name), or that you were defining a top
level function and didn't care about `this` binding rules, so using
`function` was perfectly acceptable.

# Design Patterns

These are a little more retro than the other examples, with the
classic text being the book published by the Gang of Four in 1994. But
I definitely still encountered them in the 2010's. Again, the basic
idea is very much sound: common patterns that are helpful for
designing software. But the practice did become a little dogmatic. And
in many cases the practice was a substitute for missing language
concepts.

For instance, a lot of the patterns based around executing different
code based on run-time types, such as Strategy or Visitor patterns,
can be modeled quite nicely with sum types. Sure, you can visit an AST
using a visitor pattern, but it's a lot nicer to just pattern match on
the damn thing.

In a more general sense, there is a continuing issue of people
assuming that abstraction makes better code. I've definitely run into
this pitfall where I write some Rust that's just a few functions that
call each other, but then worry that it's not abstract and
professional enough, maybe I should add some generics and traits? When
really, no, abstraction is a cost you pay when you have to limit
complexity. If there is no complexity to limit, don't abstract.

# Function Length, DRY, Single Return and other Clean Code-isms

In a sense these were the ESLint rules before ESLint rules. In code
review you'd be lectured that a function longer than 10 lines needed
to be split up, or that you should only have a single return
statement, or that repeated code was evil.

There's somewhat reasonable justification for these concepts. Long
functions can be quite hard to read, while smaller utility functions
can help with abstraction. If you have a lot of complicated control
flow, i.e. a lot of what's called cyclomatic complexity, that can be
hard to read (although I don't know how you could conclude that only a
single return statement is an acceptable solution). Repeated code does
add to mental overhead and requires changes to be propagated to
multiple places in the codebase, leaving the door open for partial
migration states.

However...there are also really compelling counter-examples. Sometimes
you just want to read a long function top to bottom instead of jumping
into 7 different utility functions. I used to feel guilty about
writing large functions in my compiler, but then I realized that the
long functions consisted of pattern matching and handling multiple,
mutually exclusive cases. In which case, was it so hard to read?
No. If one case started to get really long, maybe I'd factor it out
into a separate function, but until then, there really was no need.

Similarly, multiple return statements are great! Often you want to
handle the edgecases in the beginning, then continue on the happy path
for the rest of the function. In fact some languages make this quite
easy, such as Swift with guards and Ruby with post-fix if.

In fact, the whole advice against cyclomatic complexity, basically
lots of `if/else` conditions, is a little overwrung. There certainly
can be cases where cyclomatic complexity is too high, but one cases
where I actually don't think this applies is a long chain of unnested
`if/else` statements:

```
if (day == "Monday") {
  ...
} else if (day == "Tuesday") {
  ...
} else if (day == "Wednesday") {
  ...
}
```

Now, there are cases where this chain is a code smell, say if you
wanted to do something very dumb like get the first character of the
day, you could obviously rewrite this to:

```
day[0]
```

But if you were genuinely doing logic that depended on the day, this
is probably the best option. Heck, even if you were mapping to some
data, like say an internal id, I don't think this would be a bad
idea. Yes, yes, you could create a hash map and index with the day,
but then you're allocating memory (unless your compiler is *really*
smart) and you're having to chase a pointer, for what's basically the
same code. Sure this is all premature optimization, but you could
argue that using a hash map is premature refactoring.

The same is true with Don't Repeat Yourself (DRY). Often times it's
totally fine to repeat yourself. The guideline I generally follow is
to only deduplicate after three usages or if the repeated piece is
longer than a few lines. It should also depend on the context of the
code. For instance, I may have a helper function that I use in two
different Rust crates. I could factor it out into a separate crate,
but that would be a massive amount of overhead for a single
function. Instead, I may just copy the function and include a comment
noting that there is a duplicate in the other crate.

Again, this is a situation where people took general guidelines and
turned them into dogma. Furthermore, when giving this advice, what is
often neglected is the prioritization of the guidelines. Yes, you
shouldn't repeat yourself, but is that more or less important than
code readability? Is cyclomatic complexity important unto itself or is
it in service of legibility? You can make a list of good qualities but
the list will be self-contradictory and impossible to fulfill. The
tricky aspect of programming is not the making of the list, but
balancing all of the items in a feasible manner.

# File Structure

I used to use emacs with a relatively sparse config. Therefore, to
navigate to code, I'd have to go searching through the file tree.
