---
layout: post
title: "Git Workflow Cheatsheet"
date: 2012-03-18 10:00
tags: git
---

Creating a new branch
-------------------

{% highlight bash %}
$ git checkout -b branch_name    # create new local branch and switch to it
$ git commit -am                 # commit changes
$ git push -u origin branch_name # push branch to remote repo and track it
{% endhighlight %}

Working on someone else’s branch
-----------------------------

{% highlight bash %}
$ git fetch                      # pull new branches down
$ git checkout branch_name
{% endhighlight %}


Merging a feature branch into master
-------------------------------

{% highlight bash %}
$ git checkout master
$ git merge --no-ff branch_name  # merge branch into master with a new commit
$ git push origin :branch_name   # delete the remote branch
$ git branch -d branch_name      # delete the local branch
{% endhighlight %}


oh-my-zsh aliases
---------------

{% highlight bash %}
gst='git status'
gl='git pull'
gup='git fetch && git rebase'
gp='git push'
gc='git commit -v'
gca='git commit -v -a'
gco='git checkout'
gcm='git checkout master'
gb='git branch'
gba='git branch -a'
gcount='git shortlog -sn'
gcp='git cherry-pick'
glg='git log —stat —max-count=5'
glgg='git log —graph —max-count=5'
gss='git status -s'
ga='git add'
gm='git merge'
grh='git reset HEAD'
grhh='git reset HEAD —hard'
ggpull='git pull origin $(current_branch)'
ggpush='git push origin $(current_branch)'
ggpnp='git pull origin $(current_branch) && git push origin $(current_branch)'
{% endhighlight %}
