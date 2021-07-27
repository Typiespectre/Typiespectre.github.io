---
title: "MIT Missing Semester 연습문제 02 풀이"
lang: "ko"
layout: post
date: 2021-07-27 19:48:44 +0900
categories: [programming,]
tags: [programming,]
---

1. Read <span style="color: red">man</span> ls and write an **ls** command that lists files in the following manner
    - Includes all files, including hidden files
    - Sizes are listed in human readable format (e.g. 454M instead of 454279954)
    - Files are ordered by recency
    - Output is colorized
A sample output would look like this:
```zsh
-rw-r--r--   1 user group 1.1M Jan 14 09:53 baz drwxr-xr-x   5 user group  160 Jan 14 09:53 .
-rw-r--r--   1 user group  514 Jan 14 06:42 bar
-rw-r--r--   1 user group 106M Jan 13 12:12 foo
drwx------+ 47 user group 1.5K Jan 12 18:08 ..
```
<br />
