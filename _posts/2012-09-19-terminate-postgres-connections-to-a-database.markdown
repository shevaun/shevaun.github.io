---
layout: post
title: Terminate postgres connections to a database
enki_id: 10
tags: postgresql
---

Sometimes I want to alter a PostgreSQL database in some way but I am prevented by other connections, as this error indicates:

```bash
ERROR:  database "my_database" is being accessed by other users
DETAIL:  There are 2 other session(s) using the database.
```

It's not always easy to figure out which applications have open sessions, so here's a great script I found on [Stack Overflow](http://stackoverflow.com/a/5109190/156109) to terminate all connections to the database:

```postgres
SELECT
    pg_terminate_backend(procpid)
FROM
    pg_stat_activity
WHERE
    -- don't kill my own connection!
    procpid <> pg_backend_pid()
    -- don't kill the connections to other databases
    AND datname = 'my_database'
    ;
```
