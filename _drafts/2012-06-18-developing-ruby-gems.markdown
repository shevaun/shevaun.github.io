---
layout: post
title: Developing Ruby Gems
enki_id: 8
tags: ruby, gems, keycutter
---

### Creating a gem from scratch

1.    Create a skeleton gem
     {% highlight bash %}
     $ bundle gem my_awesome_gem
     {% endhighlight %}
2.    Write code and tests
3.    Build and install gem locally
{% highlight bash %}
$ rake install
{% endhighlight %}
Or
{% highlight bash %}
$ rake build
$ gem install pkg/my_awesome_gem-0.0.4.gem
{% endhighlight %}
4.    (optional) Push the gem to rubygems.org
    `$ gem push pkg/my_awesome_gem-0.0.4.gem`

### Updating an existing gem

1. Make the code changes
2. Update the version number in `version.rb`
3. Build and install the gem locally
`$ rake install`
4. (optional) Push the gem to rubygems.org
`$ gem push pkg/my_awesome_gem-0.0.4.gem`

### Unpacking a gem into a ruby application

`$ gem unpack my_awesome_gem -v 0.0.4 --target vendor/gems`

### Pushing gems to rubygems.org

*     Pushing a new version

`$ gem push pkg/my_awesome_gem-0.0.4.gem`

*     Dealing with multiple rubygems accounts

1. Install [keycutter](https://github.com/joshfrench/keycutter)
2. View existing keys (default key is marked with a *)
`$ gem keys --list`
3. Add a new key (you'll be prompted for your credentials)
`$ gem keys --add work`
4. Change the key used to connect to rubygems
`$gem keys --default work`
