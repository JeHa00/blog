---
title: "CI/CD를 위한 github action study basic"
date: 2023-08-03T21:18:52+09:00
draft: false
summary: github actions의 core components인 workflow, jobs, steps에 대해 알아보고 실습을 진행하면서 github action의 내장 keyword도 알아본다.  
tags: ["ci/cd"]
categories: "CI/CD"
---
# 0. Introduction

프로젝트를 진행하면서 새로 작성된 또는 수정된 코드를 repository에 push하면 자동적으로 테스트를 실행하고, 통과되면 합쳐지고 아니면 에러를 발생시키는 workflow를 적용하고 싶어서 예전부터 공부하고 싶었던 github action에 대해 study를 시작한다.

국내에서는 github action에 대한 강의나 서적을 찾지 못하여 멘토링을 찾았지만 시간 대비 너무 비싸서 포기했다. 그래서 udemy를 찾던 중 저렴한 가격에 좋은 퀄리티 강의를 찾게 되어 이 강의를 중심으로 학습을 진행한다.  

해당 강좌에서 사용되는 소스 코드를 사용하는 부분들은 빼고 블로그에 정리한다.  

해당 소스 코드를 사용하는 부분이 있다면 그 부분이 없어도 해당 블로그만 봐도 익힐 수 있도록 정리해놓는다.  

이미 알고 있는 개념들도 있지만, 놓치고 있는 부분을 확인하고자 정리해본다.  

🔆 첨부된 링크는 구글에 `github actions {keyword}` 로 입력하면 제일 첫 번째로 나온다.

&nbsp;

---

# 1. What is Git, Github and Github Action?

## 1.1 What is Git?

> **_무료로 사용할 수 있는 관리 시스템 도구로서, 소스 코드의 변화들을 관리하는 역할을 한다._**  

구체적으로 정리하자면 다음 3가지로 정리할 수 있다.

- Git의 3가지 역할
  - create a snpshot (commit)
  - branch를 사용하여 다른 코드와 작업이 가능하고,
  - 여러 브랜치와 commit들 사이로 이동할 수 있다. (checkout, switch)  

#

## 1.2 What is Github?

> **_store & manage git repositories_**

git 으로 관리되고 있는 repositories들의 cloud 저장소이자, 저장된 code들에 대해 여러 서비스를 제공하는 회사다.  

- Github의 3가지 역할
  - cloud git repository storage(push & pull)
  - code 관리 & 개발하는데 협력하는 도구 (Issues, PR 등등)
  - github Action을 통한 CI / CD

#

## 1.3 What is Github Actions?

> **_github에 의해서 제공되는 workflow 자동화 서비스로, repository와 관련하여 자동화된 프로세스들과 action들을 의미한다._**  

하지만 이 서비스에서 가장 중요한 부분은 CI / CD 다. 이 용어는 아래 용어의 약어를 의미한다.  

- CI: Continuous Integration
- CD: continuous Delivery

CI는 코드 변화을 기존에 존재한 코드와 합쳐져서 build 되고, 테스트를 거치고, 최종적으로 합쳐지는 단계를 말한다.  

CD는 CI 를 거치고 나서, 새로운 앱과 패키지 버전들이 자동적으로 배포되는 걸 말한다.  

예를 들어 코드를 수정하면 자동적으로 새로운 웹 사이트 버전을 자동적으로 업로드하고 publish 하기를 원할 것이다. 이것이 CI/CD다.  

그러면 가장 중요한 부분이 CI/CD라고 했으니 그 외의 부분들에 대해 언급하자면  workflow나 프로세스의 종류만을 자동화하는 것뿐만 아니라, repository 및 코드 관리도 수행한다.

- code reviews, issue management

&nbsp;

# 2.Github Actions - Basic Building Blocks & Components

그러면 본격적으로 github action에 대해 학습해보자.

학습 목표는 다음과 같다.

- github action에서 핵심 요소들을 이해하기  
- Workflows, Jobs & Steps 로 작업하기  
- 예시 workflow를 직접 구성해보기

## 2.1 key elements: Workflows, Jobs, Steps

