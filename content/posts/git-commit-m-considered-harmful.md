---
title: "`git commit -m` Considered Harmful"
date: 2021-03-06T18:35:03-05:00
draft: false
---

Stop using `-m` in git. It encourages short, lazy commit messages. It
makes it harder to figure out when changes occur. And yes, at some
point you will need to track down when someone refactored the parser
and introduced a buffer overflow bug.

If I had to guess why people use `-m`, it's because `git commit` by
default dumps you into vim. If you don't use vim, this is a very
unpleasant experience (cue the five million memes on quitting
vim). There's an easy solution though: change your editor! It's not
that hard:

```
git config --global core.editor emacs
```

Just substitute `emacs` for whichever editor. Personally I use `nano`
because it boots fast and has just enough emacs keybindings to be
tolerable.

Great! Now you can write commits in the civilized environment of an
editor. You can include bulleted lists, long asides, even a snippet of
code if you please.

Yes, it's sliiightly more time than `git commit -m "minor fixes and
stuff"`. But your teammates, your future self, your kids and grandkids
and great grandkids will thank you for having legible, helpful commit
messages.

Plus it can be kinda nice!  Maybe I'm weird, but there's nothing
better for me than committing a nice chunk of code and enumerating
what exactly I did. It's like a victory lap. I get to show off what I
did and how.
