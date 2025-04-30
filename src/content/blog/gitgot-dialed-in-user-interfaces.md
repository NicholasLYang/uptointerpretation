---
title: "gitgot: Dialed in User Interfaces"
pubDate: "Aug 29, 2022"
draft: false
---

I've been working on an alternate GitHub front end called
[gitgot](https://gitgot.io). Some people may be wondering why this is
a worthwhile project. Here's an explanation.

People use GitHub a lot. I know I do. They use it for their work, for
their open source projects, for school, for anything and
everything. As a result, the GitHub user interface has to accomodate
all of these different users. It has to work for the developer who is
looking at a library's repository in order to check whether it's worth
using, and it has to work for the maintainer of this library to handle
all the issues and pull requests. The result is a UI that doesn't work
well for anybody. It's dense, but not efficient. It's a UI that
requires a lot of clicking and extra typing.

In short, it's not *dialed in*. For most developers that's okay. A
student doesn't need a perfectly dialed in UI[^1]. But for
professional developers, indeed professional open source developers,
they're dealing with a firehose of issues and pull requests and
discussion posts. Any maintainer of a popular open source project can
tell you it's a constant struggle to handle this onslaught. When
you're getting thousands of issues, any amount of time shaved off is a
huge win. Here's what I'm doing to shave off time:

[^1]: That may be true, but I do think there's opportunities to make a
    more student friendly GitHub UI.


1. Keyboard first

This is not a new concept. We already interact with our editors and
terminals primarily via key commands. And many web apps have started
to become more keyboard friendly. Key commands are great! They build
muscle memory in a way that using a mouse does not. They're repeatable
and aren't limited to your visual processing speed. Indeed, GitHub
does allow some key commands, including the Command Palette, a way to
bring up a search interface for various commands. However, first, this
is not very discoverable, and second, the key commands don't encompass
everything. There are still too many situations where I have to
begrudgingly reach for my mouse.

2. Both Shortcuts & Command Search

Within keyboards there should be two ways of navigating. The first is
via a shortcut, i.e. a sequence of keys that you can press to trigger
an action. The second is via command search, i.e. searching for
commands via a Command Palette. This allows for extremely fast
navigation with shortcuts, but also discoverability using command
search. In an ideal world, the command search should also teach users
the shortcuts, thereby making them more efficient.

3. Simple, Specialized UI

GitHub has a UI that does everything a user may want to do. That may
seem like a vacuous statement, but it's actually not. Because there
are so many different types of users, this results in a cluttered UI
that does not favor anybody. Instead, we should make multiple UIs that
each focus on a specific style of usage. For instance, there should be
a UI for triaging issues. Or a really good UI for searching through
issues for getting help. Or a UI for efficiently creating issues and
assigning them.

The UI of my front end also explicitly omits certain features. I'm not
going to include a UI for changing your profile picture, or for
looking at what your friends are doing on GitHub. I want just the
features that people use every day all day.

4. URL Stores Unsaved State

The URL should store as much unsaved state as possible. In other
words, if the state is not saved to the server, it should be saved in
the URL. I'm a huge fan of this approach because it means you can
share the state of the page simply by providing a URL. This is a huge
win for code playgrounds and I could see a lot of possibilities for
other web apps.

5. Shorten Common Paths

Repositories have a lot of common patterns. For instance, there's
often a guide to contributing to a repository in a CONTRIBUTING.md
file. I don't see why this can't be a keyboard shortcut away instead
of having to look through the list of files for
CONTRIBUTING.md. Likewise, people often want to search through issues
both open and closed to find information on a bug. Currently you have
to go to issues, then delete the `is:open` predicate and
search. That's way too slow. It should be trivial to enter a
repository and enter "bug searching mode".

## Why Not A Terminal?

Some people may ask why not use a terminal? And yeah, that is a
solution for some aspects. But I do think some amount of user
interface is useful. It's hard to do simple stuff like grids in a
terminal. It's also hard to share terminals with other people. You
can't just send it to a coworker via a URL.

Besides, let's not underrate browsers here. They offer built in page
history, tabs, and an excellent ecosystem of UI libraries. And they're
portable!

If we must resort to the tired and true "X for Y" description, I'd say
that gitgot is "emacs/vim for GitHub". And indeed, I have modeled the
keybindings off of vim to some degree. Others may say Superhuman which
is also fair.

As of right now gitgot is very very alpha. I'm still working out the
key commands and user flow, not to mention the UI. But I'm already
pretty happy with it. There's nothing more fun that using your
keyboard. It just feels...right.

I'd love to know your thoughts on gitgot. Feel free to try it out at
[gitgot.io](https://gitgot.io) and email me at nick@nicholasyang.com.
