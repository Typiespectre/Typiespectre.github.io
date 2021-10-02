---
title: "MIT Missing Semester 연습문제 05 풀이"
lang: "ko"
layout: post
date: 2021-10-02 02:05:35 +0900
categories: [programming,]
tags: [programming,]
---

# 작업 제어

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
    ```sh
    > sleep 60 &
    > wait %1; ls
    
    # 그런데 그냥 "sleep 60; ls"를 해도 똑같은 결과가 나오는 것 같다...
    ```
하지만 만일 각기 다른 bash 창에서 작업을 실행하는 중이라면, 이 시도는 실패할 것입니다. `wait`은 자식 프로세스에만 적용되기 때문입니다. 지금껏 우리가 다루지 않은 기능 중 하나는 명령어 `kill`이 성공했을때 exit status가 0(zero)이 되며, 그렇지 않을때는 0이 아니라는 것(nonzero)입니다. 명령어 `kill -0`은 시그널을 보내지 않습니다만 프로세스가 존재하지 않을때 0이 아닌 exit status를 반환할 것입니다. `pidwait`이라는 이름의, pid를 가져오고 해당 프로세스가 끝날 때까지 기다리는 bash 명령어를 작성해 보세요. 불필요한 CPU 낭비 방지를 위해 `sleep` 명령어를 사용해야 합니다.
    ```sh
    > pidwait()
        {
            while kill -0 $1 2>/dev/null
                do
                    sleep 1
                done
            ls
        }
    
    > sleep 60 &
    > pidwait $(pgrep sleep)

    # 답안 참조: https://ivan-kim.github.io/MIT-missing-semester/Lecture5/
    ```
<br />

# 터미널 멀티플렉서

1. 이 `tmux` [튜토리얼](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/)을 따라해 보고 [이 단계](https://www.hamvocke.com/blog/a-guide-to-customizing-your-tmux-conf/)에 따라 기본적인 커스터마이제이션 방법을 배워보세요. (완료)

# Alias

1. 오타 낼 경우를 대비해서 `cd`로 연결되는 별칭 `dc`를 생성합니다.
    ```sh
    > alias dc="cd"
    ```
<br />

1. `history | awk '{$1="";print substr($0,2)}' | sort | uniq -c | sort -n | tail -n 10`를 실행해서 가장 많이 사용하는 명령어 10개를 확인합니다. 그리고 이들에게 짧은 별칭을 부여하는 것을 고려해 보세요. 주의: 이 방법은 Bash에서 유효합니다; 만일 ZSH를 쓰고 있다면, 그냥 `history` 말고 `history 1`를 사용하세요.
    ```sh
    # 음... 자주 사용하는 명령어 중 하나로, 알고리즘 확인 용으로 python main.py < input.txt > output.txt가 있는데,
    > alias chk="python main.py < input.txt > output.txt
    # 로 바꾸면 조금 나을까...? 그런데 그냥 화살표로 기존 히스토리에서 불러오는게 더 편한듯...
    ```
<br />

# 도트 파일

1. 도트 파일들을 위한 폴더를 생성하고, 버전 컨트롤을 구성합니다.
    ```sh
    > mkdir ~/dotfiles
    > git init ~/dotfiles
    ```
<br />

1. 최소한 프로그램 하나에 구성을 추가합니다, 예. 여러분의 셸에 약간의 커스터마이제이션 하기(시작하는 차원에서, `$PS1`을 설정해 셸 프롬프트를 커스터마이제이션 하는것과 같이 간단한 것도 좋습니다).
    ```sh
    # tmux, vimrc, zshrc에 이미 다양한 설정을 해놓고 쓰고 있다.
    ```
<br />

1. 새로운 머신에서 여러분의 도트파일을 빨리 (그리고 수작업 없이)설치하는 메서드를 생성하세요. 이것은 각 파일에 `ln -s`를 요청하는 셸 스크립트 처럼 간단한 것이어도 됩니다. 또는 [specialized utility](https://dotfiles.github.io/utilities/)를 사용할 수도 있습니다.
    ```sh
    #!/bin/bash

    files="tmux, vimrc, zshrc, vim, zsh, gitconfig, git"

    for file in $files; do
        ln -s ~/dotfiles/$file ~/.file
    done
    ```
<br />

1. 여러분의 설치 스크립트를 새로운 가상 머신에서 테스트 해 보세요.
1. 현재 도구 구성의 모든 사항을 여러분의 도트파일 레포지토리로 옮겨(migrate)줍니다.
1. 여러분의 도트 파일을 GitHub에 [공개](https://github.com/Typiespectre/dotfiles)하세요.

# 원격 머신

1. `~/.ssh/`로 이동한 다음 그곳에 SSH 키페어가 있는지 확인합니다. 만일 키페어가 없다면, `ssh-keygen -o -a 100 -t ed25519`를 통해 생성하세요. 비밀번호와 `ssh-agent`를 사용하는 것이 권장됩니다. 더 많은 정보는 [여기](https://www.ssh.com/academy/ssh/agent)서 확인하세요.