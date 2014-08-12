---
layout: post
title: running sbt in interactive mode with -feature
comments: true
tags: sbt scala
---

This has annoyed me for a while, but it's actually simple. 

While running sbt in interactive mode, sometimes you might want to compile with the '-feature' options, to see MatchError warnings for example.

Typing 'compile -feature' won't work.

    ~ sbt
    > compile -feature
    [error] Expected ID character
    [error] Not a valid command: compile (similar: completions)
    [error] Expected project ID
    [error] Expected configuration
    [error] Expected ':' (if selecting a configuration)
    [error] Expected key
    [error] Expected '::'
    [error] Expected end of input.
    [error] compile -feature
    [error]

All you need to do is to add your options to scalacOptions, exactly as you would in your build.sbt.

    ~ sbt
    > set scalacOptions += "-feature"

You can also check or confirm its scalacOptions value.

    ~ sbt
    > show scalacOptions

The option you just set is temporary (for the current session) and won't affect your project build file. You can check your current session temporary settings.

    ~ sbt
    > session list
      1. scalacOptions += "-feature"

Finally, let's re-compile everything with the warnings showing.

    > sbt
    > set scalacOptions += "-feature"
    > clean compile

More info on [inspecting the build](http://www.scala-sbt.org/0.13/docs/Howto-Inspect-the-Build.html), from the sbt doc.

Edit: you can also save back your session's options to your project build.sbt file:

    > session save

Check [this talk from Scala Days 2014](http://parleys.com/play/53a7d2c6e4b0543940d9e54d) to learn about sbt and how to write an sbt plugin.

Well, that wasn't so hard. 
