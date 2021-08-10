---
title: "MIT Missing Semester 연습문제 01 풀이"
lang: "ko"
layout: post
date: 2021-07-12 00:37:01 +0900
categories: [programming,]
tags: [programming,]
---
1. `/tmp`에  `missing`이라는 새로운 경로를 만들어 보세요.

```zsh
cd tmp && mkdir missing
```
<br />

1. `touch`라는 프로그램을 관찰해보세요. `man` 프로그램이 도움이 될겁니다.


    **touch** -- change file access and modification times    

    **touch** 명령어는 파일을 생성하거나 갱신하는 명령어이다. 존재하지 않는 파일명을 지정하면 지정된 파일명으로 새로운 파일이 생성되며, 이미 존재하는 파일명을 지정하면 해당 파일의 수정시간이 업데이트 된다.    

    **Usage**:
    ```zsh
    touch [OPTION]... FILE...
    ```
<br />

1. `touch`를 이용해서 `semester`라는 파일을 `missing` 안에 만들어 보세요.

```zsh
~/tmp/missing
touch semester
```
<br />

1. 아래 주어진 것을 그 파일에 써보세요. 단, 한번에 한줄씩:    
    ```
    #!/bin/sh
    curl --head --silent https://missing.csail.mit.edu
    ```

```zsh
echo "#\!"/bin/sh > semester
echo "curl --head --silent https://missing.csail.mit.edu" >> semester
```
<br />

1. 파일을 실행해보세요. 예를 들어, (`./semester`)라는 경로를 셸에 입력해보세요. 이것이 왜 작동하지 않는지 `ls`를 이용해 파악해보세요. (힌트: 파일의 비트 권한을 확인해보세요.)

```zsh
./semester
zsh: permission denied: ./semester
...
ls -l
-rw-r--r--  1 Typiespectre  staff  61  7 12 01:17 semester
```
<br />
    즉, **semester** 파일에서 소유자(user)에게 주어진 권한은 읽기와 쓰기이고, 그룹과 타인에게 주어진 권한은 읽기이다. 그렇기에 해당 쉘에서 바로 **semester** 파일을 실행(execute)할 수 없다.
<br />

1. `sh` 인터프리터로 시작해 명령을 실행하고, `semester` 파일에 첫 인자로 주세요. (즉 `sh semester` 이렇게!). `./semester`는 안되는데, 앞에 거는 왜 실행이 될까요?

    bash 명령어의 특성 상 읽기 권한만 있어도 스크립트가 실행될 수 있다고 한다. 왜냐하면 bash 명령어가 파일 내 존재하는 리눅스 명령어를 읽어와 실행하기 때문이다.(#!/bin/sh) 만약 파이썬이라면 동일하게 python을 붙이면 파일이 실행된다. 그러나 그룹과 타인에게 읽기 권한을 제외한다면, 권한이 거부된다(permission denied).
<br />

1. `chmod` 프로그램을 살펴보세요. (`man chmod`를 사용해보세요).

    **chmod** -- change file modes or Access Control Lists

    **chmod** 명령어는 파일의 모드(mode)를 변경하는 명령어이다. 모드란 세 가지 권한(읽기, 쓰기, 실행)과 각각의 권한을 지정할 대상(소유자, 그룹, 타인)을 포함한 파일의 속성을 말한다. 즉 **chmod** 명령을 사용하여 파일의 모드를 변경한다는 것은, 파일의 권한을 변경한다는 것과 동일한 의미를 가진다.

    **Usage**:
    ```zsh
    chmod [OPTION] [MODE] [FILE]
    ```
<br />

1. `chmod`를 활용해 `sh semester` 대신에 `./semester`을 사용 가능하게 해보세요. `sh`을 이용해 이 파일을 해석해야 한다는 것을 셸이 어떻게 알까요? 이 페이지에서 [셔뱅(shebang)](https://en.wikipedia.org/wiki/Shebang_(Unix))에 대해서 더 알아보세요. 

```zsh
chmod u+x semester
```
<br />
    소유자(user)에게 실행 권한을 주는 스크립트. 이후 **./semester**도 **sh semester** 과 동일하게 파일 실행이 가능하게 된다. 앞서 말했듯, **sh** 명령어가 파일 내 존재하는 쉘 명령어를 읽어와 실행한다고 하였는데, 이를 가능하게 하는 쉘 명령어를 **Shebang**(sharp:# + bang:!)이라고 한다. **Shebang**은 유닉스 계열 운영체제에서 스크립트가 실행될 때, 파이썬, Bash 쉘 등 어떠한 인터프리터에 의해서 동작이 되는지 알려주는 역할을 한다. 해당 파일의 **Shebang**은 **'#!/bin/sh'** 으로, **/bin/sh** 경로의 기본 쉘 또는 호환 쉘을 이용하여 파일을 실행할 것을 요청한다.
<br />

1. `|` 와 `>`를 사용해 `semester`별 "last modified" 날짜 출력을 홈 디렉토리에 `last-modified.txt`라는 파일에 작성하세요.

```zsh
./semester | grep last-modified > last-modified.txt
...
cat last-modified.txt
last-modified: Sat, 26 Jun 2021 10:14:39 GMT
```
<br />