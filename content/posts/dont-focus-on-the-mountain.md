---
title: "Don't Focus On The Mountain; Focus On The Path"
date: 2020-07-26T22:46:18-04:00
draft: false
---

This story starts with structs. I had been making some progress with
my programming language, Saber. I had implemented functions, control
flow, local variables, basically enough to get a factoral function
written.

However Saber needed more than just the basics. I wanted structs,
strings, all the nice complicated types that we take for granted. For
that I needed dynamic allocation. For that I needed an allocator. But
wait, I actually need a garbage collector. Should it be a copy
collector or a mark sweep? What about reference counting?

Crap crap crap. This is too much. Allocators make me think of malloc
and implementing malloc was hard. I don't want to do that again. Plus
how do I hook the garbage collector into the stack if I can't traverse
it?  How can I get this to work with closures?

All of these questions are circling me and I don't have answers. If I
don't have answers, I can't start coding. What do I do?

Well, usually I give up. I go work on something else for a while. I
write some JavaScript. I watch some movies.

Then, some time or another, I come back and I look at the code. And
what do I find? A glaring bug. Something small and dumb like "oh you
didn't implement type promotion properly".

I fix it. The codebase suddenly looks a little less daunting.

Next I take a look at the struct problem. I realize, wait, this isn't
that bad. I don't need to implement a garbage collector just yet. All
I need to do is something dumb and easy that gets the job done. I
write the simplest possible allocator in the world. Sure, it's done
purely in WebAssembly, but when we're getting this simple, does that
even matter?

**Note:** All code here is copied as it was, bugs and all. It's also
    without any extra explanation, as the point isn't really the code
    as much as the journey.

```
        // Size of record in bytes
        let record_size: i32 = (4 * fields.len()).try_into().unwrap();
        let mut opcodes = Vec::new();
        opcodes.push(OpCode::I32Const(record_size));
        // Global 0 is current heap pointer
        opcodes.push(OpCode::GetGlobal(0));
        // Add record size to current memory size
        opcodes.push(OpCode::I32Add);

        // Get current memory allocated in pages
        opcodes.push(OpCode::CurrentMemory);
        // 64 * 1024 = 65536 bytes per page
        opcodes.push(OpCode::I32Const(65536));
        opcodes.push(OpCode::I32Mul);

        // Currently on stack: [heap_ptr + sizeof(record), pages_allocated * PAGE_SIZE]
        // If heap_ptr + sizeof(record) > pages_allocated * PAGE_SIZE...
        opcodes.push(OpCode::I32GreaterUnsigned);
        opcodes.push(OpCode::If);
        opcodes.push(OpCode::Type(WasmType::Empty));
        // ...we call GrowMemory
        // Right now we only grow in increments of one page. In the future we can grow
        // beyond that to store large values
        opcodes.push(OpCode::I32Const(1));
        opcodes.push(OpCode::GrowMemory);
        // Error handling. If the result is equal to -1, we trap
        opcodes.push(OpCode::I32Const(-1));
        opcodes.push(OpCode::I32Eq);
        opcodes.push(OpCode::If);
        opcodes.push(OpCode::Type(WasmType::Empty));
        opcodes.push(OpCode::Unreachable);
        opcodes.push(OpCode::End);
        opcodes.push(OpCode::End);
```

It uses global variables, it unwraps Results, it doesn't free memory,
it doesn't do any halfway decent error handling. It isn't even a
function, just some inlined code to allocate. But it works. All I need
is some glue code for field accesses:

```
        let mut opcodes = self.generate_expr(&**lhs)?;
        let lhs_type = lhs.inner.get_type();
        if let Type::Record(fields) = &*lhs_type {
            let field_position = fields
                .iter()
                .position(|(field_name, _)| *field_name == *name)
                .ok_or(GenerationError::NotImplemented {
                    reason: "Field doesn't exist: Should be caught by typechecker",
                })?;
            let field_wasm_type = self
                .generate_wasm_type(&fields[field_position].1)?
                .unwrap_or(WasmType::Empty);
            let offset: u32 = (4 * field_position).try_into().unwrap();
            let load_code = match field_wasm_type {
                WasmType::i32 => OpCode::I32Load(2, offset),
                WasmType::f32 => OpCode::F32Load(2, offset),
                WasmType::Empty => OpCode::Drop,
                _ => return Err(GenerationError::NotReachable),
            };
            opcodes.push(load_code);
            Ok(opcodes)
        } else {
            Err(GenerationError::NotReachable)
        }
```

