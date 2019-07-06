---
title: "In Defense of Packages"
date: 2018-07-28T02:34:02-04:00
draft: true
---

Packages have been getting a bad rap recently. It seems like every
week or so there's a new Medium article talking about how JavaScript
programming is plumbing, how NPM is awful, how left-pad is indicative
of NPM's dependency hell, blah blah blah. You'd think the world is
ending with the rate that people bemoan packages.

And to an extent they are correct. Packages have a lot of issues. The
fact that one package could take down a fair portion of other
libraries is kind of an issue. And yes, node_modules can get very
bloated. 

But here's the key question: **What do you want instead?** Do you want
to go back to importing libraries into the global namespace with
script tags? Do you want to have to clone repositories and manually
link them? There's been a lot of bitching and moaning about packages
without recognizing the precise reasons why packages are so damn
popular: They work.

Heck, they even work really well. Remember the ancient days of yonder
where you were trying to do something fancy like an image that has
text centered that animates onto the page smoothly when you scroll
into a certain section, and you had to go on StackOverflow or whatever
and find some random person's hodge podge of JS with CSS, then if you
were lazy you'd copy it, and if you weren't you'd adapt it. You don't?
Well listen anyways. I used to do that all the damn time. I'd either
have to copy this shitty, kinda weird code, pray that a catch-all
library like jQuery has something that can handle this, or find
another large damn library with the function.
