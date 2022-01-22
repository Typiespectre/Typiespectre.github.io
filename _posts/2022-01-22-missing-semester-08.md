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
- Tilde requirements: minor version이 지정되어 있다면 patch level 변경을 허용하지만, 그렇지 않은 경우 minor-level 변경을 허용한다.
    ```
    ~1.2.3  := >=1.2.3, <1.3.0      # minor version이 지정되어 있으니 patch level 변경 허용
    ~1.2    := >=1.2.0, <1.3.0      # minor version이 지정되어 있으니 patch level 변경 허용
    ~1      := >=1.0.0, <2.0.0      # minor version이 지정되어 있지 않으니 minor-level 변경을 허용
    ```
- Wildcard requirements: 와일드카드(`*`)가 지정된 요소의 변경을 허용한다.
    ```
    *     := >=0.0.0                # 하지만 권장되지 않는 방식
    1.*   := >=1.0.0, <2.0.0
    1.2.* := >=1.2.0, <1.3.0
    ```
- Comparison requirements: 버전의 허용범위를 결정한다. (< : 명시된 버전보다 낮은버전, <= : 명시된 버전과 같거나 낮은버전, > : 명시된 버전보다 높은버전, >= : 명시된 버전보다 같거나 높은버전)
    ```
    >=1.2.7 <1.3.0                   # patch 업데이트만 허용한다
    1.2.7 || >=1.2.9 <2.0.0          # 1.2.7이거나 1.2.9 포함 minor와 patch 업데이트 허용
    >= 2.7                           # 2.7 이상의 높은 버전 허용
    ```
- Multiple requirements: 콤마(`,`)로 나누어 여러개의 버전을 표시한다. (`>= 1.2, < 1.5`)
<br />

1. 깃(Git)은 그 자체로 단순한 CI 시스템으로서 기능할 수 있습니다. 어떤 깃 레포지토리(git repository)에 있든 `git/hooks` 안에서, 특정 행위가 일어날 때 스크립트(script)로서 실행되는 (현재 비활성인) 파일들을 발견할 수 있을 것입니다. `make paper.pdf`를 실행시키고 `make` 명령어가 실패하면 커밋을 거부하는 [pre-commit hook](https://git-scm.com/book/ko/v2/Git%EB%A7%9E%EC%B6%A4-Git-Hooks)을 작성해보세요. 이러한 빌드가 안되는 버전의 paper를 어떤 커밋도 가지고 있지 않도록 방지해줄 것입니다.
    ```sh
    git hooks를 작동시키기 위해선, .git/hooks에 들어있는 pre-commit.sample을 복사하여 sample 확장자를 지워주면 된다. chmod +x로 pre-commit을 작동 가능하게 만들어야 하는 것도 잊지 말아야 한다.

    pre-commit:
    # !/bin/sh
    
    if ! make paper.pdf ; then
        echo "Cannot make paper.pdf"
        exit 1
    fi
    ```
    ```
    Output:
    $ git commit -m 'failure test'
    make: *** No rule to make target `paper.pdf'.  Stop.
    Cannot make paper.pdf
    ```
<br />

1. [Github Pages](https://docs.github.com/en/actions)를 사용해서 자동으로 개제되는 단순한 페이지를 하나 만들어보세요. 레포지토리에 [Github Action](https://github.com/features/actions)을 추가해서 레포지토리에 있는 모든 셸(shell) 파일들에 대해 `shellcheck`을 실행해보세요. (여기에 [그 방법 중 하나](https://github.com/marketplace/actions/shellcheck)가 있습니다.) 잘 작동하는지 한 번 확인해보세요!
- 원격저장소 missing-semester-kr에서 시행했다. 레포지토리 상단의 'Actions' 탭을 클릭하여, 새로운 워크플로우를 만들어 `main.yml` 파일을 형성한다. `main.yml` 파일 안에 `shellcheck` 깃허브 액션 코드를 넣는다.
    ```
    name: ShellCheck

    on:
      push:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest

        steps:
        - uses: actions/checkout@v2

        - name: ShellCheck
          uses: ludeeus/action-shellcheck@0.1.0
    ```
- 잘못된 sh파일을 원격저장소에 push하면, 레포지토리의 커밋 메세지 왼쪽에 초록색 체크 마크가 표시될 경우, `shellcheck` 깃허브 액션이 잘 적용된 것이다. 체크 마크를 클릭하여 완편의 `build` 섹션을 보면, 단계별 섹션 안에 `shellcheck` 섹션이 들어있는데, 이 안에 `shellcheck`가 잘못된 sh파일에 대해 프린트하는 구문이 들어있다.
<br />

1. [자신만의 깃허브 액션(Github Action)을 빌드](https://docs.github.com/en/actions/creating-actions)해서 레포지토리에 있는 모든 .md 파일들에 대해 [proselint](http://proselint.com/) 또는 [write-good](https://github.com/btford/write-good)을 실행해보세요. 그걸 여러분의 레포지토리에 활성화시키고, 안에 오타가 포함된 풀 리퀘스트(pull request; PR)를 보내서 잘 작동하는지 확인해보세요.
