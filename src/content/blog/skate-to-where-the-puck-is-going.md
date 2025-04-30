---
title: "Skate to Where the Puck Is Going"
pubDate: "Dec 12, 2022"
draft: true
---

JavaScript developer tools is a funny area. It started out as all of
these open source projects and over ten years later, well it's still a
bunch of open source projects. But the tenor has changed
significantly. The competition is quite intense these days. You have
runtimes debating the latest benchmarks on Twitter. You have bundlers
upon bundlers upon bundlers. I'm not complaining here; competition
breeds excellence. But there's a limit to competition. Competition
breeds excellence out of an existing market. It does not necessarily
create new markets.

I'll explain with an example. Webpack is the main bundler in town. It
worked pretty well, but it had some flaws, namely slowness. Then comes
esbuild. It's faster than webpack by a lot. Great! But it doesn't have
all of the features of webpack. Then comes Vite. It packages esbuild
and rollup to get around this lack of features. Even better! All of
these seems like great progress and it is.

However, at the end of the day, we're still talking about
bundlers. We're still talking about a piece of software that takes
your code, figures out the import graph, and builds it into some
output files. Sure there are benefits to be gained from each
iteration. But it's not like webpack suddenly became useless. And it's
not like everybody runs into webpack's issues. And it's not like
esbuild or vite fulfills all of the needs of webpack users. Therefore,
any new bundler is not just competing against the latest hotness. It's
also competing against webpack and parcel and all the other forgotten
toys.

What should you do instead? Cue the title: Skate to where the puck is
going. Wayne Gretzky famously talked about how he learned to do this
at a young age. Kids would skate to where they could visually see the
puck, instead of extrapolating where the puck would be in 10
seconds. Instead of following precedence, seek unprecedence. Build a
tool that doesn't exist already. Build something that allows people to
accomplish something they couldn't before.

This isn't to say that you should scrap any idea that's been done
before. As long as the idea isn't a wildly popular open source
project, it'll still have a lot of potential.

So let's say you're an ambitious developer who wants to create the
next hot JS developer tool. Should you build a bundler? Well,
maybe. I'm not going to rule it out.
