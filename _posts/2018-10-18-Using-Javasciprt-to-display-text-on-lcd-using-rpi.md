---
layout: post
title: Using NodeJS to display the time in an LCD using a Raspberry Pi
tags: [rpi, javascript]
---

As mentioned in the title, this post will cover how to use NodeJS to display the current time in the format of `h:mm:ss a` through an [16x2 LCD Display][2] with a [Raspberry Pi 3][3].

This was the result of what I made:

![LCD Display using JS](/assets/images/lcd_time_extra.JPG)

## The Raspberry Pi Software Dependencies

Make sure you're Raspberry Pi has [NodeJS][nodejs] installed in it.

If not then download and install it.

## The Materials

In order to follow along with this tutorial you will need the following:

**Required**:

* Raspberry Pi 
* Assorted Jumper Wires 
* 10k ohm potentiometer
* 220 ohm resistor 
* Breadboard

*Optional*:

* [Cobbler for Raspberry Pi][cobbler] 
 
> I listed the cobbler as optional because you can opt to use jumper wires instead.

## The Schematic 

> I'll only show the Schematic for now since I'm not really good at using [Fritzing][fritzing] to display the Graphical Connection.

![Schematic Diagram](/assets/images/schematic-lcd.png)

This diagram only has a minor difference from the [Adafruit Tutorial][adafruit-tutorial]. 

Since **their LCDs have builtin resistors** they didn't need to have a 220 ohm resistor to dampen the voltage towards the Anode.

## Initial Setup of Code

Run the following commands using your terminal or command-line:

    mkdir lcd-clock
    cd lcd-clock
    npm init

Run through the npm prompts and then install the [lcd][lcd-package] and [moment][moment] package using npm:

    npm install lcd moment

Then create your `lcd.js` file with the following contents in it:

{% gist 755a18f01121d568758f29d3eaffee1b %}

If you want to edit what to display in the lcd just replace the first argument in the `lcd.print()` method

> For more information on the lcd module's methods visit its [github repo][lcd-package]

Then run `lcd.js` through node:

    node lcd.js

And you should see the time through the LCD:

![Initial Time Display](/assets/images/initial-display.JPG)

An example of Displaying an extra line to the LCD:

{% gist e2fb9c21da4fbfee5fdddc983b1bc104 %}

> The Api of the lcd package does not do an automatic newline unlike the [Adafruit Python Library][adafruit-python]. That's why you have to set the cursor again and due to the nature of callbacks in Node it has to be inside the first print method to work. 

And the Output:

![Double Line LCD](/assets/images/two-lines-lcd.JPG)

[1]: https://rxjs-dev.firebaseapp.com/
[2]: https://www.amazon.com/TC1602A-09T-Compatible-Backlight-Adafruit-Raspberry/dp/B07BV14Y4D?SubscriptionId=AKIAILSHYYTFIVPWUY6Q&tag=duckduckgo-d-20&linkCode=xm2&camp=2025&creative=165953&creativeASIN=B07BV14Y4D
[3]: https://www.raspberrypi.org/products/raspberry-pi-3-model-b/
[cobbler]: https://www.adafruit.com/product/2028
[adafruit-tutorial]: https://learn.adafruit.com/assets/1757
[fritzing]: http://fritzing.org/home/
[lcd-package]: https://github.com/fivdi/lcd
[moment]: https://momentjs.com/
[8x1-display]: https://www.amazon.com/8x1-Character-LCD-STN-Blue/dp/B01GK6YJOU
[nodejs]: https://nodejs.org/en/
[adafruit-python]: https://github.com/adafruit/Adafruit_Python_CharLCD
