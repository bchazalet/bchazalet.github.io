---
layout: post
title: running sbt in interactive mode with specific scala options
---

This has annoyed me for a while, but it's actually simple. While running sbt in interactive mode, sometimes you might want to compile with the '-feature' options, to see things MatchError warnings for example.

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

All you need to do is to add your options to scalacOptions, exactly as you would in your build.sbt:

~ sbt
> set scalacOptions += "-feature"

then you can check or confirm its scalacOptions value:
~ sbt
> show scalacOptions

you can also check your current session temporary settings:
~ sbt
> session list
  1. scalacOptions += "-feature"

Finally, let's re-compile everything with the warnings showing:
> sbt
> set scalacOptions += "-feature"
> clean compile
