---
title: "Those Damn Squares"
date: 2020-07-31T00:58:43-04:00
draft: false
---

I'm not one for productivity metrics. I've tried apps like
ActivityWatch and they don't do it for me. Goodhart's Law comes to
mind: When a measure becomes a target, it ceases to be a good
measure. However there is one metric I occasionally use: GitHub
squares.

I'm referring to those squares that can either be filled in with
varying shades of green---indicating you've contributed a certain
amount of times this day---or left gray, as an indicator that you have
not contributed this day.

This metric is quite bad for a few reasons. For one, commits only
count if you commit to the master/main branch. In a lot of repos one
can't commit consistently to master/main. The repos where one can do
that tend to be newer repos that the person owns. I know a few GitHub
users who, advertently or not, have really impressive squares due to
creating a bunch of new repos and committing to them
constantly. Likewise if you squash commits, a series of 15 impressive
commits will show up as a measly one.

It's also a hilariously easy metric to game if you want. Besides the
obvious technique of keeping your blogs on GitHub
[\*ahem\*](https://github.com/nicholaslyang/horribly_underqualified),
it's not hard to make a bot that pushes something to a repo every
day. Some people have even taken to writing letters or patterns in
their GitHub squares.

The squares encourage some questionable habits. One should, of course,
commit frequently to save one's changes. However there is frequent and
inanely frequent. I try to not commit code that is blatantly broken,
in case someone tries to revert or bisect a bug. Spamming a bunch of
commits may grant you a nice deep green square, but it makes it all
the more painful to figure out which changes happened when.

Of course there's also many tasks that one can do to be productive
that do not involve commiting code to GitHub. I'd love for reading a
book on machine learning to fill in the square, but alas it does not.

With that said, I still find GitHub squares a fascinating feature. I
can look back on my history and see various events or trends. I can
see when I started working and therefore committing less to my own
projects. I can see when I had a slow week or was traveling. I can see
bursts, whether for hackathons or other reasons.

I can also get some insight into other people's development. I know
who codes nonstop. I know who pushes to master constantly. I don't
know who doesn't code however. There are plenty of amazing developers
who don't have a field of green, either because they don't push their
code to GitHub or they do their work in a private repository and
choose not to show it.

And finally there's a minor productive boon to squares. I do care when
my squares go empty. I've tried recently to increase my green
count. Sure, sometimes the green is something completely irrelevant
like a README change or a blog post. But other times it's real
meaningful work that I wouldn't have done, were I not trying to fill a
stupid square.

There's a competitive aspect here. I care about how I look compared to
my friends. I care if they're pushing out lots of work and I'm not. I
don't want to come off as lazy.

One of the most inspirational posts that I've read was by Bob Nystrom
about writing his book, Game Programming Patterns[^1]

> I started getting up early in the morning before the kids so I could have some quiet time. Ostensibly, I would write then, and sometimes I did. But, more often than not, I’d just drink coffee and stare at the Internet while I slowly roused myself. Then, after I’d worked, made dinner, gotten the kids cleaned up and ready for bed, read them books, gotten them milk, cleaned up a bit and gotten them in bed, then, I’d still have to write.

> Yet, strangely, I would. I’ve never demonstrated an ounce of self-discipline in my entire life, but for some reason I just didn’t want to skip a day. Even though I was often dog tired, I would scrape up just enough energy to put in a barely half an hour of writing. I would look at the pathetic word count and feel bad before going to sleep exhausted.

> But here’s the thing. Even tiny amounts of effort add up if you keep at it. I’d have entire weeks where I felt like I didn’t have a single good writing day, but after two of those weeks… that’s a whole chapter.

>Because I was writing every day, it made it easier to build it into the routine. I never had a chance to get used to not writing. It was just this thing that must be in the schedule, like brushing my teeth or eating, even when I traveled or did outings with the kids. I gave a talk at Strange Loop last year, and every evening after dinner, while others were out at bars socializing, I went back to my hotel room so I could write. When I flew to another office for all-day meetings, I wrote on the plane.

[^1]: He also wrote a wonderful book, Crafting Interpreters, that you
    should read.

The squares encourage that same idea. Just fill in the square. Just
push one commit. Just write one word.

Again, I'm not one for productivity tools or frankly any sort of
productivity hacking. My view is that productivity is a function of
mental health, diet, sleep, exercise, and whether you actually want to
do the task. But if I were to give a piece of productivity advice for
programmers:

Just fill in the damn square.
