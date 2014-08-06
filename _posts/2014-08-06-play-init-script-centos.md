---
layout: post
title: basic init script for a play application running on centos
comments: true
---

When deploying a play app, you might need to make it a service so that it automatically starts after a reboot for example.

On a CentOS server, you can write a bash script in /etc/init.d and enable it with chkconfig. 

This is my take on writing a -hopefully- generic script that should work for most play applications. It relies on the fact that plays automatically creates a file containing its pid within its own folder (always you can [change the location of that pid file](http://www.playframework.com/documentation/2.3.x/ProductionConfiguration) if you need to). 

I am also assuming that the application has been package with the dist command. In my case, jenkins build it with ```clean universal:package-zip-tarball``` and copies over the tgz file to the server. The resulting folder (after extracting the files) is where the init script will point at. It looks like it

{% gist bchazalet/2c0b16e7c5278fd2f3c8 %}

To make it work for another play app, it should only be a matter of updating the parameters in the first 4 lines.

Note that I use DAEMON_OPTS to pass it production server specific file such as:
```
DAEMON_OPTS="-Dconfig.resource=prod.conf -Dlogger.resource=prod-logger.xml
```
