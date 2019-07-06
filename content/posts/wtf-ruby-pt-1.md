---
title: "Wtf Ruby Pt 1: DSL Dyslexia"
date: 2018-07-08T21:41:58-06:00
draft: true
---

## Preface
This summer I've been working on the Ruby language adding type
annotations to the grammar. However, the Ruby codebase is rather
challenging. There's not a whole lot of documentation (at least in
English), there's a fair amount of C preprocessor hacking, and there's
a few tricky scripts attached. I've decided to document these
challenges in this blog. I hope that they're useful to anybody who
decides to work on the Ruby codebase, and I also hope that I can use
these notes to improve the codebase myself. If any Ruby maintainers
read this, I apologize in advance for any seemingly critical
language. I totally respect and appreciate your contributions to Ruby
and to the open source community as a whole. Please understand that
these criticisms are coming from a place of respect and learning.


## DSL Dyslexia

Today I'm going to talk about Domain Specific Languages (DSLs),
specifically the one in Ripper. Ripper, for those who don't know, is a
Ruby library that consists of a Ruby parser. Ripper allows developers
to literally "rip apart" the Ruby code into tokens, a process called
lexing, or into an Abstract Syntax Tree (AST), a process called
parsing. An AST is essentially the Ruby code turned into a general,
homogeneous form independent of syntax. 

Now the way that Ripper is generated is rather interesting. Instead of
rewriting the parser in Ruby, the developers of Ruby opted to take the
existing Ruby parser and extend it to also work with Ripper. This
makes the most sense, as Ruby has a rather complex grammar with a lot
of little details. It would have been far too time consuming and
painful to recreate the parser in Ruby. Furthermore, if new grammar
rules were to be added, two parsers would have to be updated.

So how did the developers extend the Ruby parser? Well they
implemented a DSL. Domain Specific Languages are a really interesting
form of computer languages. They are built for very specific usecases,
such as quickly writing data models, or defining objects in a graphics
engine, as opposed to general purpose languages like Python, Ruby, C,
etc. They can allow for immense expressiveness in a clear, concise
manner. However, if they are not well documented or understood, they
can be confusing, even alien. The Ruby community as a whole tends to
use DSLs a fair amount. For instance, Ruby on Rails uses a DSL to help
programmers write web applications with minimal boilerplate.

But back to the Ruby parser. The way that the developers implemented
this DSL was by adding comments into the Ruby parser file,
`parse.y`. While this is a completely understandable solution, as a
result, a fair bit of complexity was introduced. You see, the Ruby
parser is created using Bison, Bison being a very popular parser
generator. Parser generators allow programmers to create a parser by
simply defining some grammar rules in...you guessed it, a DSL. So by
adding a second DSL through comments, the developers put two DSLs in
one file. That won't be confusing, right?

Which brings us to this past day. I decided to read the code in
`ext/ripper/tools`, where the Ripper DSL is parsed. There's a fun
bit of code in `generate.rb` where it takes the Ripper annotations:
```
    /*% ripper[brace]: rb_ary_new3(1, get_value($1)) %*/
```

and, using a regular expression, or regex (`%r</\*%
*ripper(?:\[(.*?)\])?: *(.*?) *%\*/>`) splits them into:

    $1 = "brace"
    $2 = "rb_ary_new3(1,get_value($1))"
	
This is done implicitly in Ruby, as `$n` (where `n` is an integer) are
magic global variables that refer to the nth match in the regular
expression. Note that it's `rb_ary_new3(1...`, not $1. I actually had
to double check that I didn't accidentally delete the dollar sign. I
don't know why it's there.

Anyways, what's interesting is that `generate.rb` initializes a DSL
object that takes these two variables, well, `$2` and `($1 ||
"").split(",")` (which is probably an attempt to put $1 in an
array?). The DSL object's constructor, as defined in `dsl.rb`,
proceeds to do a few things which I'll get to and then evaluates
`$2`. Kinda like this:

    eval("rb_ary_new3(1, get_value($1))")

Therefore all the annotations in parse.y should be valid Ruby!
But...`$1-$9` aren't defined. Well, actually...they are. Let's take a
look at the code above this:

```ruby
    # create $1 == "$1", $2 == "$2", ...
    re, s = "", ""
    1.upto(9) do |n|
      re << "(..)"
      s << "$#{ n }"
    end
    /#{ re }/ =~ s
```
At first I didn't really pay attention to this code. But the comment
caught my eye the second time around. So I read it a little more
carefully and realized that this code is automatically generating
values for $1-$9!

