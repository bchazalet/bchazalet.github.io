---
layout: post
title: Writing a native application with scala.js and Electron
comments: true
tags: scala scala.js
---

I have been looking at [Scala.js](http://www.scala-js.org/) lately, and like [many](http://www.47deg.com/blog/functional-programming-meets-the-client-side) [other](http://underscore.io/blog/posts/2015/06/10/scalajs-scaladays.html) I am very impressed by it.

### Scala.js
Scala.js lets you write Scala code that compiles to javascript, which you then use in your web application like any other .js file. That alone is a great achievement, but it goes far beyond  thanks to all the work that has been put into providing [interoperability with Javascript](https://www.parleys.com/tutorial/scala-js-semantics-how-support-performance-javascript-interop) code. For example, manipulating the dom via jQuery in Scala is something I could not have even dreamt of.

#### The graal
To me, the killer feature of Scala.js is how cross compiling lets you [share code](https://www.parleys.com/tutorial/towards-browser-server-utopia-scala-js-example-using-crdts), functions, algorithms and models between server and client logic. The [uPickle](https://github.com/lihaoyi/upickle-pprint) library for example, makes it almost transparent to send and receive data between client and server.

But I don't want to spend too much time on Scala.js, its features and its greatness (maybe in another post), and instead I want to focus on another neat use case.

### Electron
#### From github with love
Electron was born in the process of building the [Atom](https://atom.io/) editor.

From Electron's [readme](https://github.com/atom/electron) file:
> The Electron framework lets you write cross-platform desktop applications using JavaScript, HTML and CSS. It is based on io.js and Chromium and is used in the Atom editor.
>

Now, with my own words: [Electron](http://electron.atom.io/) enables you to build native applications using web technologies. If you use [Slack](https://slack.com/), that's how their native application is built. And that's also why their web application looks so much like their native one: it's made of the very same stuff.

At the heart of Electron is an instance of Chromium which is used to render the UI and whose regular HTML5 API is extended with node.js API to give access to native features.

### Scala on the desktop
#### Another graal
Now, I am a Scala developer. I don't do much front-end development, but I know more or less my way around html, css and <s>javascript</s> Scala.js. And many times I've felt the need to take a project beyond the command line interface, with the ambition to reach a different audience. What I've always wanted was to be able to quickly build native applications using web technologies but, at the same time, I never really had the motivation to dive deep into javascript. I just was too lazy.

The combination of Scala.js and Electron lets me write my code in Scala, compile it to javascript, bundle it with assets and obtain a native application.

I have put together a very basic skeleton, [available on github](https://github.com/bchazalet/scalajs-electron-skeleton), to help anyone get started.
The result is a very basic hello world, electron-based application:

![screenshot]({{ site.url }}/public/electron-scalajs-hello-world.png)

From there onwards, the world is yours.

#### Bonus
One thing I have already mentioned about Scala.js is how it allows for code sharing between server and client, when used in the normal web application scenario. In fact, it is no different here: I can still write both my command-line application and my electron-based application using pretty much the same code (if that's what you want, I recommend following the [Scala.js cross-builing doc](http://www.scala-js.org/doc/sbt/cross-building.html)).


### Conclusion
Scala.js is obviously the result of some hard work. And as often happens with such great projects, it enables use cases that were never anticipated by their creators. Building native applications might be one of those.

[Next]({% post_url 2015-07-20-scalajs-electron-skeleton-part-2 %}), we'll see how to access the native parts (filesystem, process, etc) by using the node.js API from Scala.js, all within Electron of course.
