---
title: "MIT Missing Semester 연습문제 06 풀이"
lang: "ko"
layout: post
date: 2021-12-29 22:31:08 +0900
categories: [programming,]
tags: [programming,]
---

1. Git에 대한 경험이 없는 경우, [Pro Git](https://git-scm.com/book/ko/v2)의 처음 몇 장을 읽어보거나 [Learn Git Branching](https://learngitbranching.js.org/?locale=ko)과 같은 튜토리얼을 훑어보세요. 이러한 작업을 통해 Git 명령어와 데이터 모델을 관련시켜 보세요. (Learn Git Branching 완료)

1. [수업을 위한 웹사이트](https://github.com/missing-semester/missing-semester)를 클론해보세요.
- 버전 history를 그래프로 시각화하여 살펴보세요.
```sh
git log --all --graph --decorate --oneline
```
- 마지막으로 `README.md`를 수정한 사람은 누구입니까? (힌트: `git log`인수를 사용해보세요)
```sh
git log --all --graph --decorate README.md

output:
* commit 8010724516adc968765e8efd14991f262f0d0423
| Author: Anish Athalye <me@anishathalye.com>
| Date:   Tue Jul 27 08:29:08 2021 -0400
|
|     Separate build and links status
|
```