How, pray tell is it doing that? Well one side effect of `$n` being
magic global variables is that they cannot be directly
mutated. Therefore something like `$1 = 'mike'` doesn't
work. Therefore, to get around this issue, the developers created this
hack. 

Basically, we build up to strings, `re` and `s`. `re` is actually a
regular expression, one of the form 
`/(..)(..)(..)(..)(..)(..)(..)(..)(..)(..)/`, basically 10
`(..)` in a row. The string `s` on the other hand consists of
`"$1$2$3$4$5$6$7$8$9"`. The final operation is a match operation,
namely it matches `re` against `s`. Now, at first glance, this appears
to do nothing. There's no apparent side effects and no variables bound
except `re` and `s`, which are never used again. EXCEPT, if you
remember the meaning of `$n`. You see, in regular expressions, `(..)`
means match exactly two characters, that's it. So when you run `re`,
basically 10 of these `(..)`, against `s`, you match two characters 10
times. For instance, the first match, which will be stored in `$1`
is...yep `"$1"`, the second match, stored in `$2` is `"$2"`, and so
on.

To recap, this code generates a regular expression and generates a
string to implicitly bind 10 global variables. Pretty gnarly.

But that's not the end of it! After that, I started to wonder how in
the world the `eval` function was working. After all, these functions
aren't naturally in the Ruby scope, there aren't any other files
imported in `dsl.rb`, and there isn't a binding passed along to
eval. Also, something weird would happen. I would try to print out the
output of the function call like such:

    puts(rb_ary_new3(1, get_value($1)))

Only to get `rb_ary_push($1, get_value($3))` as my output. Kinda weird...

So I scrolled down a little and found to my horror: `def
method_missing`. To quote Gary Bernhardt,
[wat](https://www.destroyallsoftware.com/talks/wat). 

For those who don't know, `method_missing` is a rather infamous
function in Ruby. When you attempt to call a function that doesn't
exist in Ruby, Ruby normally raises a `MethodError`, which is fairly
standard, rather sensible, basically an overall normal thing to do. But
the demented, insane geniuses that are the Ruby developers decided
that this isn't flexible enough, so they gave an alternative:
`method_missing`. `method_missing`, when defined, allows you to
implicitly catch the error. Instead of raising a `MethodError`, Ruby
calls your `method_missing` and passes it the name of the method you
tried to call, along with any potential arguments you might
passed. This is NOT advised. Most Ruby style guides heavily discourage
`method_missing`. Granted, the developers of Ruby are not most Ruby
programmers. But still, it's not exactly common behavior.

Anyways, this `method_missing` does a few different things. For
instance, if your DSL annotations end in a bang, i.e. they match this
regex `/!\z/`, then it outputs this in ripper.y:

    {VALUE v1,v2;v1=$1;v2=dispatch1(assoclist_from_args,v1);$$=v2;}

or, if they begin with an id, you just get the event back, as something like:

    {$$=idCOLON2}
	
I can't actually find any example of this in `parse.y`, but we'll
assume it's used somewhere. And for the rest you basically just get
the same function that you started with (the value of `$2`). That's why
the `puts(rb_ary_new3(1, get_value($1)))` gives `rb_ary_push($1,
get_value($3))` as the output. This gives the following in ripper.y (the output of this DSL):

    $$=rb_ary_push($1, get_value($3));

Oh yeah, and if you happen to have an annotation called `opt_event`,
that actually does call a method called `opt_event`. It basically does
something similar with a few minor changes (I believe a nil check and
some other stuff).

In conclusion, this has been an excellent exercise in interesting Ruby
code. In all seriousness, I wouldn't really advise writing Ruby code
in this manner, but I have a serious amount of respect for the person
who wrote this code and thought of it in this manner. I certainly
would have written it in a more \*ahem\* pedestrian manner. 

Indeed, I might decide to attempt a refactor of this code, or at the
very least some heavy documentation. Right now I'm holding off on
refactoring simply because I don't know the whole picture and I don't
know whether my refactoring attempts would actually fix anything, or
if it would just miserably fail due to some detail of which I'm not
aware. The developers of Ruby are smart people and I can't claim to
know better than them. 

In the next post I'll explain where all those weird `dispatch`
functions came from and what they're doing.

Hope this has been helpful!

Nicholas
