---
layout: post
title: Fixing line endings for git
enki_id: 18
tags: git, dos2unix
---
Every now and then I come across this error when trying to commit files to git:

@fatal: CRLF would be replaced by LF in path/to/file@

The solution is to convert the line endings to unix format with this tool:
@$ dos2unix path/to/file@
