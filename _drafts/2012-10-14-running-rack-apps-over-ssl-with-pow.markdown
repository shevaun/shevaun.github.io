---
layout: post
title: Running Rack Apps over SSL with Pow
enki_id: 12
tags: pow, https, ssl, stud
---
I've recently switched to the "Pow":http://pow.cx/ Web Server for Rack Apps and am loving it so far.

However, today I needed to run an App over SSL and Pow doesn't have support for doing this yet. I found a simple solution on Stack Overflow, where Paul Nicolson shared his script for using Stud to generate an SSL cert for .dev

The script and instructions are "here":https://gist.github.com/2050941#file_gistfile1.md

It took me 2 mins to install, and it Just Works!
