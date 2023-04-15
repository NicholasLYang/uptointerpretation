---
title: "Vicuna Update"
date: 2023-04-15T00:57:11-04:00
draft: false
---

I've been working on a new programming language, Vicuna. I've detailed
some of my thoughts
[previously](https://uptointerpretation.com/posts/the-next-browser-language/). So
how's it going? Well I have a
[repository](https://github.com/nicholaslyang/vicuna) with a
playground, a basic compiler, and a CLI. The compiler has a lot of the
basic language features implemented, with a JavaScript backend and a
simple type checker. I'm hoping to implement some form of imports,
enums, traits and JavaScript interop, then I can start writing
non-trivial programs in Vicuna.

# Split Compilation Pipeline

One thing I've been experimenting with in Vicuna is a split
compilation pipeline where the JavaScript backend is actually not
dependent on the type checker. My hope with this architecture is that
it'll allow for faster compilation cycles. I suppose there is a risk
of users ignoring type errors, but I can iterate on the actual user
experience.

Of course, this means that my compilation cannot rely on type
information. For now, that's okay since JavaScript and Vicuna share a
lot of similar semantics, but this may not be true longer term. In
particular, if I want to add optimizations or features that do not
translate cleanly, I might want to consolidate the pipeline. Of
course, there might be some way to have the majority of the code
compile without type info, and then have the special cases compile
with type info.

# Parsing

I started out writing Vicuna with tree-sitter as my parser. As I
espoused in my [Tooling for Tooling
post](https://uptointerpretation.com/posts/tooling-for-tooling/), I
really like that tree-sitter manages to provide multiple usecases in a
single parser. You can write a parser and get syntax highlighting, a
playground, and even the ability to search code. Not to mention,
tree-sitter uses concrete syntax trees or CSTs, which are quite useful
for editor integrations.

However, I quickly ran up against some serious limitations with
tree-sitter. Tree-sitter's foundation is implemented in C. And while
there are Rust bindings, this C implementation creates a lot of
issues. Namely, I couldn't manipulate the trees in any significant
shape or form. The only way I could interact with the trees was
navigating via a rather frustrating read-only cursor interface. Not to
mention, the usage of C code made it [quite difficult to compile
tree-sitter to
WebAssembly](https://uptointerpretation.com/posts/webassembly-woes/). I
eventually succeeded, but it was not easy.

As for tree manipulation, my only recourse was to take the CST and
turn it into a Rust abstract syntax tree (AST) by traversing it and
building up the tree. Which in turn meant a lot of
`cursor.goto_first_child()` type of operations, which felt oddly
reminiscent of, well, parsing. Except, if my AST generator was out of
sync with my tree-sitter grammar, I'd get very confusing errors and
mangled ASTs.

I decided to look at other options. I'd tried [pest](https://pest.rs/)
and [LALRPOP](https://github.com/lalrpop/lalrpop), and was not a huge
fan of either. Ditto
[nom](https://github.com/rust-bakery/nom). Instead, I tried
[chumsky](https://github.com/zesterer/chumsky), a parser combinator
library that emphasizes ergonomics and error recovery. And I gotta
say, it lived up to its promise! Chumsky was very easy to use, and
very much designed with my usecase in mind: building parsers that
quickly and easily mapped to my own data structures. It didn't rely on
an inane DSL (unless you count parser combinators as a DSL, which they
could be, albeit a not-inane, very smart, type-safe DSL). And it had basically
all of the features and/or boilerplate I'd need to write a parser. The
only complaint I'd have is that due to the type-safe nature of the
combinators, it can lead to some rather...intense compiler error
messages, with angle bracket chains rivaling C++ errors. But
fortunately I had very few compiler errors writing the parser.


An example of chumsky's errors:
```
error[E0599]: the method `clone` exists for struct `Map<DelimitedBy<Padded<SeparatedBy<Padded<Then<Map<Then<impl Parser<char, String, Error = Simple<char>>, Just<char, char, Simple<char>>>, fn((String, char)) -> String, (String, char)>, impl Parser<char, TypeSig, Error = Simple<char>> + Clone>>, Just<char, char, Simple<char>>, char>>, Just<char, char, Simple<char>>, Just<char, char, Simple<char>>, char, char>, [closure@parser.rs:132:14], Vec<(String, TypeSig)>>`, but its trait bounds were not satisfied
   --> crates/vicuna-compiler/src/parser.rs:136:22
    |
136 |         .then(fields.clone())
    |                      ^^^^^ method cannot be called due to unsatisfied trait bounds
    |
   ::: /Users/nicholas/.cargo/registry/src/github.com-1ecc6299db9ec823/chumsky-0.9.2/src/combinator.rs:412:1
    |
412 | pub struct DelimitedBy<A, L, R, U, V> {
    | ------------------------------------- doesn't satisfy `_: Clone`
...
944 | pub struct Map<A, F, O>(pub(crate) A, pub(crate) F, pub(crate) PhantomData<O>);
    | ----------------------- doesn't satisfy `_: Clone`
    |
    = note: the following trait bounds were not satisfied:
            `DelimitedBy<Padded<SeparatedBy<Padded<Then<chumsky::combinator::Map<Then<impl chumsky::Parser<char, std::string::String, Error = chumsky::error::Simple<char>>, Just<char, char, chumsky::error::Simple<char>>>, fn((std::string::String, char)) -> std::string::String, (std::string::String, char)>, impl chumsky::Parser<char, TypeSig, Error = chumsky::error::Simple<char>> + Clone>>, Just<char, char, chumsky::error::Simple<char>>, char>>, Just<char, char, chumsky::error::Simple<char>>, Just<char, char, chumsky::error::Simple<char>>, char, char>: Clone`
            which is required by `chumsky::combinator::Map<DelimitedBy<Padded<SeparatedBy<Padded<Then<chumsky::combinator::Map<Then<impl chumsky::Parser<char, std::string::String, Error = chumsky::error::Simple<char>>, Just<char, char, chumsky::error::Simple<char>>>, fn((std::string::String, char)) -> std::string::String, (std::string::String, char)>, impl chumsky::Parser<char, TypeSig, Error = chumsky::error::Simple<char>> + Clone>>, Just<char, char, chumsky::error::Simple<char>>, char>>, Just<char, char, chumsky::error::Simple<char>>, Just<char, char, chumsky::error::Simple<char>>, char, char>, [closure@crates/vicuna-compiler/src/parser.rs:132:14: 132:22], Vec<(std::string::String, TypeSig)>>: Clone`
```


I've yet to make the parser properly error tolerant, but I expect that
will go relatively smoothly as well.

# Compiling To JavaScript

It's easy. So so so easy. It's basically what I do in my head when I
write JavaScript or TypeScript anyways---taking patterns from Rust and
converting them to JS semantics and syntax.

Of course there's still a lot of features to implement. I need to do
modules/imports and the big one: interop. Not to mention figuring out
what I should do about mutability and so on. But for now it's nice and
easy. I did enums in 15 minutes!

I'll probably try to add TypeScript definitions too. Maybe via JSDoc?

# Type Checking

So far type checking has been pretty simple. Honestly GitHub Copilot
has helped a lot with the boilerplate, which is both good, and an
indication that I haven't gotten to the hard stuff. I made sure to
make my language nice and simple this time, so I've avoided a lot of
messy complications that I had previously, such as patterns, inferred
parameter and return types, and type lowering.

I suspect that type checking might actually be where I introduce my
first IR, because I want to do some proper control flow
analysis. Perhaps this could lead to some optimizations as well and
then a release build pipeline. We'll see.

# Overall

This project has been significantly easier than my last language. I
suspect it's because, first, I have more experience writing compilers
and programming as a whole; second, because of that experience I
scoped down my problem by a lot; third, tools like copilot make it a
lot easier to churn out boilerplate. I'm pretty excited to see how the
next few challenges go, as they'll be a much bigger test of my compiler
knowledge.
