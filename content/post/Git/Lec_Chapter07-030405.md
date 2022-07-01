---
title: "[TIL] Git study: Lecture Chapter 07 -  git stash & git commit --amend & git rebase -i"
date: 2022-06-30T20:59:37+09:00
draft: false
summary: 커밋하기 애매한 변화를 치워두는 명령어인 git stash, commit message를 수정하는 git commit --amend, commit 과거 내역들을 수정하는 git rebase -i 를 학습한다.
tags: ["TIL", "git"]
categories: ["개발-dev git"]
---

# 0. Introduction

- 해당 내용은 [제대로 파는 Git & GitHub - by 얄코 ](https://www.inflearn.com/course/%EC%A0%9C%EB%8C%80%EB%A1%9C-%ED%8C%8C%EB%8A%94-%EA%B9%83/dashboard)를 중심으로 [Pro git : Second editions](https://book.naver.com/bookdb/book_detail.nhn?bid=7187291)을 참고하여 공부한 내용입니다.

- 이번 단원에서는 `git stash`, `git commit --amend` 그리고, `git rebase -i` 명령어를 학습해본다.

<br>

---

# 7-3. git stash

> **_커밋하기 애매한 변화를 치워두는 명령어_**

- Stash 사용법 정리

| 명령어                     | 설명                                          | 비고                           |
| -------------------------- | --------------------------------------------- | ------------------------------ |
| git stash                  | 현 작업들 치워두기                            | 끝에 save 생략                 |
| git stash apply            | 치워둔 마지막 항목(번호 없을 시) 적용         | 끝에 번호로 항목 지정 가능     |
| git stash drop             | 치워둔 마지막 항목(번호 없을 시) 삭제         | 끝에 번호로 항목 지정 가능     |
| git stash pop              | 치워둔 마지막 항목(번호 없을 시) 적용 및 삭제 | apply + drop                   |
| git stash branch(브랜치명) | 새 branch를 생성하여 pop                      | 충돌사항이 있는 상황 등에 유용 |
| git stash clear            | 치워둔 모든 항목들 비우기                     |                                |

- 변경사항을 먼저 만들어보자.

  - Tigers의 members에 `Stash`를 추가한다.
  - `tomcats.yaml`을 추가한 후, `git add`를 실행
  - 여기서 `git add`를 한 이유는 `git stash`라는 명령어를 실습하기 위해서는 먼저 `tracked` 상태여야 한다.

- `git stash` 실행: `git add` 했던 변화들이 사라진다.

  - 사라진 변화들은 sourcetree에서 `스태시` 란에서 확인할 수 있다.

- `git stash pop` 을 입력하여 원하는 시점, 브랜치에 다시 적용한다.

  - 다른 branch를 만들고 전환 후, 이 명령어로 적용해보자.

- `git add -p`처럼 원하는 것만 stash 할 수 있다.

  - Leopards의 members에 `Stash2`를 추가한다.
  - Jaguars의 members에 `Stash3`를 추가한다.
  - `git stash -p`로 `Stash2`만 선택하여 스태시한다.

- 메시지와 함께 스태시를 할 수도 있다.

  - `git stash -m 'Add Stash3'`

    ```yml
    $ git stash -m 'Add Stash3'
    Saved working directory and index state On remote-branch: Add Stash3
    ```

- 스태시 목록 보기  
   -`git stash list`

  ```yml
  $ git stash list
   stash@{0}: On remote-branch: Add Stash3
   stash@{1}: WIP on remote-branch: 1b2bbcb Edit Leopards and Tigers
  ```

- 스태시된 항목 삭제: `git stash drop`

  ```yml
  $ git stash drop stash@{0}
  Dropped stash@{0}

  $ git stash list
  stash@{0}: WIP on remote-branch: 1b2bbcb Edit Leopards and Tigers
  ```

<br>

---

# 7-4. git commit --amend

> **_Commit message 변경하기_**

- 커밋 메시지를 변경하거나, 커밋에 변화를 추가 또는 커밋 메시지를 한 줄로 변경할 수도 있다.

- 커밋 메시지 변경

  - 파일에 변화를 준 후, 커밋 메세지를 `와웅` 으로 입력해보자.
  - `git commit --amend` 를 입력하여 편집창을 띄운다.
  - Commit message: `Add a member to Panthers` 를 입력 후, `:wq`를 입력하여 저장 종료한다.
  - `git log`로 확인해보자.

- 커밋에 변화 추가: 지난 커밋에 줘야할 변화를 깜빡했을 경우

  - 파일들에 변화를 준 후, staging area에 올린다.
  - `git commit --amend`로 마지막 커밋에 포함시킨다.

- 위에 처럼 `git commit --amend`를 실행한 후, 편집기가 뜨면 그 때 메세지를 수정하는 것 외에도 단 한 줄로도 수정할 수 있다.

  - `git commit --amend -m '(커밋 메세지)'`

- 또한 바로 staging area에 올리면서 한 번에 커밋메세지를 수정하는 방법도 있다.
  - `git commit -a --amend -m '(커밋 메세지)'`

<br>

---

# 7-5. git rebase -i

> **_i란 interactive를 의미하며, 과거의 커밋 내역들을 다양한 방법으로 수정 가능하다. _**

- `git rebase -i`를 입력했을 때, 사용되는 명령어들

| 명령어    | 설명               |
| --------- | ------------------ |
| p, pick   | 커밋 그대로 두기   |
| r, reword | 커밋 메세지 수정   |
| e, edit   | 수정을 위해 정지   |
| d, drop   | 커밋 삭제          |
| s, squash | 이전 커밋에 합치기 |

- `git log`로 커밋 내역들을 확인한다.
- `git rebase -i <commit 해시 번호>`를 입력하며 다음과 같이 뜬다.

```yml
$ git rebase -i

pick 1c799ad
pick ff00ad8
pick f35344a
pick b9d4eb7
pick 8605c74
pick 59b42f3
```

- 다음 수정사항들을 진행해보자.

  - hash number가 `1c799ad`인 커밋 메세지를 `버그 수정`으로 변경한다.

    - `r` 명령어를 사용한다.

  - hash number가 `ff00ad8`인 커밋은 삭제

    - `d` 명령어 사용

  - hash number가 `b9d4eb7`을 `f35344a`에 합치기

    - 첫 항목 뒤로 `s`명령어 사용하기
    - 메시지 수정 후 저장
      - 커밋 메세지는 하나만 있으면 되므로, 두 개중 하나의 커밋 메세지를 삭제한다.

  - 위 명령어들을 입력하면 다음과 같다.

    ```yml
    $ git rebase -i

    r 1c799ad
    d ff00ad8
    pick f35344a
    s b9d4eb7
    pick 8605c74
    pick 59b42f3
    ```

  - 한 커밋 안에 두 작업이 있으므로, 2개의 커밋으로 나누는 작업을 진행해보자.
    - `git rebase -i <나눌려고 하는 commit의 이전 commit 해시 번호>` 입력
      - pick에서 `e` 로 수정하고 `:wq`
    - `git reset HEAD~`
    - 변화들을 따로 스테이지 및 커밋
    - `git rebase --continue`

<br>

---

# Reference

- [제대로 파는 Git & GitHub - by 얄코](https://www.inflearn.com/course/%EC%A0%9C%EB%8C%80%EB%A1%9C-%ED%8C%8C%EB%8A%94-%EA%B9%83/dashboard)
- [Pro git : Second editions](https://book.naver.com/bookdb/book_detail.nhn?bid=7187291)
