---
title: "MIT Missing Semester 연습문제 04 풀이"
lang: "ko"
layout: post
date: 2021-09-09 23:38:00 +0900
categories: [programming,]
tags: [programming,]
---

1. [짧은 반응형 정규식 튜토리얼](https://regexone.com/)을 완수하세요.
1. (`/usr/share/dict/words` 안에서) 적어도 세 개의 `a`가 포함되고, `s`로 끝나지 않는 단어의 개수를 찾아보세요. 그러한 단어들의 마지막 두 글자로 무엇이 가장 흔한가요? `sed`의 `y` 명령어, 또는 `tr` 프로그램은 대소문자 구분 문제를 해결하는데 도움이 될 것입니다. 그러한 두 글자 조합은 얼마나 존재하나요? 그리고 도전문제로: 두 글자 조합 중 등장하지 않는 조합은 무엇이 있나요?
<br />
    ```zsh
    첫 번째,
    cat /usr/share/dict/words 
    | grep ".*a.*a.*a.*" 
    | grep -v ".*s" 
    | wc -l

    >> 4611

    ```
    ```zsh
    두 번째,
    cat /usr/share/dict/words
    | tr {{A-Z}} {{a-z}}
    | grep ".*a.*a.*a.*" 
    | grep -v ".*s" 
    | sed -E 's/.*(..)/\1/' 
    | sort 
    | uniq -c 
    | sort -nk1,1  
    | tail -n5 
    | awk '{print $2}'

    >> on
       ae
       an
       ia
       al

    ```
    ```zsh
    세 번째,
    cat /usr/share/dict/words 
    | tr {{A-Z}} {{a-z}} 
    | grep ".*a.*a.*a.*" 
    | grep -v ".*s"
    | sed -E 's/.*(..)/\1/' 
    | uniq -c
    | wc -l

    >> 4256

    ```
    ```zsh
    도전과제,
    우선, 먼저 (aa...zz) array를 만든다.

    S=`for i in {a..z}
        for j in {a..z}
            echo "$i$j"`
    T=`cat /usr/share/dict/words
    | tr {{A-Z}} {{a-z}}
    | grep ".*a.*a.*a.*" 
    | grep -v ".*s" 
    | sed -E 's/.*(..)/\1/' 
    | sort 
    | uniq -c
    | awk '{print $2}'`

    그리고 두 어레이를 비교한다...
    echo ${S[@]} ${T[@]} | tr ' ' '\n' | sort | uniq -u

    그런데 이렇게 하는게 맞는지 잘 모르겠음...
    S의 길이: 676 (26*26), T의 길이: 118
    >> 결과값: 559
    ```
<br />
<br />
1. `sed s/REGEX/SUBSTITUTION/ input.txt > input.txt`와 같은 in-place한 치환 작업은 꽤 유혹적이지만, 그리 좋지 않은 아이디어입니다. 왜 그럴까요? 이는 `sed`의 사용에 있어서만 그런 것일까요? 이를 위해 `man sed`를 이용하여 어떻게 더 나은 방법으로 해결할 수 있는지 찾아보세요.

: `sed`의 작동방식은, 먼저 특정한 파일이나 stdin을 읽어들인 뒤, 명령에 따라 인풋이 조작되어 stdout으로 출력된다. 만약 문제의 문장처럼 리다이렉트를 하거나, `>>`로 오버라이딩을 한다면, `sed`는 출력된 결과물을 가지고 다시 인풋으로 사용하여, 결과적으로 입출력 연쇄가 끊기지 않는다. 이를 해결하기 위해선 `-i` 옵션을 사용하면 된다.
```zsh
>> sed -E -i 's/REGEX/SUBSTITUTION/ input.txt
```
`-i` 옵션은 출력을 임시 파일에 저장한 뒤, 모든 작업이 끝나면 기존 파일의 이름으로 변경한다. [참고](https://askubuntu.com/questions/795713/why-does-redirecting-sed-output-to-same-input-file-make-my-machine-unresponsive)
<br />

1. 최근 10번의 부팅을 가지고 시스템 부팅 시간의 평균, 중앙값, 그리고 최대값을 찾아보세요. Linux의 경우 `journalctl`을, macOS의 경우 `log show`를 이용하면 됩니다. 그리고 각 부팅의 시작과 끝 부분에서 로그 타임스탬프를 찾아보세요.
1. 이전 세 번의 재부팅 간에 공유되지 않은 부팅 메세지를 찾아보세요 ...
: Apple m1이어서 그런가, 설명과 인터넷을 찾아봐도 적절한 로그를 찾지 못해 풀지 못하였다...
<br />

1. `curl`을 사용하여 데이터를 가져와(fetch) 두 개의 숫자 데이터 열만 추출합니다. 만약 여러분이 HTML 데이터를 가져오는 경우, `pup`이 아마 도움이 될 것입니다. JSON 데이터인 경우, `jq`를 사용해보세요. 단일 명령으로 하나의 열의 최소값과 최대값을 찾고, 다른 명령으로 두 열 사이의 차이의 합을 구해보세요.
: 어렵고 번거로워서 포기... (흑...)
<br />