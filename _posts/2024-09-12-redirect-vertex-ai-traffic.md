---
layout: post
title: Using a reserved public address for Vertex AI pipeline outbound traffic
comments: true
tags: gcp networking
---
The goal of this article is to show how to use a reserved external IP address in your Vertex AI pipelines. The reason you might want to do this to be able to access endpoints over the internet which require sources IPs to be added to their [allowlist](https://en.wikipedia.org/wiki/Whitelist).

The resulting setup also applies to any two projects peered together and should apply to any service that is provided through a peered network.

# The idea
The idea is to connect to the Vertex AI network, force its outbound traffic through our own dedicated VPC network where we will have a VM instance fowarding the packets using NAT to a second network where they will be able to exit to the internet via a Cloud NAT with a reserved IP address.


Internet <-- Cloud NAT --> DMZ <-- NAT VM --> bridge <-- peering --> Vertex AI

# The implementation

I'll describe each step using terraform as this is what I use, but there's an equivalent gcloud command for every one of them of course. I'm using some custom terraform modules for some resources but it should not be too different if you use the google provider resources.

## Create the DMZ network
Create a network that will have access to the internet using Cloud NAT, say on `10.30.10.0/24`. Make sure that internal traffic is allowed, by allowing ingress and egress for `10.0.0.0/8`.


## Create the Bridge network
Create a network with no internet access, say on "10.40.10.0/24". Make sure that internal traffic is allowed, by allowing ingress and egress for `10.0.0.0/8`.

## Set up the NAT VM

{% highlight bash %}
sudo sysctl net.ipv4.conf.all.forwarding=1
sudo iptables -F && sudo iptables -F -t nat
sudo iptables --table nat --append POSTROUTING --out-interface ens4 -j MASQUERADE
sudo ip route add ${local.servicenetworking_internal_reserved_range} via ${local.bridge_default_gateway} dev ens5
{% endhighlight %}



# Credits
This post was very much inspired by this blog [post](https://willisc.com/assigning-a-static-ip-to-vertex-ai-pipelines-or-workbench-managed-notebooks-c8d15a77af8c) by Chris Willis


https://cloud.google.com/blog/products/ai-machine-learning/extending-network-reachability-of-vertex-pipelines


https://cloud.google.com/vertex-ai/docs/general/vpc-peering

https://access.redhat.com/solutions/53031