---
title: "CS Is Not Programming"
pubDate: "Feb 27, 2023"
draft: false
---

If you've discussed CS education or gotten a CS education, you've
probably encountered this line. It's often given as a retort for why a
CS program is not centered on teaching programming. As a pure factual
statement, it is absolutely correct: CS is not programming. Even as an
overarching statement about CS programs, it is indeed correct: CS is
not programming and therefore CS degrees should cover material that is
not programming.

However, there is a particular interpretation of this phrase that I
find troubling, namely that CS is not programming and therefore we
should not expect CS programs to properly teach people how to code,
and we should not expect CS professors to know how to code.

Let me be clear here: I am **not** saying that **every** CS professor
should know how to code. I am **not** saying that every course should
involve programming. I **am** saying that there should be a
significant portion of CS professors who know how to code because
there should be a significant portion of classes that involve
programming.

Perhaps this is a little bit of a straw man here, and perhaps this
will not line up with other people's experiences, but I've found that
CS professors get really defensive when you suggest that they could
maybe stand to teach a little more programming.

Let's start with a basic statement: You should have professors who
really know how to code teach courses like Data Structures, Operating
Systems, and Intro to CS. They should be comfortable using a computer
and writing vaguely idiomatic code that actually works. They should be
reasonably up to date on technology, so no C++ 98. None of this is
that controversial.

But what's the natural corollary? You should hire professors who know
how to code! Not all of them, but there needs to be a reasonable pool
of faculty who can teach programming. This is where the push-back
occurs. Suddenly scores of professors pop up preaching the titular
phrase: CS Is Not Programming!

I do see their point. CS is far more than programming and often
involves more knowledge of mathematics, statistics, or even physics
than programming ability. And it's understandably frustrating for
someone who is a world-reknowned expert in a field, who spends likely
hours upon hours literally pushing the limits of human knowledge, to
be forced to also keep up with programming practice.

It's also a tricky request from a university politics standpoint. By
demanding for professors who are able to teach programming, you are
effectively asking the department to make programming ability required
to be hired. Departments don't like being told what to do, and they
especially don't like being told to do stuff that goes against their
existing practice.

But nonetheless, we need professors who can code, so they can teach
students how to code. In some extreme cases, perhaps those who have
read Dijkstra's [On the Cruelty of Really Teaching Computer
Science](https://en.wikipedia.org/wiki/On_the_Cruelty_of_Really_Teaching_Computer_Science)
and took it a little too seriously, this is a controversial
statement. I would disagree. In my view, programming is a sufficiently
important subset of computer science that it is worth teaching. It is
also a subset that happens to be of key importance to almost every
computer science major. Whether it's writing compilers, building
miniature distributed systems, or implementing some algorithms,
programming is both an essential part of computer science, and an
excellent means towards learning the rest of CS. I don't know about
everybody else, but I see programming as a way to make learning really
fun. I wish professors saw it similarly.

One solution might be to split up computer science and software
engineering. Some universities already do this. I have mixed feelings
about this approach. On one hand, it does have a precedence in the
split between physics and engineering. Perhaps both fields, with their
separation, would be able to flourish. Indeed I have felt that there
is too much content to teach a fully satisfactory CS curriculum and a
fully satisfactory programming curriculum. I've also written about
[what I'd envision as an ideal
degree](https://uptointerpretation.com/posts/art-school-for-programmers/)
for software engineering. I'd love to see a software engineering
department filled with experienced programmers passing on their
wisdom. Likewise computer science could become a truly incredible
course of study with deep courses in type theory, distributed systems,
machine learning, complexity theory, and so on.

On the other hand, I worry that software engineering majors would not
get the full breadth of a proper computer science education. Really,
that's the sentiment of the phrase "CS is not programming": To become
a good programmer, you need to know computer science, and that
computer science is very often not programming. Perhaps by forcing
students to learn computer science, we are leaving them better
off. However, that may be a false dichotomy. We can teach computer
science *through* programming exercises.

Another danger is that by splitting up the two departments, all of the
students will flock to software engineering, leaving CS departments to
be forgotten and neglected. I doubt this would actually happen. First,
plenty of students would continue to get CS degrees out of some
misplaced notion that it is harder and therefore more
prestigious. Second, computer science is still absolutely,
fantastically useful. If you want to do any major foundational work in
software engineering, you must know your computer science. And third,
computer science professors are still a hot commodity for their
research developments. I don't anticipate computer science being a
neglected field anytime soon.

Some may wonder why we must do anything at all. CS departments have
existed for quite a few years now and worked just fine. Which
is...somewhat true. They've worked in that they've managed, for the
most part, to teach students computer science, and those students, for
the most part, have managed to take that computer science and turn it
into programming skills. However, if you pay attention, you'll notice
that the students are effectively
[self-teaching](https://uptointerpretation.com/posts/the-self-taught-developer-paradox/)
their own programming degree. Which means that the students who
succeed in becoming developers are those who are comfortable learning
on their own. I don't object to that---as I've previously stated, self
teaching is an essential skill for a programmer---but I do think
students could use a little more help in the beginning. Often times
what determines if a student will make it over that initial hump are
nebulous stuff like whether they feel welcome as a programmer, whether
they have received validation for their efforts, whether they have
friends who are also interested in programming, and so on. By
explicitly teaching programming, we can ensure that these factors
matter less.

Not to mention, many CS programs are experiencing record enrollment
and therefore [ridiculous
waitlists](https://www.nytimes.com/2019/01/24/technology/computer-science-courses-college.html). By
splitting into two programs, that could perhaps result in more
professors and therefore more classes.

And finally, it's a commonly cited statement that us software
developers are not very good at programming. After all, if bridges
collapsed as frequently as AWS had outages, we'd be putting our civil
engineers on trial. Perhaps this is because the core way to learn
programming is by self-teaching while learning a similar but different
subject taught by people who have little to no experience writing
software professionally. And, at least for me, the most commonly cited
examples for software design in my classes were, \*checks notes\*, the
Linux kernel[^1]? Yeah...that's not great.

[^1]: This is a whole other rant but I find it really mystifying how
    we still cite Linux or Unix conventions as the pinnacle of
    software engineering. No style that produced naming like `atoi`
    should be praised.

If we want to build better, safer, faster software, then we should
teach our students how to program instead of expecting them to teach
themselves.
