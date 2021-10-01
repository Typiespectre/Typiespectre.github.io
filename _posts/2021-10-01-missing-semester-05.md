---
title: "MIT Missing Semester 연습문제 05 풀이"
lang: "ko"
layout: post
date: 2021-10-02 02:05:35 +0900
categories: [programming,]
tags: [programming,]
---

1. 지금까지 공부한 바에 따르면, 우리는 명령어 `ps aux | grep` 로 PID를 조회하여 작업을 종료할 수 있습니다. 하지만 이것보다 더 나은 방법이 있습니다. 터미널에서 `sleep 10000`를 실행하고, `Ctrl-Z`로 그것을 백그라운드 실행하세요. 그리고 `bg`를 통해 이 작업을 계속합니다. 이제 [pgrep](https://www.man7.org/linux/man-pages/man1/pgrep.1.html)을 사용해 pid를 찾은 다움 [pkill](https://man7.org/linux/man-pages/man1/pgrep.1.html)을 실행하면 pid를 직접 타이핑하지 않고 작업을 종료시킬 수 있습니다. (힌트: -af 플래그를 사용하세요).
    ```sh
    > pgrep -fl sleep
    
    95662 sleep 10000
    12345 sleep 60
    ...

    > pkill -af sleep
    [1]  + 95662 terminated  sleep 10000
    ```
<br />

1. 여러분이 다른 프로세스가 종료되기를 기다려 새 프로세스를 시작하려고 한다고 합시다, 이것을 어떻게 할 수 있을까요? 이 실습에서 프로세스 제한은 항상 `sleep 60 &`로 합니다. 이것을 가능케 하는 또 다른 방법은 [wait](https://www.man7.org/linux/man-pages/man1/wait.1p.html) 명령어를 사용하는 것입니다. sleep 명령어를 실행한 뒤 백그라운드 작업이 끝날 때까지 기다려 `ls`가 실행되도록 하세요.
