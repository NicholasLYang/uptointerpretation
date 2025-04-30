---
title: "Stop Using So Many Damn Ternaries"
pubDate: "Jul 5, 2019"
draft: false
---

A ternary operator, for those of you who don't know, is an operator
with three parts---hence ternary operator instead of binary
operator. They look like this:

```
  cond ? a : b
```

If `cond` is true, then the expression evaluates to `a`, otherwise it
evaluates to `b`. They're very useful in some situations, such as when
you want to conditionally assign to a variable.

Contrast this

```
let a;
if (cond) {
  a = foo();
} else {
  b = bar();
}
```

With this

```
const a = cond ? foo() : bar();
```

Note that we can now use `const`, as a is never reassigned. Always a
nice benefit.

Despite these benefits, ternaries have become rather overused,
especially in the React world. This is partially due the fact that JSX
only allows expressions, which precludes JavaScript's traditional
imperative if statements from being used. Multitudes of developers,
seeing that if statements don't work inside JSX, simply substitute a
ternary operator for the if statement. This is not good.

First there's the case where you can use a better tool. For instance,
if you need to render something conditionally, say you have `Sidebar`
and an `isSidebarVisible` variable.

Some people will write the following

```
{isSidebarVisible ? <Sidebar /> : null}
```

While this is nice and declarative, it's also redundant. This works
just fine.

```
{isSidebarVisible && <Sidebar />}
```

Second there's the cases where one can substitute an if statement
quite easily.

```
<div className={classes.App}>
{isUserLoggedIn
 ? isAdmin
 ? <AdminPage />
 : <UserPage />
 : <LoginPage />}
</div>
```

Becomes

```
if (isUserLoggedIn) {
  if (isAdmin) {
   return (
     <div className={classes.App}>
       <AdminPage />
     </div>
   );
  }
  return (
    <div className={classes.App}>
      <UserPage />
    </div>
  );
}
return (
  <div className={classes.App}>
	<LoginPage />
  </div>W
); 
```

Yes, yes, the wrapper div is being repeated. If it truly behooves you,
the inner component can be assigned to a variable. A lot of people
forget (or don't know) that JSX is just syntactic sugar for function
calls.

```
let innerComponent;
if (isUserLoggedIn) {
  if (isAdmin) {
    innerComponent = <AdminPage />
  } else {
    innerComponent = <UserPage />
} else {
  innerComponent = <LoginPage />
}

return <div className={classes.App}> {innerComponent} </div>
```

One may ask why ternaries are so terrible. After all, the "better"
code is a lot more verbose, while the ternary code has a nice
compactness to it.

Ternaries aren't necessarily bad, but they can easily grow out of hand.

```
{ isUserLoggedIn 
  ? isAdmin
  ? <AdminPage />
  : isUserVerified
  ? <UserPage />
  : <VerificationPage />
  : <LoginPage /> }
```

Fun question, try evaluating this for `true, false, true`. This is
hard to read for a few reasons. It's hard to figure out the evaluation
flow. It's hard to figure out which condition goes with what
branch. Finally and most importantly, the states are implicit.

Control flow is inherently about dealing with different states in your
code. If something is in state A, execute this code. If something is
in state B, execute this code. If statements, returns, etc. are all
tools to map states to code. What if we made this state explicit?

In this example, we're conditioning on user state. In *ahem* more
civilized languages, we could simply make an enum/discriminated union
type and pattern match on it.

```
enum UserState {
  LoggedOut,
  Admin,
  Unverified,
  Verified
}

match user_state {
  UserState::LoggedOut => LoginPage,
  UserState::Admin => AdminPage,
  UserState::Verified => UserPage,
  UserState::Unverified => VerificationPage
}
```

But if we're using a language that doesn't have pattern matching, we
have to be a little smarter. For instance, we could use string
literals in JavaScript with an object.

```
const USER_STATES = {
  LOGGED_OUT: "USER_STATES_LOGGED_OUT",
  ADMIN: "USER_STATES_ADMIN",
  VERIFIED: "USER_STATES_VERIFIED", 
  UNVERIFIED: "USER_STATES_UNVERIFIED"
};

const userStates = {
 [USER_STATES.LOGGED_OUT]: <LoginPage />;
 [USER_STATES.ADMIN]: <AdminPage />;
 [USER_STATES.VERIFIED]: <UserPage />;
 [USER_STATES.UNVERIFIED]: <VerificationPage />;
}

<div className={classes.App}> { userStates[myUserState] } </div>
```

Granted, we need to figure out how to compute the userState. And for
that, you'll need to use some if statements. But once we calculate
this state, we can store it in a central location, either context or
Redux, and use the same pattern of an object literal whenever we need
to condition on userState.

```
const secretPages = {
 [USER_STATES.LOGGED_OUT]: <LoginPage />;
 [USER_STATES.ADMIN]: <SuperSecretPage />;
 [USER_STATES.VERIFIED]: <KindaSecretPage />;
 [USER_STATES.UNVERIFIED]: <NotSecretPage />;
}

<div className={classes.SecretPage}> {secretPages[myUserState]} </div>
```

What's nice about this approach is that it puts the states up
front. When we condition on userState, we're now encouraged to handle
all the cases (in languages with actual enums/pattern matching this
would be forced at compile time). And by emphasizing states, we've
exposed the underlying data structure: a state machine. Which brings
Rob Pike's quote on data structures vs algorithms to mind.

> Data dominates. If you've chosen the right data structures and
> organized things well, the algorithms will almost always be
> self-evident. Data structures, not algorithms, are central to
> programming.

Given a complicated algorithm with a lot of boolean logic such as `if
(a || b && c)`, consider refactoring it into a series of reasonings
about state. Then you won't need to trace through the program flow:
it'll be obvious.
