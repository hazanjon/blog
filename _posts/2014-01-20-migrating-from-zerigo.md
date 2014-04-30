---
layout: post
title:  "Migrating from Zerigo to Amazon Route 53"
date:   2014-01-20 23:00:00
categories: 
---

This is surprisingly simple to do and after the stratospheric price rises Zerigo recently announced its also common sense.

First up I grabbed a copy of David Chau's modified migration script from his blog [http://dchua.com/2013/08/18/migrate-all-dns-zones-from-zerigo-over-to-aws-route-53/](http://dchua.com/2013/08/18/migrate-all-dns-zones-from-zerigo-over-to-aws-route-53/). (Original credit to [Thewebfellas](https://gist.github.com/thewebfellas/3166184))

For sanitys sake I've forked the gist and posted it at the bottom of the page.

The script uses the zerigo\_dns gem, however the current version of the gem's Gemspec is incorrect and installs active record 4 rather than 3.2.0 which is required. Therefore you will want to checkout [Joaomsa's fix-gemspec-deps branch](https://github.com/joaomsa/zerigo_dns/tree/fix-gemspec-deps) and compile the gem from source, this is modified version of zerigo\_dns that has the correct Gemspec.


{% terminal %}
$ git clone https://github.com/joaomsa/zerigo_dns
$ cd zerigo_dns
$ git checkout -b fix-gemspec-deps origin/fix-gemspec-deps
$ gem build zerigo_dns.gemspec
$ gem install ./zerigo_dns-1.5.6.gem
{% endterminal %}

If you havent already, clone the script, add your zerigo and amazon credentials and install the route53 gem.

{% terminal %}
$ gem install route53

$ git clone https://gist.github.com/8531391.git ./zerigo_migration
$ cd zerigo_migration
$ vi zerigo_to_aws_route53.rb

$ ruby zerigo_to_aws_route53.rb
{% endterminal %} 

Zerigo Migration Script:
{% gist 8531391 %}