---
title: "MIT Missing Semester 연습문제 01 풀이"
lang: "ko"
layout: post
date: 2021-07-12 00:37:01 +0900
categories: [programming]
tags: [programming]
---
1. Create a new directory called **missing** under **/tmp**.
```zsh
cd tmp && mkdir missing
```

2. Look up the **touch** program. The **man** program is your friend.

**touch** -- change file access and modification times

**touch** 명령어는 파일을 생성하거나 갱신하는 명령어이다. 존재하지 않는 파일명을 지정하면 지정된 파일명으로 새로운 파일이 생성되며, 이미 존재하는 파일명을 지정하면 해당 파일의 수정시간이 업데이트 된다.

**Usage**:
```zsh
touch [OPTION]... FILE...
```

