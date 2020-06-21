---
title: "Coupled Is Not Bad"
date: 2018-03-18T16:25:20-04:00
draft: true
---

There's an often repeated phrase which has been bothering me. Namely,
"xyz technology is great because it decouples foo and bar". This
reasoning is everywhere, but the most common usage I see is with
CSS. I find CSS to be a really awful tool. It's one of those
technologies that made sense when internet connections were measured
in bytes per second, but now it really doesn't. Web developers like it
because they grew up using it, and now they can't imagine the web
without it. But really, CSS's awfulness becomes pretty apparent once
you try to explain it to non technical people. I'll probably write a
separate post about this, but the concept of global, mutable state
where *order* matters...is just not optimal. CSS libraries like
Bootstrap basically dump thousands of selectors into your page,
polluting the global namespace. Not to mention the increasingly hazy
border between structure and style (is alignment structuring? How
about visibility? Translations?). Not great.

Anyways, let's get back to coupling. Basically, I hear the same
argument for CSS: "It decouples the styling and the structure of the
program." But wait, why do we want these two things to be decoupled?
Because, when you decouple two things, you now need a way for the two
to communicate. Sometimes, this is a good thing. It's often important
to decouple programs using objects that communicate through strictly
defined interfaces. But sometimes it's not a good idea.

Let's take a common problem: animations. Let's say you want to animate
a drawer so that it "slides" off the screen. After the drawer is done
sliding, it should be removed from the DOM, cause well, why bother
rendering an object that doesn't exist? (Imagine having to animate a
hundred objects that could go off screen) If you do this with CSS
animations, you basically have to have two different classes, one of
which is the drawer visible, and the other is the drawer off
screen. Then, all you need to do is have some JS change the drawer's
class, then wait for the drawer to animate closed, then stop rendering
it. Not trivial, but not hard.

Only issue?  How do we know when to stop rendering the drawer?  Well,
CSS can't really communicate with JS, so basically the only solution
is to have JS wait for the same amount of time, then stop
rendering. But wait! What happens if our finicky users decide to hide
a drawer, then show a drawer? The drawer slides back, then JS stops
rendering it, but then, oops, the drawer is sliding out again. And
keep in mind, JS timeouts are not 100% precise. Because the browser is
(generally) single threaded, timeouts can fire later than expected. So
what we have is a good ol' race condition. I.e. will the
derender/rerender happen before the CSS translate happens?

Now granted, there are some proposals underway to solve some of these
issues. CSS variables appear to allow a way for JS and CSS to
communicate. But they don't seem to solve the fundamental problem of
the fact that the way your document looks and the document itself are
fundamentally coupled. And *that's not bad*.

Furthermore, when you take a look at all the ways of extending or
improving CSS, such as SASS, what they're basically doing is adding
programmability to styles. For instance, SASS uses essentially a
domain specific language (DSL) that looks like bastardized Ruby. Which
begs the question, why bother with a DSL when you can simply use the
existing language for the web, i.e. JavaScript?

Which brings us to CSS in JS. Most people's reaction to CSS in JS is
horror, combined with disgust. Why would we take JavaScript (ick!),
and combine it with CSS? Well for the same reason you take HTML and
put it into JavaScript (React). Styling and structure are no longer
fundamentally static. They aren't something you can render on the
server and only tweak in the front end. They're dynamic, responsive,
and inherently complex, and therefore must be programmable.

One of my favorite examples of programmable styles is React-JSS. Being
able to change styles with props is one of those "duh" moments. It's
just so amazing and so obvious that you wonder how people wrote
dynamic styles before this.

But really, CSS in JS is only the first step. I still have to remember
the precise details of how `border-styles` works, or how to get the
damn div to fit cleanly to the text, or how to get a grid to work. Now
sure, all of these things are obvious once you've done them a few
times. But why were they so hard to do in the first place?
