---
title: "Create Your Own Reality"
pubDate: "Oct 28, 2018"
draft: false
---

I've been thinking about closures recently. Closures are really
neat. And they're not particularly hard to understand, at least at a
summary level. But they're often misunderstood because it's hard to
understand *why* they're so powerful and why they're so useful. For
that, you need context. So let's get into some Γ[^1]!

[^1]: Lame type theory joke. Please ignore.

Closures stem from a simple problem: How do I hide information, while
still providing some form of access to it? Languages like C have two
ways of allocating data: stack allocation and heap allocation. Stack
allocation is very simple: you call a function, it allocates a
predetermined amount of space (called a frame), and once the function
is done being called, you automatically roll back up the stack and
deallocate the memory. What's nice about stack allocation is that it's
very hard to mess up. You can't forget to free memory, you can't
access it once it's been called. Most importantly, when you call into
a library, you know what's going to happen: stack frame gets
allocated, stuff happens, stack gets rolled back up. The issue comes
with sharing data that A. you don't know the size of at compile time,
and B. persists beyond a function call. For this, you need heap
allocation.

Heap allocation is a little messier. You manually call some sort of
allocater, in C stdlib it's called `malloc`, with a requested amount of
memory, then when you're done, you manually call free and it frees the
memory. The issue comes when you have a library or some unknown code
that operates on the heap. Namely, you don't know what's going to
happen! For all you know, the library could overwrite the memory. Or
it could free it. Or just mess it up in some way. You can think of it
as sending your memory into big, mysterious monolith that may or may
not destroy it.

So what's a smart developer like yourself to do? You create what's
called a closure. A closure stems from a simple idea: functions are
values. This may seem a little weird, depending on the languages
you've used. Nonetheless, bear with me. The first corollary to this
simple idea is that functions can be returned from functions[^2]. Again, a
little odd. But what's the big deal here? Well let's take a look at
this piece of code:

```
function foo() {
  var a = 10;
  return function() {
    console.log(a);
  }
}
var fn = foo();
fn();
``` 


What does this print? There's two (somewhat) reasonable options:
`undefined` (I'd also accept NullPointerException or an "a is
undefined" error), or `10`. The first one is tempting, especially
according to the rules of stack allocation. After all, once `foo` has
been called, the stack should be rolled up and the variables
deallocated. Even if we assign the function that `foo` returns to a
new variable, the function is trying to call a variable that no longer
exists. And if this were true, we'd basically have function-values
that are very much like C function pointers---basically just variable
GOTOs. But what if we choose the other option? What if we break the
rules here[^3]. What if we change our reality?

[^2]: A second corollary is that functions don't necessarily have to
    have names. This isn't as important.
	
[^3]: A good question to ask is what you need to implement in order
    to break these rules.

The answer here is you get an innovation. You get something that
changes programming forever. Seem overdramatic? Sure, but it's also
true. The first big deal is information hiding.

Imagine we have a dictionary that associates names to birthdays:

```
var birthdays = {
	"Alexei": "12 August 1904",
	"Olga": "15 November 1895",
	"Anastasia": "18 June 1901",
	"Nicholas": "18 May 1868"
};
```

Now imagine we pass it to a function `mysteriousMonolith` that we
don't know. This could be a library function or code written by
coworkers or even *gasp* code you copied from StackOverflow.

```
// Nobody knows what goes on in there...
mysteriousMonolith(birthdays);

// Could print the birthdays, nothing, 
// or something completely different.
console.log(birthdays)
```

For all we know, the dictionary could have been eaten by the aliens
inside the monolith. Or it could be turned into a gigantic space baby[^4].

[^4]: https://www.youtube.com/watch?v=AXS8P0HksQo&t=1s

Not ideal. Well, with closures we can do the following:

```
function createBirthdayLookup(birthdays) {
	return function(name) {
		birthdays[name];
	}
}


var lookup = createBirthdayLookup(birthdays);
```

Now, instead of passing our precious dictionary of birthdays to the
mysterious monolith, we can pass a function. 


Now, we can sleep easy by simply passing a closure:

```
mysteriousMonolith(lookup)
// Guaranteed to print the same as before
console.log(birthdays);
```

But this is only the beginning. The second big deal is where it gets
really good.

```
function createPerson(name, age, height) {
	
	function getName() {
	  return name;
	}
	
	function getHeight() {
	  return height;
	}
	
	function getAge() {
	  return age;
	}
	
	return function(msg) {
	  switch(msg) {
		  case "getName":
			  return getName();
			  break;
		  case "getHeight":
			  return getHeight();
			  break;
		  case "getAge":
			  return getAge();
			  break;
	  }
	}
}
```

This may seem familiar[^5]. Indeed, we can think of this function,
`createPerson`, as creating an object! Now you're probably wondering,
"This can't be an object! Where's the class? Where's the
inheritance?". And sure, in modern day programming, object oriented
programming is expected to follow certain idioms. Objects are
instantiated using classes, which are usually signified by a nice
`class` keyword. The class is expected to inherit from a different
class, etc, etc. But at its core, object oriented programming doesn't
have to have inheritance or fancy keywords. At their core, objects are
simply closures that respond to messages. And besides, your class?
Just a plain ol' function called `createPerson`.[^6]

[^5]: Yeah yeah, I know, there are more idiomatic ways of doing this, but I
	wanted to be explicit.
		
		
[^6]: Fun exercise to the reader: add inheritance to closure-based
    classes. I don't quite know how to do this either, so I'm
    interested to see what people do!

The really really cool part about this is that closures aren't
something built into the computer. They're not some physical law or
some reaction discovered. They were an idea that people
invented. People decided to break the laws of the stack. Except...the
laws of the stack aren't built into the computer either. In fact, the
computer has no idea about all of these mumblings about stack and heap
and their differences. To the computer, memory is just a big array. A
really really big array, but that's about it. 

So who does know about these ideas? The language! Whether it's a
compiler, an interpreter or a compiler-interpreter, whatever
implements the underlying language is the one to know all of these
concepts and translate them into machine speak. 

And that's what's so damn cool about language design. In the end,
**you're creating your own reality.**[^7] Want a world where closures
exist? Build it. Want a world where memory safety is guaranteed? Go
ahead and build it. Want a world where variables are hoisted and
`this` behaves exactly the opposite of what you'd expect? Sure thing
Mr. Eich! Go right ahead and build it.

[^7]: Not to mention, this is a pretty badass way of solving your
    problems. Got a problem? Make a reality where it doesn't
    exist. How very Thanos of you.

So maybe the next time you have a problem that you're struggling to solve,
don't just think about how to solve it. Think about how to change your
reality.


