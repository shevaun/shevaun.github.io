---
layout: post
title: Developing Ruby Gems
enki_id: 8
tags: ruby gems keycutter
---

### Creating a gem from scratch

1. Create a skeleton gem

     ```bash
	 $ bundle gem my_awesome_gem
	 ```

2. Write code and tests

3. Build and install gem locally

    ```bash
    $ rake build
    $ gem install pkg/my_awesome_gem-0.0.4.gem
    ```

    (or you can type `$ rake install` instead)

4. [optional] Push the gem to rubygems.org

     ```bash
	 $ gem push pkg/my_awesome_gem-0.0.4.gem
	 ```

### Updating an existing gem

1. Make the code changes

2. Update the version number in `version.rb`

3. Build and install the gem locally

    ```bash
    $ rake install
	```

4. [optional] Push the gem to [rubygems.org](http://rubygems.org)

    ```bash
    $ gem push pkg/my_awesome_gem-0.0.4.gem
	```

### Unpacking a gem into a ruby application

```bash
$ gem unpack my_awesome_gem -v 0.0.4 --target vendor/gems
```

### Pushing a new version to rubygems

```bash
$ gem push pkg/my_awesome_gem-0.0.4.gem
```

### Multiple rubygems accounts

1. Install [keycutter](https://github.com/joshfrench/keycutter)

2. View existing keys (default key is marked with a *)

    ```bash
	$ gem keys --list
	```

3. Add a new key (you'll be prompted for your credentials)

    ```bash
    $ gem keys --add work
	```

4. Change the key used to connect to rubygems

    ```bash
    $ gem keys --default work
	```
