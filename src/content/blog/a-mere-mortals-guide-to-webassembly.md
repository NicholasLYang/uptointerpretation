---
title: "A Mere Mortal's Guide to Webassembly"
pubDate: "Sep 22, 2021"
draft: false
---

Let's learn some WebAssembly. This guide is intended if you plan on
getting nice and intimate with WebAssembly, whether that means writing
a back-end for a compiler, hand writing WASM or inspecting the output
of an existing compiler. It's not really that useful if you're just
using Rust/C/whatever compiled to WebAssembly.

I wrote this guide because I've spent a lot of time trying to
understand the WebAssembly specification for my own
projects. Hopefully it'll make other people's attempts a little easier.

# Basics

WebAssembly is a low level language that can be run in various
contexts such as a web browser, a server side runtime, and so on. It's
intended to be a fast, sandboxed compile target. While it is low
level, it's not quite as low level as regular assembly. WebAssembly
has some quite high level features such as structured control flow,
functions, etc. while lacking low level features such as registers,
arbitrary jumps, etc.

WebAssembly is a stack based language. What that means is that
computations generally occur by manipulating a stack data
structure. Operations can push data onto the stack and pop data off of
it. For instance, instead of writing `1 + 2` we'd do:

```
i32.const 1
i32.const 2
i32.add
```

Don't worry if this seems daunting. We'll worry about the actual
opcodes later. What this does is push `1`, an `i32`, i.e. a signed 32
bit integer, onto the stack, then push `2`, another `i32`, onto the
stack, then pop both off, add them and push that sum onto the stack.

With the stack annotated:

```
i32.const 1 // [1]
i32.const 2 // [1, 2]
i32.add     // [3]
```

The main code units for WebAssembly are functions and
modules. Functions are not particularly unusual. They take in a list
of parameters and return a list of return values. Yes, multiple return
values are allowed. You can treat functions as first class variables
using `call_indirect` but we'll talk about that later. Modules contain
sections for various stuff, mostly tied around functions and the data
they need.


While WebAssembly is a stack language, it also has local and global
variables. Global and local variables act as you'd expect. Local
variables are specific to a function while global variables are
specific to a module. Locals are always mutable, globals can be
immutable.

The types in WebAssembly are very simple, just 32 and 64 bit integers,
along with 32 and 64 bit floats. WebAssembly uses 32 bit indexing (as
of September 2021) for memory and most other things.

# Encoding

There are two main encoding formats for WebAssembly: WAT and WASM
bytecode. WebAssembly Text Format or WAT[^1] is an s-expression
format, basically it uses a lot of parentheses and is very readable to
humans.

WebAssembly bytecode is a very compact encoding that is way less
readable to humans and much more readable to machines. It uses LEB128,
a variable length encoding for integers. Variable length means that a
32 bit integer doesn't have to take up a whole 4 bytes if it's
small. For instance something like 2048 is encoded as `[0x80, 0x10]`,
using 2 bytes instead of 4. LEB128 is Little Endian. If you don't
remember your computer systems, that means the least significant byte,
i.e. the byte corresponding to the lowest part of the number, is
stored first. LEB128 can encode signed and unsigned integers, both of
which are used in WASM.

I will occasionally say something like "a 32 bit LEB128 encoded
integer", which means that the integer is at maximum 32 bits long.

[^1]: I guess WTF was a little vulgar?

