---
layout: post
title: "Reviewing Command-Query Separation and its benefits"
tags: [programming-principles, review]
---

## The Principle 

[Command-Query Separation][CQS] has long been a guiding principle for me in whatever paradigm I'm in since currently I'm working and learning software on my own.

The principle states (quoted from Wikipedia):

> Every method or function should either be a *command that performs an action*, or a *query that returns data to the caller*, **but not both.**

*I usually don't call them actions in this context.*

**I call them side-effects.**

Side-Effects are but are not limited to:

* Perform a state change system-wide, in an Object or in another system.
* Perform IO (Console.print, Console.read, HTTP, MQTT, Database Queries and Transactions)
* Logging Messages in a File

The main theme here is that **when you perform a command (side-effecting function) the current state of the system is changed.**

## Background - From a Static Type Langauge Perspective

Back when I was programming in [C#][c#] and [Java][java] my rule of thumb for this principle is based off of [Mark Seemann's Encapsulation and SOLID course][pluralsight-course]:

> Every method with a `void` signature ought to be a command. *Anything else is a query*

When reading Java code that adheres to the principle:

    public class IOToMQTTHub {
        private List<String> sentMessages; 

        public IOToMQTTHub(SocketIOClient io, MqttClient mqttClient) {}
        
        public void broadcast(String topic, String message) {}
        public List<String> getSentMessages() {}
    }

Through the **method signature and the name** You would know what method performs a **command** and what doesn't.

The goodness in this is that you no longer need to read the entirety of the method's body in order to know if it changes system state or not. [Vladimir Khorikov][vlad-khov] calls this trait **method signature honesty**.

Also with proper naming conventions you are well on your way to writing [self-Documenting Code][self-documenting code] thereby making your code easier to understand and maintain.

## On to Dynamic Languages

Command-Query Separation is very clear when working with Languages with a [static type system][static-type] like Java or C# but how do we apply this with Dynamic Languages like say Javascript?

The answer to that is on the usage of the methods or functions in question:

    class IOToMqttHub {
      constructor(ioClient, mqttClient) {}
      broadcast(topic, message) {}
      getSentMessages() {}
    }
    
    ... Example Usage
    const hub = new IOToMqttHub(io, mqtt)
    hub.broadcast('room/light', 'off')
    const messages = hub.getSentMessages()

Whenever you see a `= or assignment operator` next to a function or method call then its *hopefully* a query.

I say hopefully because some module functions in JS actually do alot of side-effects in the background especially during `require()` or even during `import`

As long as that JS code is designed with CQS in mind then you can assume it so.

## End

This principle has its own shortcomings in terms of implementation especially in regards to [threading][threading-drawbacks] and when doing error-handling. This area will be covered in the next post.

This principle also requires agreement and enforcement when your working on a team. If your the only one doing this then chances are it will fail.

[static-type]: https://en.wikipedia.org/wiki/Type_system#Static_type_checking
[threading-drawbacks]: https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Drawbacks
[self-documenting code]: https://en.wikipedia.org/wiki/Self-documenting_code
[vlad-khov]: https://enterprisecraftsmanship.com/2016/04/21/what-is-functional-programming/
[c#]: https://en.wikipedia.org/wiki/C_Sharp_(programming_language)
[java]: https://en.wikipedia.org/wiki/Java_(programming_language)
[pluralsight-course]: https://www.pluralsight.com/courses/encapsulation-solid
[CQS]: https://en.wikipedia.org/wiki/Command%E2%80%93query_separation
