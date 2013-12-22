---
layout: post
title: Setting up a Rails 3.2 project to use rspec & factory_girl in the generator
enki_id: 9
tags: rails, rspec, factory_girl
---

If you are starting a new Rails project and you want to use RSpec and FactoryGirl instead of Test::Unit and Fixtures, it's easy to get Rails to recognize this and generate the correct files for you.

# Create a new rails app without the test directory (or if the app already exists, just delete the test directory)
@$ rails new myproject -T@
# Add the gems to the :development *AND* :test groups in your Gemfile

{% highlight bash %}
group :test, :development do
  gem 'rspec-rails'
  gem 'factory_girl_rails'
end
{% endhighlight %}

That's it! Now when you generate your models and controllers using @rails g model my_model@ you will also get the spec and factory created for free.
