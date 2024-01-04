---
title: "CI/CD를 위한 github action study: workflow와 job 실행 제어하기 & Jobs 와 Docker containers"
date: 2023-08-11T18:29:20+09:00
draft: false
summary: 계속 작업 중
tags: ["ci/cd"]
categories: "CI/CD"
---
# 0. Introduction

- [CI/CD를 위한 github action study basic](https://jeha00.github.io/post/ci_cd/github_action/lecture_01/)
- [CI/CD를 위한 github action study: Activity type, filters, caching, environment variables and secrets](https://jeha00.github.io/post/ci_cd/github_action/lecture_02/)

프로젝트를 진행하면서 새로 작성된 또는 수정된 코드를 repository에 push하면 자동적으로 테스트를 실행하고, 통과되면 합쳐지고 아니면 에러를 발생시키는 workflow를 적용하고 싶어서 예전부터 공부하고 싶었던 github action에 대해 study를 시작한다.

국내에서는 github action에 대한 강의나 서적을 찾지 못하여 멘토링을 찾았지만 시간 대비 너무 비싸서 포기했다. 그래서 udemy를 찾던 중 저렴한 가격에 좋은 퀄리티 강의를 찾게 되어 이 강의를 중심으로 학습을 진행한다.  

해당 강좌에서 사용되는 소스 코드를 사용하는 부분들은 빼고 블로그에 정리한다.  

해당 소스 코드를 사용하는 부분이 있다면 그 부분이 없어도 해당 블로그만 봐도 익힐 수 있도록 정리해놓는다.  

이미 알고 있는 개념들도 있지만, 놓치고 있는 부분을 확인하고자 정리해본다.  

🔆 첨부된 링크는 구글에 `github actions {keyword}` 로 입력하면 제일 첫 번째로 나온다.

&nbsp;

# 1. Controlling Workflow & Job Execution  

## 1.1   if and continue-on-error

---

workflow의 job들이 의존적으로 되어있고 실행 흐름 중간에 에러가 발생되면 그 이후 단계는 실행되지 않는다.

그런데 에러가 발생해도 뒷 작업이 실행되야한다면 어떻게 해야할까?

`if` field를 사용해서 조건을 부여하여 이를 제어할 수 있다.  

이 field와 함께 사용할 github actions 함수 몇 가지를 알아보자.

### 특별한 조건 함수들

- failure(): 한 job의 step이 실패하면 true를 반환  
- success(): 이전 Step, Job이 성공하면 true를 반환
- always(): 이전 step이나 job이 취소되도 언제나 true를 반환
- cancelled(): workflow가 취소된 경우 true를 반환

### code  

그러면 위 내용들을 code에 적용해보자.  

```yml
name: Demo
on:
  push:
    branches:
      - main
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
        ...
  
  test:
    runs-on: ubuntu-latest
    steps:
        ...
      - name: Test code
      # code 추가
        id: run-tests 
      #
        run: npm run test
      - name: Upload test report
      
      # code 추가
        if: failure() && steps.run-tests.outcome == 'failure'
      #
        uses: actions/upload-artifact@v3
        with:
          name: test-report
          path: test.json
  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
        ...

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
        ...
```

### test.json 추가

프로젝트 `.github`과 동일한 level로 `test.json`을 생성한다. 그래서 Upload test report가 실행되면 test.json이 artifacts로서 다운받을 수 있다.

그리고 `name` 필드는 artifacts의 이름을 지정한다.  

### if 와 id field 적용 그리고 outputs 사용  

내가 원하는 workflow 흐름은 Test code 단계 step이 실패하면 `Upload test report` step이 진행되기를 원한다. 이를 위해서 `if` 필드를 적용했다. 그리고 확실하게 어느 step인지를 지정하기 위해서 [github actions context:steps](https://docs.github.com/en/actions/learn-github-actions/contexts#steps-context) 문서를 참고하여 `id` 필드를 적용했다.  
이 id 필드를 사용해서 `steps.<step_id>.outputs` 나 `steps.<step_id>.conclusion`을 사용하기 위해서다.  

위 코드에서 conclusions 말고 outcome을 사용한 이유는 `continue-on-error` 없이 사용하기 위해서다. 먼저 없는 거를 진행해본 후 `continue-on-error`를 적용해보자.

### failure() 적용

그리고 `failure()` 이라는 함수를 추가한 이유는 이전 step들에서 실패하면 `Upload test report`가 진행되지 않기 때문이다. failure()를 추가하면 해당 step이 반드시 평가되기 때문에 진행된다.

그러면 `failure()`만 하면 되지 왜 `steps.run-tests.outcome`을 추가한 것일까?

`failure`는 바로 직전 step 뿐만 아니라 그 전 step까지 포함하기 때문에 `run-test`에서 실패하지 않고 그 이전에 실패해도 실행된다. 하지만 내가 원하는 건 Test code 단계에서 실패할 경우이기 때문에 직전 step 실패를 적용하고자 `&&` 연산자를 추가했다.  

Job과 Step은 `if` field를 통해서 실행을 조절할 수 있다.  

Step은 `continue-on-error` filed를 통해서 error를 무시할 수 있다. 그래서 이 표현들을 통해서 조건들을 평가한다.  

그러면 `failure()`를 또 다른 job에 적용해보자.

```yml
name: Demo
on:
  push:
    branches:
      - main
jobs:
    lint:
        ...
    test:
        ...
    build:
        ...
    deploy:
        ...
    report:
        needs: [lint, deploy]
        if: failure()
        runs-on: ubuntu-latest
        steps:
            - name: Output information
              run: |
                echo "Something went wrong"
                echo "${{github}}
```

위 report job에서 `needs` field를 추가한 이유는 이 field를 추가하지 않으면 github action에서는 다른 job이 끝나기를 기다려주지 않기 때문에 강제적으로 선행 job을 지정해야 한다.
그리고 needs에 lint, deploy job만을 지정한 이유는 첫 번째 job이고, 마지막 job을 지정하여 처음과 끝 job 까지 다 거쳐서 그 과정에서 실패할 경우 해당 report job을 실행하도록 했다.

이 needs를 사용하여 실행한 결과 다음 이미지의 job 순서를 확인할 수 있다.  needs를 선정하지 않았다면 lint와 병렬적으로 실행하여 실패한다.  

![image](https://github.com/JeHa00/image/assets/78094972/5db47ae3-b293-47f0-ba37-a60947e70a06#center)

### cache에 if 적용하기

지난 챕터에서 caching을 사용해서 dependency 설치 시간을 감소했다. 하지만 더 좋은 방법은 이미 dependency를 설치하는 캐쉬 데이터가 있다면 해당 step을 진행하지 않는 게 더 좋지 않을까?

그래서 다음과 같은 코드를 추가하자.

```yml
  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
    - name: Cache dependencies
      id: cache
      uses: actions/cache@v3
      with:
        path: ~/.npm
        key: deps-node-modules-${{ hashFiles('**/package-lock.json') }}
    
    - name: Install dependencies
    # 코드 추가
      if: steps.cache.outputs.cache-hit != 'true' 
      run: npm ci 
    #
```

실행하면 다음과 같이 Install dependencies가 실행되지 않은 걸 알 수 있다.

![image](https://github.com/JeHa00/image/assets/78094972/ea05fe5a-a6f6-4681-b398-82c3e18f3cbb#center)

### continue-on-error 적용하기  

다음으로 `continue-on-error` field에 대해 알아보자. 이를 test job에 추가한다. 그러면 test 단계에서 error가 발생해도 다음 단계인 build job이 실행된다.  

```yml
name: continue
on:
  push:
    branches:
      - main
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
    ...

  test:
    runs-on: ubuntu-latest
    steps:
      - name: Get code
        ...
      - name: Test code
        # 코드 추가
        continue-on-error: true  
        #
        id: run-tests 
        run: npm run test
      - name: Upload test report
        ...

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Get code
        uses: actions/checkout@v3
      - name: Cache dependencies
        id: cache
        uses: actions/cache@v3
        with:
          path: ~/.npm
          key: deps-node-modules-${{ hashFiles('**/package-lock.json') }}
      - name: Install dependencies
        if: steps.cache.outputs.cache-hit != 'true'
        run: npm ci
        ...

  deploy:
    ...
  report:
    ...
```

- `continue-on-error`가 추가되지 않고 test에서 에러가 발생할 경우 이후 작업은 다 생략된다.

    ![image](https://github.com/JeHa00/image/assets/78094972/6d4c8c24-9dbb-430a-850d-b0f0cb1586bb)

- `continue-on-error`가 추가되고, test에서 에러가 발생할 경우, 이후 작업은 진행된다.  

    ![image](https://github.com/JeHa00/image/assets/78094972/31088f61-8413-48df-9a80-30a08ea37f6a)

&nbsp;

## 1.2 Matrix를 사용해서 Job 실행하기  

---

### Github actions에서 Matrix란?

이번에는 Matrix에 대해 알아보자.

Matrix는 runs-on filed로 지정한 운영체제가 만약 여러 종류이고, programming language의 여러 버전을 이라면 이 모든 경우의 수를 모두 workflow를 진행하고자 할 때 사용되는 방식이다.

새롭게 `matrix.yml`을 생성하여 다음 코드를 작성해보자.

```yml
name: Matrix Demo
on: push
jobs:

# matrix 미 사용 시 build job
  build: 
    runs-on: ubuntu-latest
    steps:
      - name: Get code
        uses: actions/checkout@v3
      - name: Install NodeJS
        uses: actions/setup-node@v3
        with:
          node-version: 14
      - name: Install Dependencies
        run: npm ci
      - name: Build project
        run: npm run build 
  
# matrix 사용 시 build job
  build:
    # 추가된 코드
    strategy:
      matrix:
        version: [12, 14, 16]
        os: [ubuntu-latest, windows-latest] 
    runs-on: ${{ matrix.os }} 
    #

    steps:
      - name: Get code
        uses: actions/checkout@v3
      - name: Install NodeJS
        uses: actions/setup-node@v3
        with:
          node-version: 14
          node-version: ${{ matrix.version }}
      - name: Install Dependencies
        run: npm ci
      - name: Build project
        run: npm run build 
```

matrix가 추가된 workflow를 실행하면 다음과 같은 결과를 확인할 수 있다.

- job이 진행되는 걸 보면 `matrix: build` 를 확인할 수 있다.
- matrix로 진행되는 job들은 병렬적으로 진행된다.  
- `continue-on-error` 가 없기 때문에 선행된 job이 실패해서 이후 job이 canceled 되었다.  

{{<figure src="https://github.com/JeHa00/image/assets/78094972/ed4e80df-c888-4e65-8076-a159a778878c#center" width="80%">}}

하지만 `continue-on-error`를 job level에서 추가하면 다음과 같이 error가 발생되도 진행되는 걸 알 수 있다.

{{<figure src="https://github.com/JeHa00/image/assets/78094972/33f7efa8-2292-432e-a22d-f563ab755385#center" width="80%">}}

### matrix의 include와 exclude  

matrix는 입력된 key에 대한 모든 조합을 실행한다. 그런데, 이 조합들 중에서 어떤 경우의 수는 배제하면 좋겠고, 어떤 경우의 수는 key에 포함되지 않지만 추가해서 수행되기를 원할 경우 어떻게 해야할까?

이럴 때는 `include`와 `exclude`를 사용한다. 아래와 같이 코드를 작성한다.

```yml
name: Matrix Demo
on: push
jobs:
  build:
    continue-on-error: true  
    strategy:
      matrix:
        version: [12, 14, 16]
        os: [ubuntu-latest, windows-latest] 
        
        # 추가된 코드
        include: 
          - version: 18
            os: ubuntu-latest
        exclude:
          - version: 12
            os: windows-latest  
        #

    runs-on: ${{ matrix.os }} 
    steps:
      - name: Get code
        uses: actions/checkout@v3
      - name: Install NodeJS
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.version }}
      - name: Install Dependencies
        run: npm ci
      - name: Build project
        run: npm run build 
```

실행된 결과는 다음 이미지와 같다.

이전 이미지를 보면 node 12 version과 windows-latest 인 조합일 때 build 작업을 수행했지만, exclude로 입력하고 나서는 수행되지 않는 걸 확인할 수 있다.
그리고, include로 추가한 version 18과  windows-latest 조합이 수행된 걸 확인할 수 있다. version 18과 ubuntu-latest는 include에 포함되어 있지않고,
matrix.version에 18이 없기 때문에 수행되지 않을 걸 확인할 수 있다.

{{<figure src="https://github.com/JeHa00/image/assets/78094972/229f5645-802a-4b66-9266-01c6e8fb281b#center" width="50%">}}

&nbsp;

## 1.3 workflow 재 사용하기  

### workflow_call 이용하기  

이번 장에서는 A, B workflow가 존재할 때 한 workflow에서 다른 workflow를 가져와 사용하는 방식을 알아보고자 한다.  

예를 들어 workflow reusable.yml 과 use-reuse.yml이 있다. use-reuse.yml에서 reusable.yml을 호출해서 사용할려고 한다.  

그렇다면 reusable.yml의 on field를 다음과 같이 작성해야 한다.

```yml
# reusable.yml  

name: Reusable Deploy
on: workflow_call
jobs:
  deploy:
    runs-on: ubuntu-latest 
    steps:
      - name: Output information
        run: echo "Deploying & uploading..."
```

`workflow_call`은 workflow_dispatch와 달리 workflow가 호출되면 실행되는 event trigger다.  

그리고 reusable.yml을 호출하여 사용할 use-reuse.yml에 아래 코드를 추가한다.

```yml
# use-reuse.yml
jobs:
  build:
    ...
    
  deploy:
    needs: build
    uses: ./.github/workflows/reusable.yml  
```

위 코드를 추가하여 실행한 결과 다음과 같이 `deploy / deploy`를 확인할 수 있다.

reusable.yml의 job name이 deploy이고, 이 reusable.yml을 호출한 job이 use-reuse.yml의 deploy이기 때문이다.

{{<figure src="https://github.com/JeHa00/image/assets/78094972/d541c849-cd38-4179-ad3e-748c0115b447#center" width="80%">}}

### workflow_call 시 artifacts file name 건네주기  

workflow를 호출할 때, 단순히 호출하는 것을 넘어서 특정 값을 전달하여 호출된 workflow가 사용하도록 할 수 있다.

```yml
# reusable.yml  
name: Reusable Deploy
on: 
  workflow_call:
    inputs: 
      artifact-name:
        description: The name of the deployable artifact files
        required: false
        default: dist 
        type: string 
jobs:
  deploy:
    runs-on: ubuntu-latest 
    steps:
      - name: Get code
        uses: actions/download-artifact@v3 
        with:
          name: ${{ inputs.artifact-name }}
      - name: List files
        run: ls
      - name: Output information
        run: echo "Deploying & uploading..."

# use-reuse.yml
name: Demo
on:
  push:
    branches:
      - main
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      ...
      - name: Upload test report
        uses: actions/upload-artifact@v3
        with:
          name: dist-files
          path: dist
  deploy:
    needs: build
    uses: ./.github/workflows/reusable.yml  
    # 추가된 코드
    with:
      artifact-name: dist-files  
    #

  report:
    needs: [lint, deploy]
    if: failure()
    runs-on: ubuntu-latest
    steps:
        - name: Output information
          run: |
            echo "Something went wrong"
            echo "${{github}}"


```

- `required`:  false이면 workflow는 value를 제공하지 않아도 호출되는 게 가능하다는 의미다.  
- `default`: artifact-name의 default 값이 dist라는 의미  
- `type`: 입력되는 타입은 string 문자열이라는 의미  

`${{inputs.artifact-name}}`으로 해당 값을 가져와서 artifact의 name으로 지정한다. 

&nbsp;

---

# 2. Github actions with Docker container  

---

### Docker container 안에서 github action을 실행하는 이유

github actions는 여러 개의 job을 가질 수 있는데, 하나의 job마다 실행 환경 runners를 입력해야만 한다. 이는 우분투, 윈도우, 리눅스, MacOS가 있지만 이는 이미 만들어져있는데 기성품 같은 것이기 때문에 우리가 모든 환경을 조절할 수 없다. 그래서 docker container 너를 각 job의 실행 머신 os로 지정한다면 docker image를 정의한 작성자가 훨씬 더 많은 걸 조절할 수 있기 때문에, docker container를 runners로 지정하는 걸 추천한다.  

기존에 제공하는 os를 사용하면 이에 따라 여러 가지 세팅들을 제공하기 때문에 충분할 수 있으나, 그렇지 않은 상황에서는 container를 추천한다.  

그리고 container를 사용하면 container를 재사용할 수 있다.  

&nbsp;

---

# Reference

- [github actions - steps context](https://docs.github.com/en/actions/learn-github-actions/contexts#steps-context)
- [github actions -]()
