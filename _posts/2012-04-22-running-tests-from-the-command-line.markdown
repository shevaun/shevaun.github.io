---
layout: post
title: Running tests from the command line
enki_id: 7
tags: ruby rails test-unit rspec testing command-line
---

Sometimes you want to be able to isolate a single test and run it independently of any other tests.

### Using Test::Unit

* Run a single test file

    ```bash
	$ ruby -I"lib:test" test/unit/file_load_test.rb
	```

* Run a single test within a file

    ```bash
	$ ruby -I"lib:test" test/unit/file_load_test.rb -n test_should_load_a_gz_file
	```

### Using RSpec

* Run a single test file

    ```bash
	$ rspec spec/models/transmission_outage_spec.rb --format doc
	```

* Run a single test within a file (where 56 is the line number of the test to be run)

    ```bash
	$ rspec spec/models/transmission_outage_spec.rb:56 --format doc
	```
