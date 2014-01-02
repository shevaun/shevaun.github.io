---
layout: post
title: Remote Debugging with Pry and Pow
enki_id: 14
tags: pow pry pry-remote debugging
---

I use Pow for running my Rack Apps on my dev machine and love it. However, recently I needed to use Pry to debug some weird behaviour I was seeing in my app.
Luckily, there's a gem called [pry-remote](https://github.com/Mon-Ouie/pry-remote) which makes it dead easy to use Pry with Pow.

1. Add the gem to your Gemfile

    ```ruby
    gem 'pry-remote', group: :development
    ```

2. Install it

        $ bundle install

3. Add a debug statement to your code

    ```ruby
    binding.remote_pry
    ```

    (instead of `binding.pry`)

4. Finally, connect to your remote debugging session

        $ pry-remote
