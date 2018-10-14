---
layout: post
title: "What Adequate Functional Programming in JS taught me"
tags: [functional-programming, javascript]
---

I'm one of those developers who actually got hooked on the new age fad of [Functional Programming][1].

Aside from that alot of the experts in our industry (in terms of software craftsmanship) are actually learning and digging deeper 
into the paradigm.

[Mark Seeman][1] for instance has written a series of articles on the benefits of Functional Programming and its abstractions that make code easier to read, understand and maintain.

[Uncle Bob][2] made it very clear that one should learn it so that concurrency issues are more easily handled.

So as I picked up Javascript or NodeJS to be exact I decided to learn the paradigm. One of the best free books I have read on the subject was [Adequate Functional Programming in Javascript][3].

The things I learned from the book that were easily digestible for me: 

* First-Class functions
* Currying and Partial Application
* Function Composition


### First-Class functions

**In JS functions are first class.** If you've got a function that expects a function you should just pass it directly. 

As an example I'll use`map` from the `Array` class.

    const numbers = [1,2,3]

    const addOne = (x) => x + 1

    numbers.map(addOne)

But alot of times as the author points out it ends up being like this:
    
    const numbers = [1,2,3]

    const addOne = (x) => x + 1

    numbers.map(x => addOne(x))

Although there are no problems with both versions you have to change more parts in the second example when `map` sends more than one argument to the given function.

Lets say instead of just `addOne` you also want to add in the index as well.

So the function changes like this (hypothetically):

    const addOneAndIndex = (x,y) => x + y + 1

If you were using the first example the change would just be a rename of the passed function.

    numbers.map(addOneAndIndex)

In the second example however you would have to add in the extra arguments as well.

    numbers.map(x,y => addOneAndIndex(x,y))

So the bottom line is that **there are more parts to change** when you don't use this feature.


### Currying and Partial Application

If you've ever had experience with [Dependency Injection][4] with Objects this is as close as you can get but this one is for functions.

> Note: Currying or Partial Application in particular, when used with [Dependency Injection in mind leads to impure code][5]. Functional Programming has a different set of techniques to deal with dependencies.


With Currying and Partial Application we can transform certain functions such as this:

    const sum = (x,y) => x + y
 
    const result = sum(30,10) // 40

To be used in this manner:
 
    const addTen = sum(10)

    const result = addTen(30) // 40

To turn the original `sum` into a curried function we reimplement it like so:

    const sum = x => y => x + y

Thus when sum is given the first argument it just returns you a function that will add the first argument to the second argument through the magic of [Closures][6]

This allows you to turn multi-argument functions into one arg functions (or one-arity functions for those inclined).

    const concatLetters = (a,b,c,d) => a + b + c + d

    const curriedConcat = a => b => c => d => concatLetters(a,b,c,d)

    const startWithA = curriedConcat('A')

    curriedConcat('A')('B')('C')('D') // ABCD
    startWithA('B')('C')('D') // ABCD

Without a functional library like [ramda][8] calling curried functions is very tedious doing all the `()()()` in between calls

Using the `curry` function it becomes easier to use:

    const R = require('ramda')

    const concatLetters = (a,b,c,d) => a + b + c + d

    const curriedConcat = R.curry(concatLetters)

    const startWithA = curriedConcat('A')

    curriedConcat('A','B','C','D') // ABCD
    startWithA('B','C','D') // ABCD
   
> For a really good look into the difference of Partial Application and Currying look into this [Answer on StackOverflow][7]

For **Function Composition** I'm going to put it in the next post since it uses the techniques outlined here.


With that if you don't understand what I've just outlined here, then I suggest you read the [book][3]. Its a really good read.

[1]: http://blog.ploeh.dk/
[2]: http://blog.cleancoder.com/uncle-bob/2017/07/11/PragmaticFunctionalProgramming.html
[3]: https://mostly-adequate.gitbooks.io/mostly-adequate-guide/
[4]: https://en.wikipedia.org/wiki/Dependency_injection
[5]: http://blog.ploeh.dk/2017/01/27/from-dependency-injection-to-dependency-rejection/
[6]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures
[7]: https://stackoverflow.com/questions/218025/what-is-the-difference-between-currying-and-partial-application
[8]: https://ramdajs.com/
