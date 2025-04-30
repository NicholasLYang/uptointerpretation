---
title: "Not All Programming Jobs Are Created Equal"
date: 2025-03-26T12:41:36-04:00
draft: false
---

Let's paint a picture. You're a college senior and after sending out
hundreds of applications, going through technical screen after
technical screen, surviving a couple on-sites, you've finally gotten
an offer from a big tech company. Congrats! The recruiter gets on a
call and proudly explains to you that you'll be working in core
infrastructure using Java 7 and some internal tool called Basalt that
was written in 2006 and mostly takes XML.

This is an all too common experience for new programmers. When you're
starting out, the priority is getting *a* job. You can't afford to be
too picky. Companies know this and make New Grad pipelines that give
applicants little to no say on the team they join. They could get the
hottest, 0->1 product or a stable internal dashboard. Once a grad
joins a team, their experience becomes their norm. They start to
assume all jobs are like this specific team at this specific company.

But that's not true at all. Programming jobs span a massive spectrum
of qualities and specialities. Whether that's the style of
programming, the expectations around learning, or even compensation,
jobs in software engineering vary massively.

However, I should emphasize that there is no clean hierarchy of
jobs. You can't rank job A over job B. Ultimately it depends on what
you like as a person. The aforementioned Java 7 job could be a
wonderful, fulfilling job for someone, and a crushing, boring job for
another. It's all up to you.

## Some Jobs Are More Visible

One thing I really enjoyed about my most recent job is that a lot of
the work is open source. Not only does that give my GitHub account a
lot of cool little green squares, it also means I can freely talk
about my work to anyone. I can tell my friend that I'm working on
adding `turbo boundaries`, a command that lets you write rules about
importing packages in your monorepo, and that's not a breach of my
NDA.

This gives me a few advantages. I can give talks about my work, which
raises my profile. I can give in depth explanations to future
employers about my previous work without having to hide important
details. Heck, they can literally go and read my code if they
wish[^1].

[^1]: Although I don't know if that's a good thing.

On the flip side, this does mean I have to do a certain amount of
public facing work. This could be a random stranger reviewing my code,
or someone tagging me because they don't like Turborepo or Vercel. I
have to think about how I represent Vercel and Turborepo in public
spaces[^2]. I don't even get the full brunt of this! My amazing former
coworkers like Anthony Shew or Lee Robinson get far more public
attention. I'm in awe at how they field it with so much grace.

[^2]: This is true in all jobs, but especially when the nature of the
    work is so public.

On the other end of the spectrum, you have jobs in defense or
intelligence where you cannot even disclose basic details about your
work. Many employers understand that and try not to hold it against an
applicant, but I have to imagine it does play a factor.

But you don't have to be working in defense to have limited
visibility. Realistically a lot of jobs in tech are low
profile. They're working on internal technology or less well known
products. They're building internal dashboards to track
KPIs. Obviously these jobs are extremely valuable. But they're not
always easy to sell or explain. On the other hand, they aren't
beholden to the public in any fashion. Nobody's going to tweet at you
asking for a fix for an internal dashboard. There's not going to be
YouTubers reviewing your codebase.

## Some Jobs Are Product Oriented

