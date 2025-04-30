---
title: "Mirror Shining"
pubDate: "Dec 1, 2018"
draft: false
---

One of my earliest programming projects was for my Intro to Computer
Science class in high school. The assignment was to create a game in
NetLogo. NetLogo, for those who are not familiar, is a program where
users can control "turtles" and "patches", basically agents and the
squares they move on. Users can move the turtles, which can in turn
paint the patches they traverse.

I decided to make a simple platformer with a twist---the map was to be
encased in darkness except for a bubble of light. I started this
project early, having been bitten by a previous project that I may
have started the day before it was due. I remember getting the core
functionality down, such as the basic jumping physics (literally just
up and to the right/left, I didn't want to program actual physics);
the bubble of light that slowly expanded; level loading, etc. However,
after I finished the core functionality, I didn't stop.  I implemented
shooting projectiles and basic enemies. I added trapdoors that dropped
the player into deadly lava when stepped upon. I wanted users to be
amazed at all the functionality this game had. None of these features
were essential or required. I simply wanted to build them anyways.

I didn't know it then, but what I was doing was not
unusual. Programmers by nature like to improve their code. Whether
that's adding features or refactoring or adding scalability, we love
to constantly improve our projects. I like to call this habit "mirror
shining". Much as some people love to obsess about giving their shoes
a mirror shine, programmers love to obsess about their code.

Now, this is the usual section in the blog post structure where I'm
supposed to make a point for how mirror shining is bad and how we
should all stop doing it. But that's not true. Mirror shining is
perfectly fine. Compared to a similar term, "bikeshedding", mirror
shining isn't wasting time on useless details. Instead, it's dealing
with fundamental, often crucial points, such as improving user
experience or refactoring messy code, or scalability. It's about going
beyond what is merely required and building a product that is truly
admirable.

However, it's important to recognize when mirror shining is advisable
and when it is not. For instance, a web application that will be
continually used and maintained is a perfect case for mirror
shining. All the little details and finishing touches will play a
factor in maintenance, usability and overall customer
satisfaction. But if a company gives a 2 day take home project for a
job application? Maybe a multithreaded async S C A L A B L E webserver
isn't needed. Maybe a custom responsive UI with extra fancy animations
is a little much.

Having said that, would I still have put as much work into my project?
Certainly. After all, God is in the details.
