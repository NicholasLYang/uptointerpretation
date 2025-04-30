---
title: "Notes on Version Control"
date: 2024-02-18T11:56:57-05:00
draft: false
---

After reading a [post by
Graphite](https://graphite.dev/blog/why-ban-merge-commits) and some
[recent Hacker News
discussion](https://news.ycombinator.com/item?id=38800454), I decided
to jot down some notes on version control, specifically git.

## Saves vs Commits

Commits are subtly tricky in that they are really two different
concepts in one. In some cases, they function as a mechanism to save
your code. It's 5pm and you want to go home, so you commit your
code. Or as the meme goes, in case of fire, `git commit`, `git push`,
leave building. In those cases, it doesn't matter what the state of
your code really is. The commit is meant to prevent you from losing
the code. And it doesn't matter how often you commit. Any gamer can
tell you that saving is good.

In other cases, commits function as atomic[^1] units of code changes. This
means that before a commit, the code should work, tests should pass,
and after the commit, the code should still work, tests should still
pass. This shows up in the adage "don't break `main`". This also
generally implies that commits should convey a cohesive narrative as
to their contents. It shouldn't be half-finished code. It shouldn't
have two completely different sets of changes in the same commit.

[^1]: Note that I am *not* using atomic in [this sense](https://en.wikipedia.org/wiki/Atomic_commit#Revision_control). Yes, I know that's a little confusing, apologies.

It's important to note that these concepts are not mutually
exclusive. In fact, many git workflows include both concepts in
different situations. For instance, a common workflow is to have
feature branches that get squashed and merged into main. In that case,
commits inside a branch are generally considered saves and are
therefore don't need to be atomic changes. However, the commits onto
main, i.e. the squashed merge commit, are considered atomic code
change units.

On the flip side, there are some people who apply one of these
concepts to commits unconditionally. For instance, someone who may
insist on rebasing for everything likely sees their exact chain of
commits as crucial to persist. Each commit indicates a specific,
atomic change, and therefore rebasing allows them to update their
branch while still keeping this chain. And there is some merit to this
viewpoint. Even in the previous workflow, where pull requests are
squash merged into main, having a clean chain of atomic commits means
that reviewers can review commit by commit instead of having to tackle
an entire pull request all at once. Plus it means you can revert at a
more granular level than an entire pull request.

However, this can also create a lot of work for the author to fix up
their chain of commits to be perfect code change units that all pass
CI and don't contain irrelevant changes. Git is generally good at
combining commits via squashing, but it's not so good at splitting up
commits. And it can be especially annoying to maintain this chain by
rebasing onto main and fixing the same issues again and again and
again[^2]. Not to mention sometimes there genuinely is an emergency
and you just gotta save the damn code.

[^2]: Yes, there are tools that help solve these issues, but **then you gotta learn these tools**.

I suspect a lot of the confusion around rebasing and merging boils
down to confusing the concepts. Like if Sasha considers commits to be
saves in feature branches and atomic code change units in `main`, then
it may not make sense to them why one should rebase a feature branch
onto main. It's not like these commits are gonna stick around. Or if
Cameron considers all commits to be atomic code changes, they may be
horrified at merging `main` into a branch. After all, that'll create
an ugly merge commit in the history!

The easier solution to this confusion would be to split commits into
two different concepts. There should be a concept of saves and then a
different concept of transactions. A save is just saving the code to
the history, no guarantees, while a transaction comes with the
promises of atomic code changes. Ideally these concepts would be built
into the version control system, but even as informal definitions,
they would be helpful.

You could in theory do this by using tags so that commits become saves
and tags become transactions. But it'd be a little clunky with git.

## Semantic Diffing

I've thought about encoding a semantic meaning into a commit that can
then be used to diff code instead of the literal changes. For
instance, you could encode that a commit renames a variable from `foo`
to `bar`. If you were to merge that commit into a different branch, it
could merge cleanly even if there were some merge conflicts, because a
language server could apply the rename to the branch instead of the
literal changes.

Of course that would require third party tools like language servers,
but it'd be no different than shelling out to a different diff or
merge algorithm.

It'd also be nice for reviews, because you could filter out changes
that are large diffs but semantically meaningless.

## Terminology

Arguably one of the biggest issues with git is
terminology. Terminology has a massive effect on user experience. It's
fundamentally how the user conceives of your program and its mental
model. Git has a lot of bad terminology. Julia Evans goes over some of
it [here](https://jvns.ca/blog/2023/11/01/confusing-git-terminology/).

I seriously suspect that if you were to take `git`, fix all of the
poor terminology, and put a slightly nicer UI in front, it would be
way more usable. Take `git reflog`. It's a horrible name. At least put
a hyphen so people stop reading it as re-flog and think it's some
weird torture command. Or call it `git history` because, well, it is
the damn history. And at the very very least, don't make the
description of the command:

>   reflog               Manage reflog information

C'mon git, you can do better than that.

## Pull Requests & Code Reviews

Maybe it's just me, but code reviews are hard. They require so much
mental energy and time. You need to pull in the context, read the
code, think it through, and give feedback. There's a few reasons I
find it challenging. Let's break it down in order:

### Context

For me, context generally means the existing code that the changes are
being applied on. Acquiring context requires reading the existing
code, maybe going to definitions to understand a function or a type
better, maybe reading some comments.

This is really hard to do when reviewing on GitHub. GitHub recently
added some rudimentary go-to definition but it doesn't work
consistently and it's still quite slow. Naturally, the obvious
response is to use an IDE to review a PR. And that does work decently,
but I often forget to use that feature since I use the GitHub website
to do literally everything else. I'd love a GitHub desktop app that I
could use for everything with IDE integration.

GitHub is pushing Codespaces as the solution here, but personally I
don't love using an IDE in the browser. I want something that does
less, ideally something tailored to reading code.

It'd also be really neat for GitHub to let you see recently merged
relevant pull requests in the sidebar. That way you could get a feel
for recent changes to the code. Or even pull in context via Notion or
Linear.

### Reading The Code & Thinking It Through

When it comes to reading the actual pull request, often times the code
is not in the right order. I'll read some code, come up with some
feedback, type out a comment, only to realize it's immediately
addressed soon after. The solution here is to read the code all at
once then comment, but that requires keeping the issues in working
memory. For large pull requests, my working memory isn't big enough. I
could write notes as I go along, and really that should be built into
GitHub. Comments are slightly annoying in that you have to submit
them. Ideally it should be auto-saved notepad next to the PR.

Some people (not me) are really good at writing pull requests and
write a nice summary for me to read, then organize their commits into
atomic code changes so I can review commit by commit. Those people are
awesome! The atomic commits allows me to fit a pull request into
working memory since I can review it commit by commit, and a good
summary provides an overarching narrative for the PR. Narrative is
important because really what I'm doing in a review is building a
mental model and checking the code against it. By providing a
narrative up front, it makes it a lot easier to build my mental model.

The issue is that setting up a PR like this can be time consuming. At
least for me, I don't write code such that I can automatically produce
a chain of commits that naturally create a narrative. Often times I
try out something, then undo it in another commit, then fix a bug in
my code, etc. Yes, there are nice tools like `git rebase -i` that let
you squash commits together, but what if there's a commit that you
want to split apart? Or move part of one commit to another commit? As
a whole git doesn't offer enough facilities for editing commit
histories. GitButler claims to solve this for you. I'll have to give
it a try.

Another option is to keep PRs small and make them stackable. Graphite
is a tool that claims to make stackable PRs easier. That's a pretty
enticing option, although keeping PRs small can be difficult,
especially if you need to add testing and avoid checking in incomplete
code. Also, stackable PRs quickly get frustrating when you have slow
CI, since each PR needs to clear CI, so a single test failure can
waste several hours of CI time. I know the answer is to just make CI
faster, but that's not always feasible.

A quick, simple version of this is to leave a reviewer's guide that
consists of numbered comments listing the different pieces of a pull
request. That's not bad, but it's definitely a workaround.

### Giving Feedback

The feedback stage isn't that bad by all means. Mostly it's just
comments. Recently GitHub introduced a suggestion feature, where you
can suggest changes that can be applied with a click of a button. I
like it as an improvement over having to explain in words or with some
sample code, but it's still not a great feature. For one, you end up
having to write code in a text box with no syntax highlighting or
testing, so it's far too easy to suggest broken code. And second, it
quickly doesn't scale to changes that occur in multiple places. I'd
love the ability to apply more complicated refactors or use a better
editor (this is where Codespaces would work well).

I do find it tough sometimes to know when to submit a review versus
wait a little longer and provide more feedback. It'd be nice to make
that barrier a little less high and just have comments automatically
show up. Or even a chat/pair programming feature. Reviews feel very
"heavy" in that it's not very fun to have a discussion in a pull
request.

One nice pattern that my work uses is the `fixship` label. It
indicates that there are fixes that need to be made, but I trust the
pull request author to make them, then merge the PR. It allows reviews
to give feedback, but not block the author. However it does require
some degree of trust and can't be abused by the author to just ignore
the feedback.

Simiarly, it'd be nice to have the ability to accept a PR but also
open new tickets/issues to follow up on feedback. Like "this is good,
but we really should refactor this interface". Too often these sort of
feedback ends up blocking the PR or getting completely ignored. This
would be especially nice when combined with a tool that lets you split
a pull request in half. Often times I can get a pull request merged if
I pare down the scope, but that then requires some manual clean up
on my part.

### Psychology

Pull requests are a tricky thing to get right. I think people
underestimate how much a poor pull request experience messes with
morale. I've worked at places where pull requests just dragged on and
on, where it felt like my reviewer was attempting to put me down and
teach me a lesson instead of collaborating with me. Some of that can
be solved with tooling, but some of that is the team culture. It's
always worth weighing whether rejecting someone's code is worth the
loss of morale. I'd much rather have an engaged, encouraged teammate
at the cost of a few nits sneaking it.

At my work we have a phrase, ITG, Iterate Towards Greatness. It can be
a little corny citing it, but it is genuinely helpful to remember that
hey, it's okay that we don't get it right on our first try. [And
sometimes we will
fail](https://uptointerpretation.com/posts/things-fall-apart/).

## Facilitation

Of course, part of this responsibility lies in the author. If the
author writes a massive pull request with a lot of flaws, it's
understandable that it won't get accepted. But perhaps it's worth
finding tools that facilitate mergable PRs.

The classic example here is code formatting. It's funny, code
formatting is one of those things that has materially changed so many
facets of software development, but in a very quiet way. My first
couple jobs coding, you'd get feedback like "can you indent this with
4 spaces" or "this needs to break to fit in 80 columns". This was good
feedback, but pretty exhausting for both parties. The author would
have to go back and manually fix spacing or line breaks, while the
reviewer would have to worry about reviewing the formatting along with
the semantics.

Today, you just ask the person to run the formatter. Yeah, sure,
sometimes the formatter doesn't do a great job, but 99.9% of the time
it does a good enough job. Anybody who argues past that point is
either a formatter author or has too much time on their hands.

Are there other tools that can do the same? GitHub Suggestions
facilitate suggesting and fixing nits, because you no longer have to
open an IDE and push to GitHub to fix two line issue. Maybe an AI code
reviewer who can anticipate your coworker's nits?

## Live Version Control

There's been a lot of attempts at live version control/live
collaboration. It feels like one of those classic tar pit programmer
problems that suck in successive generations of developers. And for
some situations, I do see the appeal. Google Docs or other similar
sites work wonderfully with live collaboration.

I'm a little less sold on live collaboration as a replacement for
git. For one, I don't love the feeling of having someone watch me
code. It feels rather intrusive. Not to mention, the first iteration
of the code is rarely the version that I'll end up submitting as a
pull request. It's not particularly useful for someone to read the
first n - 1 iterations. This is the same reason live coding exercises
are not good indicators for job candidates. You're getting a very
rough sketch that was done by someone under duress.

That said, pure live collaboration is perhaps a little too much of a
straw man. I imagine there could be a lot of middle ground. Perhaps
your code gets snapshotted, but not shared unless you allow it to
be. Or perhaps it's only shared once it compiles and passes
tests. Maybe you can write a bunch of code, then add the commit points
after the fact by watching your change history and annotating it.

I tried making a code snapshot tool called
[eis](https://github.com/nicholaslyang/eis) that uses git internals to
snapshot. It effectively creates a new git index that runs parallel to
the existing one. I never got around to getting it fully working, but
I could see it being a nice way to make sure you never lose some code,
or even do some analysis of your coding process.

Or maybe I'm just wrong, and live coding is the future. People really
do love pairing on code. It could be that in a few years we're all
comfortable working in a live environment.
