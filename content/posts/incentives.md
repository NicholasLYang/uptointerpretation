---
title: "Incentives"
date: 2022-10-06T01:15:25-05:00
draft: false
---

Every kid has, while grumbling about some injustice perpetrated upon
them by their parents, declared "when I'm a parent, I won't make my
kids do these lame things. I'll be a cool parent!"  Sometimes they're
right. Most of the time, however, they are wrong. Why are they wrong?
Because they don't understand that as a kid, they have fundamentally
different incentives than as a parent. A kid may want to eat lots of
candy and not go to school, because their incentives are to get
dopamine out of eating sugar and playing. As a parent the incentives
are to ensure that their kid is healthy (i.e. not eating too much
sugar) and educated (i.e. goes to school). These incentives are
fundamentally at odds.

The same is true for programmers. Many a programmer has thought at
some point or another, "when I run a company, I won't make programmers
do these lame things. I'll be a cool boss!" These lame things, instead
of going to bed early and eating healthy, is stuff like setting
deadlines, writing imperfect code in order to ship, letting customers
determine the roadmap, and so on. In a programmer's mind, they'll run
a company where the code is done when it's done, plenty of time will
be devoted to refactoring, and no pesky PMs will tell them what to do.


You see, the incentives of a programmer are to write code in a way
that feels good, and to have the freedom to write what they want. The
first point is worth some more explaining. At least for me, there is
certain code that feels pleasurable to write and other code that feels
less pleasurable. Refactoring, for instance, is a very pleasurable
activity. This is not surprising. Refactoring is fundamentally an
attempt to "clean up code" and therefore make it more
understandable. Of course, this really means more understandable in
the eyes of the person refactoring. Combined with the general aura of
virtue that surrounds refactoring, means that programmers are very
much incentivized to refactor. They get to remake the code to be
easier for them to understand and they get to feel superior for doing
so.

This focus on pleasure can also apply to languages. I like writing
Rust. It's a fantastic combination of a language that lets me express
my ideas, that challenges me but doesn't frustrate me, and that lets
me write fast code. If I were starting a company, I'd be very tempted
to write my code in Rust.

Should I though? In most cases, probably not. If I'm writing a
back-end server, I'd probably spend more time getting my server
library connected to my auth library to my database than I
should. Something like JavaScript or Ruby on Rails would be a lot more
pragmatic, at the cost of being less "fun".

And really, the latter point of freedom is just an extension of the
former point; in order to spend time doing the pleasurable tasks, one
must have the freedom to avoid the less pleasurable ones.

The incentives of a company leader are quite different. The company
leader needs to build a product that people actually want. The leader
needs to promise stuff in advance, then hit those deadlines. And if
the code is a little messy or there's a small bug or two? Not a big
deal.

Neither of these people are in the wrong or right. Both of their
incentives are valid. A programmer needs to fight for quality because
a leader will likely not. A leader needs to fight for customer needs
because a programmer will likely not. It's important that we have this
balance. And indeed, both parties have an incentive to keep the other
party happy, and therefore compromise.

Which does mean that when this balance is disrupted, say, when a
programmer becomes a company leader, there is a real problem. Unlike a
parent, a newly minted leader doesn't always understand this change in
incentives. They may continue to play by their old programmer
incentives. What's especially dangerous here is that they won't
receive any negative feedback for these actions. The other programmers
will cheer them on for providing what the programmers want. Only when
reality comes knocking in the form of no users or no funding will the
new leader realize how much their incentives have shifted. Even then,
there is some danger that the leader still won't change because
they're afraid of looking like they "sold out" and became an evil
manager.

Which, again, managers are not inherently evil. Sure, there are
managers whose methods hurt the company. But there are also
programmers who hurt the company: programmers who spend endless time
refactoring and not shipping; programmers who refuse to talk to users
out of hubris; programmers who pick tech stacks out of their personal
interest and not out of pragmatism.

Going back to the parent analogy, you can have bad parents and still
recognize the utility of parenting.

Therefore, if you're switching positions, whether it's becoming an
engineering manager, starting a company, working as a PM, etc., you
should probably reassess your incentives and act accordingly. And
begrudge people their incentives either! Just because they're not
doing what you want, doesn't mean they're bad at their job or out to
get you.
