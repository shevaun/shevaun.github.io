---
layout: post
title: Upgrading PostgreSQL from 9.1 to 9.2 on Mac OSX
enki_id: 19
tags: postgresql homebrew mac osx
---

## Steps to upgrade PostgreSQL from 9.1 to 9.2 on Mac OSX using Homebrew

*Note: If you aren't on version 9.1.2, change step 5 to be your version.*

### 1. Stop your postgres server

If you're using the launcher:

```bash
$ launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist
```
otherwise:

```bash
$ pg_ctl -D /usr/local/var/postgres stop -s -m fast
```

### 2. Move your existing installation

```bash
$ mv /usr/local/var/postgres /usr/local/var/postgres91
```

### 3. Download & upgrade the 9.2 formula

```bash
$ curl https://raw.github.com/fragility/homebrew/737af01178590950749cf5e841f2d086c57\
         c5a80/Library/Formula/postgresql.rb > /usr/local/Library/Formula/postgresql.rb
$ brew upgrade postgresql
$ initdb /usr/local/var/postgres -E utf8
```

### 4. If you have the postgis library installed, do the following

```bash
$ brew uninstall postgis
$ brew tap homebrew/versions
$ brew edit postgis15
```
replace all instances of `postgresql9` with `postgresql`

```bash
$ brew install postgis15
```

### 5. Upgrade your databases

```bash
$ pg_upgrade -b /usr/local/Cellar/postgresql/9.1.2/bin -B \
    /usr/local/Cellar/postgresql/9.2.0/bin -d /usr/local/var/postgres91 -D \
	/usr/local/var/postgres
```

### 6. Start the postgres server

If you're using the launcher:

```bash
$ launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist
```

Otherwise:

```bash
$ pg_ctl -D /usr/local/var/postgres -l /usr/local/var/postgres/server.log start
```

That should be all you need to do to upgrade Postgres, but keep reading if you're still having issues.

## Other issues you might encounter

### Missing socket directory

When I tried to connect to my new postgres server with a Rails app, I discovered I didn't have the socket directory that the rails apps use to connect to postgres.

```bash
$ vi /usr/local/var/postgres/postgresql.conf
```

change `unix_socket_directory = '/var/pgsql_socket'`

```bash
$ mkdir /var/pgsql_socket
```
(and chown/chmod as necessary)

```bash
# or unload launcher
$ pg_ctl -D /usr/local/var/postgres stop -s -m fast
# or load launcher
$ pg_ctl -D /usr/local/var/postgres -l /usr/local/var/postgres/server.log start
```

### Upgrade Postgis databases from 1.5 to 2.0

(instructions by Joseph Leniston)

If you are using an earlier version of PostGIS e.g. 1.5 and are upgrading to 2.0.x then you can either do a [HARD UPGRADE](http://postgis.refractions.net/documentation/manual-2.0/postgis_installation.html#hard_upgrade) or you can simply drop your DB and start again with the new version from an updated template:

Assuming your database.yml looks like this:

```yaml
development:
adapter: postgresql
encoding: unicode
database:
schema_search_path: public
template: template_postgis
host: localhost
port: 5432
```

1. Remove old postgres scripts

    ```bash
	$ psql -d template_postgis < \
	    /usr/local/Cellar/postgis15/1.5.8/share/postgis/uninstall_postgis.sql
	```

2. Connect to new database

    ```bash
	$ psql -d template_postgis
	```

3. Install PostGIS (your file paths may vary)

    ```postgres
	CREATE EXTENSION postgis;
	CREATE EXTENSION postgis_topology;
	GRANT ALL ON geometry_columns TO PUBLIC;
	GRANT ALL ON geography_columns TO PUBLIC;
	GRANT ALL ON spatial_ref_sys TO PUBLIC;
	-- vacuum freeze: it will guarantee that all rows in the database are
	-- "frozen" and will not be subject to transaction ID wraparound
	-- problems.
	VACUUM FREEZE;
	SELECT PostGIS_full_version(); --should be new version e.g. 2.0.3
	```

4. Recreate database

	```bash
	$ rake db:drop
	$ rake db:create
	```
