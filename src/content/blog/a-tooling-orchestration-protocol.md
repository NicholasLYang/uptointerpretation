---
title: "A Tooling Orchestration Protocol"
pubDate: "Oct 22, 2023"
draft: false
---

I've written about what I call [Tooling for
Tooling](https://uptointerpretation.com/posts/tooling-for-tooling/),
basically meta-tools that help people build better developer tools
faster and easier. Arguably the most famous and ubiquitous meta-tool
is not actually a tool---it's the Language Server Protocol (LSP). By
defining a protocol for language implementations to communicate with
editors, the LSP lets language authors write a single LSP server and
let their users bring any editor that implements an LSP
client. Similarly, editor authors only need to write a single LSP
client and let their users bring any language server of their choice.

This is really fantastic! Previously if you used a niche editor or
language, it was highly unlikely that you'd get good language
integrations. At best you'd get syntax highlighting and some primitive
tagging system. Now it's very common to expect solid go-to definition,
semantic syntax highlighting, and other niceities previously
restricted to IDEs on mainstream languages.

However, the LSP does have its limits. It's fundamentally a tool for
editors, so the abstraction is a persistent server that needs to be
started and queried. This makes sense for an editor, but it doesn't
scale well into other domains such as CLIs or web services. This also
doesn't necessarily play well with how tools are implemented under the
hood. A lot of tools are originally conceived and implemented as
one-shot systems, i.e. you run `eslint` or `clang` and wait until the
process ends. Usually this process takes a few seconds to run. Whereas
a language server is queried repeatedly as the code changes, and is
expected to return an answer within a few hundred milliseconds (or
risk providing a poor user experience). This usually necessitates some
amount of caching or incremental computation, which is often awkwardly
tacked on post-facto.

There are attempts to extend the protocol with persistence
([LSIF](https://lsif.dev/) and
[SCIP](https://about.sourcegraph.com/blog/announcing-scip)). I'm
pretty excited about these as a way to offer better static tooling,
i.e. a supercharged GitHub code navigation. But persistence is only
part of the story.

What I would really love is a way to extend this protocol beyond
editors. Specifically, I'd love for a protocol that handles the other
ways in which we invoke tools. There are a few ways that I've
encountered: as a watcher, as a CI process, and as part of a build
graph. Each of these ways are essentially front-ends to how we use a
tool. And yet each of them are either implemented in an ad-hoc,
limited fashion, or are completely separate to the tool and only
interact with it at a very high level.

## Watcher

Let's start with the watcher. In a lot of tools we use watch mode,
basically a mode where the tool watches a set of files and re-runs
when they change. This could be a dev server, a linter, a compiler,
really anything that processes your code.

This may seem like a relatively simple process. But it's really
not. For one, consistent, efficient file watching is surprisingly
tricky. At Vercel, we did a lot of work to get our file watching code
to behave the same across operating systems. Turns out there's a lot
of subtle differences in how OS's implement file system events. It's
also very easy to do a simple action, like move a directory or delete
`node_modules` and end up with a cascade of events that destroy your
app's performance, or worse, cause an out-of-memory error.

Second, when we talk about file watching, we usually mean something a
little more sophisticated than just watching a list of files and
returning changed or unchanged. Usually you want to have globbing to
declare the set of files that are being watched, as well as which
files to exclude. You probably want to filter using the `.gitignore`
files, unless you want users to accidentally get terrible performance
out of the box because you ended up watching `node_modules` and
`dist`. And you likely want some way of surfacing which files have
changed, so the tool can make an intelligent decision about what to
do. Maybe it can only rebuild those files. Or maybe it can rebuild a
single module instead of multiple modules.

Beyond file watching, a watcher process has a few other
requirements. Maybe you want to run it as a daemon. That would require
interprocess communication (IPC), lifecycle handling, and so on. Maybe
you want a way to alert the user. Back in 2016-2017, I had a webpack
setup that would send me an OS alert when my build finished. That says
more about the performance of JavaScript tooling than anything else,
but I'd love similar functionality for my similarly slow Rust builds.

## CI

With CI, there are similar but different requirements. Instead of file
watching you probably want some degree of change detection. What files
have changed in this pull request or commit? That may influence which
tools are even being run. Which in turn may influence what your build
environment may be. Do you really want to install Node.js if you don't
need to run ESlint?

Likewise, you want some way of surfacing errors. For most CI systems,
they just show you stdout and stderr. I never understood how that
could be a good user experience. Errors could be so much richer and
dynamic. You could provide a link to the relevant code in GitHub or
wherever it's stored. You could have an automatic fix button---or
heck, if the tool indicates that all of the errors are fixable, it
could enter a self-healing loop. You could keep a database of errors
and how they were solved previously. You could link and share them to
your coworkers. You could link to the [error code
index](https://doc.rust-lang.org/error_codes/error-index.html) for the
language. Errors are not just text, they're deeply hyperlinked structures.

## Build Graphs

This is where I have the most professional knowledge, since I work on
a build graph tool, Turborepo. If you're not familiar, Turborepo is a
build tool for JavaScript. It allows you to speed up your builds by
leveraging caching and parallelism. In particular, it works very well
for monorepos, where you may have a lot of packages that depend on
each other and need to be run in a maximally efficient manner.

With Turborepo, we're executing different tools like ESLint or Next in
the build graph. In some cases, we'd like the ability to integrate
with a tool at a deeper level. For instance, with a testing library,
it would be nice to surface UI prompts to the user for interaction. Or
maybe we want errors to appear in our [Vercel
Spaces](https://vercel.com/docs/workflow-collaboration/vercel-spaces#runs)
UI. Or going even deeper, maybe we want the ability for a tool to
provide data that another tool takes in. All of this is theoretically
feasible with our current methodology of forwarding stdin and stdout,
but it could be a lot nicer with a proper protocol.

## Solution

In each of these cases, we need a way to interact with a tool that is
more sophisticated than stdin, stdout, stderr. Basically, we need a
protocol around tooling inputs and outputs.

This protocol would help tools receive inputs in smarter ways by
letting them subscribe to specific changes, watching files, and even
integrating with version control. Once the tool has received these
inputs, the protocol would also help with outputs, say by event
sourcing errors in a consistent schema, so they could be consumed by a
web UI, by a terminal, and by an error database.

In a really ambitious case, all of these protocol messages could be
backed by a content addressed store, which could allow you to replay a
tooling execution, either remotely or locally.

I'd love to build this. If you're interested too, send me an email at
nick@nicholasyang.com.