I write a test Saber program

```
let foo = \() => {
  let p = Person {
    age: 10
  };
  print(p.age);
}
```

It works!

Great, what's next? I factor out the allocator into a simple function:

```
    // Generates very simple allocator scheme
    // Checks if there's enough space and if not,
    // it allocates more
    fn generate_allocator(&self, memory_size: i32) -> Vec<OpCode> {
        let mut opcodes = Vec::new();
        opcodes.push(OpCode::I32Const(memory_size));
        // Global 0 is current heap pointer
        opcodes.push(OpCode::GetGlobal(0));
        // Add record size to current memory size
        opcodes.push(OpCode::I32Add);

        // Get current memory allocated in pages
        opcodes.push(OpCode::CurrentMemory);
        // 64 * 1024 = 65536 bytes per page
        opcodes.push(OpCode::I32Const(65536));
        opcodes.push(OpCode::I32Mul);

        // Currently on stack: [heap_ptr + sizeof(record), pages_allocated * PAGE_SIZE]
        // If heap_ptr + sizeof(record) > pages_allocated * PAGE_SIZE...
        opcodes.push(OpCode::I32GreaterUnsigned);
        opcodes.push(OpCode::If);
        opcodes.push(OpCode::Type(WasmType::Empty));
        // ...we call GrowMemory
        // Right now we only grow in increments of one page. In the future we can grow
        // beyond that to store large values
        opcodes.push(OpCode::I32Const(1));
        opcodes.push(OpCode::GrowMemory);
        // Error handling. If the result is equal to -1, we trap
        opcodes.push(OpCode::I32Const(-1));
        opcodes.push(OpCode::I32Eq);
        opcodes.push(OpCode::If);
        opcodes.push(OpCode::Type(WasmType::Empty));
        opcodes.push(OpCode::Unreachable);
        opcodes.push(OpCode::End);
        opcodes.push(OpCode::End);
        opcodes
    }
```

Then, ah crap, I need strings. Strings are complicated though. There's
Unicode with all of its complex stuff like graphemes, code points,
etc. And how do I print? WebAssembly only lets me transmit simple data
types to JavaScript like ints or floats.

Getting discouraged again...

Another break. I do some research, try to look at other projects like
wasm-bindgen. Can't really figure it out.

Then a breakthrough. After reading the wasm spec some more, I realize
that I can run a WebAssembly module with a memory object I
created in JavaScript. That means I can write JavaScript and access
the same memory as my WebAssembly code!

Ah okay things are getting easier. Rust provides a way to convert
strings into bytes. All I need to do is allocate enough space and
write the string into that space. Oh and tack on a length since we're
not doing C style strings.

```
    Value::String(s) => {
        let mut bytes = s.as_bytes().to_vec();
        let raw_str_length: i32 = bytes.len().try_into().expect("String is too long");
        // Make bytes a multiple of 4 for ease of use;
        while bytes.len() % 4 != 0 {
            bytes.push(0);
        }
        // We tack on 4 more bytes for the length
        let mut opcodes = self.generate_allocator(raw_str_length + 4);
        // Pop on global 1 as return value (ptr to str)
        opcodes.push(OpCode::GetGlobal(1));
        opcodes.push(OpCode::GetGlobal(1));
        opcodes.push(OpCode::I32Const(raw_str_length));
        opcodes.push(OpCode::I32Store(2, 0));
        for b in bytes.chunks_exact(4) {
            // Increment global 1 by 4 bytes
            opcodes.push(OpCode::GetGlobal(1));
            opcodes.push(OpCode::I32Const(4));
            opcodes.push(OpCode::I32Add);
            opcodes.push(OpCode::SetGlobal(1));
            let packed_bytes = ((b[0] as u32) << 0)
                + ((b[1] as u32) << 8)
                + ((b[2] as u32) << 16)
                + ((b[3] as u32) << 24);
            opcodes.push(OpCode::GetGlobal(1));
            opcodes.push(OpCode::I32Const(packed_bytes as i32));
            opcodes.push(OpCode::I32Store(2, 0))
        }
        Ok(opcodes)
```

