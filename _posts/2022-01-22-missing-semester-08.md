---
title: "MIT Missing Semester 연습문제 08 풀이"
lang: "ko"
layout: post
date: 2022-01-22 20:28:02 +0900
categories: [programming,]
tags: [programming,]
---
1. 대부분의 메이크파일(Makefile)은 `clean`이라고 하는 타겟을 제공합니다. 이건 `clean`이라는 파일을 생성하기 위한 것이 아니라 make에 의해서 다시 빌드될 수 있는 모든 파일들을 싹 지우기 위한 것입니다. 모든 빌드 스텝들을 "되돌리리(undo)"할 수 있는 방법이라고 생각하시면 되요. 위에 있는 `paper.pdf` 메이크파일을 위한 `clean` 타겟을 구현해보세요. [포니](https://pinocc.tistory.com/131) 타겟(phony target)을 구현해야할 겁니다. 아마 [git ls-files 명령어](https://m.blog.naver.com/PostView.nhn?blogId=cyberpass&logNo=221037298316&proxyReferer=https:%2F%2Fwww.google.com%2F)가 유용할 겁니다. 아주 흔한 `make` 타겟들은 [여기](https://www.gnu.org/software/make/manual/html_node/Standard-Targets.html#Standard-Targets)에 나열되어 있습니다.
```sh
.PHONY: clean

clean:
	git ls-files -mo | xargs rm -f

# git ls-files -mo는 수정되거나 새로운 파일들을 나열한다. xargs로 파일을 지운다.
```
<br />

1. [Rust's build system](https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html)에서 의존성들에 대한 버전 요구사항들을 명시하는 다양한 방법들을 살펴보세요. 대부분의 패키지 레포지토리가 비슷한 문법을 지원합니다. 각각의 방법에 대해서 그 특정한 종류의 요구사항이 말이 되는 활용 사례를 제시해보세요.
- [해당 블로그](https://velog.io/@slaslaya/Semantic-Versioning-2.0.0-MAJOR-MINOR-PATCH%EC%99%80-%EB%AA%85%EC%84%B8%EC%97%90-%EA%B4%80%ED%95%98%EC%97%AC)에서 Node의 시맨틱 버저닝을 잘 설명해놓았다.
- Caret requirements: 왼쪽에서부터 맨 처음 0이 아닌 요소를 기준으로 어떠한 업데이트가 허용되는지 설명한다.
    ```
    ^1.2.3  :=  >=1.2.3, <2.0.0     # 왼쪽에서 맨 처음 0이 아닌 요소는 major이기 때문에 minor, patch 업데이트 허용
    ^1.2    :=  >=1.2.0, <2.0.0
    ^1      :=  >=1.0.0, <2.0.0
    ^0.2.3  :=  >=0.2.3, <0.3.0     # 왼쪽에서 맨 처음 0이 아닌 요소가 minor이기 때문에, patch 업데이트 허용
    ^0.2    :=  >=0.2.0, <0.3.0
    ^0.0.3  :=  >=0.0.3, <0.0.4     # 왼쪽에서 맨 처음 0이 아닌 요소가 patch이기 때문에, 업데이트를 허용하지 않음
    ^0.0    :=  >=0.0.0, <0.1.0     # major와 minor 둘 다 0이기 때문에 해당값 내에서 유연성 허용
    ^0      :=  >=0.0.0, <1.0.0     # major이 0이기 때문에 해당값 내에서 유연성 허용. 하지만 0.1과 0.2가 호환성이 손상되는 변경(breaking-change)일 수도 있다. 왜냐하면 0은 초기 개발을 위해 사용하므로, 아무 때나 마음대로 바꿀 수 있기 때문이다.
    ```
- Tilde requirements: 

