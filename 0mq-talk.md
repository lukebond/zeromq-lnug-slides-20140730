<!-- background: #fff4e3-->
<!-- color: #2d2422 -->
<!-- font: rockwell -->

# 0MQ with Node.js

***

## About me

Luke Bond  
Node.js Server Developer at YLD.io  
http://yld.io  
[@lukeb0nd](https://twitter.com/lukeb0nd)  
https://github.com/lukebond  
http://lukebond.ghost.io

***

# What is 0MQ?

***

## What is 0MQ?

From the horse's mouth (http://zeromq.org):

* Connect your code in any language, on any platform.
* Carries messages across inproc, IPC, TCP, TPIC, multicast.
* Smart patterns like pub-sub, push-pull, and router-dealer.
* High-speed asynchronous I/O engines, in a tiny library.
* Backed by a large and active open source community.
* Supports every modern language and platform.
* Build any architecture: centralized, distributed, small, or large.
* Free software with full commercial support.

***

## 0MQ Philosophy

> Originally the zero in ØMQ was meant as "zero broker" and (as close to) "zero latency" (as possible). Since then, it has come to encompass different goals: zero administration, zero cost, zero waste. More generally, "zero" refers to the culture of minimalism that permeates the project. We add power by removing complexity rather than by exposing new functionality.

In many ways it is a reaction to centralised, monolithic and expensive message broker solutions of the past.

***

## So 0MQ is just sockets?

Funny: http://zeromq.org/topics:omq-is-just-sockets

**tl;dr**: on the surface it appears to be "just sockets" but that ignores all the wonderful ways in which it simplifies difficult problems.

***

## 0MQ vs RabbitMQ, ActiveMQ et. al.

* Centralised vs decentralised
* Heavy-weight vs light-weight
* Monolithic vs componentised
* Out-of-the-box vs roll-your-own
* All singing, all dancing vs only what you need

0MQ provides the building blocks for you to build things like RabbitMQ.

But the idea is that you could use it to build the solution _your_ problem needs.

***

## "Why Should I Care?", you ask

By default we tend to reach for tools we know:
* Amazon SQS
* RabbitMQ or other message broker
* HTTP/REST

Exposure to 0MQ will have you thinking outside this box, using simpler, leaner components.

Great for microservices, and great for what Node's great at: asynchronous, networked I/O.

***

### Node.js Bindings

```
$ npm install zmq
```

Not perfect, but does a good job of 'Node-ifying' 0MQ

Wraps up a lot of the C-ness and removes boilerplate.

***

## Patterns

A ZeroMQ network is a network of nodes or sockets, each functioning in a certain mode or role.

Each mode can only interoperate with other particular nodes; e.g. 'REQ' works with 'REP'.

These modes functioning together are called patterns. Examples of patterns are request-reply, publish-subscribe, push-pull and router-dealer.

***

## Patterns - Request-Reply

![Request-Reply](https://github.com/imatix/zguide/raw/master/images/fig2.png)

***

## Patterns - Publish-Subscribe

![Publish-Subscribe](https://github.com/imatix/zguide/raw/master/images/fig4.png)

***

## How This Looks in Node.js Code

_producer.js:_

```js
var zmq = require('zmq')
  , sock = zmq.socket('push');

sock.bindSync('tcp://127.0.0.1:3000');
console.log('Producer bound to port 3000');

setInterval(function(){
  console.log('sending work');
  sock.send('some work');
}, 500);
```

***

## How This Looks in Node.js Code

_worker.js:_

```js

var zmq = require('zmq')
  , sock = zmq.socket('pull');

sock.connect('tcp://127.0.0.1:3000');
console.log('Worker connected to port 3000');

sock.on('message', function(msg){
  console.log('work: %s', msg.toString());
});
```

***

## Patterns - Divide and Conquer

![Divide and Conquer](https://github.com/imatix/zguide/raw/master/images/fig5.png)

***

## Patterns

Great for:
- data transformation pipelines
- queues / flow control
- message routing
- activity streams (pub-sub)

***

# Reliable Request-Reply (RRR) Patterns

* Lazy Pirate
* Simple Pirate
* Paranoid Pirate

***

# Pirates??

![Pirates](http://www.gamestm.co.uk/wp-content/uploads/2014/03/MIpirates.jpeg)

***

# Pirates??

![Pirates](http://www.gamestm.co.uk/wp-content/uploads/2014/03/MIpirates.jpeg)

* Reliable-Request-Reply (RRR)
* Lazy/Simple/Paranoid relates to the approach to reliability

***

## Lazy Pirate Pattern

![Lazy Pirate Pattern](https://github.com/imatix/zguide/raw/master/images/fig47.png)

* Client retry
* Can handle server crashes (if recovery means restarting the same server)

***

## Simple Pirate Pattern

![Simple Pirate Pattern](https://github.com/imatix/zguide/raw/master/images/fig48.png)

***

## Simple Pirate Pattern

* Queue as proxy to multiple servers (workers)
* Can recover from client and worker crashes/restarts
* ...but not queue crashes; clients will recover but workers won't

***

## Paranoid Pirate Pattern

![Paranoid Pirate Pattern](https://github.com/imatix/zguide/raw/master/images/fig49.png)

***

## Paranoid Pirate Pattern

* Queue-worker heartbeat
* Will recover from all scenarios
* Queue is still a central point of failure

***

## These are just a few patterns of many...

The 0MQ guide includes many other useful patterns for different messaging and networking scenarios.

You can 'easily' build persistence on top of these patterns.

***

## Code

I've implemented these patterns as Node.js modules:

* Lazy pirate client - https://www.npmjs.org/package/zeromq-lpclient
* Lazy pirate server - https://www.npmjs.org/package/zeromq-lpserver
* Paranoid pirate queue - https://www.npmjs.org/package/zeromq-ppqueue
* Paranoid pirate worker - https://www.npmjs.org/package/zeromq-ppworker

_(I haven't implemented the Simple Pirate server)_

***

# Demo!

### _fingers crossed!_

***

## Where to learn more?

The ZeroMQ Guide: http://zguide.zeromq.org

Also available in old-fashioned paper:
![O'Reilly Book](http://akamaicovers.oreilly.com/images/0636920026136/cat.gif)

***

# Happy hacking!

Luke Bond  
Node.js Server Developer at YLD.io  
http://yld.io  
[@lukeb0nd](https://twitter.com/lukeb0nd)  
https://github.com/lukebond  
http://lukebond.ghost.io