---

Github Action에서는 실행

위 3가지는 Code repository와 관련 있다.  

- workflow는 몇 개가 되든 추가할 수 있다.
- 하나의 workflow는 한 개 이상의 Job을 포함한다.  
- 하나의 job은 한 개 이상의 Step을 포함한다.  
  - Step은 실질적인 것들을 의미하는데,
  - code 를 다운 받거나, dependency를 설치하거나, 테스트를 진행하는 등의 단계들을 의미한다.

**Workflows**

- Github repository에 부착된다  
- 한 개 이상의 Job을 가진다  
- Events를 기반으로 시작된다.  
- `.github/workflows`  

**Jobs**

- 하나의 Job마다 하나의 `Runner`를 정의한다.
  - **_`Runner`는 `runs-on`으로 지정한다. 그래서 job마다 `runs-on`을 입력해야 한다. 즉 job마다 자신만의 virtual machine을 가져서 다른 machine과 job으로부터 완전히 격리시켜야 한다는 의미다._**  
- 1개 이상의 `Steps`를 포함한다.
- 병렬적으로 또는 동기적으로 실행된다.
- 실행 조건을 부여할 수 있다.

**Steps**

- 하나의 `Action` 또는 하나의 `shell script`를 실행한다.  
  - `Action`은 workflow의 가장 작은 실행 단위
  - 커스텀 또는 third party action을 사용할 수 있다.  
- steps는 순서대로 실행된다.
- 실행 조건을 부여할 수 있다.

**정책**

