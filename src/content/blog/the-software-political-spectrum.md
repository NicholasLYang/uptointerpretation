---
title: "The Software Belief Spectrum"
pubDate: "Oct 15, 2022"
draft: true
---

Much like politics, there is a gradient of belief systems within
software development. If you read enough old writing about software
development, you can get an idea of general trends within this
gradient.

One trend that I've noticed is a general move towards what I'd call a
liberalization of software development. Historically, software
development was rather conservative, in that programmers were not
given a lot of freedom to work as they wish. Think of the film Office
Space. Peter is a programmer who has multiple bosses, who does not
have any control over his work, and who needs to file endless boring
paperwork. Software developers had no control over what to build, and
were treated as "code monkeys". And of course, software was mostly
closed source. Free distribution of software was relatively unheard
of.

In present time, at a macro trend, I'd guess that this is still the
case. Most programmers do not work at tech driven companies where the
programmers have the power to decide what to build, where bosses are
technical, where their code is open source, etc. But in the micro, a
lot has changed. There are many places that let programmers decide
their tasks, that have technical bosses, and where open source is
prized. Basically, the Overton window of what is considered to be good
software development has widened significantly.

Most of this is probably not a surprise to people. You can look at
something like Office Space or Dilbert and recognize that many
companies, while not perfect, are far more liberal with their work
environments. What might be more of a surprise are the challenges that
come with this liberalization. While I am personally a fan of more
liberal work environments, I predict that we will see a bit of a
backlash to them as potential downsides make themselves
known. Specifically, liberal work environments can fall into issues
with shipping, customer impact, and non-pragmatic technology. Without
addressing these issues, programmers run the risk of seeing a
regression to more conservative styles of development. I propose that
we get ahead of these issues and keep the freedom that we programmers
have earned.

# Shipping

A common defiant refrain about shipping code is that "it's done
when it's done". This is often in response to the classic conservative
framework for shipping: managers, often non-technical ones, set
deadlines and the programmers have to race to complete them. And in
light of this framework, the refrain is understandable. Development
time is not something that can just be adjusted according to the whim
of management. Fred Brooks' eponymous law---that adding more people to
a late software project makes it later---is simply a corollary of this.

However, this refrain has become more than a pithy retort to an
overbearing system of management. It has extended itself to become an
entire ideology, an ideology that states that management has no say
over development deadlines: "It's done when it's done and therefore do
not attempt to hold me to a deadline."

In reality this is a similarly ridiculous framework. Managers have
many tools at their disposal to adjust deadlines. They can change what
done means, in other words cutting scope. They can investigate what is
slowing developers down. Perhaps a developer is stuck on some task and
needs to just back out and focus on something different for a
bit. Perhaps, contrary to Brooks' law, there are not enough developers
on the task. Yes, adding more developers to a project can reduce
efficiency in general, but some tasks are more parallelizable than
others.
