---
layout: post
title:  "Bash: Useful Tips and Tricks"
date:   2019-07-30 11:48:00 +0200
categories: bash
---
A collection of tips and tricks I have found useful. I will try to keep it updated.

## Ensure root
When you want to ensure that a script is run a root:
```bash
#!/bin/bash
if ! [ $(id -u) = 0 ]; then
   echo "script must be run as root!"
   exit 1
fi
```
