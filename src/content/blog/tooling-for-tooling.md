---
title: "Tooling for Tooling"
pubDate: "Dec 3, 2022"
draft: false
---

We've seen a boom in programming language tooling in the past few
years. Language servers, formatters, and linters have become
commonplace in most languages. I'd call it a golden age, but I suspect
this is only the beginning.

## Fulfilling Developer Expectations

This explosion has in turn raised the bar for developer experience. No
longer is it satisfactory to provide a basic syntax highlighting
scheme for a programming language and call it editor
support. Programmers expect[^0] to be able to refactor their code
automatically, to reformat files, to check errors. They expect
sophisticated syntax highlighting that uses semantic information.

[^0]: Of course this is an overgeneralization. But I anticipate this
    will be increasingly true, especially with the rise of Visual
    Studio Code and language servers.

All of these expectations are very difficult to fulfill. They require
a language server, which generally shares code with the programming
language's implementation. Because language servers are fundamentally
a service that an editor queries and not a traditional compiler
pipeline, this often necessitates the programming language
implementation moving from a pipeline model to a query
model[^1]. Language servers are often involved in practically every
keystroke, thus they need to be performant, thus they require
sophisticated parsers that can reparse extremely quickly and store the
entire source text in full fidelity. These parsers also need to be
error tolerant, so that one syntax error doesn't completely break the
editor's syntax highlighting.

[^1]: In theory I suppose you could have the language server use more
    granular parts of the implementation, but that's effectively turning
	the internal pieces of a programming language into an API.

Linting and formatting are similarly arduous. Linting requires a large
body of lints, which in turn requires a linter API so that the
community can write custom lints. Formatting is a lot of tiny
subjective decisions around aesthetics. A formatter is also quite
useless if it only works for a couple languages. Prettier is very
powerful because it works in almost every web language imaginable. I
don't have to think, I just have to use Prettier.

All of this is to say that building a comprehensive developer
ecosystem is extraordinarily tough. It's no surprise that the tooling
quality for more niche languages is significantly lower. It's no
surprise that the two main companies driving programming language
tools are Microsoft and Jetbrains, the former being a behemoth that
can afford to spend billions on loss leading tools, and the latter
being a business that has devoted years and years of developer hours
to programming language tools, slowly winning the developer
market. Either way, they have devoted a massive amount of resources
towards creating programming language tools.

I don't think it's controversial to say that it should be easier to
make these tools. Tooling makes it easier for new languages to gain
traction, which in turn allows academic innovations like borrow
checking, dependent types, automatic parallelization, to gain users
and get real world usage.

## Making This Easier

How can we make programming language tooling easier to write?
Naturally, the answer pops up at Microsoft, well, GitHub. GitHub
wanted to have a collection of fast, high resolution parsers for lots
of programming languages in a consistent, easy to implement manner. To
solve this problem, they built tree-sitter.

