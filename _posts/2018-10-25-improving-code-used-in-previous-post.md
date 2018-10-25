---
layout: post
title: Using RxJS to improve the code in displaying the current time in an LCD
tags: [rpi, javascript, nodejs, rxjs]
---

In the previous [article][article] you saw how we can use [NodeJS][node] and a [Raspberry Pi][rpi] to display the current time along with extra text through an [16x2 LCD][16x2-lcd].

But there's something that bothers me quite a bit.

**Its on the readability of the code**. 

When you get inside the event handler on the lcd the levels of nesting go deep starting with `setInterval()`.

    const Lcd = require('lcd');
    const moment = require('moment')
    const lcd = new Lcd({ rs: 25, e: 24, data: [23, 17, 21, 22], cols: 16, rows: 2})

    lcd.on('ready', () => {
        setInterval(() => { 
        lcd.setCursor(0, 0);
        lcd.print(moment().format('h:mm:ss a'), (err) => {
            if (err) {
                throw err;
            }
         });
        }, 1000);
    });

    process.on('SIGINT', () => {
      lcd.close();
      process.exit();
    });

There's probably a better way to do this. 


That's where [RxJS][rxjs] comes in. It is a *functional reactive library for dealing with events and cases with time based logic* such as this.

It allows you to compose such operations and better error-handling in a declarative manner. 

>This isn't a monad tutorial but RxJS Observables are [monads][monads]. A Superset of the [IO Monad][io-monad] specifically because **time is a first class citizen**. One key takeaway that Monads give you is composability and the power to do [Railway Oriented Programming][railway].

Observable types are sequences so you need an Observable to start the sequence.

The first of the operations is the `lcd.on('ready', cb)`. Based on its signature the lcd module is an [EventEmitter][emitter]. 

In order to turn that into an Observable we need the `fromEvent` function from rxjs.

    const onLcdReady = fromEvent(lcd, 'ready')

To provide 1 second intervals we use the `interval` function from rxjs to provide interval behavior

    const everySecond = interval(1000)

Then to show the current time we need to wrap it in a Observable

    mergeMap(() => of(moment().format('h:mm:ss a'))) 

> I kind of did that inside of the lift function since defining the `moment().format('h:mm:ss a')` as a `const` would net me the same time when it was created. Simply put **the time won't update.**

To weave them all together we use `mergeMap`:

    onLcdReady.pipe(
      mergeMap(() => everySecond),
      mergeMap(() => of(moment().format('h:mm:ss a'))))

This code returns an Observable that emits the current time every second. By itself this won't do anything since we haven't called `subscribe` yet. 

So the full code:

{% gist bb33879123ff375dfd4e06bcf5235e12 %}

Once we call subscribe the functions inside the `mergeMaps` are executed every second due to the `interval` function. The raw time is passed to the `subscribe` function ready to be shown in to the [16x2 LCD][16x2-lcd]

Then run it using `npm`.

As you can see its now more concise. This is why I love [declarative programming][declarative] than [imperative programming][imperative] since it reads better. (In this case, it reads better)

## Caveat

It does fall short when you want to display two lines. You have to put the second `print` method inside the first's callback.

{% gist 7eb3c2ff2249f1f4b9126f80ce6bb45f %}

Because of how the lcd's API is designed. Once you call `setCursor()` it **clears up the lcd display** and was designed to be like that for optimization purposes.

So right now I'm looking to transfer to a Framework called [Johnny Five][johnny-five] since its APIs are more high level than the ones provided in lcd.

[article]: https://pofay.github.io/2018/10/18/Using-Javasciprt-to-display-text-on-lcd-using-rpi.html
[node]: https://nodejs.org/en/
[rpi]: https://www.raspberrypi.org/products/
[16x2-lcd]: https://www.adafruit.com/product/181
[rxjs]: https://rxjs-dev.firebaseapp.com/
[monads]: http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html
[io-monad]: https://medium.com/@luijar/the-observable-disguised-as-an-io-monad-c89042aa8f31k
[railway]: https://fsharpforfunandprofit.com/posts/recipe-part2/
[declarative]: https://en.wikipedia.org/wiki/Declarative_programming
[imperative]: https://en.wikipedia.org/wiki/Imperative_programming
[emitter]: https://nodejs.org/api/events.html#events_class_eventemitter
[johnny-five]: http://johnny-five.io/

