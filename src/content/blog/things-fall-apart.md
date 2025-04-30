---
title: "Things Fall Apart"
pubDate: "Sep 23, 2023"
draft: false
---

How bad would it be if your code broke? That's a question I wish we
asked ourselves more. Sometimes the answer is really really bad. If
you write code for airplanes or medical devices, failure is quite
literally fatal. Or maybe you're writing software that trades billions
of dollars of financial instruments. Failure is bad there, but maybe
not as bad as in the previous group.

However, for the majority of us, the answer is not that
dramatic. Maybe someone's website won't work for a bit. Maybe a
dashboard will be out of sync. Maybe you lose a few users. Yes, it's
bad but it's not **bad**.

Why is this a question worth asking? Because the potential downsides
of code breaking dictate your strategy for software development. It
dictates how often you release, how much testing you do, what you tell
your users, and so on. It dictates your tolerance for risk.

I remember discussing with a former colleague about shipping a
developer tool. They hemmed and hawwed, and eventually confessed that
they thought the tool was not ready for production. I was honestly
pretty baffled. I asked them, "what does production mean here?"
Because you see, our tool was not in any shape or form in the direct
path for a user's product. It would not affect the correctness of
their code. The worst it would do is create some small amount of
inconvenience. It was not running on a server. Nevermind that we had
extensive testing and quite good code review.

The issue here was twofold. The first is that errors are more damning
if you don't believe in your product's inherent value. It's like that
old rule of dating: You'll know if they're interested. If someone is
interested in your product, then you can have a couple bugs here and
there and they'll still use it. If the condition for the product is
perfect execution, well, that's an impossible condition. It's like
someone saying they'll date you if you behave perfectly. Maybe they
just don't want to date you.

The second issue is pride. As software developers, we have a certain
pride in our code. Our code is correct, not like those other
developers. It's why not-invented-here syndrome exists. It's why
developers reinvent the wheel. We believe that we can do better. As
such, it's not so nice to admit that you can't write code that's
always better than other people's code. It's not so nice to admit that
sometimes you mess up.

But that is the reality of programming. By shipping your code, you are
opening yourself up to bugs, to ridicule, to people questioning your
intelligence because how could you have missed such a simple bug?!
Too bad. You will mess up. Projects with far more at stake have messed
up. Bridges have collapsed; space shuttles have exploded; planes have
crashed. Failure is how we learn.

You should not avoid failure at all costs. There is always too high a
cost. Any sane manager understands this. If you asked them whether
they would delay their release for a year if they could guarantee
almost zero bugs, who would do it? What about two years? Ten?  Never?
Failure is something to be weighed and balanced. This is why we don't
build buildings with two feet thick walls out of poured
concrete. There is a cost to infallibility.

I wish we discussed this more in my CS major. In the rare case study
we'd study something like the
[Therac-25](https://en.wikipedia.org/wiki/Therac-25), which, while a
good example of how concurrent programming can be tricky, is also a
total strawman. I'll be sure to review it when I start writing code
for radiation therapy machines, but until then, I think the stakes are
a little less dire.

And sometimes it's okay to willingly create bugs for your users. I
love writing Rust because it's a wonderful, expressive, fast,
well-designed language. I also love it because it's totally obsessive
about getting your code right. It will nitpick you on details like
strings must be valid UTF-8 while filesystem paths do not have to be
valid UTF-8. That can save your bacon. It can also send you into a
spiral of obsessing about whether your users pass you a non-UTF-8 path
and what should you do? And oh no, maybe we should figure out a way to
rewrite the path and inform the user or maybe we could just pass bytes
and then only convert to string when necessary, and so on. I love
writing Rust, but on occasion I have to remind myself that it's okay
to just throw an error and tell the user to use a UTF-8
path. Sometimes it's okay if your code doesn't catch all of those edge
cases.

Not to get all philosophical, but we'll all be dead someday. It's not
worth wasting your time trying to ship perfect code.