Tree-sitter appears to be your run of the mill parser
generator. However, there are some key decisions that make it more
powerful. For one, it fulfills the needs described above; it is fast
enough to run on every keystroke and error tolerant. Second, and more
importantly, it is a tooling ecosystem unto itself. Use tree-sitter
and you get more than just a parser. You get a playground to test out
your grammar. You get a bindings to lots of different languages. You
get syntax highlighting practically for free. You get [a domain
specific language for matching on
code](https://tree-sitter.github.io/tree-sitter/code-navigation-systems).

This makes it very compelling to buy into the tree-sitter ecosystem. A
language can implement a parser and get a fairly decent set of
language tools for free.

The experience of using tree-sitter is also quite nice. Gone are the
days of bison with its one long manual and documentation in the form
of professor's slides from ancient compiler classes. There's no
awkward DSL with half-hearted editor support. To build a tree-sitter
grammar you write some JavaScript[^2] and then tree-sitter's very nice
CLI builds the compiler from that JS. It's very straightforward.

[^2]: Albeit very much a DSL inside JavaScript.

With that said, you'd be forgiven for not being impressed with
tree-sitter's offerings. A playground, syntax highlighting, and some
code navigation isn't exactly Christmas. Indeed, while I love
tree-sitter very very much, it frustrates me that they haven't pushed
this to its limits. Take the [parser
bindings](https://docs.rs/tree-sitter/latest/tree_sitter/index.html). Right
now they're fairly low level, essentially a cursor to a concrete
syntax tree of homogenous `Node` types. Instead, I'd love some way of
generating AST definitions from grammar rules. This is not a
tree-sitter tutorial, so if you don't understand this, I'd recommend
looking at the [tree-sitter
docs](https://tree-sitter.github.io/tree-sitter/). Anyways, taking a
rule like:

```
statement: $ => choice($.varDeclaration, $.functionDeclaration, $.expressionStatement)
```

I'd love for a Rust `Statement` type to be generated:

```
enum Statement {
  VarDeclaration(VarDeclaration),
  FunctionDeclaration(FunctionDeclaration),
  ExpressionStatement(ExpressionStatement)
}
```

Obviously there should be functionality to tweak the naming and inline
declarations when necessary, but you get the idea. From there, there
should be a way to parse into this AST, while keeping the CST around
just in case.

There could also be a way to automatically generate a basic formatter
from the grammar. It wouldn't be anything on-par with Prettier, but it
could be a start that others customize.

Likewise, I've heard some fairly reasonable criticism of tree-sitter's
error message story. I'm not an expert in error messages or parser
error recovery, but I imagine there's ways to work within the
framework, whether that's offering the ability to fall back to a
secondary grammar for reparsing, or even to a hand-written parser.

This is an entirely separate rant, but I also find it extremely
frustrating that tree-sitter generates C. Nobody should have to deal
with building C.

## But Why?

An astute reader may observe that I'm asking for tree-sitter to add
features that make it easier to use for a compiler front-end. They
would be correct. Remember how I said that language servers and
compilers almost always need to share implementation? Currently that
means we twist the existing compiler into a language server based
form. Instead, what if the tools used to implement a compiler just
naturally had language server functionality built into them? What if
you could write your compiler with tree-sitter and get syntax
highlighting and simple code navigation utterly for free? What if your
users could start [writing lint
rules](https://siraben.dev/2022/03/22/tree-sitter-linter.html)
immediately?

All of these things are table stakes for developers. Why can't they be
for implementations?

And tree-sitter is just one piece of the puzzle. What if incremental
computation frameworks like [salsa](https://github.com/salsa-rs/salsa)
and [the Turbo engine](https://github.com/vercel/turbo)[^3] were
naturally included in compilers? What if we could create proper
libraries for writing type checkers? I could see some sort of library
focused around type variables and scopes to be very powerful. Maybe we
could get automatic type annotations, or automatic typed based
refactoring.

All of these tools are what I'd call **meta-tools**. They allow
programming language implementors to increase their productivity in a
comparable manner to how developer tools increase the productivity of
regular developers.

[^3]: Disclaimer, I work on Turborepo at Vercel.

Of course, the OG meta-tool is LLVM. Compile to LLVM and you get a
bunch of architectures and optimizations for free. One could argue
that other tools like HACS, bison, Racket, JavaCC, etc. are also
meta-tools. I'd push back on that claim. Meta-tools should be usable
in a production environment; they should facilitate a sophisticated
user experience; and they should come with an ecosystem of "free"
benefits. A tool such as Racket does have an ecosystem of various
benefits, but I do not know of real, production languages that use
Racket for their compilation. A snarky response could be Racket
itself, but one implementation does not a meta-tool make. A meta-tool
must work as a compiler implementation tool beyond one
implementation. Bison simply lacks the usability story, ditto HACS,
and ditto JavaCC.

## But How?

This process will not be easy. Compiler authors, by their very own
nature, are often victims to not-invented-here syndrome. I've seen
teams turn their nose up at tree-sitter and insist on hand-writing
their own parsers. It's not difficult to fall into this trap; any
halfway decent senior developer can come up with a bunch of plausible
reasons why a library just isn't right and therefore we must rewrite
everything from scratch. I'm sympathetic myself. I love hand writing
recursive descent parsers, especially compared to the frankly
infuriating bunch of parser generators out there.

To combat this, meta-tools must offer such a compelling suite of
features that it becomes difficult to refuse. Using tree-sitter should
plug you into a first-class ecosystem with facilities for testing,
code navigation, AST generation, playgrounds, linting, etc.

The experience of using a meta-tool should also be first class. Avoid
domain specific languages with custom syntax. Don't make users write
code inside a DSL (looking at you bison). Users should be able to
write in the syntax of a commonly used language such as
JavaScript. Make it as easy as possible to get the data out from the
meta-tool and into the compiler as quickly as possible.

Meta-tools must fundamentally be extensible. If a language implementor
hits a barrier where they cannot accomplish the last 1% of their
features, that will spell doom for the meta-tool long term. It'll give
credence to not-invented-here arguments.

The tools also must be aligned culturally. The maintainers should be
receptive to language implementor's needs. They will need to
accomodate as many requests as reasonably possible. It's very easy to
dismiss a tool if the authors don't respond to user feedback. I don't
mean to be flippant here. It's really really hard to maintain an open
source project. I'm not saying that maintainers should just work
harder. Keeping the balance between user feedback and avoiding feature
creep is an eternal challenge in product design. This will not be
easy. What I am saying is that the development of a meta-tool should
be focused around language implementors first and foremost. On the
bright side, there are far fewer language implementors than there are
say, Webpack users.

On the flip side, the language implementors will need a similar
cultural alignment. We're already seeing a shift from a focus on pure
language semantics with syntax and editor tooling seen as side
projects, to a focus on usability and tooling. Rust is semantically an
OCaml with typeclasses, affine types, and unboxed types. What keeps
Rust from being an Inria PhD student's thesis that nobody uses is its
focus on tooling, good compiler diagnostics, and ecosystem. I'd like
to see some language implementors having that focus from the
beginning. Unless the implementors see developer experience as a
priority, they won't take this path.

I've been writing as if the language implementors and the meta-tool
implementors are different people. I actually suspect this will not be
the case. Most likely the successful meta-tools will arise out of
language implementation. They will be battle tested and therefore more
compelling to other developers. The only danger is that they will be
too specialized to language implementation they arose out of.

## Raise The Bar

And finally, the way to make these tools more compelling is to raise
the bar to the next level. Programmers currently expect syntax
highlighting, formatting, linting, refactoring, etc. What's next?
Maybe it's better code generation for scaffolding projects. Maybe it's
extremely quick deployment based on incremental compilation. Maybe
it's seamless interop with other languages. Whatever it is, if the gap
widens between the languages that emphasize developer experience and
the languages that don't, perhaps more implementors will catch on to
this disparity.

Right now it's too common to see implementors compare their language
to others on the basis of features. You have lifetimes but we have
unboxed types; we have hygienic macros and you don't. What that
neglects entirely is the experience of these features. Will unboxed
types have the same quality of error messages as lifetimes? Will there
be automatic fixes suggested? Will the macros provide good error
messages? Will they make debugging more difficult? These are the
questions that matter to users, not the pure semantics.

If you want to chat more about meta-tools, I'd love to talk! Feel free
to email me at nick@nicholasyang.com