Then to read the string, I can access the same memory from JavaScript

```
const printString = memArray => ptr => {
    const len = memArray[ptr];
    const out = new Uint32Array(len/4);
    for (let offset = 0; offset < len/4; offset++) {
        const i = offset + ptr + 1;
        out[offset] = memArray[i];
    }
    const decoder = new TextDecoder();
    console.log(decoder.decode(out));
}
```
Turns out, JavaScript comes with a decoder that can take bytes and
turn it into a string. Huh, this is a lot easier than I thought!

Okay okay, but now I need to actually write the garbage
collector. This is the hard part.

More discouragement...

Wait, before I write the garbage collector, I should just port the
allocator to JS. Should be a lot easier now.

```
const alloc = memory => size => {
    // size + 4 because we gotta tack on a block length
    const alignedSize = ((size + 4) + 3) & ~0x03;
    let memArray = new Uint32Array(memory.buffer);
    let ptr = 0;
    while (ptr < memArray.length) {
        const len = memArray[ptr];
        // If length is 0, block is not initialized. We can assume everything
        // from here to end of array is free
        // We multiply (memArray.length - ptr) by 4 because they're dealing with
        // a 32 bit array
        if (len === 0) {
            if ((memArray.length - ptr) * 4 > alignedSize) {
                // Or with 1 to indicate allocated
                memArray[ptr] = alignedSize | 1;
                return (ptr + 1) * 4;
            } else {
                memory.grow(Math.max(alignedSize/PAGE_SIZE, 1));
                memArray = new Uint32Array(memory.buffer);
                memArray[ptr] = alignedSize | 1;
                return (ptr + 1) * 4;
            }
        // If the block is allocated, we move on
        } else if ((len & 1) === 1) {
            ptr += len - 1
        // If the length is big enough, we allocate the block
        } else if (len > alignedSize) {
            memArray[ptr] = len + 1;
            return (ptr + 1) * 4;
        } else {
            ptr += len;
        }
    }
    memory.grow(Math.max(alignedSize/PAGE_SIZE, 1));
    memArray = new Uint32Array(memory.buffer);
    memArray[ptr] = alignedSize | 1;
    return (ptr + 1) * 4;
}
```

Hmm that's a lot better. Much easier to work with.

Alright but now I need to write the GC for real. What's the simplest
possible GC to write? I got it! Reference counting. That's pretty easy.

But for references to work, I need to know what's a reference at
runtime. If I have to deallocate a struct, I need to know which fields
are references to other data so that I can decrement their counts.

Okay okay, this is doable. I can create a type id, embed it in the
struct, then generate a lookup table that the garbage collector uses
to figure out the fields.

That way, the garbage collector can see a type id of 14, go to its lookup table:

```
export const typeInfo = {14:[true]}
```

...then realize hey, 14 corresponds to a struct with 1 field that is a reference[^1]

[^1]: Yes yes, this is very inefficient. I could pack it into a bitmap
    but whatever

Then it can use that info to collect properly.

Wow, this is looking better!

Alright, now I need to write the actual GC. Deep breath...

