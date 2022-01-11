---
title: "MIT Missing Semester 연습문제 07 풀이"
lang: "ko"
layout: post
date: 2022-01-11 22:15:35 +0900
categories: [programming,]
tags: [programming,]
---

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
<br />
