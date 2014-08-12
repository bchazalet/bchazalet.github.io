---
layout: post
title: hacking a sbt plugin
comments: true
tags: sbt scala
---
A sbt is a powerful tool. And it turns out to be very easy to start hacking an existing sbt plugin. Let's see how.

In the following case, we will be cloning the [sbt-git](https://github.com/sbt/sbt-git) plugin on our machine. It's a small plugin (small enough to be able to look at the code and understand what is going on) that adds some git functionnality to your build. For example, you can set up your project version to include the sha of the HEAD commit.

## Step 1: using the plugin
Let's start with a project that uses the sbt-git plugin.

{% highlight bash %}
~ mkdir ~/simplistic-project
~ cd ~/simplistic-project
{% endhighlight %}

Our project/plugins.sbt should look like this:
{% highlight scala linenos %}
resolvers += "jgit-repo" at "http://download.eclipse.org/jgit/maven"

addSbtPlugin("com.typesafe.sbt" % "sbt-git" % "0.6.4")
{% endhighlight %}

The first line is necessary because the plugin uses JGit internally so we need to add a resolver for sbt to find it. And the second line adds the sbt-git plugin to our project.

Our build.sbt like this:
{% highlight scala linenos %}
import com.typesafe.sbt.SbtGit._

versionWithGit

// Optionally:
git.baseVersion := "0.1"
{% endhighlight %}

To demonstrate that the plugin indeed adds the sha of the HEAD commit to the baseVersion, we will initialize a git repository in that same folder and make a first commit:
{% highlight bash %}
~ git init
~ git add build.sbt project/plugins.sbt
~ git commit -m 'first commit'
{% endhighlight %}

And, in sbt, we should get a version that includes the HEAD commit's sha:
{% highlight bash %}
~ sbt
> show version
[info] 0.1-b3942776b084fffd4b5291732f57d86171c6d9f5
{% endhighlight %}
## Step 2: use our cloned plugin version

So far, we have just set up our project to use the sbt-git plugin. But we would like to hack locally and be able to link our project to our local version of the plugin.

Let's clone the sbt-git repo (in our home folder):

    ~ git clone git@github.com:sbt/sbt-git.git ~/sbt-git


All we need to do to tell our current project to use our local version of the plugin is to modified our project/plugins.sbt to:
{% highlight scala linenos %}
resolvers += "jgit-repo" at "http://download.eclipse.org/jgit/maven"

lazy val root = (project in file(".")).dependsOn(customSbtGit)

// replace the path with yours
lazy val customSbtGit = uri("file:///home/bchazalet/sbt-git")
{% endhighlight %}

That's it. Sbt will build the plugin next time you build the project (our project now depends explicitly on the local sbt-git plugin).

## Step 3: build the plugin from github
The problem with the local setup is that our build is not very portable: it will not work on another machine that does not have the version of the plugin locally at the exact same location. This is not good at all if we use tools like Jenkins, since they won't be able to find the plugin and the build will fail.

Instead, we can push our version of the plugin to a new repository on github (or directly fork from sbt-git). If you fork the repository from github, your repository will be available at a new URL that will look similar to this [https://github.com/bchazalet/sbt-git.git](https://github.com/bchazalet/sbt-git.git) (I am using the https URI here, because the git one might fail depending on your firewall configuration; it does at my company).

So, let's use that in our build instead of our local clone. We can modify our project/plugins.sbt to look like this:
{% highlight scala linenos %}
resolvers += "jgit-repo" at "http://download.eclipse.org/jgit/maven"

lazy val root = (project in file(".")).dependsOn(customSbtGit)

lazy val customSbtGit = uri("https://github.com/bchazalet/sbt-git.git")
{% endhighlight %}

Note that you can even specify the branch you will like to build the plugin from by using the fragment part of the url. For instance, I have a branch called find-git-dir and I can use it directly in my build:
{% highlight scala linenos %}
lazy val customSbtGit = uri("https://github.com/bchazalet/sbt-git.git#find-git-dir")
{% endhighlight %}

More information on [sbt plugins, here](http://www.scala-sbt.org/0.13/docs/Plugins.html).