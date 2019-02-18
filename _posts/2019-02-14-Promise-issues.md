---
layout: post
title: Promise Issues
tags: [javascript, functional-programming, data-structures,
concurrency]
---

This line of code from a very good [blog post on why promises are a bit dangerous][broken-promises] that I actually tried in my using terminal with [nodejs][nodejs]:

    Promise.resolve({ then: () => console.log("Hello!") })

And reinforcing what the article said:  **It never resolves.**

## The Points of my issues on Promises

In this [blog post][broken-promises] the [Author][@avaq] points out a lot problems that Promises have (read the **summary on Promises** which details alot of it).

I'm very particular about Point 1 and 3 and I'm going to outline them.

## The First Point: Eager Promises

> Promises are eager, which sets them up for a variety of issues as well as making them useless for side-effect management. 
> [@avaq][@avaq]


Promises are eager because the moment you call a `.then()` on a Promise it's going to execute.

Although the type-hints are capable of showing you if a function returns a `Promise<T>` you may never know if underneath that function that it has already made a `.then()` call to an external service.

In order for you to know what the heck happened in that function you need to peer into its implementation.

**The moment you invoke a Promise returning function the state of the system may have changed**

## The Third Point: Mixing Exceptions with Expected Failures

> Promises mix exceptions with expected failures. 
> [@avaq][@avaq]

The things that I've learned about **genuine exceptions**:

* It represents a real bug in your system (NPEs, undefined properties, etc)
* Generally you don't know how to handle them (causes your APIs for example to return a 500 response unintentionally) and **when they happen the process crashes**.
* [Try-catched at the highest level or at the lowest level][try-catch-boundary]

And The things I've learned about **fake exceptions**:

* Used to represent an expected error(Email/Password validations, Record not found, etc.)
* Generally custom-made exceptions to be somewhat identifiable (NoSuchRecordException, WeakPasswordException, etc.)
* **Try-catched anywhere and bubbles up the call chain**

One thing to note about exception handling is that once and exception occurs in your `try {...}` blocks is that it jumps directly to your `catch {...}` block. 

The reason it does that is to tell you through the stacktrace **what line in your try {...} that went awry** and its this characteristic that makes exceptions just [Another form of goto][exceptions-are-gotos].

If you utilize methods that throw exceptions for expected errors and wrap those methods in a Promise then you can get away with `try-catch`.

But you would no longer have the ability to branch between handling those expected errors and those geniune exceptions.

Because `Promise.catch()` mushes them together and you **might handle an geniune exception the wrong way instead of letting the process crash and restart**.

What I generally do instead is to utilize [Folktale's Result container for such things][folktale-result] when dealing with expected errors:

    new Promise(someSideEffectThatMayFail()).then(Result.Error, Result.Ok)

### Conclusion

> TLDR: 
  * Promises just unwind side-effects when calling its `.then()` making it not safe to call every time(its not idempotent)
  * Don't use exceptions on expected errors and wrap it in a Promise, instead use some sort of Result Container like [folktale's][folktale-result] to represent them and wrap it in a Promise.

And those are my issues on Promises. The only thing redeemable about them is that they enable `async/await` which leads to another issue on [function colors][function-colors].

[exceptions-are-gotos]:http://xahlee.info/comp/why_i_hate_exceptions.html
[broken-promises]:https://medium.com/@avaq/broken-promises-2ae92780f33
[function-colors]:http://journal.stuffwithstuff.com/2015/02/01/what-color-is-your-function/
[try-catch-boundary]:https://enterprisecraftsmanship.com/2015/02/26/exceptions-for-flow-control-in-c/
[nodejs]:https://nodejs.org/en/
[folktale-result]:https://folktale.origamitower.com/docs/v2.3.0/migrating/from-data.either/
[@avaq]:https://medium.com/@avaq