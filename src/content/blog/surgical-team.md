---
title: "Surgical Team"
pubDate: "Jul 14, 2020"
draft: true
---

I've been (re)reading[^1] The Mythical Man-Month. It's a classic book
on software engineering, but as with many classics, it is a product of
its time. I'm making a series of posts to analyze how the book's ideas
can be adapted to modern times, and hopefully provoke some discussion.

[^1]: I didn't get through it the first time, maybe this time I will

Chapter 3 discusses the Surgical Team, a programming team with one
main surgeon and a supporting team to aid the surgeon.

Why this format? In previous chapters, Brooks highlighted his
eponymous law: Adding more manpower to a late software project makes
it later. Furthermore Brooks describes the infamous concept of a 10x
programmer: Certain programmers are supposedly 10x as productive as
other, weaker programmers. Therefore it stands to reason that large
systems should be designed with as few programmers as possible. This
is a problem, as large projects are fundamentally infeasible with too
few programmers. Brooks provides the Surgical Team---originally
posited by Harlan Mills---as a solution.

The team consists of the following members:

- A lead surgeon, who writes all of the code and is the fundamental
  driving force behind the project. They must have "great talent, ten
  years experience, and considerable systems and application
  knowledge, whether in applied mathematics, business data handling,
  or whatever"

- A copilot, who is the right hand of the surgeon. The surgeon
  discusses and consults them, but doesn't have to follow their
  advice. They "may even write code, but he is not responsible for any
  part of the code"

- An administrator, who is responsible for all of the logistical
  issues with money, personnel, space, etc.

- An editor, who edits the documentation written by the surgeon. They
  may add citations, refine it, provide feedback, etc.

- Two secretaries. They handle correspondence and non product issues.

- A program clerk who maintains the technical records, such as
  computer output, along with machine readable and technical readable
  files. They may also distribute copies of the codebase to the team.

- A toolsmith who designs tooling and custom libraries for the surgeon.

- A tester who writes tests, both integration and unit tests.

- A language lawyer, essentially a programmer with a deep knowledge of
  the programming language, who can find neat tricks or unconventional
  uses of the programming language. They can give advice to the
  surgeon on how to effectively use the language.

These are all oversimplifications, but they will suffice.

The end result is a single programmer who can work at the peak of
their abilities. A few of these teams combined could then tackle major
projects with far less manpower.

In these descriptions are some anachronisms. I can't say I know any
programmers with secretaries. There's a lot of description devoted
towards storing results of computer runs and other bookkeeping that
can be done easily with computers. Some of these jobs can definitely
be automated today.

However when reading a classic text like this, it's important to brush
away the outdated minutiae and find the core idea. This core idea is
generally more enduring than the details.

In this case, the idea is twofold: specialize tasks and minimize the
number of minds on a project.

What's interesting is that neither idea has been widely adopted in the
time since the book was written. I suppose there's a concept of front
end/back end/full stack, but those aren't the specializations Brooks
espouses.

