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
cat /usr/share/dict/words
 | grep ".*a.*a.*a.*"
 | grep -v ".*'s"
 | wc -l

4611
```
```zsh
cat /usr/share/dict/words | grep ".*a.*a.*a.*" | grep -v ".*s" | sed -E 's/.*(..)/\1/' | sort | uniq -c | sort -nk1,1  | tail -n5 | awk '{print $2}'

on
ae
ia
an
al
```
```zsh
cat /usr/share/dict/words | tr {{A-Z}} {{a-z}} | grep ".*a.*a.*a.*" | grep -v ".*s" | uniq | wc -l

4815
```
```zsh
우선, 먼저 (aa...zz) array를 만든다.

S=`for i in {a..z}
    for j in {a..z} do
        echo "$i$j"
    done`
T=`/usr/share/dict/words | grep ".*a.*a.*a.*" | grep -v ".*s" | sed -E 's/.*(..)/\1/' | sort | uniq -c | sort -nk1,1 | awk '{print $2}'`

그리고 두 어레이를 비교한다...
echo ${S[@]} ${T[@]} | tr ' ' '\n' | sort | uniq -u

그런데 이렇게 하는게 맞는지 잘 모르겠음...
```
<br />
