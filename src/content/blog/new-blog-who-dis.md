---
title: "New Blog, Who Dis"
pubDate: "Apr 30, 2025"
draft: false
---

You may have noticed that the blog looks different. I was writing a
recent post, when I made the stupid mistake of trying to add images. I
tried to use Hugo's image optimization features, which didn't
work. Even more stupidly, I decided to upgrade Hugo using Homebrew in
an attempt to fix the image issues. When I upgraded, I discovered that
the theme I was using was abandoned and no longer worked with the new
versions of Hugo. I couldn't easily downgrade because apparently
Homebrew doesn't let you do that anymore. I could have probably
downloaded an old version of Hugo and trundled along, or I could have
recreated the blog in a new template, but tbh I was getting a little
too frustrated with Hugo.

As an aside, what's with static site generators and being brittle
abandonware? Just going into a static site generator's docs feels like
stepping into a old, dingy shop. Okay, maybe that's an exaggeration
and not fair to the maintainers. And I did get a solid few years out
of Hugo before having to switch. Perhaps it's the weird overlap of
relying on third party templates maintained by various random
people---it's not Hugo's fault my template is abandoned after
all---and relying on templating plus configuration languages, the
unholy duo of developer frustration.

In any case, I decided to switch to Astro. It seems nice so far. It's
mostly plain JavaScript, CSS and HTML, which I understand and can
write decently. But only time will tell if it lasts. I wouldn't be
surprised if in 5 years I come back and Astro is looking like
Hugo. Perhaps next time I'll just try plain HTML and CSS. At least
then it won't break on me.

I'll likely be tweaking the styles as time goes on. I basically just
took the vanilla Astro template, ripped out the styles and added a
light layer of Tailwind. Apologies if it's a little buggy for the time
being.