Likewise companies do not try to minimize the number of minds on a
given project. If anything the commonly held opinion is to scale up
your engineering quickly, as there's a lot of features necessary for
large products. Dan Luu has a [great
discussion](https://danluu.com/sounds-easy/) on this.

# Why Didn't It Work?

So what gives? Was Brooks wrong? In a sense that's the wrong question
to ask. I don't know if the surgical model is better or worse. Nobody
really knows. Software engineering is an infamously hard area to apply
A/B testing, as no software project is the same. Instead we should ask
why the surgical model wasn't adopted. For that, I have a few thoughts.

When I was reading this passage, my immediate thought was that most of
these jobs sounded boring, except the surgeon and maybe the toolsmith
or language lawyer. Boring because, well, they didn't involve actually
programming. In Brooks' time, that made sense. Computers weren't
ubiquitous so having only one person write the code made sense.

I suspect a lot of programmers stop paying attention at that
point. Most of us are programmers because we, well, love
programming. Having only one person in a 10 person team write code
isn't palatable to both programmers and their managers.

There's also the issue of culture. Programmers have a culture of
independence and do it yourself. Every programmer is self-taught to an
extent. The surgical team operates (heh) under the concept of total
monarchy. What the surgeon wants, the team does. A lot of programmers
would chafe under that mandate.

Finally there's the question of finding surgeons. As any programmer
who has applied to jobs can attest, hiring in tech is less than
ideal. Nobody knows how to find the best people or even how to judge
one's competence. Under the surgical model this is even worse since to
program, you need to have experience. How do you get experience? You
work as a surgeon. How do you become a surgeon? Get hired as a
surgeon. How do you get hired as a surgeon? Have experience
programming. Uhh...

# And Yet...

And yet when I read this passage, I noticed some similarities with
modern practices. For instance, the toolsmith is reflected in modern
development's emphasis on tooling. Whether it's TypeScript, Sorbet,
NPM, etc., major companies have invested in tooling. This is at a much
higher level, but the idea of having custom tools or libraries for a
team is not unheard of.

At the lower level, most interns at large companies end up working on
auxiliary systems such as monitoring dashboards or analytics. That way
they can get experience writing significant systems, but in a way that
doesn't leave them responsible for business crucial code. Many interns
are already great developers but having them write code that directly
effects millions of customers is a little suspect. These intern
projects often do take the role of a toolsmith.

The concept of a language lawyer, essentially a guru in that language
or technology, has also been espoused by other people. Joel
Spolsky---in his book Smart and Gets Things Done---gives the advice
that if you're building a major system in a stack, you should have an
employee who is a guru in said stack. Which makes sense; it's why
companies poach people like Bjarne Stroustrup or Ken Thompson[^2].

[^2]: Well not the *only* reason. Obviously they're good programmers,
	but having someone who can tell you the ins and outs of a language
	is extremely beneficial even if they don't write a single line of
	code.

The focus on a singular mind is also reflected in the benevolent
dictator for life (BDFL) position common to many open source
projects. One could argue that the reason projects such as Python or
Linux succeeded was that they were the work of one singular, brilliant
mind.


# Film Crew

While I was reading these descriptions, I started thinking about film
crews. Film crews are remarkably similar to the surgical team
layout. A film is fundamentally the concept of one person: the
director[^3]. The director consults other team members, such as the
cinematographer, the assistant directors, the screenwriter, but
fundamentally the idea on the screen is the director's[^4].

[^3]: I'm taking an auteur theory stance, sorry Dziga Vertov Group.

[^4]: And maybe the producer

However there's a big difference in film crews: class. Film crews are
very much split between the people doing the physical work like
lighting (gaffers) and electrical (grips), and the people doing the
more high level activities like directing or writing. Often times
directors and screenwriters have gone to film school, while the
gaffers and grips haven't. Grips and gaffers make a modest hourly wage
while directors and screenwriters can pull in major money.

This hierarchy works because directors have a major responsibility:
creative vision. A director is the person responsible for combining
the forces of writing, picture, editing, acting and sound into a
cohesive creation. This task is considered so difficult that the
director is placed in a position of worship. If you were to ask a grip
on the set of an Ingmar Bergman film whether they could swap jobs with
Bergman, they'd probably say no. Programming is the opposite. If you
were to ask a developer whether they could swap jobs with another
developer, they'd probably say yes.

Brooks is no stranger to this idea; he directly brings up artistic
vision in his next chapter on cathedrals. Unfortunately for Brooks,
artistic vision is not how most people view programming. But perhaps
it should be? I will caution that the hiring process for directors is
in many ways worse than the hiring process for programmers[^5].

[^5]: Fun game: name as many POC or non-male directors in 1 minute as
    you can.

But perhaps we would end up with more projects that have a cohesive
vision, that don't feel cobbled together or hacky or broken. Perhaps
we should give the Linuses, the Guido van Rossums, the Fabrice
Bellards of the world a surgical team and let them work at their
peaks. Perhaps we've been making the Suicide Squad of programs the
whole time, when we could have been making the Rear Window of
programs.
