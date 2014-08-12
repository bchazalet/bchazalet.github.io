---
layout: post
title: hacking a sbt plugin
comments: true
tags: sbt scala
---
A sbt is a powerful tool. And it turns out to be very easy to start hacking an existing sbt plugin. Let's see how.

In the following case, we will be cloning the sbt-git plugin on our machine. The plugin sbt-git is a small plugin (small enough to be able to look at the code and understand what is going on) that adds some git functionnality to your build. For example, you can set up your project version to include the sha of the HEAD commit.

Let's start with a project that uses the sbt-git plugin.

Our project/plugins.sbt should look like this:

resolvers += "jgit-repo" at "http://download.eclipse.org/jgit/maven"
addSbtPlugin("com.typesafe.sbt" % "sbt-git" % "0.6.4")

and our build.sbt like this:

import com.typesafe.sbt.SbtGit._

versionWithGit

// Optionally:
git.baseVersion := "0.1"

lazy val root = (project in file(".")).dependsOn(customSbtGit)

lazy val customSbtGit = uri("file:///Users/bchazalet/development/code/sbt-git")


or from a specific branch from git:

lazy val root = (project in file(".")).dependsOn(customSbtGit)

lazy val customSbtGit = uri("https://github.com/bchazalet/sbt-git.git#find-git-dir")

