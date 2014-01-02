---
layout: post
title: Manually Backing up and Restoring a PostgreSQL Database
enki_id: 17
tags: postgresql pg_dump
---
I often find I am creating dumps of data and restoring it to another database, but not frequently enough that I can remember the syntax off the top of my head.

### Dump PostgreSQL database to a file

```bash
$ pg_dump -h [db_host] -c [db_name] > /where/to/dump/db_name_dump.sql
```

### If the database to be restored doesn't already exist, create it

```bash
$ psql template1
```
```postgres
CREATE DATABASE [db_name] OWNER [db_owner] ENCODING 'UTF8';
```

### Restore PostgreSQL database from the dump file

```bash
$ psql -h [db_host] [db_name] < db_name_dump.sql
```
