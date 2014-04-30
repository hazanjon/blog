---
layout: post
title:  "Update Amazon Route 53 dynamically using Dynroute"
date:   2014-01-25 23:00:00
categories: 
---

After the shock of Zerigo's price 'updates' I have now migrated all my DNS hosting over to Amazon's Route 53 service. Its still slightly more expensive that I use to pay but I am not out of pocket due to a nice lump of amazon credit I picked up at last years TNW Hack Battle.

So far I have found Route 53 incredibly easy to setup and use. However the one feature that was missing was the ability to dynamically update one of my domains to my home IP. Most of the time my IP stays unchanged but occasionally BT will change my IP several times in one day.

A did a bit of searching and eventually found [Dynroute](https://github.com/alessioalex/dynroute) by [Alexandru Vladutu](https://github.com/alessioalex). This has turned out to be an absolute godsend! Dynroute is written in NodeJS and can be installed easily using npm:

{% terminal %}
$ npm install -g dynroute
{% endterminal %}

Now keeping my home domain up to date is as simple as setting up the following command as a cron (You can run it as a daemon but I prefer cron):

{% terminal %}
$ /usr/bin/dynroute -d mydomain.com --once
{% endterminal %}
