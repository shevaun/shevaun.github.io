---
layout: post
title: Uses for CAT
enki_id: 2
tags: cat, linux, ubuntu
---

### Append line(s) to end of a file

{% highlight bash %}
$ cat >> notes.txt
Get milk on the way home
Ctrl-D
{% endhighlight %}

### Create a new file

{% highlight bash %}
$ cat > awesome_script.rb
#!/usr/bin/env ruby
puts "Hello World!"
Ctrl-D
{% endhighlight %}

### Count occurrences of a string

{% highlight bash %}
$ cat <path_to_files> | grep -c <string>
{% endhighlight %}