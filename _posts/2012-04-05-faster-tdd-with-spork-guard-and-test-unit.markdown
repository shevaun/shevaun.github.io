---
layout: post
title: Faster TDD with Spork, Guard & Test::Unit
enki_id: 5
tags: rails, guard, spork, test unit, tdd
---

Using [spork](https://github.com/sporkrb/spork) and [guard](https://github.com/guard/guard) together is a great way to speed up the test feedback cycle. Having minimum latency between code changes and test output is a great productivity booster, and is especially useful for certain tasks such as Test Driven Development, fixing bugs or refactoring code.

Follow these steps to set up Spork & Guard in your Rails app with Test::Unit (steps can be easily adapted for RSpec).

### 1. Add the following gems to your Gemfile

{% highlight ruby %}
group :test do
  gem 'spork-testunit'   # runs Test::Unit with spork
  gem 'guard-spork'      # runs spork with guard
  gem 'guard-test'       # runs Test::Unit with guard
end
{% endhighlight %}

### 2. Install and bootstrap guard and spork

{% highlight bash %}
$ bundle install
$ spork --bootstrap
$ guard init spork
$ guard init test
{% endhighlight %}

### 3. Edit your Guardfile (in Rails.root)

__Note:__ The spork section needs to be above any other test sections in your Guardfile, because they are loaded in the order they are specified.

Add or remove watch expressions as you need to (see the [guard docs](https://github.com/guard/guard) for usage), but don't forget to add the `:test_unit_env => {'RAILS_ENV' => 'test'}` option to the spork section and the `:drb => true` option to the test section.

#### Example Guardfile:

{% highlight ruby %}
 guard 'spork', :test_unit_env => { 'RAILS_ENV' => 'test' }, :wait => 60 do
  watch('config/application.rb')
  watch('config/environment.rb')
  watch(%r{^config/environments/.+\.rb$})
  watch(%r{^config/initializers/.+\.rb$})
  watch('Gemfile')
  watch('Gemfile.lock')
  watch('test/test_helper.rb') { :test_unit }
end

guard :test, :drb => true, :cli => '--use-color=true' do
  watch(%r{^lib/(.+)\.rb$})     { |m| "test/#{m[1]}_test.rb" }
  watch(%r{^test/.+_test\.rb$})
  watch('test/test_helper.rb')  { "test" }

  # Rails example
  watch(%r{^app/models/(.+)\.rb$})    { |m| "test/unit/#{m[1]}_test.rb" }
  watch(%r{^app/controllers/(.+)\.rb$})  { |m| "test/functional/#{m[1]}_test.rb" }
  watch(%r{^app/views/(.+)/.+\.erb$})
                               { |m| "test/functional/#{m[1]}_controller_test.rb" }
  watch('app/controllers/application_controller.rb')
                                        { ["test/functional", "test/integration"] }
end
{% endhighlight %}

### 4. Edit your test/test_helper.rb

`spork --bootstrap` will have added `Spork.prefork` and a `Spork.each_run` blocks to your `test_helper.rb`. Read the instructions and move all your code in `test_helper` into one of these blocks.
The more code you move to `Spork.prefork` the faster your tests will run.

__Note:__ Having the line `fixtures :all` in your prefork block will load all your model classes (if they have fixtures) so be aware that changes to these classes will not be picked up until you reload spork. One way around this is to remove the `fixtures :all` line and instead require the fixtures you need in each test.

#### Example test_helper.rb:

{% highlight ruby %}
require 'rubygems'
require 'spork'

Spork.prefork do
  # Loading more in this block will cause your tests to run faster. However,
  # if you change any configuration or code from libraries loaded here, you'll
  # need to restart spork for it take effect.

  # You typically want to place most of your (slow) initializer code in here, in
  # particular, require'ing any 3rd-party gems that you don't normally modify
  # during development.

  ENV["RAILS_ENV"] = "test"
  require File.expand_path('../../config/environment', __FILE__)
  require 'rails/test_help'

  class ActiveSupport::TestCase
    fixtures :all
  end
end

Spork.each_run do
  # The Spork.each_run block is run each time you run your specs.  In case you
  # need to load files that tend to change during development, require them here.
  # With Rails, your application modules are loaded automatically, so sometimes
  # this block can remain empty.

  # Note: You can modify files loaded *from* the Spork.each_run block without
  # restarting the spork server.  However, this file itself will not be reloaded,
  # so if you change any of the code inside the each_run block, you still need to
  # restart the server.  In general, if you have non-trivial code in this file,
  # it's advisable to move it into a separate file so you can easily edit it
  # without restarting spork.
end
{% endhighlight %}

### 5. Start up Guard

Now you're all ready to go. Run:

{% highlight bash %}
$ guard
{% endhighlight %}

to start up an instance of guard which will boot up spork and run all of your tests.

Now as soon as you make changes to any watched files, the relevant tests will run, super fast!

#### Guard tips and tricks

* To run all of your tests, hit 'return' in the terminal running guard.
* To pause guard, type 'p' (hit 'return' to resume).