- public repository에 대해서는 Github Action은 무료다.  하지만 private이면 매달 특정 사용량까지만 무료다.  
- 보다 구체적인 사용 요금 정책은 [여기](https://docs.github.com/en/billing/managing-billing-for-github-actions/about-billing-for-github-actions)를 확인한다.
- 만약 repository tab에서 `Actions` 를 확인하지 못한다면 [여기](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/enabling-features-for-your-repository/managing-github-actions-settings-for-a-repository)를 클릭하여 설명을 확인하자.  

#

## 2.2 Practice 1  

---

해당 실습은  `gh-firsts-action`이라는 public repository를 생성하여 이 repository에서 진행했다.  

그러면 첫 `workflow`를 생성해보자. 첫 예제인 만큼 `Actions` tab에 들어가서 간단한 worflow인 `Simple workflow` 를 선택해서 사용해보자. 해당 workflow의 `configure`를 선택하자.

`runners`에 대한 상세 설명 [여기](https://docs.github.com/ko/actions/using-github-hosted-runners/about-github-hosted-runners)를 확인하자.

workflow yml 파일을 작성해보자. 위에부터 아래 순으로 하나 하나 설명해보겠다.

- name, on, jobs, runs-on, steps, name, run은 github action에 내장된 keyword다.  
- `name`: workflow의 이름을 말한다.
  - 아래 예시의 이름은 `First workflow`다.  
- `on`:  아래 workflow를 실행시킨 이벤트를 정의한다.
  - `workflow_dispatch`는 Actions tap에서 수동적으로 직접 실행시키는 걸 말한다.  
- `jobs`: job들을 정의한다.  
  - 첫 번째 job의 이름은 `first-job` 으로 명명한다. 'firstjob'으로 해도 되고, job의 이름을 짓는 건 자유롭다.  
  - `runs-on`: 해당 job이 실행될 환경을 말한다.  구체적인 환경 확인은 위에 언급된 runners에 대한 설명을 본다.  
    - **_job마다 자신만의 runner를 가지고 있다. 즉 job마다 `runs-on`을 입력해야 한다. 이 말은 job마다 자신만의 virtual machine을 가져서 다른 machine과 job으로부터 완전히 격리시켜야 한다는 의미다._**
  - `steps`: 이제 여러 step들을 정의한다. 여기부터는 `-` 를 붙어서 key - value 형식으로 작성한다.  
  - 위에 Workflows, Jobs, Steps의 설명에 언급된 것처럼 step은 하나의 shell script를 실행하는데, 아래 step은 총 2개의 script를 실행하도록 정의했다.  

```yml
name: First workflow 
on: workflow_dispatch  
jobs: 
  first-job:
    runs-on: ubuntu-latest
    steps:
      - name: Print greeting
        run: echo "Hello World!"
      - name: Print Goodbye
        run: echo "Done - bye!"
```

- 위 스크립트는 여러 step을 통해 shell commands를 실행했다. 하지만 다음과 같이 pipe symbol(|)을 사용하여 하나의 step에서도 실행할 수 있다.

    ```yml
    ...
    run: |
        echo "First output"
        echo "Second output"  
    ```

그러면 이를 다 작성했으면 오른쪽 상단에 `Commit Changes...`를 클릭하여 아래 이미지에 보이듯이 팝업창의 `Commit changes` 를 클릭하여 작성한 workflow를 생성하자.  

![image](https://github.com/JeHa00/image/assets/78094972/df136ac2-6e5e-40be-afbe-0cee661a6649)  

그러면 Actions tab을 클릭하면 다음 이미지처럼 화면이 보일 것이고, 오른쪽 위에 `Run workflow`를 클릭하면 실행할 브랜치와 수동으로 실행시킬 수 있다.

![image](https://github.com/JeHa00/image/assets/78094972/a9e5fed0-51cc-4bd7-890b-0e7d95e9a396)

- 직접 실행하는 workflow_dispatch로 실행할 경우, workflow의 name이 위 이미지처럼 title로 뜬다.  

&nbsp;

## 2.3 workflow를 실행시키는 event의 종류들  

---

다음 실습을 실행하기에 앞서서 workflow를 실행시키는 여러 events들에 대해 알아보자.  

github action에서 내장된 event 들 중 많은 것들은 repository와 관련있다.

### repository와 관련된 events

- push: pushing a commit
- pull_request: pull request action (opened, closed,..)
- create: branch 나 tag 생성 시  
- fork: repository가 fork 될 때  
- issues: 하나의 issue가 열리고, 삭제될 때 등등  
- issue_comment: Issue나 PR에 comment가 달릴 때  
- discussion: Discussion action과 관련하여 생성되고, 삭제되고, 수정되거나 등등  
- 기타 등등

#

### repository와 관련 없는 events

- workflow_dispatch: 수동적으로 workflow를 진행시키는 것  
- repository_dispatch: REST API 요청으로 workflow를 진행시키는 것  
- schedule: workflow를 특정 시간에 실행시키는 것
- workflow_call: 다른 work flow에 의해서 호출될 경우  

Github workflow의 이용 가능한 모든 event들을 알고 싶다면 [여기](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows) 를 클릭하자.  

&nbsp;

## 2.4 What are Actions?

---

command는 작성자에 의해 정의되는 shell command로 대체로 단순한다.  

하지만 Action은 빈번하게 반복되는 일을 수행하는 애플리케이션으로 대체로 복잡하다. custom Action을 만들 수 있고, 또는 공식적인 Action을 사용할 수 있다.  

그래서 코드를 내려받는 Action 중에 [actions/checkout](https://github.com/actions/checkout)이 존재한다. Github 팀에 의해서 만들어지고 유지 관리되고 있는 repository다.  

위 action외에도 [github action market](https://github.com/marketplace?type=actions)에 들어가면 여러 action을 확인할 수 있다.

&nbsp;

## 2.5 Practice 2

---

그러면 다음 실습으로 보다 실제 프로젝트에 가깝게 하기 위해서 local에서 workflow를 작성하여 진행해볼려고 한다.

실습 1에서 만든 repository와는 상관 없이 진행한다. 두 번째로 진행할 실습은 참고 강의에서는 reactJS로 진행했다. reactJS를, js를 만질 줄 몰라도 상관없다. 이 글은 frontend를 학습하기 위한 글이 아닌, github action을 학습하기 위한 글이기 때문이다.
**_소제목이 무엇을 의미하는지, 또는 소제목 대로 진행하기 위해서는 어떻게 yml을 작성하면 되는지_** 를 이해하면 된다. 단지 실습을 통해서 여러 내용을 학습하기 위한 것일 뿐이다.  

이번 실습을 하기 전에 workflow를 진행시키는 event 들에 대해 알아보자.

```yml
name: Test Project
on: push
jobs:
  test: 
    runs-on: ubuntu-latest 
    steps:
      - name: Get code

```

#

### run과 uses의 차이  

그러면 다시 yml 작성으로 돌아가보자.

name을 작성했으니 그 다음으로 runs를 작성하면 될까?

그렇지 않다.

`runs`은 Action이 아닌 command를 실행할 때 사용하는 내장된 키워드다.

Action을 실행하기 위해서는 `uses`를 사용해야 한다. 그 다음으로 해당 Action의 식별자를 입력한다.

```yml
name: Test Project
on: push
jobs:
  test: 
    runs-on: ubuntu-latest 
    steps:
      - name: Get code
        uses: actions/checkout@v3
```

`actions/checkout`은 [github action market](https://github.com/marketplace?type=actions)에 들어가서 `checkout`을 검색하여 들어가면 다음 이미지와 같이 Usage에서 `- uses: actions/checkout@v3`를 확인할 수 있다. Actions를 입력할 때 버전도 함께 입력하자. 업데이트 되면서 현재 사용하고 있는 Action이 조금 달라질 수도 있기 때문이다.  

![image](https://github.com/JeHa00/image/assets/78094972/3fe4e5fd-68bc-4811-829e-7c192c61bc64)  

#

### with  

몇 몇 Action의 경우에는 추가적인 세팅을 요구하기도 한다. 그럴 때는 `with` keyword를 하단에 입력하여 사용한다. `uses` keyword를 사용한 후 입력한다.  

```yml
name: Test Project
on: push
jobs:
  test: 
    runs-on: ubuntu-latest 
    steps:
      - name: Get code
        uses: actions/checkout@v3
        with: 
          ...
```

그렇다면 추가로 입력해야하는 설정들의 key 값은 무엇이 있는지 어떻게 알 수 있을까?

바로 위에 첨부한 이미지를 보면 `uses` keyword 밑에 `with` keyword가 있는 걸 알 수 있다. 이 설정들은 그대로 사용한다면 수정하지 않아도 되지만, custom 하여 사용한다면 수정해야할 수도 있다.  

`actions/checkout@v3`에는 특별하게 요구되는 옵션이 없으므로 `with`는 사용하지 않는다.  

#

### 이미 설치된 소프트웨어 확인  

python 기반 프레임워크를 사용한다면 python을, js라면 Node.js 가 설치해야 한다. `runs-on`에 있는 작성한 소프트웨어에 미리 설치된 소프트웨어로 뭐가 있는지 확인해서 없으면 python 또는 node.js를 설치해야 한다. 이를 확인하기 위해서 [pre-installed software](https://docs.github.com/ko/actions/using-github-hosted-runners/about-github-hosted-runners#supported-software)에 있는 os 목록들 중 해당되는 os를 클릭하여 확인한다.  

```yml
name: Test Project
on: push
jobs:
  test: 
    runs-on: ubuntu-latest 
    steps:
      - name: Get code
        uses: actions/checkout@v3
      - name: Install NodeJS
        uses: actions/setup-node

      # or 

      - name: Install Python
        uses: actions/setup-python
```

#

### 직접 programming language 설치하기  

하지만 만약 직접 설치를 해야한다면 다음과 같이 설치 step을 추가한다.  
step을 추가할 때는 `actions/setup-node` 를 검색하여 나온 [actions/setup-node](https://github.com/actions/setup-node) 또는 `actions/setup-python` 을 검색하여 나온 [actions/setup-python](https://github.com/actions/setup-python) 에 들어가면 작성 안내를 확인할 수 있다.  

```yml
name: Test Project
on: push
jobs:
  test: 
    runs-on: ubuntu-latest 
    steps:
      - name: Get code
        uses: actions/checkout@v3
  # Python
      - name: Install Python
        uses: actions/setup-python@v4
        with:
            python-version: '3.10'  
   
   # or NodeJS
      - name: Install NodeJS
        uses: actions/setup-node@v3
        with:
            node-version: 18

```

#

### Dependency 설치하기  

그 다음으로 dependency를 설치해보자.  

```yml
name: Test Project
on: push
jobs:
  test: 
    runs-on: ubuntu-latest 
    steps:
      - name: Get code
        uses: actions/checkout@v3
  # Python
      - name: Install Python
        uses: actions/setup-python@v4
        with:
            python-version: '3.10'  
      # dependency
      - name: Install dependencies 
        run: pip install -r requirements.txt  # poetry도 사용 가능하다.  
   
   # or NodeJS
      - name: Install NodeJS
        uses: actions/setup-node@v3
        with:
            node-version: 18
      # dependency
      - name: Install dependencies 
        run: npm ci 
```

#

### test 실행하기  

다음으로 test를 실행하는 step을 작성해보자.  

```yml
name: Test Project
on: push
jobs:
  test: 
    runs-on: ubuntu-latest 
    steps:
      - name: Get code
        uses: actions/checkout@v3
  # Python
      - name: Install Python
        uses: actions/setup-python@v4
        with:
            python-version: '3.10'  
      - name: Install dependencies 
        run: pip install -r requirements.txt 
      # tests
      - name: Run tests
        run: pytest  # 해당 글 작성자는 pytest를 통해 test를 하기 때문에 pytest를 작성했다.  
   
   # or NodeJS
      - name: Install NodeJS
        uses: actions/setup-node@v3
        with:
            node-version: 18
      - name: Install dependencies 
        run: npm ci 
      # tests
      - name: Run tests
        run: npm test
```

여기까지 작성했으면 remote repository에 push를 하자.

❗️ udemy 강좌에서는 원격을 등록할 때 Personal access tokens 을 사용하는 방식으로 했으나 나는 그 방식을 사용하지 않았다. 만약 그 방식으로 remote origin을 등록했다면 github의 `settings`에 들어가서 `New personal access token` 항목에 들어가 `Select scopes`에서 `workflow`를 클릭한 후 토큰을 생성해야 local에서 작성한 workflow를 원격에 push할 수 있다.

### workflow 확인하기  

push가 완료된 후, github repository의 Actions tab에 들어가서 확인하면 생성했던 workflow가 이미 실행 중인 걸 확인할 수 있다. 왜냐하면 새로 생성된 workflow의 `on` keyword에 대한 value가 `push` 이기 때문이다.  

강의의 소스 코드 예제가 js이기 때문에 Node.js 중심으로 작성된 workflow다. 실행 결과는 다음과 같다.

![image](https://github.com/JeHa00/image/assets/78094972/6ce19e8a-2717-4e96-92b9-837d7e41a830)

그러면 테스트 코드 일부를 수정한 후, 다시 원격에 push 해서 fail를 발생시켜보자. 실행 결과는 다음과 같다.

![image](https://github.com/JeHa00/image/assets/78094972/7f89f052-564f-4bd1-b066-1faf988b2199)

실패한 step을 클릭하면 어느 부분으로 실패했는지까지 알 수 있다.

#

### 여러 개의 job 추가하기: 병렬적으로 실행하기

이번에는 `test` 란 job에 추가로 `deploy` 라는 job을 추가해보자. job을 추가하는 것이기 때문에, `test`와 동일한 level로 띄어쓰기를 해야 한다.  

예제에서는 실제 hosting provider를 가지고 있지 않기 때문에 단지 `echo` 하는 걸로 대신하겠다.  

```yml
name: Deploy Project # Test Project에서 다음과 같이 수정  
on: push
jobs:
  test: 
    runs-on: ubuntu-latest 
    steps:
      - name: Get code
        uses: actions/checkout@v3
  # Python
      - name: Install Python
        uses: actions/setup-python@v4
        with:
            python-version: '3.10'  
      - name: Install dependencies 
        run: pip install -r requirements.txt 
      - name: Run tests
        run: pytest
  deploy:   # 또 다른 job 추가하기
    runs-on: ubuntu-latest
    steps:  
      - name: Get code
        uses: actions/checkout@v3
      - name: Install Python
        uses: actions/setup-python@v4
        with:
            python-version: '3.10'  
      - name: Install dependencies 
        run: pip install -r requirements.txt 
      - name: Deploy
        run: echo "Deploying..."

  # or NodeJS
      - name: Install NodeJS
        uses: actions/setup-node@v3
        with:
            node-version: 18
      - name: Install dependencies 
        run: npm ci 
      - name: Run tests
        run: npm test
  deploy: # 또 다른 job 추가하기  
    runs-on: ubuntu-latest
    steps:  
      - name: Get code
        uses: actions/checkout@v3
      - name: Install NodeJS
        uses: actions/setup-node@v3
        with:
            node-version: 18
      - name: Install dependencies 
        run: npm ci 
      - name: Build project
        run: npm run build
      - name: Deploy
        run: echo "Deploying..."
```

동일한 level로 아래와 같이 작성한 두 job들은 순차적으로 실행되는 게 아닌 아래 이미지처럼 **_병렬적으로_** 실행된다.  

![image](https://github.com/JeHa00/image/assets/78094972/e42ffbb7-925c-4de7-af94-643f5bd20c8a)

완료되면 아래 이미지와 같이 Total duration으로 전체 걸린 시간을 알 수 있다.  

![image](https://github.com/JeHa00/image/assets/78094972/808c3a9c-e2a9-47d6-8b7c-f837ea80032e)

#

### 🔆 github action에 뜨는 workflow 소제목  

직접 실행하는 workflow_dispatch로 실행할 경우, workflow의 name이 위 이미지처럼 title로 뜬다. 하지만 push 같은 자동적으로 실행되도록 하는 event의 경우 commit message가 title로 뜬다.

- add new job이 commit message로 작성한 것  

#

### 병렬이 아닌 순차적으로 workflow 실행하기

job 식별자를 입력한 후에 `needs`라는 내장 키워드를 입력하여, 선 작업을 지정한다. 선 작업으로 진행된 job이 끝나야 `needs`가 작성된 job이 실행될 수 있다.  

```yml
name: Deploy Project
on: push
jobs:
  test: 
    runs-on: ubuntu-latest 
    steps:
      - name: Get code
        uses: actions/checkout@v3
        ... 
  
  deploy: 
    needs: test  # 선작업 지정하여 순차적으로 실행 
    runs-on: ubuntu-latest
    steps:  
      - name: Get code
        uses: actions/checkout@v3
        ...
```

아래 이미지에서 확인할 수 있듯이 Actions tap을 들어가보면 jobs의 위치가 바뀐 걸 알 수 있다.  

![image](https://github.com/JeHa00/image/assets/78094972/e2f3a63b-748e-47fd-bc14-9ef05536ef01)

하지만 만약 test job이 fail되면 아래 이미지처럼 deploy는 실행되지 않는다.

![image](https://github.com/JeHa00/image/assets/78094972/9fa956f8-ab7e-4ff4-bcfc-643c84b083fe)  

#

### 하나의 event가 아닌 여러 events를 trigger로 사용하기: 대괄호로 사용하기

이전 코드에서 작성된 event는 다음과 같이 하나다.

```yml
name: Deploy Project
on: push # 하나의 event
jobs:
  test:
    ...
  deploy:
    ...

```

그러면 여러 event를 정의해보자. 여러 events를 정의하기 위해서는 대괄호를 사용해야 한다.  

```yml
name: Deploy Project
on: [push, workflow_dispatch] 
jobs:
  test:
    ...
  deploy:
    ...

```

아래 이미지는 Multiple events로 수정하여 push하고, 수동적으로 workflow를 main branch에서 실행한 것이다.  

![image](https://github.com/JeHa00/image/assets/78094972/01858c10-1d42-4071-8efe-4c3e837185c3)

event에 workflow_dispatch가 있기 때문에 동시에 workflow를 실행할 수 있다.  

### Expressions and Github context  

위 yml처럼 command를 실행할 수도 있지만 환경변수를 필요로 하는 command를 실행할 필요가 있을 수 있다.  

3번째 workflow `output.yml` 를 생성해보자.  

```yml
name: Output information
on: workflow_dispatch
jobs:
  info:
    runs-on: ubuntu-latest 
    steps:
      - name: Output github context 
        run: echo "${{ toJSON(github) }}"
```

위 workflow를 보면 `${{ }}` 을 확인할 수 있다. 이 문법으로 작성된 text는 github에 신호를 보낸다.  
Github에 의해서 이용가능하도록 만들어진 metadata에 접근할 것이다.  
`github`는 내장된 key 값이다.  그리고 Github의 내장된 함수인 `toJson`으로 이 값을 감쌀 것이다.

내장된 context key값은 [Github Actions context](https://docs.github.com/en/actions/learn-github-actions/contexts) 를 확인하면 알 수 있다.  그리고 github actions의 내장된 function expression은
[Github Actions expressions](https://docs.github.com/en/actions/learn-github-actions/expressions)를 보면 알 수 있다.  

`github` key는 위 문서에 따르면 진행 중인 workflow에 대한 정보를 의미한다고 한다. `toJSON`은 위 문서에 따르면 JSON 형태로 바꿔준다.  

새로 생성한 workflow를 수동적으로 실행한 후, 출력물을 확인하면 github context object로부터 가져온 데이터를 확인할 수 있다. 아래 이미지를 확인하므로써 Practice 2를 마친다.  

![image](https://github.com/JeHa00/image/assets/78094972/a850fc13-fc82-4f6c-ba6a-5323300121fc)

&nbsp;

# 3. Summary

---

그러면 Github actions의 core component인 workflow, job, step을 다시 짚어보자.  

### Core components

> **_workflow 안에는 한 개 이상의 Job들이 존재하고, Job 안에는 한 개 이상의 step이 존재한다._**

- workflows: define Events + Jobs
  - event란 workflow를 실행시키는 trigger다.  
  - 하나 이상의 event와 Job을 정의하는 component
- Jobs: define Runner + Steps  
  - runner와 step을 정의하는 component
  - job들은 병렬로 또는 순차적으로 실행 가능하다.  
- Steps: Do the actual works
  - 실제 작업을 수행하는 component
  - 여기서 work는 Github action에서 내장되어 정의된 작업 또는 직접 정의한 work가 해당된다.  
  - 실제로 실행되는 actions  

#

### Events / Triggers

Events의 종류에는 매우 다양한데, 크게 분류하자면 repository와의 관련 유무로 나눌 수 있다.  

#

### workflow 정의하기

- `.github/workflows/<file>.yml` 인 경로로, 파일 형식으로 작성하여 정의한다.  

- 이 때 작성 장소는 local과 github 모두에서 작성 가능하다.

- 그리고 Github Actions syntax를 반드시 따라야 한다.  

#

### Runners

- runner는 job 들을 실행하는 서버들을 의미한다.  
- 이 runners는 OS에 따라서 다르게 사전에 정의된 runner들이 존재한다.  
- 하지만 custom runner도 정의내릴 수 있다.  

&nbsp;

---

# Reference

- [[udemy lecture] Github Actions - The Complete Guide](https://www.udemy.com/course/github-actions-the-complete-guide/)  
- [github actions billing policy](https://docs.github.com/en/billing/managing-billing-for-github-actions/about-billing-for-github-actions)
- [github settings for repository](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/enabling-features-for-your-repository/managing-github-actions-settings-for-a-repository)
- [github actions - runners](https://docs.github.com/ko/actions/using-github-hosted-runners/about-github-hosted-runners)
- [github actions - runners: pre-installed software](https://docs.github.com/ko/actions/using-github-hosted-runners/about-github-hosted-runners#supported-software)
- [github actions - events that trigger workflows](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows)
- [github actions market](https://github.com/marketplace?type=actions)
- [actions/setup-python](https://github.com/actions/setup-python)
- [actions/setup-node](https://github.com/actions/setup-node)
- [Github Actions context](https://docs.github.com/en/actions/learn-github-actions/contexts)
- [Github Actions expressions](https://docs.github.com/en/actions/learn-github-actions/expressions)  
