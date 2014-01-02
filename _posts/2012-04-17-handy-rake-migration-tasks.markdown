---
layout: post
title: Handy Rake Migration Tasks
enki_id: 6
tags: ruby rails rake migrations
---

*    Run Pending Migrations

     ```bash
	 $ rake db:migrate
	 ```

*    Rollback last migration

     ```bash
	 $ rake db:rollback
	 ```

*    Rollback several migrations

     ```bash
	 $ rake db:rollback STEP=3
	 ```

*    Rerun last/several migrations

     ```bash
	 $ rake db:migrate:redo (STEP=3)
	 ```

*    Recreate the database from scratch

     ```bash
	 $ rake db:drop db:create db:migrate
	 ```
