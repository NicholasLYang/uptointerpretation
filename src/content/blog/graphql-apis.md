---
title: "Graphql APIs"
pubDate: "Jun 23, 2018"
draft: false
---

I've been using GraphQL for almost a year now. I've used it in a few
small hackathon projects, along with the [Stuyvesant Spectator
site](https://stuyspec.com). I like using GraphQL because it's rather
easy to query complicated relations, especially for React
components. On StuySpec it allowed us to split the fetching logic into
smaller, more granular queries, which got rid of the large up front
fetch that we previously had (not that you need GraphQL to solve that,
you can easily use query params). That being said, I'll preface this
post by saying that I'm not necessarily a GraphQL evangelist. GraphQL
has its pros and cons, just like any other tool.

When I write GraphQL APIs, I use Rails with graphql-ruby. I chose this
combination because I was converting the Stuyvesant Spectator's Rails
REST API to GraphQL and I didn't want a full rewrite. Also, I do like
Rails and tend to reach for it in need.

After a few months, I really started to like graphql-ruby. For one,
it's damn easy to use. I basically write a few GraphQL ObjectType
definitions, add a few queries, and the magic of Rails takes care of
the rest. Since the ObjectTypes are basically using the Rails models
underneath the hood, all the usual Rails associations still work. If I
have a one to many relation on Articles to Comments (an Article
`has_many` Comments), all I need to do is specify a `comments` field
on the Article ObjectType that has a type of an array of Comment
ObjectTypes. Basically, ActiveRecord came to the rescue.

However, not everything in the Rails + graphql-ruby stack is
great. For instance, there's no real good solution to
authentication. I had to manually work around Devise Token Auth with
some semi messy code for StuySpec. And authorization isn't great
either. The official solution for graphql-ruby requires a Pro
membership which costs 900 dollars a year. Yeah...no. Other solutions
exist, but they're not that mature.

But these are the minor problems. The elephant in the room came to me
when I was writing my 2nd or 3rd GraphQL on Rails API. I looked at my
code and I recognized barely anything from old school Rails. I don't
use Rails views. I use exactly one controller (GraphqlController). I'm
not using RESTful resources. Okay, sure, I'm using ActiveRecord with
migrations. But why do I need a framework for an ORM? I felt like I
was using an SUV for the seat warmer.

So I decided to check out the other side of the fence. Namely,
Node. After all, isn't that where it all started? GraphQL.js is the
"reference implementation" of GraphQL. But with Node comes plenty of
other problems. Namely, it's just *so* *damn* *tedious*.

GraphQL in Rails is literally installing a library, writing a few
field types, and that's it. And in theory that's also GraphQL in
Node. Except you have to link resolvers to a database manually. Oh but
you need to set up an ORM for the database. And then get migrations to
work. Oh and then set up the actual server. And then figure out a sane
organization for the files. All the while trying to understand
documentation that uses varying generations of JavaScript. Gotta love
decorators in your documentation when it's not even a Stage 3
proposal! Or ES6 imports when they *barely* work in vanilla
Node. Look, I get it, you want the new shiny, but **please** don't
make me transpile my server.

However I digress. In short, running your own GraphQL server is perfectly
doable, but damn, is it tedious. And maybe coming from a world of
Express or Flask that's fine, but coming from the nice and smooth
world of Rails, it felt like building a Lego Death Star out of 1x2
blocks.

And really, this is symptomatic of a more fundamental problem; the
Node community many of the lessons of Rails. In their rush to get on
the hype train, Node developers threw out the baby with the
bathwater. They forgot what made Rails so effective. DHH et
al. realized that most web apps fall into the same patterns. So they
decided to take some simple, tested solutions to these problems, and
codify them into a framework. Ideas like Model View Controller,
Convention over Configuration, Don't Repeat Yourself, helped make
Rails into a really fantastic tool. If I had to choose a particular
slogan for why I love Rails, it would be "Automate the Boring Parts".

Sure, Rails has its messy bits. There's way too much magic, Rails
views are looking pretty out of date, and the community can be a
little dogmatic. But it became popular for a reason.

Why can't we do the same for GraphQL? Why can't we automate the boring
parts? And why can't we make it GraphQL first? Instead of building a
REST framework that tacks on GraphQL at the last second, why don't we
build it from the ground up around GraphQL? These are all questions
that have been ringing around in my mind. I might very well make this
framework.

And if I were to make it, here are some of the features I would add:

- Simple, but declarative models. Let's be real here, models in Rails
  are confusing as hell. I remember opening my first model file and
  wondering where all the code was. I'd probably combine ObjectTypes
  and Rails models into a single file that declares the database
  schema and then also contains the fields.

- Instead of Model View Controller, how about Model Resolver Query?
  Queries are the entrypoints for GraphQL, resolvers glue together the
  models (taking the place of controllers) and models contain most of
  the actual validation, fetching, business logic code.

- Query shortcuts. I've written a fair amount of ArticleById
  queries. It'd be nice to have an automatic bit of syntactic sugar
  for these. Something like "queryable_on :id"

- Code generation. Cause who likes writing boilerplate?

That's all I have now. Oh and the name of this prospective framework?
Lattice. Cause, get it? A Graph version of Rails?

...

Oh everyone's a critic.
