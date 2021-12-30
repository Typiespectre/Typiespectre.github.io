---
title: "MIT Missing Semester 연습문제 06 풀이"
lang: "ko"
layout: post
date: 2021-12-29 22:31:08 +0900
categories: [programming,]
tags: [programming,]
---

1. Git에 대한 경험이 없는 경우, [Pro Git](https://git-scm.com/book/ko/v2)의 처음 몇 장을 읽어보거나 [Learn Git Branching](https://learngitbranching.js.org/?locale=ko)과 같은 튜토리얼을 훑어보세요. 이러한 작업을 통해 Git 명령어와 데이터 모델을 관련시켜 보세요. (Learn Git Branching 완료)

1. [수업을 위한 웹사이트](https://github.com/missing-semester/missing-semester)를 클론해보세요.
- 버전 history를 그래프로 시각화하여 살펴보세요.
	```sh
		> git log --all --graph --decorate --oneline
	```
<br />
- 마지막으로 `README.md`를 수정한 사람은 누구입니까? (힌트: `git log`인수를 사용해보세요)
	```sh
		> git log --all --graph --decorate README.md

		output:
		* commit 8010724516adc968765e8efd14991f262f0d0423
		| Author: Anish Athalye <me@anishathalye.com>
		| Date:   Tue Jul 27 08:29:08 2021 -0400
		|
		|     Separate build and links status
		|
	```
<br />
- `_config.yml`의 `collections:` 라인의 마지막 수정과 관련된 커밋 메세지는 무엇입니까? (힌트: `git blame`과 
	```sh
		> git blame _config.yml
		
		output:
		fa309f93 (Anish Athalye 2020-05-16 11:13:27 -0400 29)
		
		> git show fa309f93

		output:
		commit fa309f93162654a5a4993df8b151a1efc26ceb5c
		Author: Anish Athalye <me@anishathalye.com>
		Date:   Sat May 16 11:13:27 2020 -0400

			Fix Travis CI build

		diff --git a/_config.yml b/_config.yml
		index fce0b9f..526d6d1 100644
		--- a/_config.yml
		+++ b/_config.yml
		@@ -26,3 +26,4 @@ exclude:
		   - README.md
		   - Gemfile
		   - Gemfile.lock
		+  - vendor	
	```
<br />
1. Git을 배울 때 흔히 저지르는 실수 중 하나는 대용량 파일을 커밋하거나 Git에서 관리해서는 안되는 민감한 정보를 추가하는 것입니다. 저장소에 파일을 추가하는 커밋을 몇번 해보고 history에서 해당 파일을 삭제하세요. (당신은 아마 [이걸](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository) 보고 싶을 거에요)
	```sh
		# making some commits
		> touch test.txt

		# delete commit
		> git reset --soft HEAD~1
		
		# unstage file
		> git restore --staged test.txt
		
		https://stackoverflow.com/questions/1338728/delete-commits-from-a-branch-in-git
	```
<br />
1. GitHub에서 일부 저장소를 clone하고 기존 파일 중 하나를 수정합니다. `git stash`할 때 어떤 일이 생기나요? `git log --all --oneline`을 실행할 때 무엇을 볼 수 있나요? `git stash pop`을 실행하여 `git stash`한 작업을 취소하세요. 어떤 시나리오가 유용할까요?
	```sh
		> echo "<p>This is 404 page</p> >> 404.html
		
		> git stash
		
		output:
		Saved working directory and index state WIP on master: 0601c2c Merge branch 'weeklies/use--no-echo'
		# 맨 처음에 수행했던 echo 명령어가 취소되었다.

		> git log --all --oneline
		
		output:
		3e635b7 (refs/stash) WIP on master: 0601c2c Merge branch 'weeklies/use--no-echo'
		ebdc659 index on master: 0601c2c Merge branch 'weeklies/use--no-echo'

		> git stash pop

		output:
		Dropped refs/stash@{0} (3e635b7614f975130c646371ec4166b2bc20be61)
		# 맨 처음에 수행했던 echo가 다시 복원되었다.
		# 다양한 파일을 수정해도, stash를 수행하면 staged 되지 않은 작업한 내용이 모두 사라지는 것 같다.
	```
<br />
1. 많은 명령어 도구들과 마찬가지로 Git은 `~/.gitconfig`라고 불리는 환경설정 파일 (또는 dotfile)을 제공합니다. `git graph`를 실행할 때 `git log --all --graph --decorate --oneline`의 출력을 얻을 수 있도록 `~/.gitconfig`에 별칭을 생성해 보세요.
	```sh
		> git config --global alias.graph 'log --all --graph --decorate --oneline'
	```
<br />
1. `git config --global core.excludesfile ~/.gitignore_global`을 실행하면 `~/.gitignore_global`에서 전역 무시 패턴을 정의할 수 있습니다. 이후 `.DS_Store`와 같이 OS와 관련되거나 에디터와 관련된 임시 파일을 무시하도록 전역 gitigore 파일을 설정해 보세요.
	```sh
		> touch ~/.gitignore_global
		
		> git config --global core.excludesfile ~/.gitignore_global

		# cat ~/.gitconfig
		: [core]
			excludesfile = ~/.gitignore_global
		
		# writing gitignore files
		> vim temp.txt

		# copy to ~/.gitignore_global
		> cat temp.txt >> ~/.gitignore_global
	```
1. [수업을 위한 웹사이트의 저장소](https://github.com/missing-semester/missing-semester)를 clone하고, 오타 또는 기타 개선 사항을 찾아 Github에 pull request를 해보세요. (생략)

