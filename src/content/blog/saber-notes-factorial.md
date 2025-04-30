---
title: "Saber Notes Factorial"
pubDate: "Apr 14, 2020"
draft: false
---

I've been working on my language,
[Saber](https://github.com/nicholaslyang/saber) a lot recently. I
figured I should keep some notes on implementation in case someone
finds them useful, even if that's only future-me. Saber is a scripting
language intended to be a lightweight row-polymorphic
psuedo-functional language that compiles to WebAssembly. Think OCaml
meets the best parts of ES6. Of course right now I'm just trying to
get basic functionality finished. The compiler is written in Rust.

In my previous post I had gotten parameters and local variables
working. After that, I started working on getting general statement
code generation finished, control flow and function calls
finished. This allowed me to write factorial, my first non
trivial Saber program. Pretty neat if I say so myself.

# Return and Other Statements

Stack based VMs are kind of interesting in that compiling expressions
is a lot more simple (at least to me) than compiling
statements. Expressions in stacks are fairly simple. You push the
necessary parts to the expression on to the stack, whether those are
function arguments, terms in an arithmetic expression, etc., then the
VM evaluates them into a single element on the stack. Statements are a
little weird in that most of the time they should result in no terms
on the stack. In a lot of cases that means you might need to throw
away a value. For instance, this is a statement: ``` foo(); `` However
if `foo` returns a value, that means we need to discard it. But if foo
doesn't return anything, we need to not discard it.

Another slightly tricky thing is that code generation for return
statements is a little different depending on where the return
statement is placed. If it's at the end of a function, say:

```
let add = \(a, b) => {
 return a + b
}
```

Then we don't need to generate a return operation. By virtue of being
in a stack based VM, we naturally return whatever's on the stack at
the end of a function[^1].

[^1]: Which actually means Rust/Ruby's idiom of the last element in a
    block being the block's value makes total sense for a stack VM

Up until now I had generated return statements with no return op, as I
didn't have any other statements, so the only statement that I was
emitting was a return statement. But if I'm adding control flow, I
need to have the return op so that I can actually tell WASM "hey, I
want to return early".

This isn't hard as return is just another opcode. However I don't want
to always emit the code, as if the return statement is the last
statement in a function, the return op is redundant. It's a minor
optimization but hey, why not. The answer is easy: thread an `is_last`
boolean through `generate_stmt`. If you're generating a return and
`is_last` is true, then don't emit the return.

```
fn generate_stmt(&mut self, stmt: &TypedStmt, is_last: bool) -> Result<Vec<OpCode>> {
    match stmt {
        TypedStmt::Return(expr) => self.generate_expr(expr),
        TypedStmt::Return(expr) => {
	        let mut opcodes = self.generate_expr(expr)?;
            if !is_last {
                opcodes.push(OpCode::Return);
            }
			Ok(opcodes)
        }
		...
	}
}
```

Next came if statements. I actually don't plan on having if statements
long-term. Eventually I want to transition to if expressions that can
act as statements (but just evaluate to (), or the empty type). This
is how Rust implements a lot of the traditional statements:

```
// Technically an expression of type ()
if cond {
  foo();
} else {
  bar();
}

// If expr evaluates to an integer
let var = if cond {
  12
} else {
  11
};
```

But for now we're doing if statements because it's easier. The initial
code gen wasn't too bad. WebAssembly has if statements built in, which
is kinda high level for an "assembly" language, but hey I'm not gonna
look a gift horse in the mouth. You push on the condition expression,
the if opcode and the expected type (the empty type in this
case). Then the opcodes corresponding to the then block (i.e. the
block that runs if the condition is true), then an else opcode and the
block corresponding to else (if it exists), then an end opcode. Not
crazy.

What's tricky is that WebAssembly requires *some* sort of return value
if the if statement is the last statement in a function. Even if both
branches return, WebAssembly gets a little uneasy. Therefore I figured
that you need to push on a special opcode `unreachable` that indicates
that either branch returns.

If by some virtue one branch doesn't return, we get an error.

# Function Calls

After that I implemented functions calls. This is getting a little
hazy as I'm actually writing these posts a few months later, but I
basically generated a function index for each function, then on a call
expression, I check if the name corresponds to a function, get the
index, and emit a call opcode with said index. This only works for
functions defined statically, i.e. without any funny function-as-value
stuff. For that I'd need indirect calls.

One slight complication is that the index generation needs to happen
before we typecheck the function body, as otherwise recursive calls
won't work.

# Factorial

With if statements, return and function calls down, I was able to
write a simple factorial program! It looked like the following:

```
let fact = \n => {
  if n < 2 {
    return 1;
  }
  return n * fact(n - 1);
}
```

Very simple but satisfying to compile.
