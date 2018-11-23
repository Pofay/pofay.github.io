---
layout: post
title: Switching to MQTT over HTTP for IOT
tags: [protocols, mqtt, iot]
---

In my experiments with IOT I have used [HTTP][http] extensively for device communication in the form of [RESTFul APIs][restful].

But in my journey these past 2 weeks I was able to come across [MQTT][mqtt].

As stated in its [wikipedia page][wikipedia-mqtt]:

>It is a publish-subscribe-based messaging protocol. It works on top of the TCP/IP protocol. It is designed for connections with remote locations where a "small code footprint" is required or the network bandwidth is limited.

It is one of the protocols designed for IOT systems when they are constrained with slow internet speed or intermittent disconnection from its network and when the data to send is a bit small (temperature readings, float switch is up etc.).

It solves the first issue of network speed and disconnection through its Message Bus and through the Quality of Service levels.

For a really good understanding of its Quality of Service levels checkout [HiveMQ's article][hiveMq] on the subject.

I'm still in the process of learning what more the protocol can offer.

## Architectural Differences Between HTTP vs MQTT

[http](/assets/images/http.png)

HTTP is based around a client/server architecture.

The client issues a request to a resource to the server and the server responds to that request with the appropriate resource data.

The client might issue a GET, POST, PUT, PATCH, DELETE while the server may respond with a number of [Status Codes][status-codes] for each type of request with or without the associated data.

The Data that is associated with a request and a response might be a JSON, XML, YAML, HTML or some other format.

[mqtt](/assets/images/mqtt.png)

MQTT on the hand is based on Brokers, Publishers and Subscribers since it builds upon the [publish-subscribe pattern][pub-sub]. 

Subscribers and Publishers need to connect to the broker in order to do their respective roles.

Publishers publish a message with a signature of `{ topic, messageContent }` and Subscribers sibscribe to a `{ topic }`.

Whenever a Publisher publishes a `topic` that a Subscriber is subscribing it will receive the message.

You can think of it as like chat-rooms wherein you only receive messages from rooms you are in.

The data that flows in MQTT are only Strings or StringBuffers.

That means a format like JSON needs to be stringified first before being published and needs to be parsed back to JSON on receiving.

Connections in MQTT are long-lasting like [WebSockets][websocket].

Due to its pub-sub architecture MQTT allows Bi-directional communication: **The Subscriber can also be a Publisher and the Publisher can also be a Subscriber**.

## Flexibility, Support and Performance

MQTT is a bit flexible as it can run over the [WebSocket Protocol][websocket] and therefore also run on top of HTTP.

**This allows browsers to also participate in the Publishing and Subscribing of Messages as long as it connects to the broker.**

MQTT is also well supported on production environments for IOT and Web Apps.

Examples include but are not limited to:

* [Heroku][heroku-mqtt]
* [AWS][aws-mqtt]
* [ThingsSpeak][thingsSpeak]
* [Node-RED][node-red]

For its Performance Benefits I'll just point you to some really good resources. Let's just say that since MQTT is data-centric rather than document-centric its much faster over HTTP.

* [MQTT vs HTTP][mqtt-vs-http]
* [MQTT Performance over HTTP][mqtt-performance]


## Closing

In future articles that I'm going to write especially on IOT the protocol that I'll be using will now be MQTT instead of HTTP.

[heroku-mqtt]: https://devcenter.heroku.com/articles/cloudmqtt
[aws-mqtt]: https://docs.aws.amazon.com/iot/latest/developerguide/protocols.html
[thingsSpeak]: https://www.mathworks.com/help/thingspeak/use-arduino-client-to-publish-to-a-channel.html
[node-red]: https://randomnerdtutorials.com/esp8266-and-node-red-with-mqtt/
[mqtt-vs-http]: https://medium.com/mqtt-buddy/mqtt-vs-http-which-one-is-the-best-for-iot-c868169b3105
[mqtt-performance]: https://flespi.com/blog/http-vs-mqtt-performance-tests
[http]: https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol
[restful]: https://restfulapi.net/
[MQTT]: https://mqtt.org/
[wikipedia-mqtt]: https://en.wikipedia.org/wiki/MQTT
[hiveMq]: https://www.hivemq.com/blog/mqtt-essentials-part-6-mqtt-quality-of-service-levels
[pub-sub]: https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern
[websocket]: https://en.wikipedia.org/wiki/WebSocket
[status-codes]: https://www.restapitutorial.com/httpstatuscodes.html
