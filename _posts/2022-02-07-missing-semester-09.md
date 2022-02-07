---
title: "MIT Missing Semester 연습문제 09 풀이"
lang: "ko"
layout: post
date: 2022-02-07 22:17:11 +0900
categories: [programming,]
tags: [programming,]
---
## 1. Entropy
1. 패스워드는 사전 내의 5개의 소문자 단어를 합한 것으로 가정하고, 각 단어는 100,000크기의 사전에서 무작위로 선택합니다. 예를 들면 `correcthorsebatterystaple`입니다. 이 때, 이 패스워드의 엔트로피는 몇 비트인가요?
    - 엔트로피는 `log_2(# of possibilities)`와 동일하며, _bits_ 단위로 측정된다. 위 경우, `(# of possibilities)`는 `100,000^5`가 되고, 계산 결과, 대략 _83bits_가 나온다.
2. 암호가 8개의 임의 영문자(소문자, 대문자 모두 포함)와 숫자를 선택하는 다른 암호를 고려합니다. 예를 들면 `rg8Ql34g`입니다. 엔트로피는 몇 비트인가요?
    - `(# of possibilities)`는 `(26+26+10)^8`이다. 계산 결과, 대략 _48bits_가 나온다.
3. 어느 쪽이 더 강력한 패스워드인가요?
    - 첫번째 경우가 훨씬 강력하다.
4. 공격자가 초당 10,000개의 암호를 추측할 수 있다고 가정합니다. 각 암호들을 해독하는데 평균적으로 얼마나 걸릴까요?
    - 년단위로 계산할 경우, 공격자가 1년 동안 추측할 수 있는 암호의 개수는 `365*24*60*60*10000 = 315360000000`이다. 이를 각 패스워드에 대입하면, 첫 번째의 경우, `(2**83)//315360000000 =  30667829011025`년이 걸리고, 두 번째의 경우, `(2**48)////315360000000 = 892`년이 걸린다.
<br />

## 2. Hash 함수
1. [mirror](https://www.debian.org/CD/http-ftp/)에서 Debian 이미지를 다운로드합니다. [Argentinean mirror](http://debian.xfree.com.ar/debian-cd/current/amd64/iso-cd/)에서 다운로드 한 경우, `debian.org`로 호스팅되는 공식 Debian 사이트에서 [해쉬 값](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/SHA256SUMS)을 가져와 교차 확인(예: `sha256sum` 명령어)을 합니다.
    ```sh
    """ 미러 사이트에서 이미지를 다운로드하고, 공식 사이트에서 해쉬 값을 다운로드한다.
    curl -O -L -C - http://debian.xfree.com.ar/debian-cd/current/amd64/iso-cd/debian-mac-11.2.0-amd64-netinst.iso
    curl -O -L https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/SHA256SUMS
    ```
<br />

## 3. 대칭키 암호
1. [OpenSSL](https://www.openssl.org/): `openssl aes-256-cbc -salt -in {input filename} -out {output filename}`을 사용하여 AES암호화로 파일 암호화를 합니다. `cat` 또는 `hexdump`를 사용해 내용을 보십시오. `openssl aes-256-cbc -d -in {input filename) -out {output filename}`을 사용하여 파일을 해독하고 `cmp`를 사용하여 해당 파일이 원본 파일과 동일한지 비교하세요.
    ```sh
    """ test파일 암호화
    openssl aes-256-cbc --salt -in test -out test.enc
    
    """ 패스워드를 입력해야 한다.

    """Output:
    cat test.enc
    > salted__{정체를 알 수 없는 문자들}

    """ test.enc 해독
    openssl aes-256-cbc -d -in test.enc -out test.dec

    """ 위에서 설정한 패스워드를 입력하면 해독된다.

    cmp test test.dec
    """ 두 내용이 동일하기에 에러메세지가 뜨지 않음.
    ```
<br />

## 4. 비대칭키 암호
1. [SSH Keys](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-2)를 접근해야 하는 컴퓨터에 설정합니다(Kerberos가 SSH keys와 이상하게 동작하기 때문에, Athena가 아닌 것으로 합니다). 연결된 튜토리얼처럼 RSA키를 사용하는 것이 아닌 더 안전한 [ED25519키](https://wiki.archlinux.org/title/SSH_keys#Ed25519)를 사용합니다. 개인 키가 비밀번호로 암호화 되었는지 확인하여 저장시 보호되도록 합니다.