Programming is always in service of some kind of product, whether
that's an open source tool, an internal API, or a desktop
application. But there are some jobs where you have to do a lot more
product work. This could be talking to users, deciding which features
to prioritize, figuring out the target audience of a product,
pivoting, and so on. Gergely Orosz has categorized this type of
developer as the [product minded
engineer](https://blog.pragmaticengineer.com/the-product-minded-engineer/).
Generally startups require more of these roles because the product is
still being formed, and because a startup may prefer a developer who
can pull double duty as a product manager. An engineer who doesn't
have this product sense risks working on irrelevant features or
building for non-existent customers.

Other jobs are much less product focused. Maybe they're at big tech
companies where product managers or leaders can fulfill this role. Or
maybe they're working on existing stable products that only require
maintenance and small new features. In these jobs, developers can
focus much more on implementation. For instance, if a developer was
working on a browser, while there would certainly still be some
product work, there wouldn't be any fundamental uncertainty about the
product. There wouldn't be a chance that the browser would pivot to
become a database, or have to find product market fit, or end up being
scrapped for a different project entirely[^3].

[^3]: Maybe the old Edge engine counts?

Generally, product oriented jobs require more flexibility and
interpersonal skills. If that's not your preference, try to avoid
these jobs.

## Some Jobs Require More Learning

There's a common complaint that front-end development is in a constant
state of churn, and therefore requires learning a new stack every few
years. There's some truth to that, although it's more a combination of
your stack and company culture. If you're writing COBOL and C++98 in a
slow moving legacy team, you're probably not going to need to learn
new stuff very often. If you're writing Rust and React on a greenfield
product, then you might need to keep up to date on the latest features
and general ecosystem movement.

Learning can create one of the bigger differences in job
satisfaction. If you see learning new technologies as a fun hobby,
whether that's perusing Hacker News, reading blog posts, doing small
side projects, and so on, you won't really count that as work. If you
see it as work or even tedium, then learning is just another few hours
of labor that you need to do. That's the difference between feeling
like your job is 35 hours a week or feeling like your job is 50 hours
a week.

I want to emphasize that there's no value judgement here. Learning off
the job is controversial because it exists between labor and
leisure. For better or worse, it's necessary in certain jobs. If that
doesn't appeal to you, try to avoid jobs that deal with fast moving
technologies or are at companies that use new technology.

## Some Jobs Leadership Cares About

In any company that isn't a tiny startup, there will be different
teams that leadership cares about in different amounts. While leaders
try to make it clear that like parents with children, they value every
team equally, there's always going to be the darling teams that
leadership focuses on, and the rest of the org.

Often these teams may be the ones doing cutting edge work, like new
products or new technology initiatives. Or maybe they're in an area
that's currently very popular, such as AI. Google's AI teams are
probably getting a lot more attention right now. Or maybe leadership
has taken a liking to the people on the team.

In any case, attention is a double edged sword. On one hand, you get
much more resources, more praise, more chance for promotions. On the
other hand, you can get a lot more work, more pressure, and more blame
if stuff doesn't go right. With teams that are not the focus, you may
be able to coast by without too much effort. However, if you want to
make a big impact or climb the ladder, you may chafe at being ignored
by leadership.

## Some Jobs Pay A Lot (And Don't Suck)

This one seems kind of obvious. But there's some myths that come with
pay. Namely, jobs that pay more are more boring, require more hours,
have more stress, basically that they come with more tradeoffs. That's
not necessarily true. Some of the least interesting, most stressful
jobs I've worked at were also the lowest paying.

In fact, I'd argue there's a loose inverse correlation between pay and
stress. The highest paying developer jobs tend to be at places where
programming is highly valued and needed by the company. These places
are often run by developers or former developers. When leadership is
technical, they tend to understand how developers work and facilitate
effective work environments. This could be [reducing meetings to allow
for deep work](https://www.paulgraham.com/makersschedule.html),
permitting remote work, avoiding micromanagement, investing in
developer tooling, and so on. Technical leaders also understand
software quality, and are willing to devote time towards improving
code.

Of course this is a generalization and no leader, no matter how
technical, is going to provide the perfect work environment. I'd even
argue that's against their [own
incentives](https://uptointerpretation.com/posts/incentives/). But
technical leaders have the advantage of empathy. They've been a
software engineer and they know how to work.

On the flip side, jobs that pay very little are either early stage
startups or companies that see developers as a cheap commodity or a
cost center that they need to minimize. For a startup, they literally
cannot afford to provide a better work environment. For the latter,
it's unlikely that leadership sees developers as valuable enough to
treat well.

It's easy to look at the very high salaries and assume that there is
some sort of catch. For many high paying white collar jobs such as
corporate lawyers or investment banking, there is indeed a cost of
long hours and corporate nonsense. Software engineering is unique in
somewhat avoiding that trade-off.

Also, pay comes in different forms. Some places pay solely in
cash. Others have equity. That equity[^4] can be in stock or in
options; it can be sellable or not; it can have different tax
implications. Or a job can pay via bonuses. Depending on your risk
tolerance and financial goals, you may want a specific type of
compensation. Some companies have better benefits such as 401k
contributions or nice health insurance. Depending on your lifestyle,
that could be a difference of tens of thousands of dollars.

[^4]: This isn't an equity guide, look elsewhere for that.
