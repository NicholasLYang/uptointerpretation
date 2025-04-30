---
title: "How Good Is It: Tech Interviewing"
date: 2024-07-02T16:19:18-07:00
draft: true
---

Tech interviews are such a pervasive part of our industry. It's nearly
impossible to get a job at a reputable place without some sort of
technical screen. But how good are they? Are they indeed the
meritocratic tests that let companies hire the best of the best? Or
are they arbitrary, tedious, and painful processes that push away
talented candidates?

Let's start at the beginning and go through the history.

# Puzzle Interviews

Tech interviews as we know them started in the 90's. Microsoft is
often credited with the invention, supposedly from Bill Gates' love of
puzzles. These early tech interviews mostly started as open ended
puzzles/problems, such as Fermi Estimations, where candidates would
have to guess the number of piano tuners in Seattle, or more absurd
questions, such as asking how you could escape from a blender if you
were the size of a fly. These replaced the stereotypical "what are
your strengths and weaknesses" questions.

The aim of these questions was to get an idea of how people were as
problem solvers. Of course, because they were open ended, this put a
lot of pressure on the interviewer to determine the success
criteria. It'd be pretty absurd to reject a candidate simply because
they guessed a different number of piano tuners than you would
have.

These interviews didn't contain any code and were mostly abstract
exercises. Perhaps some whiteboarding would be involved, but nothing
intense.

**Is It Good?** Overall, probably not. While the questions can be fun
exercises, they quickly became a game of whether you knew that
specific brand of problem. If you happen to know Fermi Estimations, or
that a fly has enough strength relative to size to jump out of a
blender with ease, sure, you can solve the problem. If not, you were
much less likely. Also this leaves a lot to the interviewer's
subjective analysis of the candidate's intelligence, which is fraught
with bias. I think you can have a similar Socratic-style interview but
more focused around technical topics. That said, these questions were
probably better than just interpersonal ones, which had these same
issues and much more.

## FizzBuzz

FizzBuzz is maybe the most famous coding interview question. It asks
the candidate to print out the numbers from 1 to 100, replacing
numbers that are divisible by 4 with `Fizz` and divisible by 7 with
`Buzz`. If the number is divisible by both, the code should print
`FizzBuzz`. If you've done even rudimentary programming, this should
be pretty easy. There's a minor edgecase of printing both `FizzBuzz`
without the newline, but really, it's absurdly simple. And yet, this
seemed to stump a lot of candidates. Even more surprising, senior,
established candidates would fail this interview!

Jeff Atwood has [a very famous blog
post](https://blog.codinghorror.com/why-cant-programmers-program/)
about this phenomenon, which cites another blog post with a dead
link. Judging by the publishing date, FizzBuzz dates back to the early
2000's. This seemed to have created a lot of noise, since the comments
section is closed "due to high volume". Indeed, I had heard about this
phenomenon via general word of mouth. According to general internet
folklore, getting a programming job in the 90's and early 2000's was
as simple as knowing basic HTML.

I don't know if this directly lead to much change other than
candidates knowing that you absolutely must know how to do FizzBuzz if
you want a job.

**Is It Good?** Yep. Having someone who can't code at all, like
not even a for loop with some if statements, is not good. FizzBuzz was
smart because it was the absolute bare minimum and yet it still
worked. Yes, it quickly went out of style because people just started
memorizing FizzBuzz, but it was still an important reckoning for the
nascent tech industry.

# Smart And Gets Things Done

Joel Spolsky, the founder of Fog Creek Software, and the owner of an
at the time very famous blog, Joel On Software, published a short book
on tech hiring called Smart and Gets Things Done. The book espouses
his philosophy of hiring at Fog Creek, basically expanding on his blog
posts.

A short aside, perhaps Fog Creek and Joel On Software are forgotten
names at this point, but I want to emphasize that they were quite
famous at this point. I'd argue that Steve Yeggie, Joel Spolsky, and
Jeff Atwood were the most famous tech bloggers on the scene. This book
definitely sold decently, especially after Jeff Atwood and Joel
Spolsky teamed up to do StackOverflow (yes, *that* StackOverflow).

Anyways, this book talked about a few main points:

- Hire only the best of the best.
- Don't be afraid to get false negatives.
- The best of the best are really hard to find, so invest a lot of
  time into finding people through unconventional methods.
- Good school/grades can matter.
- Internships are a key way to find talent early and basically 3 month interviews
- Treat candidates like rockstars.
- Use code interviews.

It's hard to say whether the book directly influenced the industry, or
if this was just where things were going, but the trends were very
clear. Internships went from free or low paid gruntwork, to high paid
coveted positions doing important projects. Google was a prime example
of an early fancy internship. These internships often directly
recruited from top CS schools like MIT or CMU. Interns would get flown
out for their on-site interviews and given free swag.

And the interviews were technical! These were a little more tricky
than FizzBuzz. Perhaps you'd get a question on why string
concatenation is slow in C (Joel seemed to love that one). Perhaps
you'd have to do a binary search. Nonetheless they were still
relatively simple questions, more a medium level bar to indicate that
yes, you got a CS education and knew how to write code. Joel cites
pointer and recursion questions as the ultimate arbiter, which
frankly, is not *that* high.

Anyways, this sort of hiring took off and by the mid 2010's, you'd be
hard pressed to find an interview process for a hip tech company that
wasn't in this vein.

**Is It Good?** Ehh I'd say a soft yes (or following Joel's advice, a
No Hire). Treating candidates well and asking baseline technical
questions are good ideas. I'd say using grades and school prestige is
very much a bad idea. Plenty of people who go to less than top schools
are great engineers. In fact, lots of tech companies do try to hire
from outside the top prestigious schools (although it is still much
easier for an MIT student to get a job). Biasing towards false
negatives is somewhat reasonable at a micro level, but I think it has
some issues in a larger context. More on that later.

