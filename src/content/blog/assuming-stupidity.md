---
title: "Assuming Stupidity"
pubDate: "Jul 26, 2019"
draft: false
---

We've all heard of the term mansplaining---when a man condescendingly
explains something to a person, often female, who already knows
it. It's quite terrible and unfortunately too common on tech.

While I don't want to discredit the phenomenon of
mansplaining, what I've noticed is that this situation of a person
assuming another person's stupidity and using it to condescend or
treat them badly is far more common than just the cases of
mansplaining. Especially in tech.

This condescension and rudeness stems from an assumption of
stupidity. These assumers (or "asses" for short) often cite times
where they assumed the opposite, namely that a person was competent,
and paid the resulting price for it. Maybe a junior developer wrote
some awful code that crashed the server. Maybe somebody deleted a prod
database. "I just can't trust people" they claim.

However the opposite of assuming competence is not to assume
stupidity. The opposite of assuming competence is to be
skeptical. However not a rude, aggressive skepticism, but a friendly,
polite, but thorough skepticism.

Let's give some examples.

**Junior Dev** Hey, how do I write to prod?

**Assuming Alex** Oh no no no, **never** write to prod. That's a
*terrible* idea.

**Junior Dev** Okay, well I need to run my code on a server. What should I
do?

**Assuming Alex** You'll need to work on the staging environment. For
that, you'll need SSH keys, which you'll need to generate using your
terminal. You can open your---

**Junior Dev** ---oh yeah I've used ter

**Assuming Alex** ---terminal by going to Applications and double
clicking on Terminal. Then you'll need to generate your keys---

**Junior Dev** Uh huh, I already did that. <sup>I've sshing into servers since I was 15</sup>

**Assuming Alex** SSH is how we connect to servers. You can write commands on SSH like `ls`.

**Junior Dev** ... <sup> Buddy I wrote a terminal in class </sup>

\<*15 minutes of condescending explaining later*\>

**Assuming Alex** And that's how you SSH into a terminal

**Junior Dev** Great....<sup> Damn that took forever. Not talking to him again</sup>


There's a few mistakes here. The first is when Alex responds to the
junior dev's request to write to prod with horror. Sure, it's not a great
idea, but acting in such a dramatic fashion just makes the junior dev feel
stupid. The second mistake Alex makes is *not asking any
questions*. Alex is spitting out information without any context on
the junior dev's knowledge or the project's current state. Not only is
this advice given from a place of ignorance, as we can see from the
junior dev's attempts at interjections, it's knowledge the junior dev already
has.

What would a healthier conversation look like? Let's give it a try.

**Junior Dev** Hey, how do I write to prod?

**Skeptical Sarah** Hmm, why do you want to write to prod?

**Junior Dev** Oh well I can't process this data locally because it's too
big.

**Skeptical Sarah** Ah, that makes sense. How about we work on the
staging environment instead. Are you familiar with staging
environments?

**Junior Dev** Yeah, sure...

**Skeptical Sarah** Just to double check, could you give me a quick
explanation of them?

**Junior Dev** Hmm, well, erm, they're environments that stage stuff?

**Skeptical Sarah** Close! They're environments that mirror prod as
closely as possible which allow us to test code meticulously before we
deploy for real.

**Junior Dev** Huh, okay. Thanks for that!

**SS** Now to connect to the staging server, you'll need
SSH keys. Are you familiar with SSH?

**JD** Yep! I've had to SSH into servers for class.

**SS** Great! Could you send me your public key? If you need help with
that, feel free to ping me again.

**JD** Sounds good!

With this conversation, Sarah manages to dissect why the junior dev wants
to write to prod, offer a solution and most importantly *do it without
being a jerk*. When Sarah senses that the junior dev doesn't know
something, she doesn't just vomit out info, but instead nudges the
junior dev towards the right path.

Some may argue that the junior dev may not actually know how SSH keys work
and mess up, maybe send Sarah their private key. Sure, but messing up
is part of the process. It's not the end of the world if Sarah ends up
with the junior dev's private key, but the junior dev learns a valuable lesson
in the process. Also, because Sarah was nice and approachable, the
junior dev is much more likely to approach her again and ask for help if
they get stuck on their SSH keys.

Why does this matter? For one it's always good to not be a jerk. But
also assuming stupidity makes people less likely to ask you
questions. They'll see you as a person who at best wastes their time
with lengthy explanations and at worst is mean to them. And lack of
communication creates the precise problems that an assumption of
stupidity seeks to prevent in the first place. Stupidity also obscures
the true root cause of a potential issue. When Alex assumed the junior
dev was just stupid for asking for prod access, he missed a
potentially crucial bit of information by not asking why the junior
dev wanted prod access. Perhaps the junior dev has a valid
reason. Perhaps there's an underlying issue that needs to be
fixed. Maybe, upon hearing that the junior dev was having trouble
processing the data locally, Alex could have advised a better
alternative, like smaller batches of data. But by assuming stupidity,
Alex was unable to discover the root cause of the intern's problem.

TL;DR: Being an asshole to get a point across is an inherently
contradictory idea; the act of being an asshole inherently hampers
communication.
