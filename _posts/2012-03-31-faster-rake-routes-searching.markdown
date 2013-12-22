---
layout: post
title: Faster Rake Routes Searching
enki_id: 4
tags: rails, routes, grep
---

### groute

If you are sick of the time it takes to run `rake routes` every time you want to inspect your routes or search for a particular one, check out this gem that my friend [Jordan Carter](http://wetware.co.nz/) wrote called [groute](https://github.com/jordandcarter/groute).

It caches your routes in a temporary file so that subsequent searches are lightning fast, and only regenerates the routes when they change.

### Usage

Install the gem:

{% highlight bash %}
$ gem install groute
{% endhighlight %}

and use it like this:

{% highlight bash %}
$ groute products
{% endhighlight %}

which is the same as doing:

{% highlight bash %}
$ rake routes | grep products
{% endhighlight %}

but way faster!
