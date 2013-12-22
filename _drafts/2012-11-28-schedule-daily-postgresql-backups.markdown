---
layout: post
title: Schedule daily PostgreSQL backups
enki_id: 16
tags: postgresql, backups, cron, pg_dump
---
### Create cron task to back up database

@$vim /etc/cron.d/db_backups@

{% highlight bash %}
# m  h  dom mon dow user     command
12 3  *   *   * <user> pg_dump -c <db_name> > "/path/to/backups/`date +\%Y-\%m-\%d-<db_name>.sql`"
{% endhighlight %}

This will create a backup at 3:12am every day. Be sure to put a newline at the end of the file.

Don't forget to create the @/path/to/backups@ directory and allow postgres to write to it.

