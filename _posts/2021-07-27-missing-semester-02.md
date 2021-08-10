---
title: "MIT Missing Semester 연습문제 02 풀이"
lang: "ko"
layout: post
date: 2021-07-27 19:48:44 +0900
categories: [programming,]
tags: [programming,]
---

1. [`man ls`](https://www.man7.org/linux/man-pages/man1/ls.1.html)를 읽고 다음과 같은 방식으로 파일을 나열하는`ls` 명령을 작성합니다.
    - 모든 파일 포함, 모든 숨겨진 파일 포함
    - 사이즈는 사람이 읽을 수 있을 법한 형식으로 (e.g. 454M instead of 454279954)
    - 최신순 파일 정렬
    - 색상화 되어 출력

    출력 예시는 아래와 같습니다.

    ```zsh
    -rw-r--r--   1 user group 1.1M Jan 14 09:53
    baz drwxr-xr-x   5 user group  160 Jan 14 09:53 .
    -rw-r--r--   1 user group  514 Jan 14 06:42 bar
    -rw-r--r--   1 user group 106M Jan 13 12:12 foo
    drwx------+ 47 user group 1.5K Jan 12 18:08 ..
    ```    

    My answer:
    ```zsh
    ls -ahltG
    (mac zsh에선 기본적으로 컬러 세팅이 되는 것 같다.)
    ```
<br />

1. 다음을 수행하는 bash 함수`marco` 및`polo`를 작성합니다.
`marco`를 실행할 때마다 현재 작업 디렉토리가 어떤 방식으로 저장되어야합니다. 그러면`polo`를 실행할 때 어떤 디렉토리에 있든 상관없이 `polo` 가`cd`를 수행해서 `marco` 를 실행 한 디렉토리로 돌아갑니다.
디버깅을 쉽게하기 위해`marco.sh` 파일에 코드를 작성하고`source marco.sh`를 실행하여 쉘에 정의를 (재)로드 할 수 있습니다.

    My answer:
    ```zsh
    #!/bin/bash

    marco() {
        export marco=$(pwd)
    }

    polo() {
        cd $marco
    }
    ```
<br />

1. 거의 실패하지 않는 명령이 있다고 가정 해보십시오. 그것을 디버그하려고 출력을 캡처해야하지만, 실패를 실행하는 데 시간이 오래 걸릴 수 있습니다.
실패 할 때까지 표준 출력 및 오류 스트림을 파일로 캡처하고 마지막에 앞의 모든 것을 출력하는 bash 스크립트를 작성하십시오. 스크립트가 실패하는 데 걸린 실행 횟수도 보고 할 수 있다면 보너스 포인트입니다.

    ```zsh
    #!/usr/bin/env bash
    # check_num.sh
    n=$(( RANDOM % 100 ))

    if [[ n -eq 42 ]]; then
        echo "Something went wrong"
        >&2 echo "The error was using magic numbers"
        exit 1
    fi

    echo "Everything went according to plan"
    ```
    
    My answer:
    ```zsh
    #!/bin/bash

    c=0
    touch result
    while [[ "$?" -ne 1 ]]; do
        c=$((c+1))
        ./check_num.sh >result 2>result
    done

    echo "Error found in count $c"
    cat result
    ```
<br />

1. 강의에서 다루었 듯이`find`의`-exec`는 검색하는 파일에 대한 작업을 수행하는데 매우 강력합니다. 
그러나 zip 파일을 만드는 것과 같이 ** 모든 ** 파일로 작업을 수행하려면 어떻게해야합니까?

    지금까지 본 것처럼 명령은 인수와 STDIN 모두에서 입력을받습니다.
    명령을 파이핑 할 때 STDOUT을 STDIN에 연결하지만 'tar'와 같은 일부 명령은 인수에서 입력을 받습니다. 이러한 문제를 해결하기 위해 STDIN을 인수로 사용하여 명령을 실행하는 [`xargs`](https://www man7.org/linux/man-pages/man1/xargs.1.html) 명령이 있습니다.
    예를 들어`ls | xargs rm`은 현재 디렉토리의 파일을 삭제합니다.

    당신의 임무는 폴더에서 모든 HTML 파일을 재귀 적으로 찾아서 zip 파일을 만드는 명령을 작성하는 것입니다. 파일에 공백이 있어도 명령은 작동되어야 합니다. (hint: check `-d` flag for `xargs`)

    My answer:
    ```zsh
    find . -type f  -name '*.html' -print0 | xargs -0 tar zcvf html.tar.gz
    ```
    (-print0을 사용할 경우, 각 결과는 null 문자, 즉 \0으로 끝난다. 이러한 옵션을 지정하는 이유는, 먼저 macOS의 find 옵션이 기본 BSD find 명령어와 다르기 때문이고, 두 번쨰로 파일이나 디렉토리 이름에 개행이나 공백이 들어가 있는 경우에도 정확하게 처리할 수 있기 떄문이다.)
<br />

1. (고급) 명령 또는 스크립트를 작성하여 디렉토리에서 가장 최근에 수정 된 파일을 재귀적으로 찾으시오. 그리고 모든 파일을 최신순으로 나열 할 수 있습니까?

    My answer:
    ```zsh
    find . -type f | ls -t | head -1
    ```
<br />