# Cracking The Coding Interview

As interview questions became ubiquitous, candidates started to
realize, hey, I need to pass them to get a job. Enterprising employees
began realizing that there was money to be made in teaching people how
to pass these interviews. The touchstone here was the book Cracking
The Coding Interview by Gayle Laakmann McDowell. The book is a hefty
green volume that goes over all of the different genres of interview
questions such as array problems, graph problems, dynamic programming,
etc. Basically it was a test prep book for technical interviews.

Since more and more candidates started prepping for interviews,
companies started having to raise the bar for interviews by asking
harder questions. This was compounded by companies now being comprised
of people who prepped for interviews and therefore saw it as normal,
de jure practice. Something like a binary search or a sorting problem
was just too obvious.

What added to this was the crazy bull run of tech companies in the
2010's. Stocks went up, compensation skyrocketed and suddenly big tech
was getting hundreds of thousands of applications. Asking harder
questions was a way to filter out all the "unnecessary" applications

**Is It Good?** Probably not. I think it was good to democratize the
information, but it effectively kicked off an arm's race of
increasingly difficult questions.

# Leetcode

Of course, a book can contain only so much information. As coding
questions became harder and more prevalent, sites popped up for
interview prep. Leetcode became the most dominant, to the point where
coding questions are now categorized in common parlance by their
Leetcode rating of easy, medium or hard.

Once prep went online, there started to be more digital spaces
dedicated to talking about them, such as Blind and
/r/cscareerquestions. These spaces engendered a very competitive,
almost Darwinian approach to job hunting, with users encouraging
others to "grind leetcode" for months on end.

Sites also popped up that allowed users to solve their questions
online in a coding playground. This was a marked improvement from
whiteboards (well, in some capacities) or Google Docs, but it also
meant that companies could now screen with coding questions before
even talking to a candidate. Candidates would sometimes even get a
HackerRank coding screen before their resume was even screened.

**Is It Good?** No. The culture around interviewing became about
passing through increasingly tough and arbitrary bars. The people who
succeed in this framework are those who want to chase clout, are
familiar with gaming systems such as college admissions, and transfer
that expertise to tech hiring. A common joke is that redditors go from
/r/applyingtocollege to /r/csMajors to /r/cscareerquestions. The
result is a larger tech culture of people who care more about clout
chasing and [gaming the
system](https://uptointerpretation.com/posts/all-in-the-game/).

## Counter Movements

As coding questions became more prevalent, there were some counter
movements to them. Some companies embraced the take home question,
citing that live coding was too stressful for candidates. Of course,
that does create the problem of making candidates devote multiple
hours to a question, sometimes before even doing a single
interview. There's been some talk of compensating candidates, but that
seems to be an outlier.

There's also been some backlash to extended processes. I know some
companies emphasize fast processes as a selling point to candidates. I
definitely appreciate when a company can get back to me in 2 weeks
versus the 2 months for large companies.

Some companies have started doing more practical tests like reviewing
a PR, finding a bug, or even going through a project that a candidate
wrote. Each of these have their flaws, but they do offer a different
approach to interviews which is welcome.

**Is It Good?** Yes. Even though these alternate strategies may not
work, or even be worse for candidates, I value the emphasis on
experimentation. Tech interviewing has a long way to go. That said,
there is a bias towards having a public profile as a developer, such
as an active GitHub account. I personally benefit from that but I
think it's unfair to the many excellent developers who do their work
privately or only at work.

# Conclusion

Coming back to the idea of false negatives over false positives, at a
macro level, I find this quite harmful. Because it fundamentally
encourages not thinking about who you are excluding. This could be
underrepresented groups, who may not have access to the cultural
knowledge around interviews. Yes, it's Leetcode and
/r/cscareerquestions, but you need to get introduced to reddit or
Leetcode somehow. You need access to a community that you may not know
about. But it could also just be people who don't wish to play the
game. I know a lot of excellent programmers who are utterly
disillusioned with job applications and don't apply to big tech. It
goes back to the [dark matter of
applicants](https://uptointerpretation.com/posts/the-dark-matter-of-applicants/). If
your company is intimidating, you won't get some of the great
applicants.

Of course it's a stretch to say that companies never think about the
	people they reject. They certainly do. But still we seem entrenched in
the same interview process. Companies will think about pipelines and
reaching out to underrepresented groups, but they won't consider
fundamentally changing the interview process. Usually they fall back
on some vague meritocratic statement that this would compromise their
talent requirements. Of course companies rarely ask whether their
interviews actually select good talent.

As a whole, I suspect that the utility of these interviewing
strategies is decreasing pretty sharply. They're becoming just another
test to study for, like the SAT. And like the SAT, they're becoming
filled with candidates whose main skillset is test taking and looking
impressive. Some of these candidates are great programmers, but a lot
are just looking for high pay and are willing to prep for it. In order
to keep getting interesting, talented candidates, companies will have
to make different, more interesting interview processes. They'll have
to find ways to look at potential rather than rote skill. They'll have
to judge based on more nuanced skills like debugging or design.

I, for one, am looking forward to these changes.
