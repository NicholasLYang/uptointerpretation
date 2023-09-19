---
title: "How Good Is Unix Culture"
date: 2023-05-21T23:50:14-04:00
draft: true
---

When you're a young programmer on the internet, reading the blogs and
the history, you come across Unix culture[^1] everywhere. Whether it's
classic essays like Worse Is Better or reddit comments telling you to
read *The C Programming Language* by Brian Kernighan and Dennis
Ritchie (aka K&R for this in the know), Unix culture pervades computer
culture.

[^1]: The term Unix culture is a bit of a false synecdoche in that
    computing culture extends beyond Unix, but it's good enough for
    this blog post.

But how good is Unix culture?

In some ways that's loaded question. Unix culture is largely of the
past, and it's easy to criticize the past with our current
knowledge. On the flip side, Unix culture has occupied an almost
hagiographic position in the tech community. People fawn over the
elegance of its composable tools, the simplicity of its languages, the
legendary male hackers with their all-lowercase handles (dmr,
ken). The foundations, both literal in Unix and C, and ideological,
are embedded in modern computing.

We're at a pivotal moment in computing history,

# Style and Usability

To start, the style of Unix programming has lost a lot of its sheen. I
can't say that from a purely practical perspective that naming
conventions such as `atoi` or `alloca` are worth preserving. I wonder
how many programmers have been discouraged or how many people have
neglected to learn Unix better simply because the naming is so
opaque. Some may scoff at this, saying that a mere name should not
discourage someone, but I do think it has an effect. A name is also a
signifier of what the tool does, which can signal to the user what
further usages it may have. If the name is opaque, it hampers feature
discovery.

A simple example of this is `cat`. I, as many people, first
encountered `cat` as a way to easily print out files. I'd write `cat
README.md` and see the file in stdout. However, this functionality is
really just a side effect of `cat`'s real purpose, which is to
con**cat**enate files. This is not readily apparent from the name. If
`cat` was named something more obvious, like `combine` or `concat`
then perhaps a user could intuit what tool is meant to do. The Unix
response would be that `concat` is too long of a name, that we should
value terseness. This was partially because of physical constraints;
monitors were literally 80 characters across and longer names would
take up more space. I don't believe these restrictions apply anymore.

Similarly, the Unix style dictates that a program should output
nothing by default. A user types in `cat` and gets nothing but a blank
line while the program waits for further input. It's not exactly a
friendly greeting. At best you get a short help text of:

```
usage: grep [-abcdDEFGHhIiJLlMmnOopqRSsUVvwXxZz] [-A num] [-B num] [-C[num]]
	[-e pattern] [-f file] [--binary-files=value] [--color=when]
	[--context[=num]] [--directories=action] [--label] [--line-buffered]
	[--null] [pattern] [file ...]
```

This is certainly far better than a blank line, but it's still not
fantastic. I have no clue what `[-abcdDEFGHhIiJLlMmnOopqRSsUVvwXxZz]`
does, nor `[-A num] [-B num] [-C[num]]`. There's no instructions on
how `grep` works or what it does. Sure, sure, one can always check
`man grep`, but it wouldn't hurt to just explain in the help text.

In modern shell tools, it's customary to produce help text when no
subcommand is given, for instance with cargo:

```
Rust's package manager

Usage: cargo [OPTIONS] [COMMAND]

Options:
  -V, --version             Print version info and exit
      --list                List installed commands
      --explain <CODE>      Run `rustc --explain CODE`
  -v, --verbose...          Use verbose output (-vv very verbose/build.rs
                            output)
  -q, --quiet               Do not print cargo log messages
      --color <WHEN>        Coloring: auto, always, never
  -C <DIRECTORY>            Change to DIRECTORY before doing anything
                            (nightly-only)
      --frozen              Require Cargo.lock and cache are up to date
      --locked              Require Cargo.lock is up to date
      --offline             Run without accessing the network
      --config <KEY=VALUE>  Override a configuration value
  -Z <FLAG>                 Unstable (nightly-only) flags to Cargo, see 'cargo
                            -Z help' for details
  -h, --help                Print help

Some common cargo commands are (see all commands with --list):
    build, b    Compile the current package
    check, c    Analyze the current package and report errors, but don't build object files
    clean       Remove the target directory
    doc, d      Build this package's and its dependencies' documentation
    new         Create a new cargo package
    init        Create a new cargo package in an existing directory
    add         Add dependencies to a manifest file
    remove      Remove dependencies from a manifest file
    run, r      Run a binary or example of the local package
    test, t     Run the tests
    bench       Run the benchmarks
    update      Update dependencies listed in Cargo.lock
    search      Search registry for crates
    publish     Package and upload this package to the registry
    install     Install a Rust binary. Default location is $HOME/.cargo/bin
    uninstall   Uninstall a Rust binary

See 'cargo help <command>' for more information on a specific command.
```

Yes, this is a lot, and in the days of slow, small computer monitors,
it may have been excessive, but those days are long past. Today, this
output makes it significantly easier to learn a program.

The style also extends to programming languages. C was a language of
minimal abstraction, the common descriptor is "portable
assembly[^2]". The goal was to restrict the programmer as little as
possible, leaving all of the nitty details of memory, pointers, and
computer execution visible. Even now, it is a language that values
tricks and concision. People compete to write the most twisted C
programs that resist interpretation.

With decades past, there's an assumption that this minimal abstraction
was necessary due to the computing constraints of the time; the
programmer can fine-tune the execution of the program to run as
quickly as possible. And that may be true partially. But it's
important to note that higher level languages such as Pascal and
FORTRAN existed alongside C. In *Coders At Work*, Frances Allen
bemoans that C became so widely used because of its *inhibition* of
performance. Because C gave so much freedom, compilers could not
effectively statically analyze the code and provide
optimizations. Whereas with restrictive languages such as FORTRAN,
these optimizations were more feasible.

[2]: Although truthfully this is a misnomer on par with "egg cream"; not portable, not assembly.

# Culture

In general Unix culture has a lot of tropes of masculinity and "no
true Scotsman" within it. We use "cowboy" to describe programmers who
do daring tasks. Apocrypical posts about [Real
Programmers](http://www.catb.org/jargon/html/story-of-mel.html) litter
the internet.


# Safety

In some regards, the shine has certainly worn off. C, while a
foundational language in computers, is not most programmers' language
of choice for development. Anybody who has a rudimentary knowledge of
security will acknowledge that the style of programming described in
K&R is extremely unsafe, with lines like `while (*s++ = *t++)` leading
to buffer overflow attacks and millions of dollars lost. Unix's
security model was also quite naive. (TODO: fill this in)


- Cowboy hacker culture
  - Neuromancer
- Barrel memory
- Creates lingo and exclusive culture, which prevents outsiders, i.e. more diversity from entering
- Values simplicity above all else
  - Golang vs Rust
  - Simplicity is a lie

