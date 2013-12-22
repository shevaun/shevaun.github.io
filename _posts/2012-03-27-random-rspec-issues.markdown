---
layout: post
title: Random RSpec issues
enki_id: 3
tags: rspec, testing
---

### If RSpec hangs while running a test

Look for cases where you have the subject followed by space should, e.g.

{% highlight ruby %}
specify { project should eq another_project }
{% endhighlight %}

and replace the space with a full stop:

{% highlight ruby %}
specify { project.should eq another_project }
{% endhighlight %}

This has happened to me twice now, and the first time it took ages to debug.
