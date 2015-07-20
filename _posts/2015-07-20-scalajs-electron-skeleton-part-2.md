---
layout: post
title: Writing a native application with scala.js and Electron
comments: true
tags: sbt scala scala.js
---
When we [last]({% post_url 2015-07-19-scalajs-electron-skeleton %}) left each other, we ended up with an Electron application, embedding a tiny bit of our own javascript, compiled from Scala.js. Any way you look at it, that was pretty useless. I mean, at best we were able to re-use some code and assets that we had already available from a website.

### Stepping up
So, if you feel the need to write a desktop application, it is surely because you need to integrate with some native features such as accessing the filesystem or messing about with processes.

You may have [read](https://lihaoyi.github.io/hands-on-scala-js/#DeviationsfromScala-JVM) that java.io.File is not available from Scala.js. That makes sense, the java API around File will at some point interface with the OS and access the underlying filesystem, which in the context of a browser is not something you can do.

But in the context of Electron, access to native functionalities is provided through [io.js](https://iojs.org/en/index.html). A quick look at its [API documentation](https://iojs.org/api/) shows that there is a lot we can play with.

### Dynamic all the things
Scala.js really shines when it comes to interoperability with javascript code (for more details, that [talk](https://www.parleys.com/tutorial/scala-js-semantics-how-support-performance-javascript-interop) is worth watching), and it's no different for talking to io.js within Electron. All it takes is importing js.Dynamic and [learning](http://www.scala-js.org/doc/calling-javascript.html) [how](https://stackoverflow.com/questions/28656343/how-to-invoke-nodejs-modules-from-scala-js) to use it.

Node.js (which io.js inherits from) allows to load a module via its require function. So whenever we'll need a module, we'll have to call require with the module identifier as an argument (e.g. require("fs")). The io.js API is very helpful too, and [reminds](https://iojs.org/api/fs.html#fs_file_system) us of that.

#### Using the fs module
So, here is an example of how to list files from a directory (alternatively have a look at the [skeleton's code](https://github.com/bchazalet/scalajs-electron-skeleton/blob/4e28b88c1da1a3d1d26a9b8a879e65651651ff4f/scalajs/src/main/scala/com/example/electronapp/ElectronApp.scala)):

{% highlight scala linenos %}
import scala.scalajs.js
import js.Dynamic.{global => g}
import org.scalajs.jquery.jQuery

object MyApp extends js.JSApp {

  val fs = g.require("fs")

  def main(): Unit = {
    display(listFiles("."))
  }

  def listFiles(path: String): Seq[String] = {
    fs.readdirSync(path).asInstanceOf[js.Array[String]]
  }

  def display(filenames: Seq[String]) = {
    jQuery("body").append("<ul>")
    filenames.foreach { filename =>
      jQuery("body").append(s"<li>$filename</li>")
    }
    jQuery("body").append("</ul></p>")
  }

}
{% endhighlight %}

Here we use the readdirSync() function from the filesystem module previously loaded via require. io.js often provides a synchronous and an asynchronous version of the same function, and following the node.js philosophy, we should really be using the asynchronous (read non-blocking) one here.

#### Callback -not quite yet- hell
Let's try to rewrite our code in order to use the asynchronous readdir. Asynchronous in javascript means callback, so we will have to provide a function to handle the list of filenames once it is retrieved from the filesystem. In our case, that function, from Seq[String] => Unit is already implemented and it is named display.

TODO

{% highlight scala linenos %}
import scala.scalajs.js
import js.Dynamic.{global => g}
import org.scalajs.jquery.jQuery

object MyApp extends js.JSApp {

  val fs = g.require("fs")

  def main(): Unit = {
    jQuery("body").append("<p>Hello World from Scala.js</p>")
    listFiles(".", display)
  }

  def display(filenames: Seq[String]) = {
    jQuery("body").append("<p>Listing the files in the '.' using io.js API:")
    jQuery("body").append("<ul>")
    filenames.foreach { filename =>
      jQuery("body").append(s"<li>$filename</li>")
    }
    jQuery("body").append("</ul></p>")
  }

  def listFiles(path: String, callback: Seq[String] => Unit): Unit = {
    fs.readdir(path, { (err: js.Dynamic, files: js.Array[String]) =>
      if(err != null)
        println(err) // prints to console
      else
        callback(files)
    })
  }
}
{% endhighlight %}

TODO

### Conclusion
More questions than answers
but I'll keep that for another post on using future in Scala.js.
