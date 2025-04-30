---
title: "Tips for Reading Code"
pubDate: "Dec 10, 2021"
draft: false
---

I've been trying to get better at reading code. It's an important
skill. Too often people neglect this skill, struggle to read other
people's code, and then accuse the code of being poorly
written. Sometimes the code is poorly written, but, [as Joel Spolsky
points
out](https://www.joelonsoftware.com/2000/04/06/things-you-should-never-do-part-i/),
sometimes the problem is just inherently messy. Not to mention, the
source code is the most up to date documentation. If you're having
trouble using a library, reading the source code can clear up a lot of
issues.

I'm not amazing at reading code, but here's my tips:

- Use a good IDE

Yes, yes, your emacs config is amazing. I know. I'm typing this in
emacs right this second. But the truth is that a good IDE is way more
functional than an editor, unless that editor is VSCode. You could
spend hours setting up your config to use language servers and file
navigators and whatever, but an IDE gets you all of the way without
any work.

From there, learn to use the editor. Learn the keyboard shortcuts for
goto definition, and go back from goto definition. Learn to search in
a file and search across directories. If you can, watch someone who is
experienced with the IDE.

A good IDE can really help with chasing down a particular execution
path. If I'm reading the [TypeScript
compiler](https://github.com/microsoft/TypeScript/) codebase and I
come across `getContextuallyTypedParameterType` that in turn calls
`isContextSensitiveFunctionOrObjectLiteralMethod` (yeesh), I can goto
the definition and see that
`isContextSensitiveFunctionOrObjectLiteralMethod` calls `isInJSFile`,
`isFunctionDeclaration`, `isFunctionExpressionOrArrowFunction`, etc.

- Embrace grep/ripgrep and find/fd

While an IDE is great, sometimes it's better to use a simpler
tool. Two of the tools that I use the most are
[ripgrep](https://github.com/BurntSushi/ripgrep) and
[fd](https://github.com/sharkdp/fd). They're both modern replacements
for old school command line tools, ripgrep replaces grep and fd
replaces find. I find the newer tools a lot more friendly but the old
school ones are great too. Ripgrep searches for a string or regular
expression in your files and fd searches for a string or regex in your
file names.

These are really great for when you need to dive into a big codebase
and get to some really specific code. For instance, let's say I'm
trying to figure out how [Istio](https://github.com/istio/istio) loads
WASM files. I don't know Istio and I don't really know Go, the
implementation langauge. However, I can load up the Istio codebase and
use `fd` to see if there are any files with "wasm" in their name:

```
pilot/pkg/model/wasm.go
pilot/pkg/networking/core/v1alpha3/extension/wasmplugin.go
pilot/pkg/networking/core/v1alpha3/extension/wasmplugin_test.go
pkg/test/datasets/validation/dataset/extensions-v1alpha1-WasmPlugin-invalid.yaml
pkg/test/datasets/validation/dataset/extensions-v1alpha1-WasmPlugin-valid.yaml
pkg/wasm
releasenotes/notes/reliable-wasm-remote-load.yaml
releasenotes/notes/wasm-extension-dashboard.yaml
releasenotes/notes/wasmplugin.yaml
tests/integration/telemetry/stats/prometheus/api/stats_wasm_filter_test.go
tests/integration/telemetry/stats/prometheus/api/stats_wasm_tcp_filter_test.go
tests/integration/telemetry/stats/prometheus/wasm
tests/integration/telemetry/stats/prometheus/wasm/bad_wasm_filter_test.go
tests/integration/telemetry/stats/prometheus/wasm/stats_wasm_filter_test.go
tests/integration/telemetry/stats/prometheus/wasm/stats_wasm_tcp_filter_test.go
```

Okay there's a few but not too many. It seems like the files in
`releasenotes/` and `tests/` are not that important. Likewise the
`yaml` files don't see that important. Let's remove those:

```
pilot/pkg/model/wasm.go
pilot/pkg/networking/core/v1alpha3/extension/wasmplugin.go
pilot/pkg/networking/core/v1alpha3/extension/wasmplugin_test.go
pkg/wasm
```

This leaves us three files and one directory. At this point I'd
probably figure out what `pilot/` and `pkg/` mean by reading the
documentation and the code. My intuition is that `pkg/wasm` will have
the meat of it because it seems to be an actual package dedicated to
WASM.

Of course you could do this in an IDE, but I happen to like using
these tools.

- Find the meat of the code

If you're diving into a new codebase, sometimes it can be hard to
figure out where the action is happening. Too often you're confronted
with a bunch of folders, most of which are rarely touched or hold
configuration, documentation, testing, etc. These files can create a
lot of noise and mess with the aforementioned tools. For instance, in
the prior example we had to filter out all the `releasenotes/` and
`tests/` directory.

Usually there's one or two directories that hold the meat of the
project. In smaller projects that'll often be a `src/` directory. In
bigger projects you may have a `packages` or `lib` directory that
stores the libraries that make up the project. Often this will depend
on the language. In Rust it would not be too weird to have a `crates/`
directory, for instance. Another way to find the meat of the code is
to look at previous pull requests and see where the majority of the
changes occur.

- Don't get bogged down in the weeds

If you're tackling a big codebase, it can be tempting to just start
reading. Maybe that works for some people but it doesn't work for
me. Realistically you're not going to be able to read a few thousand
line codebase and understand it in time to complete your task. You're
gonna have to rely on imperfect knowledge. That's why I recommend
using searching techniques to drill down into the relevant parts of
the codebase. If you are going to read the code, try to find the
central entrypoint. This isn't always true, but usually there is some
function or object that is the main do-er.

For instance, let's say I'm trying to read the [Rust name
resolver](https://github.com/rust-lang/rust/tree/master/compiler/rustc_resolve)
codebase. I start in the
[lib.rs](https://github.com/rust-lang/rust/blob/master/compiler/rustc_resolve/src/lib.rs)
and start reading from the top:

```rust
enum Weak {
    Yes,
    No,
}

#[derive(Copy, Clone, PartialEq, Debug)]
pub enum Determinacy {
    Determined,
    Undetermined,
}

impl Determinacy {
    fn determined(determined: bool) -> Determinacy {
        if determined { Determinacy::Determined } else { Determinacy::Undetermined }
    }
}

/// A specific scope in which a name can be looked up.
/// This enum is currently used only for early resolution (imports and macros),
/// but not for late resolution yet.
#[derive(Clone, Copy)]
enum Scope<'a> {
    DeriveHelpers(LocalExpnId),
    DeriveHelpersCompat,
    MacroRules(MacroRulesScopeRef<'a>),
    CrateRoot,
    // The node ID is for reporting the `PROC_MACRO_DERIVE_RESOLUTION_FALLBACK`
    // lint if it should be reported.
    Module(Module<'a>, Option<NodeId>),
    RegisteredAttrs,
    MacroUsePrelude,
    BuiltinAttrs,
    ExternPrelude,
    ToolPrelude,
    StdLibPrelude,
    BuiltinTypes,
}

/// Names from different contexts may want to visit different subsets of all specific scopes
/// with different restrictions when looking up the resolution.
/// This enum is currently used only for early resolution (imports and macros),
/// but not for late resolution yet.
#[derive(Clone, Copy)]
enum ScopeSet<'a> {
    /// All scopes with the given namespace.
    All(Namespace, /*is_import*/ bool),
    /// Crate root, then extern prelude (used for mixed 2015-2018 mode in macros).
    AbsolutePath(Namespace),
    /// All scopes with macro namespace and the given macro kind restriction.
    Macro(MacroKind),
    /// All scopes with the given namespace, used for partially performing late resolution.
    /// The node id enables lints and is used for reporting them.
    Late(Namespace, Module<'a>, Option<NodeId>),
}
```

Bored and confused already? I am. I have some idea of what this code
does, just by virtue of having worked on compilers, but it's still
very overwhelming. Instead, lets skip down, scanning for something
that seems important...

```rust
/// The main resolver class.
///
/// This is the visitor that walks the whole crate.
pub struct Resolver<'a> {
    session: &'a Session,

    definitions: Definitions,

    graph_root: Module<'a>,

    prelude: Option<Module<'a>>,
    extern_prelude: FxHashMap<Ident, ExternPreludeEntry<'a>>,

    /// N.B., this is used only for better diagnostics, not name resolution itself.
    has_self: FxHashSet<DefId>,

    /// Names of fields of an item `DefId` accessible with dot syntax.
    /// Used for hints during error reporting.
    field_names: FxHashMap<DefId, Vec<Spanned<Symbol>>>,

    /// All imports known to succeed or fail.
    determined_imports: Vec<&'a Import<'a>>,

    /// All non-determined imports.
    indeterminate_imports: Vec<&'a Import<'a>>,

    /// FIXME: Refactor things so that these fields are passed through arguments and not resolver.
    /// We are resolving a last import segment during import validation.
    last_import_segment: bool,
    /// This binding should be ignored during in-module resolution, so that we don't get
    /// "self-confirming" import resolutions during import validation.
    unusable_binding: Option<&'a NameBinding<'a>>,
    
```

Oh hey this seems important! It's the `Resolver` struct in
`rustc_resolve` and indeed the comment indicates that it's the main
class. But geez there's a lot of fields. Let's skip them too. Now that
we know the main class is called `Resolver`, we can look for its
methods by searching for an `impl` block for `Resolver`. Oh hey we
found it:

```rust
impl<'a> Resolver<'a> {
    pub fn new(
        session: &'a Session,
        krate: &Crate,
        crate_name: &str,
        metadata_loader: Box<MetadataLoaderDyn>,
        arenas: &'a ResolverArenas<'a>,
    ) -> Resolver<'a> {
        let root_def_id = CRATE_DEF_ID.to_def_id();
        let mut module_map = FxHashMap::default();
        let graph_root = arenas.new_module(
            None,
            ModuleKind::Def(DefKind::Mod, root_def_id, kw::Empty),
            ExpnId::root(),
            krate.span,
            session.contains_name(&krate.attrs, sym::no_implicit_prelude),
            &mut module_map,
        );
        let empty_module = arenas.new_module(
            None,
            ModuleKind::Def(DefKind::Mod, root_def_id, kw::Empty),
            ExpnId::root(),
            DUMMY_SP,
            true,
            &mut FxHashMap::default(),
        );
```

Again, lots of stuff here. At this point we have our pick of methods
that we can trace. I like the look of `resolve_crate`:

```rust
    /// Entry point to crate resolution.
    pub fn resolve_crate(&mut self, krate: &Crate) {
        self.session.time("resolve_crate", || {
            self.session.time("finalize_imports", || ImportResolver { r: self }.finalize_imports());
            self.session.time("finalize_macro_resolutions", || self.finalize_macro_resolutions());
            self.session.time("late_resolve_crate", || self.late_resolve_crate(krate));
            self.session.time("resolve_main", || self.resolve_main());
            self.session.time("resolve_check_unused", || self.check_unused(krate));
            self.session.time("resolve_report_errors", || self.report_errors(krate));
            self.session.time("resolve_postprocess", || self.crate_loader.postprocess(krate));
        });
    }
```

The comment indicates it's an entrypoint and it calls a bunch of
stuff. At this point I'd use my IDE to goto definition for each of
these functions and start reading. If I encounter stuff that requires
understanding the code we skipped before, only then will I go back and
read that code.

- Take notes

Taking notes can help a lot with organizing your brain. I'm not the
greatest note taker so I can't give too much advice. Some people like
typing notes on a computer, others enjoy writing in a notebook. I've
started to enjoy writing in a notebook, albeit more for designing my
own code rather than reading others'. You can take notes on which
functions do what, on the overall architecture, etc. Just make sure
you're not spending time taking notes just for the sake of taking
notes. Too often in school we learn to take notes because our teacher
says to take notes. These notes should be genuinely new pieces of
knowledge that you want to remember.

- Build the code and break something

You should try to build the code pretty soon after you start reading
the codebase. It's almost impossible to learn a codebase solely by
reading it. You need to execute code and see what happens. You can
stick print statements into the codebase and see what they print. You
can try to add a feature and see how it breaks.

A good, mature codebase should have tests and static analysis that
prevents you from doing anything too stupid. Often times you can learn
a lot just by seeing what tests your change breaks. If your codebase
doesn't have sufficient testing, you can still learn a lot by showing
your code to someone who does understand the codebase and having them
give you feedback. Of course, this person should be tactful and nice
in their feedback.

It may feel really uncomfortable contributing to a codebase where you
don't understand a significant portion. That's completely
understandable and truth be told it's a sign that you're accurately
assessing the risks of modern, large scale software development. It's
hard to make and maintain big code bases. That's (partially) why we
get paid a lot. But you have to do your best. Besides, at a certain
point it's not necessarily your fault if the company lets people
contribute bad or broken code to a codebase. Sure, you should always
aim to write good code, but at scale, that's really hard. The lead
developers have to set up reasonable precautions and safety measures
such as testing, QA, fuzzing, verification, code review, etc. if they
want any assurance that the codebase will stay functional and well
maintained.

And my final piece of advice:

- Ask the original author or maintainer

You should do all of the above, but really, asking the person who
wrote the code is your best bet. You can use [git
blame](https://git-scm.com/docs/git-blame) to figure out who wrote the
code. Hopefully they're still at the company, at which point you can
schedule a meeting or DM them. If they're not at the company, they may
still respond to a polite email if they're nice. However, I wouldn't
abuse this option unless you are explicitly being mentored/supervised
by this person, they have the bandwidth to attend to you, or you
absolutely cannot figure this out on your own. Likely they have their
own tasks, so if you can get away with figuring stuff out on your own,
that's probably better for both their productivity and your skills as
a programmer. That said, if you politely reach out and they're mean to
you, don't let that deter you! That's their problem and not
yours. Hopefully the next person will be more accomodating.
