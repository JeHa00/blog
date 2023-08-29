---
title: "Poetry 와 Pyenv의 자주 사용하는 명령어 정리"
date: 2023-03-31T23:38:50+09:00
draft: false
summary: Poetry와 Pyenv의 자주 사용되는 명령어를 정리한다.  
tags: ["TIL", "python"]
categories: ["Python"]
---

# 0. Introduction

---
Poetry와 Pyenv의 자주 사용하는 명령어를 정리해둔다.  

### Poetry를 사용하는 이유

- dependency를 **group 별로** 나눠서 관리할 수 있다.  
- dependency 간 **충돌을 관리** 해주고, 설치 시 기본적으로 최신 버전으로 설치해준다.
- package 관리 뿐만 아니라 **가상환경까지 설치 가능** 하다.  
- import 시 **절대 경로** 로 사용할 수 있다.
  - ❗️이를 위해서 만든 app 안에 설치하는 게 아닌 밖에 설치해야 한다.  

#

### Pyenv를 사용하는 이유

`virtualenv <가상환경 이름> --python=<python version 명>` 으로 생성하는 방식의 경우, 가상환경을 버전과 관련하여 정리하지 않는다. 하지만, pyenv의 경우에는 버전별로 설치한 후, 이 버전 안에서 여러 가상환경을 만들기 때문에 같은 python 버전을 공유하는 여러 가상환경이 있으면 패키지를 공유할 수 있다.  

#

### Poetry로 생성한 가상환경을 사용하지 않는 이유

위에 Pyenv를 사용하는 이유와 동일하다.
Poetry로 생성한 가상환경은 일반 가상환경이랑 관리 방식이 동일하다. 버전별로 관리하는 게 아닌, poetry가 생성한 가상환경을 기준으로 디펜던시를 설치하기 때문에, 동일한 파이썬 버전이어도 가상환경이 다르면 아래처럼 파이썬 패키지가 중복 설치되는 문제점이 존재한다.

```
- Poetry로 생성한 가상환경 1 (Python 3.10.8 사용)
  - bin/
  - lib/
- Poetry로 생성한 가상환경 2 (Python 3.10.8 사용)
  - bin/
  - lib/
```

&nbsp;

# 1. Poetry 명령어 정리

---

poetry 공식문서는 [여기](https://python-poetry.org/docs/)를 클릭한다.

- **Poetry 설치 및 삭제하기**
  - 설치하기: `curl -sSL https://install.python-poetry.org | python3 -`
  - 삭제하기: `curl -sSL https://install.python-poetry.org | python3 - --uninstall`

- **pyproject.tomal 생성하기:** `poetry init`

- **poetry에 dependency 추가하기** : `poetry add {dependency name to install}`

- **dependency를 group 별로 추가하기**:`poetry add --group {group name} {dependency to install}`

- **poetry에 dependency 제거하기:** `poetry remove {dependency name}`

- **poetry의 pyproject.toml에 있는 dependencies 설치하기:** `poetry install`
  - 현재 프로젝트에 있는 pyproject.toml을 읽어서, 해당 dependencies 를 설치하는 명령어로, 실행하면 `poetry.lock` 이 생긴다.  
  - poetry.lock과 pyproject.toml이 일치하지 않는 문제가 생기면 lock을 삭제한 후, 다시 poetry install을 실행하자.

- **Poetry가 바라보고 있는 가상환경 정보 확인하기:** `poetry env info`
  - 위 명령어는 pyproject.toml이 존재하는 directory에서 실행한다.  

- **Poetry 버전 확인:** `poetry --version`  

&nbsp;

# 2. Pyenv 명령어 정리

---
poetry 공식문서는 [여기](https://github.com/pyenv/pyenv#set-up-your-shell-environment-for-pyenv )를 클릭한다.

- **실행 명령어:** `pyenv shell {python version installed}`
- **설치된 버전들 확인:** `pyenv versions`
- **파이썬 특정 버전 설치하기:** `pyenv install {python version to install}`
- **가상환경 생성하기:** `pyenv virtualenv {python_version to install} {virtualenv_name to install}`
- **가상환경 지정하기(활성화하기):** `pyenv shell {virtual_name installed}`
- **가상환경 삭제하기:** `pyenv uninstall {virtualenv installed}`

&nbsp;

# 3. Poetry와 Pyenv 사용 순서

---

1. pyenv 가상환경을 생성
2. pyenv 가상환경 활성화하기
3. 프로젝트 내부로 이동 후, poetry init 실행
4. poetry env info 로 system과 가상환경 모두 pyenv를 바라보는지 확인  
5. poetry에 dependency 추가하기
6. dependency를 모두 추가하면 poetry install 실행: 개인 프로젝트는 파이썬 공식 프로젝트가 아니기 때문에 반드시 해야한다.
7. main.py가 있는 위치로 이동 후, 프로젝트 실행  

❗️ poetry 설치 후 프로젝트 directory 경로가 절대경로로 인식되지 않는다면 가상환경이 제대로 실행되고 있지 않거나, poetry가 설치가 잘 안된 것이므로 재확인한다. 이런 경우 `poetry install`를 잊지 않는다.  
❗️ `poetry env info` 를 실행하여 **Virtualenv** 와 **System** 모두 pyenv를 바라보고 있는지 확인한다.  

&nbsp;

# 4. Poetry 설치 시 프로젝트 directory 구조  

- pyproject.toml에서 name의 값이 project root directory가 기본적으로 들어가지는데 이를 기준으로 절대경로가 형성된다.

- 그리고 Dockerfile 실행 시 pyproject.toml과 동일한 레벨에 해당 name을 가지고 있는 directory 와 README.md 가 poetry 설치가 가능하여 container 화가 가능하다.  

```yml
# directory 구조

- <생성한 project name>
  ㄴ config
  ㄴ <app 또는 domain name>
ㄴ pyproject.toml
ㄴ poetry.lock
ㄴ README.md
```

# Reference

---

- [파이썬 의존성 관리자 Poetry 사용기](https://spoqa.github.io/2019/08/09/brand-new-python-dependency-manager-poetry.html)
- [poetry 가상환경](https://python-poetry.org/docs/)  
- [pyenv 가상환경](https://github.com/pyenv/pyenv#set-up-your-shell-environment-for-pyenv )
