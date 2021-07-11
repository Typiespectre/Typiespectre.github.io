---
title: "MIT Missing Semester 연습문제 01 풀이"
lang: "ko"
layout: post
date: 2021-07-12 00:37:01 +0900
categories: [programming]
tags: [programming]
---
1. Create a new directory called **missing** under **/tmp**.
```zsh
cd tmp && mkdir missing
```
<br />
2. Look up the **touch** program. The **man** program is your friend.

    **touch** -- change file access and modification times    

    **touch** 명령어는 파일을 생성하거나 갱신하는 명령어이다. 존재하지 않는 파일명을 지정하면 지정된 파일명으로 새로운 파일이 생성되며, 이미 존재하는 파일명을 지정하면 해당 파일의 수정시간이 업데이트 된다.    

    **Usage**:
    ```zsh
    touch [OPTION]... FILE...
    ```
<br />
3. Use **touch** to create a new file called **semester** in **missing**.
```zsh
~/tmp/missing
touch semester
```
<br />
4. Write the following into that file, one line at a time:    
    #!/bin/sh
    curl --head --silent https://missing.csail.mit.edu
```zsh
echo "#\!"/bin/sh > semester
echo "curl --head --silent https://missing.csail.mit.edu" >> semester
```
<br />
5. Try to execute the file, i.e. type the path to the script (**./semester**) into your shell and press enter. Understand why it doesn’t work by consulting the output of **ls** (hint: look at the permission bits of the file)
```zsh
./semester
zsh: permission denied: ./semester
...
ls -l
-rw-r--r--  1 Typiespectre  staff  61  7 12 01:17 semester
```
즉, **semester** 파일에서 소유자(user)에게 주어진 권한은 읽기와 쓰기이고, 그룹과 타인에게 주어진 권한은 읽기이다. 그렇기에 해당 쉘에서 바로 **semester** 파일을 실행(execute)할 수 없다.
<br />
<br />
6. Run the command by explicitly starting the **sh** interpreter, and giving it the file **semester** as the first argument, i.e. **sh semester**. Why does this work, while **./semester** didn’t?    

bash 명령어의 특성 상 읽기 권한만 있어도 스크립트가 실행될 수 있다고 한다. 왜냐하면 bash 명령어가 파일 내 존재하는 리눅스 명령어를 읽어와 실행하기 때문이다.(#!/bin/sh) 만약 파이썬이라면 동일하게 python을 붙이면 파일이 실행된다. 그러나 그룹과 타인에게 읽기 권한을 제외한다면, 권한이 거부된다(permission denied).
<br />
<br />
7. Look up the **chmod** program (e.g. use **man chmod**).
    **chmod** -- change file modes or Access Control Lists

    **chmod** 명령어는 파일의 모드(mode)를 변경하는 명령어이다. 모드란 세 가지 권한(읽기, 쓰기, 실행)과 각각의 권한을 지정할 대상(소유자, 그룹, 타인)을 포함한 파일의 속성을 말한다. 즉 **chmod** 명령을 사용하여 파일의 모드를 변경한다는 것은, 파일의 권한을 변경한다는 것과 동일한 의미를 가진다.

    **Usage**:
    ```zsh
    chmod [OPTION] [MODE] [FILE]
    ```
<br />
8. Use **chmod** to make it possible to run the command **./semester** rather than having to type **sh semester**. How does your shell know that the file is supposed to be interpreted using **sh**? See this page on the [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) line for more information.
```zsh
chmod u+x semester
```
소유자(user)에게 실행 권한을 주는 스크립트. 이후 **./semester**도 **sh semester** 과 동일하게 파일 실행이 가능하게 된다. 앞서 말했듯, **sh** 명령어가 파일 내 존재하는 쉘 명령어를 읽어와 실행한다고 하였는데, 이를 가능하게 하는 쉘 명령어를 **Shebang**(sharp:# + bang:!)이라고 한다. **Shebang**은 유닉스 계열 운영체제에서 스크립트가 실행될 때, 파이썬, Bash 쉘 등 어떠한 인터프리터에 의해서 동작이 되는지 알려주는 역할을 한다. 해당 파일의 **Shebang**은 **'#!/bin/sh'** 으로, **/bin/sh** 경로의 기본 쉘 또는 호환 쉘을 이용하여 파일을 실행할 것을 요청한다.
<br />
<br />
9. Use **|** and **>** to write the “last modified” date output by **semester** into a file called **last-modified.txt** in your home directory.
```zsh
./semester | grep last-modified > last-modified.txt
...
cat last-modified.txt
last-modified: Sat, 26 Jun 2021 10:14:39 GMT
```