For this guide we'll worry about WASM bytecode because it's what most
compilers output. However, valid WASM bytecode is easily convertible
to WAT using `wasm2wat`. You can get `wasm2wat` as part of
[WABT](https://github.com/WebAssembly/wabt), The WebAssembly Binary
Toolkit. However invalid bytecode will not always be convertible. You
can use the `--no-check` flag with `wasm2wat` but if the bytecode is
badly encoded, `wasm2wat` can't do much. Speaking of tooling...

# Tooling

I highly recommend downloading WABT. It's a great set of tools that
makes your life a lot easier. I use `wasm-objdump`, `wasm2wat` and
`wasm-validate` all the time. Of course good old `hexdump` and
`hexl-mode` in emacs are good, especially if you're rolling your own
encoder. [Binaryen](https://github.com/WebAssembly/binaryen) is also
really great for optimizing WebAssembly and has some command line
tools as well.

The tooling and infrastructure around WebAssembly is still very new
but there's a couple decent libraries in the Rust
ecosystem. [walrus](https://github.com/rustwasm/walrus) is an
excellent library for code generating WebAssembly. It isn't meant to
be a full fledged compiler back-end but for small tasks it's
great. There's other WebAssembly libraries like
[parity-wasm](https://github.com/paritytech/parity-wasm) that also
seem great.

I also have a library for emitting WebAssembly in JavaScript called
[wasup](https://github.com/NicholasLYang/wasup). It's quite good if I
do say so myself.

I'm not familiar enough with the different runtimes to say which one's
the best. I've used
[wasmtime](https://github.com/bytecodealliance/wasmtime) and it seems
to work great.

# Hex

With WebAssembly, there's a really high chance that you're gonna have
to read some hexidecimal. In theory you could use something like
walrus or emit WAT and not deal with hex, but it always pops up
somewhere. It's not that bad though. Your brain is very good at
pattern recognition and will start to see the patterns quite soon.

Keeping an ASCII table around too isn't a bad idea.

# Concepts

Here's some concepts that you'll need to understand to write WASM

## Indices

WebAssembly does almost everything via indices. Functions are called
by their index, local and global variables are referenced by index,
etc. Indices are again 32 bit integers encoded using LEB128.

If you're using something like walrus, indices are abstracted away
which is quite nice. If you're not, well you'll have to keep track of
them.

## Sections

WebAssembly modules can have up to 12 sections plus unlimited custom
sections. The sections are:

1. Type

Type definitions for functions, either defined in the module or
imported. Note that multiple functions can share the same type
definition

2. Import

Imports for stuff like functions, tables, memories or global
variables.

Important note: imported functions share the same index space as
functions defined in the module. I.e. if there are two functions
imported they'll have indicies 0 and 1, then the first function
defined in the module will have index 2.

3. Function

Function is a deceptively simple section. All it does is map the
functions defined in the module to their type definitions.

4. Table

Declares the tables used in the module. Tables are used to store
references and can be indexed by runtime values. This can be used to
implement dynamic function calls by keeping a table of function
references.

5. Memory

Declares the module's memory. Currently only one memory is supported.

You can also have a module import a memory if you want.

6. Global

Declares global variables by giving their type, whether they're
mutable and a (limited) expression to evaluate to give their initial
value.

7. Export

Declares exports. Like imports these can be functions, tables,
memories, and global variables.

8. Start

Just a single index of a function to be called on module
instantiation. Function cannot take arguments.

9. Element

Declares the actual elements of the tables declared in the tables
section.

10. Code

The code section declares the actual code for functions. It also
declares the local variables used in the code body. We should note
that parameters and local variables shared the same index space. So if
you have a function that takes in one argument, it'll have index 0, and
the first local variable will have index 1.

Also, local variables are declared by giving a type and the number of
local variables, something like:

```
[(2, i32), (5, f32)]
```

Which defines 2 i32 local variables and 5 f32 local variables.

11. Data

Declares constant data for programs such as strings. This data will be
copied into memory. There are active and passive data segments; active
segments are copied at instantiation while passive segments are copied
using the `memory.init` instruction.

12. Data count

Stores the number of data sections. Used to keep validation of modules
a single pass.


## Vectors

A very common pattern in WebAssembly bytecode are vectors. Vectors
consist of a 32 bit LEB128 length, followed by the items. These show
up everywhere.

## Length

Because WebAssembly is made to be deserialized very quickly, the
bytecode includes length info for various constructs. Each section
starts with the section id then the length of the section in bytes,
again as an unsigned 32 bit LEB128 integer. Same with code
bodies. This can be tricky to serialize as you need to know the length
up front.

# Let's Code

Let's write a program to encode WebAssembly. This is a simplified
version of [wasup](https://github.com/NicholasLYang/wasup), my
WebAssembly encoder/decoder library. I'm going to use a JavaScript
array with a whole bunch of concatenations. I'm not going to cover all
of WebAssembly cause you can just read the wasup source for
that. Instead I'm gonna write just enough to get a binary search
program working in WebAssembly.

The preamble consists of the Unicode string `\0asm` and the version
number, in this case `1`. Note that the version is a fixed width 32
bit integer, *not* an LEB128 encoded int. However it is still little
endian.

```
function encodePreamble() {
	return [0x00, 0x61, 0x73, 0x6d, 0x01, 0x00, 0x00, 0x00]
}
```

Next we need to encode types. Let's start with the basic primitive
types: numbers and references.

```
enum NumType {
  i32 = 0x7f,
  i64 = 0x7e,
  f32 = 0x7d,
  f64 = 0x7c
}

enum RefType {
  funcRef = 0x70,
  externRef = 0x6f
}

type ValueType = RefType | NumType
```

Function types have a very simple setup. They're the constant `0x60`
followed by a vector of parameter types and a vector of return types.

Let's write a quick helper for vectors. This is the first case where
we need LEB128 ints, so let's write the implementation for encoding
signed and unsigned LEB128. I'm not gonna go into too much detail
about LEB128 encoding. It's a fairly easy algorithm to [read
about](https://en.wikipedia.org/wiki/LEB128).

```
function encodeLEB128U(
  n: number,
): number[] {
  if (!Number.isInteger(n)) {
    throw new RangeError(`n must be an integer, instead is ${n}`);
  }

  const buffer = [];
  // Loop until the next to last 7 bytes
  while (n > 0x7f) {
    buffer.push((n & 0x7f) | 0x80);
    n = n >> 7;
  }

  buffer.push(n);
  return buffer;
}

export function encodeLEB128S(
  n: number,
): number[] {
  if (!Number.isInteger(n)) {
    throw new RangeError(`n must be an integer, instead is ${n}`);
  }

  let buffer = [];
  n |= 0;
  while (true) {
    const byte = n & 0x7f;
    n >>= 7;
    if ((n === 0 && (byte & 0x40) === 0) || (n === -1 && (byte & 0x40) !== 0)) {
      buffer.push(byte);
      return buffer;
    }
    buffer.push(byte | 0x80);
  }
}

function encodeVector<T>(items: T[], encodeFn: (t: T) => number[]): number[] {
  return [
    ...encodeLEB128U(items.length),
	...items.flatMap(item => encodeFn(item))
  ]
}
```

Okay now function types:

```
interface FuncType {
  paramTypes: ValueType[];
  returnTypes: ValueType[];
}

function encodeFuncType(funcType: FuncType) {
  return [
    0x60,
    ...encodeVector(funcType.paramTypes, (a) => [a]),
    ...encodeVector(funcType.returnTypes, (a) => [a]),
  ];
}
```

The type section is just a vector of func types combined with the
standard section preamble. Most sections have this format so let's
just make a function to encode that:

```
function encodeSection<T>(id: number, items: T[], encodeFn: (t: T) => number[]) {
  const bytes = encodeVector(items, encodeFn);
  return [
    id,
	...encodeLEB128U(bytes.length),
	...bytes
  ]
}
```

Now a specific function for the type section:

```
function encodeTypeSection(types: FuncType[]) {
  return encodeSection(1, types, encodeFuncType);
}
```

Now let's handle the function section. The function section is very
simple, just a list of type indices.

```
function encodeFuncSection(typeIndicies: number[]) {
  return encodeSection(3, typeIndicies, encodeLEB128U);
}
```

The memory section is next. Memories are encoded as limits,
basically a minimum and optional maximum value.

```
interface Limits {
  min: number;
  max?: number;
}

function encodeLimits(limits: Limits) {
  if (limits.max) {
    return [0x01, ...encodeLEB128U(limits.min), ...encodeLEB128U(limits.max)]
  } else {
    return [0x00, ...encodeLEB128U(limits.min)]
  }
}
```

Encode the section:

```
function encodeMemorySection(memories: Limits[]) {
  return encodeSection(5, memories, encodeLimits);
}
```

Next are exports, since we need to access the memory object from JS in
order to write the array to memory, and we need to call the function.

Let's define them. Exports are a name, followed by a type and an
index, where the type explains what kind of index.

```
enum ExportType {
  Function = 0,
  Table = 1,
  Memory = 2,
  Global = 3,
}

interface Export {
  name: string;
  type: ExportType;
  index: number;
}
```

Encoding isn't too surprising. We use the `TextEncoder` API to convert
the string to bytes:

```
function encodeExport(exportEntry: Export) {
  const nameBytes = (new TextEncoder()).encode(exportEntry.name);
  return [
    ...encodeLEB128U(nameBytes.length),
    ...nameBytes,
    exportEntry.type,
    ...encodeLEB128U(exportEntry.index),
  ];
}

function encodeExportSection(exports: Export[]) {
  return encodeSection(7, exports, encodeExport);
}
```

After that we need a code section and that's it. We could put the
array in the data section but that's not strictly necessary. I'm gonna
define a few instructions for our binary search.

```
enum InstrType {
  If = 0x04,
  BrIf = 0x0d,
  Return = 0x0f,
  Call = 0x10,
  Drop = 0x1a,
  I32Load = 0x28,
  I32Const = 0x41,
  I32GtS = 0x4a,
  I32Eq = 0x46,
  I32Add = 0x6a,
  I32Mul = 0x6c,
  I32DivU = 0x6e,
  LocalGet = 0x20,
  LocalSet = 0x21,
  LocalTee = 0x22,
}

type Instruction =
  | [InstrType.I32Load, number, number]
  | [InstrType.I32Const, number]
  | [InstrType.I32GtS]
  | [InstrType.I32Eq]
  | [InstrType.I32Add]
  | [InstrType.I32Mul]
  | [InstrType.I32DivU]
  | [InstrType.LocalGet, number]
  | [InstrType.LocalSet, number]
  | [InstrType.LocalTee, number]
  | [InstrType.Drop]
  | [InstrType.BrIf, number]
  | [InstrType.Return]
  | [InstrType.Call, number]
  | [InstrType.If, ValueType, Instruction[], Instruction[]];
```

We use this two type setup of `InstrType` and `Instruction` because
instructions can take intermediates, basically static values that come
after the instruction in the code.

Technically the definition for the if instruction is incorrect because
if instructions can have more types than `ValueType` expresses, but
that's not necessary for this example.

Now let's define function bodies:

```
interface FuncBody {
  locals: [number, ValueType][];
  instructions: Instruction[];
}
```

Now we can encode instructions:

```
function encodeInstruction(instruction: Instruction): number[] {
  switch (instruction[0]) {
    case InstrType.I32Load:
      return [
        instruction[0],
        ...encodeLEB128U(instruction[1]),
        ...encodeLEB128U(instruction[2]),
      ];
    case InstrType.I32GtS:
    case InstrType.I32Eq:
    case InstrType.I32Add:
    case InstrType.I32Mul:
    case InstrType.I32DivU:
    case InstrType.Return:
    case InstrType.Drop:
      return [instruction[0]];
    case InstrType.I32Const:
      return [instruction[0], ...encodeLEB128S(instruction[1])];
    case InstrType.LocalGet:
    case InstrType.LocalSet:
    case InstrType.LocalTee:
    case InstrType.BrIf:
    case InstrType.Call:
      return [instruction[0], ...encodeLEB128U(instruction[1])];
    case InstrType.If:
      return [
        instruction[0],
        instruction[1],
        ...instruction[2].flatMap(encodeInstruction),
        0x05,
        ...instruction[3].flatMap(encodeInstruction),
        0x0b,
      ];
  }
}
```

Depending on the instruction, we encode the intermediates accordingly.

Encoding the function body is similar, the only difference is that we
need to know the length of the body in advance. Fortunately that's
pretty easy with our setup:

```
function encodeFuncBody(body: FuncBody) {
  const encodedBody = [
    ...encodeVector(
      body.locals,
      ([count, type]) => [...encodeLEB128U(count), type],
    ),
    ...body.instructions.flatMap(encodeInstruction),
    0x0b, // end opcode
  ];
  return [...encodeLEB128U(encodedBody.length), ...encodedBody];
}

function encodeCodeSection(bodies: FuncBody[]) {
  return encodeSection(10, bodies, encodeFuncBody);
}
```

And with this we're done with our encoding functions! Let's start
writing our binary search implementation. But first, let's write a TS
implementation:

```
function exampleBinarySearch(
  arr: number[],
  n: number,
  start: number,
  end: number,
): number {
  const mid = Math.floor((start + end) / 2);

  if (n == arr[mid]) {
    return mid;
  }

  if (mid == start) {
    return -1;
  }

  if (n > arr[mid]) {
    return binarySearch(arr, n, mid, end);
  } else {
    return binarySearch(arr, n, start, mid);
  }
}
```

There's the [classic overflow
bug](https://stackoverflow.com/questions/6735259/calculating-mid-in-binary-search)
in this implementation but meh. Also yes, the final `else` clause is
there for a reason.

Let's translate this to WebAssembly! Our function is going to take in
a pointer to an array (i32), the number we're looking for (i32), a
start index (i32), and an end index (i32). It'll return an index or -1
(i32). So the signature will be (i32, i32, i32, i32) => i32:


Our index space looks like:

0. `arr`
1. `n`
2. `start`
3. `end`

```
const binarySearchType = {
  paramTypes: [NumType.i32, NumType.i32, NumType.i32, NumType.i32],
  // return index
  returnTypes: [NumType.i32],
}
```

Now what should our local variables be? Looking at the TS
implementation, we could use a local variable for `mid` and `arr[mid]`:

```
const binarySearchLocals = [[2, NumType.i32]]
```

Adding on to our index space:

4. `mid`
5. `arr[mid]`

Let's start writing the code...

```
const binarySearchInstructions = [
```
First let's start with computing `mid`. We get `start` and `end`, pop them onto the stack...
```
  [InstrType.LocalGet, 2],
  [InstrType.LocalGet, 3],
```
Then we add them together
```
  [InstrType.I32Add],
```
And divide by 2
```
  [InstrType.I32Const, 2],
  [InstrType.I32DivU],
```
Now let's assign this result to `mid`
```
  [InstrType.LocalSet, 4],
```

Now let's work on getting `arr[mid]` assigned to a local. Let's get
`mid`:

```
  [InstrType.LocalGet, 4],
```
We take `mid`, multiply it by 4, and add `arr` to it, getting `arr + (mid * 4)`. The multiplication is because
each entry is 32 bits or 4 bytes long. Adding that to `arr` gets us a pointer to the element.
```
  [InstrType.I32Const, 4],
  [InstrType.I32Mul],
  [InstrType.LocalGet, 0],
  [InstrType.I32Add],
```
We then load from this address, i.e. we dereference
```
  [InstrType.I32Load, 0, 0],
```
And now we assign this to a local
```
  [InstrType.LocalSet, 5],
```

Alright let's work on the `if (arr[mid] == n) { return mid }` code. This is a really easy pattern
to translate to WASM because we can use the `br_if` instruction. We start by pushing on the value
we will return if the condition is true, i.e. `mid`
```
  [InstrType.LocalGet, 4],
```
Next let's push on the condition:
```
  [InstrType.LocalGet, 5],
  [InstrType.LocalGet, 1],
  [InstrType.I32Eq],
```

Alright now we call `br_if` with a relative depth[^1] of 0. This will
break out of our current block if the condition is satisfied, i.e. if
the topmost value on the stack is not 0. Since the current block is
the function block, this will cause an early return and since `mid` is
already on the stack, that'll be the return value.

[^1]: Relative depth refers to the number of nested blocks you want to
    break out of.

```
  [InstrType.BrIf, 0],
```
However if we don't break, we have this value on the stack that's useless, so we drop it
```
  [InstrType.Drop],
```
Alright let's repeat this pattern but for the `if (start == mid) { return -1 }` code. Pop on `-1`
```
  [InstrType.I32Const, -1],
```
Now check if `start == mid` and break if so
```
  [InstrType.LocalGet, 4],
  [InstrType.LocalGet, 2],
  [InstrType.I32Eq],
  [InstrType.BrIf, 0],
```
If not, drop the `-1`
```
  [InstrType.Drop],
```
Now let's do the final condition. Because the resulting value requires calling a function, we can't do the same `br_if` pattern. Instead let's use an `if` instruction. Again, we pop on the condition
```
  [InstrType.LocalGet, 1],
  [InstrType.LocalGet, 5],
  [InstrType.I32GtS],
```
And then we use an `if` instruction that returns an `i32`. If `n > arr[mid]` then we call `binarySearch(arr, n, mid, end)`
```
  [InstrType.If, NumType.i32, [
    [InstrType.LocalGet, 0],
    [InstrType.LocalGet, 1],
    [InstrType.LocalGet, 4],
    [
      InstrType.LocalGet,
      3,
    ],
    [InstrType.Call, 0],
  ],
```
Otherwise we call `binarySearch(arr, n, start, mid)`
```
  [
    [InstrType.LocalGet, 0],
    [InstrType.LocalGet, 1],
    [InstrType.LocalGet, 2],
    [InstrType.LocalGet, 4],
    [InstrType.Call, 0],
  ]]
];
```

Now let's wrap it up in a nice function and convert it to a byte array (`Uint8Array`)

```
function encodeModule() {
  return new Uint8Array([
    ...encodePreamble(),
    ...encodeTypeSection([binarySearchType]),
    ...encodeFuncSection([0]),
    ...encodeMemorySection([{ min: 1 }]),
    ...encodeExportSection([{
      name: "binarySearch",
      type: ExportType.Function,
      index: 0,
    }, { name: "memory", type: ExportType.Memory, index: 0 }]),
    ...encodeCodeSection([{
      locals: binarySearchLocals,
      instructions: binarySearchInstructions,
    }]),
  ]);
}
```

Here's a helper to write the array to memory:
```
function writeArrayToMemory(arr: number[], startIndex: number, memory: WebAssembly.Memory) {
  const buffer = new Uint32Array(memory.buffer);

  for (let i = 0; i < arr.length; i++) {
    buffer[startIndex + i] = arr[i];
  }
}
```

Now we instantiate:

```
const { instance } = await WebAssembly.instantiate(encodeModule());
```

Get the exports:

```
const { memory, binarySearch } = instance.exports;
```

Write to memory:

```
writeArrayToMemory([-4, -2, 0, 2, 8, 13, 21, 54], 0, memory as WebAssembly.Memory);
```

And now let's run binary search! The `array` is at address 0, `n` will
be `21`, our `start` will be 0 and our `end` will be 7. TypeScript
will complain cause it doesn't know our export is a function but
that's okay we can tell it to be quiet.

```
// @ts-ignore
console.log(binarySearch(0, 21, 0, 7));
```

And finally let's wrap this in a function to make it cleaner:

```
function wasmBinarySearch(arr: number[], n: number): number {
  writeArrayToMemory(arr, 0, memory as WebAssembly.Memory);
  // @ts-ignore
  return binarySearch(0, n, 0, arr.length - 1);
}
```

Congrats! You've successfully written binary search in WebAssembly with your own emitter!

# Sources

- https://hacks.mozilla.org/2017/02/a-cartoon-intro-to-webassembly/
- http://troubles.md/wasm-is-not-a-stack-machine/
- https://blog.scottlogic.com/2018/04/26/webassembly-by-hand.html
- https://webassembly.github.io/spec/core/
- https://github.com/WebAssembly/design/blob/main/BinaryEncoding.md
