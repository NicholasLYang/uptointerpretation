---
title: "Fixing Nits Quickly"
date: 2022-07-31T16:53:19-04:00
draft: true
---

How many times have you gotten the following comment on your PR?

> nit: checkProgram -> typeCheckProgram

Putting aside the validity of the rename, it's an annoying change. You
have to go to your computer, open the IDE, rename the variable,
commit, and push. If you're already at work, it's not a huge deal, but
sometimes I get a review back when I'm afk[^1]. Something that should
be a 5 second change is now a pain because I can't open my IDE.

[^1]: Who still uses afk? It's been a sec since I've heard it.

This is also awkward from the reviewer's end. Often times the new name
**is** a lot better, but it feels rather, well, nitpicky to hold up a
PR for a rename.

What if this could be a single button click on a website? We can do
this with the power of language servers!

## Language Servers

A language server is a smart analyzer for a programming language that
can be plugged into different editors. The idea is to separate the
functionality we expect from an IDE such as smart highlighting,
autocomplete, refactoring, from the specific IDE. Often times these
services are implemented by the compiler itself. That means that many
of the compilers you use are secretly language servers too!

Language servers speak a common protocol, the Language Server Protocol
or LSP. It's a pretty simple protocol, basically just JSON RPC with
some special types. We could implement it ourselves, but fortunately,
VSCode comes with [some](https://www.npmjs.com/package/vscode-jsonrpc)
[handy
libraries](https://www.npmjs.com/package/vscode-languageserver-protocol)
for LSP.

## Renaming Flow

What should our workflow be for the renaming process? Ideally a
reviewer should be able to comment a refactor command on the line
where they want the refactor to happen, which then gets parsed by the
system and used to trigger the actual code change.

![A comment on a pull request that says "rename(flattened_parameters,
flattened_params)"](/img/posts/fixing-nits-quickly/RenameCommand.png)

Because in theory anybody can trigger this code change, we don't want
to actually commit to the pull request. Instead, we're going to create
*another* pull request onto the existing pull request's branch. Yes,
PRs on PRs. In a more complete version of this, we could validate the
user and allow direct committing for contributors.

The next question is where we should run this refactoring job. Again,
in a more production version of this, this would run on our own job
server. However, this is not so simple. For one, we'd need to spin up
a container for each job, as language servers are not guaranteed to be
safe[^2]. Second, it's not so simple to clone a repository
quickly. Repositories can get quite large and can take a few minutes
to download.

[^2]: rust-analyzer for instance runs macros pre-emptively. Since Rust
    macros can read files and do other evil things, we have to sandbox
    them.

Instead, we can run this in an environment that is already sandboxed
and already optimized for downloading repositories: GitHub Actions!
Sure, GitHub Actions won't scale super far but for a proof of concept
it's worth it.

Inside the action, we'll have the actual refactoring work done by a
small CLI. I decided to make this a CLI so that it could be used in
other usecases. More on that later.

To recapitulate, the workflow is to write a refactor command in a
comment, have it trigger a GitHub Action that then applies the
refactor and opens a pull request onto the existing PR.

## Getting Locations

In a normal editor, we'd have the precise line and column information
for a symbol, which we could send off to the language server and get a
rename immediately.

However, in a pull request, we don't have the column info. The comment
is applied to a single line. This could cause some issues in some
cases, say:

```
let foo = foo.foo + 5
```

If we get a command to rename `foo` to `bar`, which `foo` are we
talking about?

Fortunately, language servers offer a solution. They have a
`PrepareRenameRequest` that allows you to send a position and see if
it's a valid symbol for renaming. We can get all the occurrances of a
given name in the line, then see if any are a valid symbol. If none
are, we error. If exactly one is, then we go ahead with the rename. If
there are multiple valid symbols, we error. There is a possibility for
offering some way to disambiguate symbols, but again, let's save that
for version 1.1.

## Renaming

After we've gotten the correct symbol, we can issue a
`RenameRequest`. Instead of changing the files itself, the language
server instead returns a list of changes to be made to various
files. Fortunately for us, the VSCode library that we're using,
[vscode-languageserver-protocol](https://www.npmjs.com/package/vscode-languageserver-protocol)
has a way to apply these edits.

That about wraps up the CLI part. We give it a line number, a file
name, an old name and a new name, and it does the work for us. If you
want to check out the CLI, the repository is
[here](https://github.com/NicholasLYang/refactor-cli).

## Parsing Commands

For the command, it should be pretty straightforward, something like:

```
rename(oldName, newName)
```

We should allow leading and trailing whitespace, but nothing else inside the comment. That way, you can say:

> You can trigger a rename with `rename(foo, bar)`

Without actually triggering the rename.

A simple regex works to parse this:

```
/^\s*\/rename\s*\(\s*(\w[\w\d]*)\s*,\s*(\w[\w\d]*)\s*\)\s*$/
```

From there, we get the new name and old name out in a simple
JavaScript script and send it over to the next stage:

```
const comment = \`${{ github.event.comment.body }}\`;
const side = \`${{ github.event.comment.side }}\`;
// We make sure it's the right side of the diff
// otherwise we're renaming old code.
const isRightSide = side === 'RIGHT';
const matches = comment.match(/^\s*\/rename\s*\(\s*(\w[\w\d]*)\s*,\s*(\w[\w\d]*)\s*\)\s*$/);
if (matches && isRightSide) {
  console.log('::set-output name=isMatch::true');
  console.log('::set-output name=symbolName::' + matches[1]);
  console.log('::set-output name=newSymbolName::' + matches[2]);
} else {
  console.log('::set-output name=isMatch::false');
}
```

The `::set-output` allows us to pass variables to subsequent stages in
a GitHub Action.

If there's no match, we cancel the rest of the action and return. Yes,
this does mean that we need to parse all pull request comments. While
that's inevitable, we could probably make it a lot lighter by using
our own system instead of spinning up an action for each comment.

From there we download the CLI and run it on our code; then we make
the pull request. As far as I know there's no way to reply to an
existing PR comment, but that would be ideal here too.

If you want to check out the action code, it's
[here](https://github.com/NicholasLYang/refactor-cli/blob/main/actions/rename.yml).

## Conclusion

This is a really simple proof of concept that I threw together. That
said, I think it's pretty neat. It's super nice being able to apply
simple changes without touching an editor. I'm going to look into
making this a more full fledged setup. In the future I'd love the
ability to add comments, rename files, maybe even factor out code into
a separate function.
