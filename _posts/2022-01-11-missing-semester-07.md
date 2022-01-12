---
title: "MIT Missing Semester 연습문제 07 풀이"
lang: "ko"
layout: post
date: 2022-01-11 22:15:35 +0900
categories: [programming,]
tags: [programming,]
---
### 디버깅

1. 리눅스라면 `journalctl`, 맥OS라면 `log show`를 사용하여 마지막 슈퍼 유저의 접근과 명령어에 대해 출력하세요. 만약 실행한 명령이 없으면 `sudo ls`같은 무해한 명령어를 실행한 뒤에 다시 확인할 수 있습니다.
```sh
> log show --last 24h | grep sudo
# or...
> log show --info --debug --last 24h --predicate "process == 'sudo'
```
<br />
1. 다양한 명령어에 친숙해지기 위해서 [이곳](https://github.com/mingrammer/pdb-tutorial)에서 `pdb` 튜토리얼을 해보세요. 더욱 심화된 튜토리얼을 하고 싶다면 [이곳](https://realpython.com/python-debugging-pdb/)을 참조하세요. (완료)
1. [shellcheck](https://www.shellcheck.net/)을 설치하고 다음 스크립트를 체크해보세요. 어떤 부분이 틀렸는지 알아보고 고치세요. 경고 메세지를 자동으로 볼 수 있도록 코드 에디터에 린터(linter) 플러그인을 설치하세요.
    ```sh
    #!/bin/sh
    ## Example: a typical script with several problems
    ## myscript.sh
    for f in $(ls *.m3u)
    do
      grep -qi hq.*mp3 $f \
        && echo -e 'Playlist $f contains a HQ file in mp3 format'
    done
    ## ---[EOF]---
    ```

    ```sh
    > shellcheck myscript

    In test line 3:
    for f in $(ls *.m3u)
             ^---------^ SC2045 (error): Iterating over ls output is fragile. Use globs.
                  ^-- SC2035 (info): Use ./*glob* or -- *glob* so names with dashes won't become options.


    In test line 5:
      grep -qi hq.*mp3 $f \
               ^-----^ SC2062 (warning): Quote the grep pattern so the shell won't interpret it.
                       ^-- SC2086 (info): Double quote to prevent globbing and word splitting.

    Did you mean:
      grep -qi hq.*mp3 "$f" \


    In test line 6:
        && echo -e 'Playlist $f contains a HQ file in mp3 format'
                ^-- SC3037 (warning): In POSIX sh, echo flags are undefined.
                   ^-- SC2016 (info): Expressions don't expand in single quotes, use double quotes for that.
    ## ---[EOF]---
    ```

    ```sh
    ## Vim의 syntastic 플러그인을 사용하면 어디가 잘못되었는지 표시해준다.
    #!/bin/sh
    for f in *.m3u
    do
      grep -qi "hq.*mp3" "$f" \
        && echo "Playlist $f contains a HQ file in mp3 format"
    done
    ```
<br /><br />

1.(심화) [reversible debugging](https://undo.io/resources/reverse-debugging-whitepaper/)에 대해 읽어보고 [rr](https://rr-project.org/) 혹은 [RevPDB](https://morepypy.blogspot.com/2016/07/reverse-debugging-for-python.html)를 사용하여 간단한 작업을 해보세요.
- TDD(Time Travel Debugger)는 기존의 디버거와 다르게 코드의 진행과 반대로도 디버깅을 할 수 있는 기능을 가지고 있다(마음대로 함수 밖으로 빠져나오거나 들어갈 수도 있다!) 하지만 파이썬을 사용하는 입장에서, RevPDB는 2017년 이후 업데이트가 되지 않고 있고, 여전히 알파 단계이며 파이썬 2.7만을 지원한다고 한다. 이외에도 파이썬에는 마땅한 TDD가 없는 것 같다. 기존 pdb에서 jump를 이용하면 코드를 마음대로 이동할 수 있는 것 같기도 하다.

### 프로파일링
1.[이곳](https://missing-semester-kr.github.io/static/files/sorts.py)에 다양한 정렬 알고리즘이 구현되어 있습니다. [cProfile](https://docs.python.org/3/library/profile.html)과 [line_profiler](https://github.com/rkern/line_profiler)를 사용하여 삽입 정렬과 퀵 정렬의 수행 시간을 비교해보세요. 각 알고지름에서 병목 현상이 일어나는 곳은 어디인가요? `memory_profiler`를 사용하여 메모리 사용량을 비교해보고 왜 삽입 정렬이 나은지를 알아보세요. 내장된 버전의 퀵 소트를 체크해보세요. 도전: `perf`를 사용하여 각 알고리즘의 주기 횟수 및 캐시 히트/미스를 확인해보세요.

