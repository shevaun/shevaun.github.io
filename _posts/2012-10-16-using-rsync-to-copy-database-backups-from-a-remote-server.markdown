---
layout: post
title: Using Rsync to copy database backups from a remote server
enki_id: 13
tags: rsync, crontab, backups
---

I create daily backups of this blog's database, but since they are stored on the same server as my application, they won't be much good if the whole server dies.

I wrote a small script to rsync my database backups onto my MacBook Air, which gets routinely backed up onto an external hard drive with Time Machine.

### Create script to rsync backup directory

```bash
$ vi ~/scripts/rsync-livetocode-backups
```
```bash
#!/bin/bash
rsync -rv shevaun@livetocode:/var/backups/postgres ~/backups/livetocode
```

### Make the script executable

```bash
$ chmod +x ~/scripts/rsync-livetocode-backups
```

### Write cron task to run the script daily

```bash
$ crontab -e
```
```bash
# m  h  dom mon dow user command
0 12  *   *   *  * ~/scripts/rsync-livetocode-backups
```

### View the cron task to make sure it saved correctly

```bash
$ crontab -l
```
