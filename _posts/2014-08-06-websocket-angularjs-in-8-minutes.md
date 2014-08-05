---
layout: post
title: websocket and angularjs in 8 minutes
comments: true
---

I had a very basic webpage running a websocket that simply updated few numbers within the page. Pretty basic, and pretty ugly looking too.

// insert gist here

And that was ok, because the page just lets an operator be updated in real-time of quite a long process (running in play + akka).

But having seen what can be done with angularjs, and angular-ui I thought it was worth trying to improve that page (and I had a little while to spare that day).

So I googled 'websockets angularjs' to find out that you could integrate [socket io with angular](http://www.html5rocks.com/en/tutorials/frameworks/angular-websockets/), or implement a [multiplexing websocket that integrates with angularjs](http://clintberry.com/2013/angular-js-websocket-service/). This is all very well, but I hadn't that much time to spare. I just wanted something very simple, even if it wasn't the proper way.

But assuming you already have your websocket, all you really need to do is to move it to a controller, to update your model from your controller, and make sure you push the changes to your view.

// insert gist here

angular-ui


