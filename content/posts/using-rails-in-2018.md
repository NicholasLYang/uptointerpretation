---
title: "Using Rails in 2018"
date: 2018-03-03T17:06:43-05:00
draft: false
---

I'm a huge fan of Ruby on Rails. I've used it for numerous hackathons
and various other projects. The Stuyvesant Spectator
[website](https://stuyspec.com) is built with a Rails back end and the
previous iteration of this blog was my first Rails project. However,
as a shameless advocate of Rails, I get to hear a lot of inaccurate
ideas about Rails, such as:

- Isn't Rails dead? I don't know anybody who uses it
- Rails is too confusing
- Why should I use Rails when I can use Node?

I'd like to address these concerns and give some tips on how to learn
and use Rails in 2018.

First, let's address the elephant in the room: is Rails dead? Well,
no. Not by any reasonable standard. Many companies, such as GitHub,
Shopify, Netflix, Crunchbase, Soundcloud, Airbnb, etc. use
Rails. Furthermore, Rails development is still ongoing at a rapid
pace, with Rails 5.2 beta recently released.

However, when people usually ask this question, what they usually mean
is "I haven't heard anything about Rails from Hacker
News/Reddit/Medium". And yes, by that metric, Rails is pretty damn
dead. But before we start getting out the pallbearers, let's analyze
precisely why Rails is no longer talked about. Partially it's because
there's not a lot to talk about. Developer evangelists don't advertise
it anymore. After all, developers who use Rails don't need to be
convinced that Rails works. They just use it.

Second, is what I call the *bootcamp effect*. Basically, as companies
started to want Rails developers, bootcamps started teaching
Rails. This lead to a few major issues. First, there were a bunch of
Rails developers on the scene who did not understand the core ideas of
Rails (MVC, CoC), who did not understand Ruby, and basically did not
understand software development. As they started to write bad code,
they began to wonder why their applications weren't maintainable, why
their code wasn't good. And the easy target? Rails. I mean, just look
at the sheer amount of StackOverflow questions on Rails. It's fairly
clear that from most of the questions the inquirer doesn't actually
know Ruby. They just "know" Rails. Then, as new tools such as Node and
Golang came out, developers switched to them and suddenly their code
was so much better. Well...not quite. It's more likely they just got a
clean slate. Or perhaps they actually decided to learn the underlying
language instead of just relying on Rails' DSL. After that, the
bootcamp graduates started making overgeneralizations about Rails. Oh,
you know, the usual, "A is always better than B because..." or "Don't
use B because xyz". Partially true, but not completely.

Finally, there's the parts of Rails that legitimately are dead. For
instance, CoffeeScript and the Asset Pipeline. Yeah...while you could
totally use them in 2018, they're definitely out of date. I'll get
back to this, but front end development is definitely one area where
Rails has fallen behind.

To summarize, Rails is not dead, but simply no longer hyped up by the
*bootcamp effect* or by developer evangelism. There are plenty of
companies that use Rails and it's not going away anytime soon.

Though to be entirely fair, the *bootcamp effect* is not entirely the
fault of the bootcamp graduates. Rails is a pretty terrible
my-first-framework. Which brings us to the next comment: Rails is
confusing. Honestly...I don't disagree. Rails abstracts away a lot of
important aspects of developing a basic REST application, such as
routing, handling requests and accessing a database. For people who
already know how these aspects work, these abstractions are extremely
useful for developing flexible software quickly. But for a beginner?
Not great. Part of the reason Express is so likeable is because it's a
very easy mental model: Request -> Router -> Callback. Rails doesn't
have that easy mental mode. Not to mention, features like magical auto
imports can be extremely confusing at first. But again, Rails was not
designed for bootcamp graduates. It was designed for people who
understood Ruby, understood web development and wanted to
automate the boring parts.

Most importantly, Rails was designed to be extreeeeemly
opinionated. The whole concept of convention over configuration was an
attempt to simplify the structure of a web application by enforcing
certain standards. This makes total sense for an experienced developer
who understands concepts like Model-View-Controller, or aphorisms like
"fat models, skinny controllers". But for a beginner? All the little
opinionated aspects and strictness makes it really hard to just write
code.

So why should you use Rails in 2018? Well there's a few reasons. But
first, a demo.

<div class="video">
{{< youtube RFj_UPM2jXc >}}
</div>

Without a single line of code, I managed to add basic CRUD
functionality for articles. Again, terrible for beginners. But for a
developer who doesn't want to spend hours on controller boilerplate?
Amazing.

And this easiness doesn't just apply to code generation. Take GraphQL
for instance. In the Node world, writing GraphQL resolvers is a bit of
a nightmare. You have to manually write every single resolver for
every field. Want to get comments from an article model? Great! You
need to write a resolver to fetch the comments. Granted, there are
some shortcuts, like
[join-monster](https://github.com/stems/join-monster) and other
libraries. But it's just a damn pain. You need to find an ORM, connect
the ORM to the database, connect the ORM to the GraphQL library, then
write out the schema definitions, blah blah blah. In Rails? It's as
simple as adding the GraphQL gem, defining the fields in your object
type and writing a query. Want nested data? Define the types and
Rails' built in ORM, ActiveRecord takes care of the rest.

Again, quick demo:

<div class="video">
{{< youtube JPUUgL6Jr0s >}}
</div>

And if you do want more fine grained control of your fields, well you
can also just define a field using a Ruby lambda and have it resolve
to whatever you want.

You see, the main power of Rails is its' cohesiveness. Every piece of
the puzzle, from the web server to the ORM to the GraphQL gem
integrates perfectly. You don't have to string npm package after npm
package together just to get the basics down. You don't have to
navigate tutorials that only handle 4 out of the 10 packages you're
using. It just works.

So, without further ado, here are some tips on Rails development in
2018.

**Use the API mode**

Let's be honest here. Front end development is a bit of a runaway
train right now. The Angular hype was quickly overtaken by the React
hype which is now being challenged by the Vue hype. Gulp/grunt was
replaced by Webpack which might be replaced by Parcel. And while Rails
has tried to keep up with tools like Webpacker, it's just a lost
cause. Webpacker inherently ties your Rails application to webpack and
forces you to couple your SPA with your API. And for what? Rails views
and Rails controllers are coupled because they integrate
tightly. There's no equivalent reason to couple your front end SPA
with your back end.

Meanwhile, Rails shipped an API mode in 5.1 that strips out all the
front end code and leaves a simple, elegant REST API. This means you
get all the wonderful generators, tight integration and easy of use
that people love about Rails, minus the outdated asset pipeline.

**Try GraphQL Ruby**

I can't emphasize enough how great GraphQL Ruby is. It integrates so
well into the Rails ecosystem and it gives a better development
experience than anything I've found in Node for GraphQL.

**Learn Ruby**

I mean, this should go without saying. But Rails is not Rails without
Ruby. Not knowing Ruby is like trying to write React code while not
knowing JavaScript. It just doesn't work.


**Don't Listen To the Haters**

Just because all you hear is Node, Golang and Elixir, doesn't mean
other frameworks are bad or dying. Rails is a fantastic framework that
only keeps getting better over the years. Check it out, or give it
another chance.
