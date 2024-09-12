---
layout: post
title: Using a reserved public address for Vertex AI pipeline outbound traffic
comments: true
tags: gcp networking
---
The goal of this article is to show how to use a reserved external IP address in your Vertex AI pipelines. The reason you might want to do this to be able to access endpoints over the internet which require sources IPs to be added to their [allowlist](https://en.wikipedia.org/wiki/Whitelist).

The resulting setup also applies to any two projects peered together and should apply to any service that is provided through a peered network.


## The idea




## Credits
https://willisc.com/assigning-a-static-ip-to-vertex-ai-pipelines-or-workbench-managed-notebooks-c8d15a77af8c

https://cloud.google.com/blog/products/ai-machine-learning/extending-network-reachability-of-vertex-pipelines