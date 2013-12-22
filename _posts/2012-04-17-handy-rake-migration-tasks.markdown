---
layout: post
title: Handy Rake Migration Tasks
enki_id: 6
tags: ruby, rails, rake, migrations
---

*    Run Pending Migrations

     `rake db:migrate`

*    Rollback last migration

     `rake db:rollback`

*    Rollback several migrations

     `rake db:rollback STEP=3`

*    Rerun last/several migrations

     `rake db:migrate:redo (STEP=3)`

*    Recreate the database from scratch

     `rake db:drop db:create db:migrate`
