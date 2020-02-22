---
title: "We Get It"
date: 2019-11-09T04:04:51-05:00
draft: true
---

Spend enough time procrastinating on reddit, you inevitable end up
reading the same links again and again and *again*. One of the common
links I see are the ones that complain about JavaScript. There's
[wat](https://www.destroyallsoftware.com/talks/wat), the [tried and
tired ecosystem
article](https://medium.com/commitlog/the-internet-is-at-the-mercy-of-a-handful-of-people-73fac4bc5068)
and many many more.

To all of those articles, I just want to say:

**We get it**

We get it, JavaScript sucks. It's insecure, it has weird features, npm
is out of control, etc. Heard it all. Yep, even that one. Thanks for
your feedback.

Now what are you gonna do about it? Complaining about valid issues is
great! But ultimately you need to stop complaining and start
fixing. Which is where a lot of these articles stop[^1].

[^1]: I'd like to exclude Gary Bernhardt here because he's awesome and
    because that talk was clearly meant in jest.

And oh boy are there a lot of projects working on fixing
JavaScript. For one, you can make TC39 proposals to add new features
to the language. You may say the damage has been done, but I disagree!
Features such as triple equals (===), arrow functions (=>) and
optional chaining (?.) have done a lot to improve the JavaScript
experience. I'd go as far as to say that if you don't know ES6+,
you're not really qualified to judge JavaScript. That's like judging
Java pre-generics, or C++ pre-templates or Go, uhh, well you get my
point.

Another great place to start is contributing to tooling. Whether it's
[TypeScript](https://github.com/microsoft/typescript),
[npm](https://github.com/npm/cli) or whatever tools you use, you can
help make the JS ecosystem better. A great innovation that npm just
pushed is automatic dependency screening. That way you can see if your
project has vulnerabilities whenever you run npm.

Or hell, if you just hate JavaScript, why not work on getting another
language into the browser? With WebAssembly, you can compile a number
of languages to the browser. Go, Rust, C# and many others are already
targeting the browser.

You're not obligated to help the JS community. But if you're so
passionate that you're screaming from the rooftops about how
JavaScript sucks, maybe spend that effort helping make it suck less.

However, while I will admit that parts of JavaScript truly do suck
(semicolon insertion, automatic globals, etc.), I don't think
JavaScript gets enough credit for the stuff it does get right. For
one, arrow functions are totally awesome! The syntax is concise and
clear, and totally makes me want to strip out all the
fun/func/fn/function keywords in other languages.

Also, package management is pretty nice now! npm installs locally by
default. We have lockfiles and dependency auditing. It's pretty great!
Python's whole pip install is global by default doesn't look as great
anymore[^1].

[^1]: And don't give me the whole "just remember to turn on
    virtualenv" shtick. That's like saying "just remember the types in
    your code". Totally missing the point.

It's also great having a lightweight object/struct type with
destructuring. Almost feels antiquated to have to put quotes around
keys in a Python dict.