```
// Takes in size in bytes
export const alloc = (memory: WebAssembly.Memory) => (size: number) => {
    // size + 8 because we gotta tack on a block length and refcount
    const alignedSize = ((size + 8) + 3) & ~0x03;
    let memArray = new Uint32Array(memory.buffer);
    let ptr = 0;
    while (ptr < memArray.length) {
        const len = memArray[ptr];
        // If length is 0, block is not initialized. We can assume everything
        // from here to end of array is free
        if (len === 0) {
            // We multiply (memArray.length - ptr) by 4 because they're dealing with
            // a 32 bit array
            if ((memArray.length - ptr) * 4 > alignedSize) {
                // Or with 1 to indicate allocated
                memArray[ptr] = alignedSize | 1;
                memArray[ptr + 1] = 1;
                return (ptr + 2) * 4;
            } else {
                memory.grow(Math.max(alignedSize/PAGE_SIZE, 1));
                memArray = new Uint32Array(memory.buffer);
                memArray[ptr] = alignedSize | 1;
                memArray[ptr + 1] = 1;
                return (ptr + 2) * 4;
            }
            // If the block is allocated, we move on
        } else if ((len & 1) === 1) {
            ptr += len
            // If the length is big enough, we allocate the block
        } else if (len > alignedSize) {
            memArray[ptr] = len + 1;
            memArray[ptr + 1] = 1;
            return (ptr + 2) * 4;
        } else {
            ptr += len;
        }
    }
    memory.grow(Math.max(alignedSize/PAGE_SIZE, 1));
    memArray = new Uint32Array(memory.buffer);
    memArray[ptr] = alignedSize | 1;
    memArray[ptr + 1] = 1;
    return (ptr + 2) * 4;
};

export const dealloc = (memory: WebAssembly.Memory) => (bytePtr: number) => {
    let memArray = new Uint32Array(memory.buffer);
    const ptr = (bytePtr - 8)/4;
    memArray[ptr + 1] -= 1;
    if (memArray[ptr + 1] === 0) {
        // Remove allocated flag
        memArray[ptr] = memArray[ptr] ^ 1;
        const typeId = memArray[ptr + 2];
        if (typeId === STR_INDEX) {
            return;
        }
        if (!(typeId in typeInfo)) {
            throw new Error(`RuntimeError: Invalid type for struct. Most likely internal error`);
        }
        const structInfo = typeInfo[typeId];
        structInfo.forEach((isRef: boolean, index: number) => {
            if (isRef) {
                const ptr2 = memArray[ptr + 3 + index];
                dealloc(memory)(ptr2);
            }
        });
    }
}

export const clone = (memory: WebAssembly.Memory) => (bytePtr: number) => {
    const ptr = bytePtr/4;
    const memArray = new Uint32Array(memory.buffer);
    if (memArray[ptr - 1] < 1) {
        throw new Error(`RuntimeError: Invalid refcount for struct. Most likely internal error`);
    }
    memArray[ptr - 1] += 1;
    return ptr;
}
```

Huh, that wasn't too bad. But how do I hook it up to the compiler?
Well...I'm already calling alloc when I need to allocate stuff, so
that's fine. I have a dealloc function that I need to call when stuff
goes out of scope. Well scope is tied to blocks which are
expressions. I suppose I could just emit dealloc calls at the end of a
block.

For that I'll need a way to iterate through the items that are in
scope. That's not too hard:

```
pub fn get_scope_entries(&self, scope: usize) -> impl Iterator<Item = &(Name, SymbolEntry)> {
    self.scopes[scope].symbols.iter()
}
```

Then I need to see if those items are references and if so, emit a
dealloc call with the pointer:

```
let entries = self.symbol_table.get_scope_entries(*scope_index);
for (_, entry) in entries {
    if let EntryType::Var { var_type, index } = &entry.entry_type {
        if is_ref_type(*var_type) {
            opcodes.push(OpCode::GetLocal((*index).try_into().unwrap()));
            opcodes.push(OpCode::Call(DEALLOC_INDEX));
        }
    }
}
```

Some bug fixes and boom, I have a garbage collector!

Well, somewhat. I still need to hook up clone when we create two
references to the same resource. I still need to break cycles. I need
to figure out how to add nested functions and closures. But dammit,
I'm still proud of my code.

For some people this post may seem dumb or overly tedious. I can
certainly hear them saying: "It's only a garbage collector, just write
the damn thing"

But for many others that isn't true. Stuff like writing a garbage
collector or writing a compiler sounds intimidating. I know it does to
me.

What I've learned from this process:

- Don't think too much about what you're making. You can't become
consumed with worry because you're writing a garbage
collector/compiler/runtime.

- Stay close to the code. Don't worry about the high level
  ideas. Solve it one problem at a time.

- Code doesn't have to be great the first time around. Better to have
  something kinda working rather than be stuck for months.

- You'll come up with solutions that seem obvious in retrospect but
  were totally not.

Working on some projects is like standing at the foot of a
mountain. It's very easy to spend your time staring at the mountain,
trying to figure out how to climb that part or work around this
obstacle. Don't do that. Focus on the path in front of you. Focus on
your next step. Then start walking.

**Addendum:** This post isn't claiming that planning or thinking about
the large ideas is bad. You should do that. But at a certain point you
need to just write code. Problems that seem unsurmountable can open up
with some programming. Everything is doable with enough patience and
incremental progress.
