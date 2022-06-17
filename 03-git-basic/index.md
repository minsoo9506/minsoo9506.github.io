# [Git] 기본 내용 정리


Git 기본적인 명령어 정리

<!--more-->

## 기본 개념 & 명령어

- 원격 저장소(Remote Repository): 원격 저장소 전용 서버
- 로컬 저장소(Local Repository): 내 PC에 파일이 저장되는 개인 전용 저장소
- 작업공간과 저장소 사이에 *인덱스*라는 가상의 공간이 있으며 commit하기 위해서는 그 공간에 파일을 _stage_ 해야 한다. (`git add`)
- `HEAD`
  - 브랜치가 여러 개 있을 때, `HEAD`로 브랜치나 커밋을 가리킨다.
  - 브랜치는 커밋을 가리키므로 `HEAD`도 커밋을 가리킨다.
  - 결국 `HEAD`는 현재 작업 중인 브랜치의 최근 커밋을 가리킨다.
- `git reset [파일명]` : unstage 시키는 명령어
- `git log -n<숫자>` : 최신 n개의 커밋만 살펴본다.
  - `git log --oneline --graph --decorate --all` : CLI환경에서 이력을 확인하기에 좋다.
- `git remote add <원격저장소 이름> <원격저장소 주소>`: 원격저장소를 등록
- `commit`, `push`, `pull`

## 브랜치

- 여러 명이서 동시에 작업을 할 때에 다른 사람의 작업에 영향을 주거나 받지 않도록, 먼저 메인 브랜치에서 자신의 작업 전용 브랜치를 만든다. 그리고 원하는 기능을 추가삭제한다. 나중에 병합(merge)하여서 하나로 합치기도 한다.
- `git branch`: 현재 브랜치 확인
- `git branch <브랜치 이름>`: 브랜치 생성
- `git checkout <브랜치 이름>`: 해당하는 브랜치로 체크아웃
  - 근데 Git 2.23 부터 `checkout`을 대신해 `switch`, `restore`가 도입되었다. `checkout`의 기능들을 나누어서 가져갔다고 이해하면 된다.
  - `switch`: switch branch
  - `restore`: restore working tree files
    - `git restore <파일>`: 파일의 수정 내용 복원
    - `git restore --staged <파일>`: stage에서 다시 빼내기
- `git merge <브랜치 이름>`: 브랜치 병합
  - 예시) bugfix라는 브랜치를 만들어서 업무를 진행한 뒤에 master 브랜치에 병합하고 싶다. 그러면 먼저 master 브랜치로 switch를 하고 병합명령어를 진행하면 된다. 그러면 master 브랜치가 가리키는 커밋이 bugfix 브랜치와 같아진다. 이를 *fast-forward (빨리감기) 병합*이라고 한다.
  - 그런데 병합을 하려는데 master 브랜치에 그 사이에 변경사항이 존재한다면? 당연히 fast-forward가 불가능하다. 이런 경우 먼저 양쪽의 변경을 반영한 병합커밋(merge commit)을 만들고 병합해야한다. 이 경우에는 브랜치가 남겨지므로 작업 확인 및 관리에서 유용한 경우가 생길 수 있다. 물론 복잡해진다. (non fast-forward 병합)

```bash
# bugfix 브랜치에서 기능수정 진행 #
git switch master
git merge bugfix # master에서 변경사항이 없으면 fast-forward 진행
```

- `git rebase <브랜치 이름>`: 브랜치 rebase
  - `merge`와는 조금 다른 `rebase`라는 방법도 있다.
  - non fast-forward의 상황과 동일하지만 브랜치의 이력을 남기지 않고 master 브랜치 뒤에 bugfix를 놓고 (rebase) fast-forward 병합을 진행한다.
  - rebase의 경우 충돌 부분을 수정 한 후에는 `commit`이 아니라 `--continue` 옵션으로 실행한다.

```bash
# bugfix 브랜치에서 기능수정 진행 #
git rebase master # bugfix 브랜치에서 진행
# 충돌 수정 #
git add .
git rebase --continue
```

- `git reset --hard <이동할 커밋 체크섬>`: 현재 브랜치를 지정한 커밋으로 옮긴다. 작업폴더의 내용도 함께 변경된다.
  - 근데 '커밋 체크섬'을 사용하는게 쉽지는 않다. 이런 경우 `HEAD~`와 같이 이용한다. 여기서 `HEAD~`는 바로 헤드의 부모 커밋, `HEAD~2`는 할아버지 커밋을 의미한다.
- `git branch -d <브랜치 이름>`: 브랜치 삭제

## 원격저장소

- `pull`: 원격 저장소에서 로컬로 pull하는 경우 충돌이 없으면 자동으로 병합커밋이 만들어 진다. 충돌이 있으면 수동으로 수정하고 커밋을 해야한다.
- `fetch`: 단순히 원격 저장소의 내용을 확인만 하고 로컬 데이터와 병합은 하고 싶지 않은 경우 사용한다.
  - `pull`은 내부적으로 `fetch` + `merge`

## 태그 (tag)

- 커밋을 참조하기 쉽도록 이름을 붙이는 것
- 일반태그, 주석태그
- `git tag <태그이름>`
  - 현재 HEAD가 가리키는 커밋에 태그가 붙는다.
- `git tag -am <태그설명> <태그이름>`: 해당 tag에 대한 설명 작성
- `git tag -d <태그이름>`: tag 지우기

## 커밋 변경

- `git commit --amend -m <커밋메세지>`: 이전에 작성한 커밋 수정하기

```bash
# 일단 commit을 한 상태
# 그 이후에 추가적으로 수정을 진행
git add .
git commit --amend -m "bugfix commit"
```

- `git revert HEAD`: HEAD가 가르키는 해당 커밋을 지운다.
  - 지운 이력이 남는다.
- `git reset --hard HEAD~`: 필요없어진 커밋 지우기
  - 더 이상 필요 없어진 커밋들을 버릴 수 있다.
  - 명령어 실행 시 어떤 모드로 실행할 지 지정하여 'HEAD' 위치와 인덱스, 작업 트리 내용을 함께 되돌릴지 여부를 선택할 수 있다.
  - 종류는 soft, mixed, hard 가 있다.
- `git cherry-pick <특정 커밋>`: 특정 커밋을 가져오고 싶을 때 사용

## Reference

- 팀 개발을 위한 Git GitHub 시작하기 (한빛미디어)
- [누구나 쉽게 이해할 수 있는 Git 입문](https://backlog.com/git-tutorial/kr/)

