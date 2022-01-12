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
<br />

1. (심화) [reversible debugging](https://undo.io/resources/reverse-debugging-whitepaper/)에 대해 읽어보고 [rr](https://rr-project.org/) 혹은 [RevPDB](https://morepypy.blogspot.com/2016/07/reverse-debugging-for-python.html)를 사용하여 간단한 작업을 해보세요.
- TDD(Time Travel Debugger)는 기존의 디버거와 다르게 코드의 진행과 반대로도 디버깅을 할 수 있는 기능을 가지고 있다(마음대로 함수 밖으로 빠져나오거나 들어갈 수도 있다!) 하지만 파이썬을 사용하는 입장에서, RevPDB는 2017년 이후 업데이트가 되지 않고 있고, 여전히 알파 단계이며 파이썬 2.7만을 지원한다고 한다. 이외에도 파이썬에는 마땅한 TDD가 없는 것 같다. 기존 pdb에서 jump를 이용하면 코드를 마음대로 이동할 수 있는 것 같기도 하다.

### 프로파일링

1. [이곳](https://missing-semester-kr.github.io/static/files/sorts.py)에 다양한 정렬 알고리즘이 구현되어 있습니다. [cProfile](https://docs.python.org/3/library/profile.html)과 [line_profiler](https://github.com/rkern/line_profiler)를 사용하여 삽입 정렬과 퀵 정렬의 수행 시간을 비교해보세요. 각 알고리즘에서 병목 현상이 일어나는 곳은 어디인가요? `memory_profiler`를 사용하여 메모리 사용량을 비교해보고 왜 삽입 정렬이 나은지를 알아보세요. 내장된 버전의 퀵 소트를 체크해보세요. 도전: `perf`를 사용하여 각 알고리즘의 주기 횟수 및 캐시 히트/미스를 확인해보세요.
```sh
> python -m cProfile -s cumulative sorts.py

Output:
Ordered by: cumulative time

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
    77576    0.013    0.000    0.075    0.000 random.py:334(randint)
    77576    0.031    0.000    0.061    0.000 random.py:290(randrange)
33820/1000    0.022    0.000    0.034    0.000 sorts.py:23(quicksort)
34284/1000    0.023    0.000    0.025    0.000 sorts.py:32(quicksort_inplace)
     1000    0.023    0.000    0.023    0.000 sorts.py:11(insertionsort)

## for CPU profiling ##
## insertionsort 위에 @profile을 붙여야 한다.
## insertionsort profiling
> kernprof -l -v sorts.py

Output:
Wrote profile results to sorts.py.lprof
Timer unit: 1e-06 s

Total time: 0.224724 s
File: sorts.py
Function: insertionsort at line 9

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
     9                                           @profile
    10                                           def insertionsort(array):
    11
    12     25770       6736.0      0.3      3.0      for i in range(len(array)):
    13     24770       6723.0      0.3      3.0          j = i-1
    14     24770       7402.0      0.3      3.3          v = array[i]
    15    225488      73164.0      0.3     32.6          while j >= 0 and v < array[j]:
    16    200718      65448.0      0.3     29.1              array[j+1] = array[j]
    17    200718      56791.0      0.3     25.3              j -= 1
    18     24770       8201.0      0.3      3.6          array[j+1] = v
    19      1000        259.0      0.3      0.1      return array
## 병목 현상은 while문에서 발생하는 것으로 보인다.

## quicksort profiling
Output:
Wrote profile results to sorts.py.lprof
Timer unit: 1e-06 s

Total time: 0.094387 s
File: sorts.py
Function: quicksort at line 20

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
    20                                           @profile
    21                                           def quicksort(array):
    22     33650      15128.0      0.4     16.0      if len(array) <= 1:
    23     17325       6589.0      0.4      7.0          return array
    24     16325       6872.0      0.4      7.3      pivot = array[0]
    25     16325      25918.0      1.6     27.5      left = [i for i in array[1:] if i < pivot]
    26     16325      25845.0      1.6     27.4      right = [i for i in array[1:] if i >= pivot]
    27     16325      14035.0      0.9     14.9      return quicksort(left) + [pivot] + quicksort(right)
## 병목 현상은 pivot을 기준으로 값을 나누는 과정에서 발생하는 것으로 보인다.
## insertionsort보다 quicksort가 더 빠르다.

## for memory profiling ##
## 위와 동일하게 insertionsort 함수 위에 @profile 설정
## insertionsort memory profiling
> python -m memory_profiler sorts.py

Output:
Filename: sorts.py

Line #    Mem usage    Increment  Occurrences   Line Contents
=============================================================
     9  166.953 MiB  166.859 MiB        1000   @profile
    10                                         def insertionsort(array):
    11
    12  166.953 MiB    0.000 MiB       25371       for i in range(len(array)):
    13  166.953 MiB    0.000 MiB       24371           j = i-1
    14  166.953 MiB    0.000 MiB       24371           v = array[i]
    15  166.953 MiB    0.000 MiB      221666           while j >= 0 and v < array[j]:
    16  166.953 MiB    0.047 MiB      197295               array[j+1] = array[j]
    17  166.953 MiB    0.000 MiB      197295               j -= 1
    18  166.953 MiB    0.047 MiB       24371           array[j+1] = v
    19  166.953 MiB    0.000 MiB        1000       return array

## quicksort memory profiling
Output:
Filename: sorts.py

Line #    Mem usage    Increment  Occurrences   Line Contents
=============================================================
    20  166.219 MiB  166.141 MiB       33590   @profile
    21                                         def quicksort(array):
    22  166.219 MiB    0.047 MiB       33590       if len(array) <= 1:
    23  166.219 MiB    0.016 MiB       17295           return array
    24  166.219 MiB    0.000 MiB       16295       pivot = array[0]
    25  166.219 MiB    0.000 MiB      155153       left = [i for i in array[1:] if i < pivot]
    26  166.219 MiB    0.000 MiB      155153       right = [i for i in array[1:] if i >= pivot]
    27  166.219 MiB    0.016 MiB       16295       return quicksort(left) + [pivot] + quicksort(right)

## insertionsort와 다르게 quicksort는 또 다른 array를 만들기에 효율이 떨어질 수 있다고 생각하였지만, 위 결과를 보면 오히려 insertionsort가 메모리를더 차지하는 것처럼 보인다. inplace하게 리스트 내부의 요소를 변경해도 메모리 증가가 발생하는 것 같다. 그렇다면 in-place quicksort는 어떨까?

## in-place quicksort memory profiling
Filename: sorts.py

Line #    Mem usage    Increment  Occurrences   Line Contents
=============================================================
    29  164.812 MiB  164.547 MiB       34828   @profile
    30                                         def quicksort_inplace(array, low=0, high=None):
    31  164.812 MiB    0.031 MiB       34828       if len(array) <= 1:
    32  164.812 MiB    0.000 MiB          33           return array
    33  164.812 MiB    0.047 MiB       34795       if high is None:
    34  164.812 MiB    0.047 MiB         967           high = len(array)-1
    35  164.812 MiB    0.000 MiB       34795       if low >= high:
    36  164.812 MiB    0.000 MiB       17881           return array
    37
    38  164.812 MiB    0.000 MiB       16914       pivot = array[high]
    39  164.812 MiB    0.000 MiB       16914       j = low-1
    40  164.812 MiB    0.031 MiB      129812       for i in range(low, high):
41  164.812 MiB    0.000 MiB      112898           if array[i] <= pivot:
42  164.812 MiB    0.016 MiB       58727               j += 1
43  164.812 MiB    0.047 MiB       58727               array[i], array[j] = array[j], array[i]
44  164.812 MiB    0.000 MiB       16914       array[high], array[j+1] = array[j+1], array[high]
45  164.812 MiB    0.000 MiB       16914       quicksort_inplace(array, low, j)
46  164.812 MiB    0.047 MiB       16914       quicksort_inplace(array, j+2, high)
47  164.812 MiB    0.000 MiB       16914       return array

## 여전히 quicksort보다 메모리를 많이 차지하는 것처럼 보인다. 내가 값을 잘못 해석하고 있는건가...?
```
<br />
- `perf`는 Linux 명령어이여서 테스트할 수 없었다.

1. 다음은 각 숫자에 대한 함수를 사용하여 피보나치 숫자를 계산하기 위한 파이썬 코드입니다.(다음 코드는 논란의 여지가 있습니다.) 코드를 파일에 넣고 실행 가능하게 만드세요. [pycallgraph](https://pycallgraph.slowchop.com/en/master/)를 설치하고 `pycallgraph graphviz -- ./fib.py`라는 명령어와 함께 위 코드를 실행한 뒤 `pycallgraph.png`를 체크해보세요. `fib0`은 몇 번이나 호출되었을까요? 메모이제이션(memoization)하면 위 함수를 개선할 수 있습니다. 주석처리된 부분의 주석을 제거하고 이미지를 다시 생성해보세요. 이번에는 `fibN` 함수가 몇 번이나 호출되었나요?
```sh
#!/usr/bin/env python
def fib0(): return 0

def fib1(): return 1

s = """def fib{}(): return fib{}() + fib{}()"""

if __name__ == '__main__':

    for n in range(2, 10):
        exec(s.format(n, n-1, n-2))
    # from functools import lru_cache
    # for n in range(10):
    #     exec("fib{} = lru_cache(1)(fib{})".format(n, n))
    print(eval("fib9()"))
```
<br />

![pycallgraph.png](https://typiespectre.github.io/images/prog/pycallgraph.png){: width="50%" height="50%"}
- `fib0`은 총 21번 호출되었다.

![pycallgraph2.png](https://typiespectre.github.io/images/prog/pycallgraph.png){: width="50%" height="50%"}
- 모든 함수는 총 한 번 실행되고, 중복되는 함수는 캐시처리 된다!
<br />

1. 수신하려는 포트가 이미 다른 프로세스에 사용되고 있는 것은 일반적인 문제입니다. 이를 위해 프로세스의 pid를 알아내는 방법을 배워봅시다. `4444`포트에서 수신 대기하는 최소한의 웹 서버를 만들기 위해서 `python -m http.server 4444`명령을 실행합니다. 다른 터미널에서 `lsof | grep LISTEN`을 사용하여 모든 수신 프로세스와 포트를 출력합니다. 해당 프로세스의 pid를 찾고 `kill <PID>` 명령을 실행하여 프로세스를 종료합니다.
```sh
## `lsof`는 list open files(열려있는 파일 나열)을 뜻하는 명령으로, 수많은 유닉스 계열 운영 체제에서 열려있는 모든 파일과, 그 파일들을 열고 있는 프로세스들의 목록을 출력한다. (wikipedia)

> lsof -i -n -P | grep LISTEN
## -i: IP소켓을 나열한다
## -n: 호스트 이름을 결정하지 않는다(DNS 없음)
## -P: 포트 이름을 결정하지 않는다(이름 대신 포트 번호 나열)

Output:
Python      19165   ckgun  4u   IPv6 0x2d2d2e910f44e82f      0t0  TCP *:4444 (LISTEN)

> kill 19165

Output:
[1]    19165 terminated  python -m http.server 444
```
<br />

1. 프로세스 리소스를 제한하는 것은 편리한 도구가 될 수 있습니다. `stress -c 3`을 실행해보고 `htop`으로 CPU사용량을 시각화해보세요. 그리고 `teskset --cpu-list 0,2 stress -c 3`을 실행한 뒤 이를 다시 시각화 해보세요. `stress`가 3개의 CPU에 걸쳐있나요? 그렇지 않다면 그 이유는 무엇일까요? [`man taskset`](https://www.man7.org/linux/man-pages/man1/taskset.1.html)을 읽어봅시다. 도전: [`cgroups`](https://www.man7.org/linux/man-pages/man7/cgroups.7.html)를 사용하여 동일한 문제를 풀어보세요. `stress -m`의 메모리 소비를 제한해보세요.
- `stress`와 `cgroups`는 Linux 한정 명령어이기에 생략하였다.

1. (심화) `curl ipinfo.io` 명령어는 HTTP 요청을 수행하고 공용 IP에 대한 정보를 가져옵니다. [Wireshark](https://www.wireshark.org/)를 열고 요청을 스니핑한 뒤어 `curl`이 주고받은 패킷에 응답하세요. (힌트: HTTP 패킷을 보려면 `http` 필터를 사용하세요.)
- Wireshark를 작동하고 `curl`로 요청을 수행하면 나는 HTTP 프로토콜로 정보를 요청하고, 응답자는 HTTP/JSON 프로토콜로 정보를 제공한다. HTTP/1.1 200 OK와 자바스크립트 객체를 제공한다.

