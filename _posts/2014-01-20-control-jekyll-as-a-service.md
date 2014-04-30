---
layout: post
title:  "Controlling Jekyll as a Service on Ubuntu"
date:   2014-01-20 00:00:00
categories: 
---

After having to restart my dedicated server a few times I realised that while the VM would come back online automatically I was getting bored of having to log into the server to startup my Jekyll blog.

To achieve this I created an upstart script for Jekyll. 

{% gist 8725263 %}

To use the script simply create the file `jekyll.conf` in `/etc/init` and then replace the env variables with your own jekyll site information, then try starting jekyll using your new upstart script!

{% terminal %}
$ start jekyll
jekyll start/running, process 1234
{% endterminal %}
