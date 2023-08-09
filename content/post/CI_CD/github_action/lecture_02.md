---
title: "CI/CD를 위한 github action basic study 01"
date: 2023-08-06T22:13:42+09:00
draft: true
summary: Activity type, filters, github actions caching을 학습해본다.  
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

## 1. Activity type and Filters 학습하기  

---

지난 학습을 통해서 `on: [push, workflow_dispatch]`로 events를 정의해놓으면 모든 push마다 workflow가 진행된다는 걸 알게 되었다.

그런데 이를 보다 더 정밀하게 event를 정의하고 싶다면 어떻게 해야할까?

그럴 때는 Activity type 또는 filter를 적용해야 한다. event의 Activity types 과 filters에 대해 알아보자.  

Activity type에는 예를 들어서 event가 pull_request라면 activity type에는 opened, closed, edited 가 존재한다.  

Filters는 push event라고 한다면 target branch에 대해 조건을 걸 수 있다.  

- [github actions events](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#pull_request)를 보면 여러 event와 activity type가 나와있다. 해당 event를 사용할 때는 `Note`를 반드시 읽어보도록 하자.  

#

### types를 입력하는 3가지 방식과 types가 존재할 때 여러 event를 입력하는 방식  

```yml
name: Events Demo 1
on:
    pull_request:
        # types를 입력하는 3가지 방식: 반드시 types 이어야 한다. 
        types: [opened, edited]
        types: opened
        types:
            - opened
    workflow_dispatch:
jobs:
    deploy:
        runs-on: ubuntu-latest
        steps:
            ...
```

그러면 아래와 같이 작성했다고 하면 수동적으로 실행했을 때와 PR을 open했을 때 workflow가 진행된다.  

```yml
name: Events Demo 1
on:
    pull_request:
        types:
            - opened
    workflow_dispatch:
jobs:
    deploy:
        runs-on: ubuntu-latest
        steps:
            ...
```

아래 이미지를 통해 실행되는 걸 확인할 수 있다.

![image](https://github.com/JeHa00/image/assets/78094972/4594b9e1-9536-482f-99a2-ff3cd661ad55)

하지만 `types: closed` 인 경우에는 PR이 닫혀지고 나서 실행된다.  

이처럼 types를 무엇으로 지정하냐에 따라서 workflow 진행 유무 기준을 구체적으로 정할 수 있다.

#

### push에 대한 filter 적용

push를 할 때 내가 원하는 branch와 원하는 경로에 들어올 때만 실행하는 방법도 있다.  

push keyword에 `branches` keyword를 입력해보자.  

```yml
name: Events Demo 1 
on:
    push:
        branches:
            - main
            - 'dev-*'
            - 'feat/**'  
```

위 workflow의 의미는 다음과 같다.

- main branch와
- `dev-`로 시작하는 branch와
- `feat/` 로 시작하는 branch
위 3가지 branch에 push를 할 경우에만 workflow를 실행한다.

만약 `feature/` 로 시작한다면 실행되지 않는다.  

#

### workflow 경로에 따라 실행하기  

```yml
name: Events Demo 1
on: 
    push:
        paths-ignore:
            - '.github/workflows/*'
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Output event data
        run: echo "${{toJSON(github.event)}}"
      - name: Get code
        uses: actions/checkout@v3 
      - name: Install dependencies
        run: npm ci
      - name: Test code
        run: npm run test
      - name: Build code 
        run: npm run build 
      - name: Deploy project
        run: echo "Deploying..."
```

이 상황에서 만약 main branch로 push를 할 경우 Events Demo 1은 실행되지 않는다.  

#

### Forked Pull Request Workflows

만약 누군가 repository를 fork하고 main branch에 push할 경우, forked repository가 아닌 원래 repository에서 경고 표시로 workflow 가 뜨면서 진행되지 않는다.

이렇게 된 이유는 모든 사람들이 repository를 fork할 수 있는데, 악의적으로 workflow를 실행시켜서 cost를 과도하게 유발시키 수 있기 때문에, forked repository에서 main으로 push한 경우에는 바로 workflow가 진행되지 않는다.  

#

### 진행 중인 workflow 취소하기  

기본적으로 workflow가 진행 중이다가 실패하면 workflow는 당연히 취소된다.  

또한, 수동적으로 직접 workflow를 취소할 수도 있다.

아래 이미지의 오른쪽 위를 보면 `Cancel workflow` button을 통해서 중지할 수 있다.  

![image](https://github.com/JeHa00/image/assets/78094972/891aa483-1cc9-4ad4-b90e-c61f33e334f8)  

#

### workflow skip 하기  

참고할 공식 문서는 [여기](https://docs.github.com/en/actions/managing-workflow-runs/skipping-workflow-runs) 다.

위 문서에 따르면 commit message를 작성할 때 다음 메세지를 작성하면 된다.

- `[skip ci]`
- `[ci skip]`
- `[no ci]`
- `[skip actions]`
- `[actions skip]`  

그러면 main branch에 push는 했지만 github actions는 실행되지 않는 걸 확인할 수 있다.  

&nbsp;

# 2. Job Data & Outputs

---

## What is Job Artifacts  

Job Artifacts란 Job에 의해 생긴 결과물로 예를 들어서 log file이나 website files 등이 있다.

이 Job Artifacts를 Github UI나 REST API를 사용하여 수동적으로 다운받아 사용하는 방식이 있고 Job을 통해 Action으로 Job Artifacts를 다운받고 사용하는 방식이 있다.

예를 들어서 `npm run build`로 실행하여 생긴 dist folder는 build job에 의해서 생긴 artifacts다.  

그러면 이 Artifacts를 upload 및 download 하도록 해보자.

이 작업이 필요한 이유는 **_각 job 단계에서의 runner machine은 다르기 때문에 같은 runner machine 정의를 가진다고 해도 각 runner machine에서는 그 파일을 가지고 있지 않는다. 그래서 job이 build -> deploy 순서로 존재한다고 할 때 build 단계에서 업로드한 파일을 deploy 단계에서 가지기 위해서는 '다운로드'를 진행해야 한다. 그리고 이를 위해서 업로딩이 필요하다._**

## Job artifacts를 업로딩하기  

이 Actions를 통해 생긴 결과물인 Job artifacts를 사용할 수 있다. workflow를 다음과 같이 작성한다.

```yml
name: Deployment Exercise 2
on: 
  push:
    branches:
      - main  
jobs:
  test:
    runs-on: ubuntu-latest
    ...
  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Get code
        uses: actions/checkout@v3
      - name: Install dependencies
        run: npm ci
      - name: Build website  
        run: npm run build

        # 이 부분을 추가
      - name: Upload artifacts    
        uses: actions/upload-artifact@v3  
        with:
          name: dist-files  
          path: |
            dist
            package.json  

# package.json은 경로를 여러 개 입력하는 방법을 보여주기 위해 입력한 예시

  deploy:
    needs: build
    ...
```

아래 이미지처럼 Artifacts가 생성된다. 이를 클릭하면 수동으로 다운 받을 수 있다.  

![image](https://github.com/JeHa00/image/assets/78094972/79c4e698-656a-4688-af67-38d14ff18d41)

자동으로 다운받아지기 위해서는 `deploy` job 단계에서 다운받는 step을 추가한다.  

```yml
...
  build:
    needs: test
    ...
        # 이 부분을 추가
      - name: Upload artifacts    
        uses: actions/upload-artifact@v3  
        with:
          name: dist-files  
        # 이 부분은 수정한다.
        #   path: |
        #     dist
        #     package.json  
          path: dist

    deploy:
        needs: build
        runs-on: ubuntu-latest
        steps:
            # 추가한 단계
            - name: Get build artifacts 
              uses: actions/download-artifact@v3 
              with:
                name: dist-files  
            #
            - name: Output contents
              run: ls  
            - name: Deploy
              run: echo "Deploying..."
```

아래 이미지를 보면 Output contents step 단계에서 ls를 실행하여 파일 목록을 확인했다.

![image](https://github.com/JeHa00/image/assets/78094972/961cdee2-31d4-42e2-8e28-e61dfef6e182)

다운로드 시 Github에서 제공하는 Action을 사용하여 할 수 있다. 이 때 설정을 추가해야 한다. 업로드 했던 파일의 이름을 `name` key로 value를 입력한다. 그러면 deploy job이 실행되는 folder 안에 dist-files 내부 파일들이 바로 담겨진다.  

#

## Artifacts와 Job Outputs

'Artifacts'는 log file이나 website files로 사용된다면 'Job Outputs'는 순차적으로 일어나는 다음 job 들에 사용되는 값들을 말한다. 예를 들어서 이전 step 단계에서 생성된 파일의 이름을 말한다. 그러면 Job Outputs를 확인해보자.

아래 yml은 Job Output을 이용하는 workflow다.  

```yml
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
  
  build:
    needs: test
    runs-on: ubuntu-latest
    # 추가한 key: outputs
    outputs:
      script-file:  ${{ steps.publish.outputs.file-name }}
    #
    steps:
    ...
      # 추가한 step
      - name: Publish JS filename  
        id: publish
        run: find dist/assets/*.js -type f -execdir echo 'file-name={}' >> $GITHUB_OUTPUT ';'
      #
    ...
  
  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
        ...
      # 추가한 steps  
      - name: Needs 
        run: echo "${{ needs }}"
      - name: Build
        run: echo "${{ needs.build }}"
      - name: Output 
        run: echo "${{ needs.build.outputs}}"
      - name: Output filename 
        run: echo "${{ needs.build.outputs.script-file }}"
      - name: Deploy
        run: echo "Deploying..."
      #
```

### build job의 Publish JS filename step  

- `steps`라는 context name은 아래 공식문서에서와 같은 의미를 가지고 있어서 steps를 통해 해당 step의 정보를 얻을 수 있다.  
  - **_Information about the steps that have been run in the current job._**  

- 위 단계에서 아래 command를 실행했다.  
- `find dist/assets/*.js -type f -execdir echo 'file-name={}' >> $GITHUB_OUTPUT ';'`
- 위 명령어의 의미는 다음과 같다.
  - `dist/assets/*.js`인 데이터를 찾는데, 이 데이터의 타입은 `f`로 파일이고,
  - `execdir`을 사용해서 해당 파일이 있는 경로에서 `echo` 커맨드를 실행한다.
  - `file-name` key에 해당하는 value를 $GITHUB_OUTPUT에 저장한다.  
  - $GITHUB_OUTPUT은 `outputs`에 선언된 key 값에 연결된다. `file-name` key의 value에 저장된다.  
  - `script-file`이 `file-name`을 가리키기 때문에 `script-file`에 저장된다.  

#

## 다른 job의 output 이용하기  

deploy job을 보면 여러 개의 환경 변수들을 출력하고 있다

- needs, needs.build, needs.build.outputs, needs.build.outputs.script-file

아래 image를 보면 해당 step name 별로 출력된 결과를 볼 수 있다.

![image](https://github.com/JeHa00/image/assets/78094972/43799a52-5703-42c8-b488-67acd3835d6f)

`needs.build.outputs.script-file`로 적어뒀던 Output filename step만 다른 값을 출력한 걸 확인할 수 있다.  

이처럼 github output에 값을 담아두면 다른 job에서도 꺼내서 사용할 수 있는 걸 알 수 있다.  

그리고, **_Artifacts와 outputs의 차이_** 를 확실히 알게 되었다. Artifacts는 외부에서 다운받을 수 있는 결과물이지만, outputs는 단순한 값이라는 걸 확인했다.  

#

## cache를 사용하여 Dependency 설치 시간을 단축시키기  

여태 실습한 workflow는 간단하기 때문에 1분 내외지만 복잡한 workflow를 하게 되면서 시간이 많이 걸리게 된다.

이번 섹션에서는 이 workflow 실행 시간을 줄이는 것에 초점을 둔다.

실행 시간을 줄이는 방법 중 하나는 각 Step 마다 반복되는 부분을 빠르게 진행하는 것이다.  

반복되는 부분을 확인해보자.

- Test job: Get code => Install Dependencies => Test App

- Build job: Get code => Install Dependencies => Build Project  

여러 step 중에서 `Get code` 부분이 반복된다. 하지만 이 부분은 '1s' 밖에 걸리지 않기 때문에 이는 많이 느리지 않는다.

또한, `Install dependencies` 부분이 반복된다. 이 부분은 '12s'나 걸리기 때문에 많이 느리다.

그렇다면 dependencies 를 설치할 때 매 step마다 달라지는가? 얼마나 달라지는가?

dependencies는 자주 바뀌지 않는다. 그렇기 때문에 이 부분을 공통적으로 사용해도 문제가 없다. 이 부분을 단축한다면 전체 workflow 실행 시간이 줄어들 것이다. dependencies를 설치하는 부분의 시간을 'cache' 를 사용하여 단축해보자.  

각 job마다 실행되는 runner (os)가 다른데 어떻게 cache를 사용할 수 있을까?

[github actions - cache](https://github.com/actions/cache) 를 사용하여 해당 job이 끝나면 지정한 폴더 또는 파일을 저장하여 다른 job이 실행될 때 이를 꺼내서 사용하는 방식으로 cache가 저장 및 사용된다.

코드는 다음과 같다.

```yml
jobs:
  test:
    runs-on: ubuntu-latest
    steps: 
      - name: Get code
        uses: actions/checkout@v3
      # 추가된 코드
      - name: Cache dependencies
        uses: actions/cache@v3  
        with:
          path: ~/.npm
          key: deps-node-modules-${{ hashFiles('**/package-lock.json') }}  
      #
      - name: Install dependencies
        run: npm ci
      - name: Lint code
        run: npm run lint  
      - name: Test code
        run: npm run test
```

위 추가된 코드에 대해 설명하자면

- `~/.npm`은 npm 에서 생성한 자체 cache folder인데, 이 folder를 actions를 사용하여 다른 workflow 또는 job 공용으로 사용하기 위해 github server에 있는 github cloud에 저장한다.

- key는 미래에 이 cache data를 가져올 때 필요하다.
- hashFiles를 사용한 이유는 혹시 dependency가 업데이트되었을 때, 중복되면 않되기 때문에 고유 식별자 역할을 하기 위해 사용한다.
- `**/package-lock.json` 인 이유는 경로 상관없이 package-lock.json을 저장할 것이기 때문이다.  

test job에 작성된 `Cache dependencies`를 다음 단계 job인 build에도 추가한다.

```yml
  build:
    needs: test
    runs-on: ubuntu-latest
    outputs:
      script-file:  ${{ steps.publish.outputs.file-name }}
    steps:
      - name: Get code
        uses: actions/checkout@v3
      # add code
      - name: Cache dependencies
        uses: actions/cache@v3  
        with:
          path: ~/.npm
          key: deps-node-modules-${{ hashFiles('**/package-lock.json') }}  
      #
      - name: Install dependencies
        run: npm ci
    ...
```

그러면 실행 결과를 확인해보자. 아래 이미지는 `test` job에서의 이미지다.  

![image](https://github.com/JeHa00/image/assets/78094972/5687fcc8-a0aa-462f-b043-f476da3066e3)

Cache dependencies 단계에서는 해당 key를 발견할 수 없다고 떴지만 Post Cache dependencies 단계에서 해당 key 값으로 cache에 저장된 걸 알 수 있다.  

위 이미지에서는 안보이지만 test job에서 Install dependencies 단계에서는 '11s'가 걸렸다.

그러면 `test` 다음 job인 `build` job일 때 설치 시간, Cache dependencies를 확인해보자.

![image](https://github.com/JeHa00/image/assets/78094972/a3e2a328-e38a-405b-ae99-8f2468a28f49)

- Cache dependencies 단계에서 다음 메세지를 확인할 수 있다.

  - `cache restored from key: deps-node-modules-...`

- test 단계에서는 해당 key를 발견할 수 없다고 떴지만, 그 다음 build 단계에서는 해당 key로 cache에서 꺼낸 걸 알 수 있다.  

- 그리고 그 다음 step인 Install dependencies 단계의 설치 시간은 '5s'로 줄어든 걸 확인할 수 있다.  

다른 workflow에도 똑같은 Install dependencies step이 존재한다면 실행 시간이 짧아진 거를 확인할 수 있을 것이다.  

#

## Summary

### Artifacts

- **Job** 실행에 의한 결과물로 공유되거나 분석될 수 있다.  
- Artifacts 또는 Job artifacts라고 불린다.  
- Github Actions에서 제공하는 action을 통해 uploading 과 downloading을 제공한다.  

### Outputs

- **Steps** 실행에 의해 생성된 결과물이고 단순한 값들이며, Job 간에 공유될 수 있다.
- 'steps' context를 통해서 Jobs들은 이를 사용 및 공유할 수 있다.  
- `echo '{output-name}={output-value}' >> $GITHUB_OUTPUT` 명령어로 key-value로 저장한다.  
- 위 command를 사용하는 step의 경우 `id`를 key를 입력해야 한다.  
- 다른 job에서 이를 사용할 경우, `steps.{step_id}.outputs.{key}`로 사용가능하다.  

    ```yml
    build:
        needs: test
        runs-on: ubuntu-latest
        outputs:
            script-file:  ${{ steps.publish.outputs.file-name }}
        steps:
        - name: Publish JS filename  
            id: publish
            run: find dist/assets/*.js -type f -execdir echo 'file-name={}' >> $GITHUB_OUTPUT ';'
    ```

### caching

- 반복되고, 느린 step들의 속도를 증가시킬 수 있다.  
- 전형적으로 사용되는 경우는 dependency 설치지만, 어느 파일과 폴더 모두 가능하다.  
- ❗️ 하지만 artifacts를 caching

&nbsp;

# Github actions with Docker container  

---

### Docker container 안에서 github action을 실행하는 이유

github actions는 여러 개의 job을 가질 수 있는데, 하나의 job마다 실행 환경 runners를 입력해야만 한다. 이는 우분투, 윈도우, 리눅스, MacOS가 있지만 이는 이미 만들어져있는데 기성품 같은 것이기 때문에 우리가 모든 환경을 조절할 수 없다. 그래서 docker container 너를 각 job의 실행 머신 os로 지정한다면 docker image를 정의한 작성자가 훨씬 더 많은 걸 조절할 수 있기 때문에, docker container를 runners로 지정하는 걸 추천한다.  

기존에 제공하는 os를 사용하면 이에 따라 여러 가지 세팅들을 제공하기 때문에 충분할 수 있으나, 그렇지 않은 상황에서는 container를 추천한다.  

그리고 container를 사용하면 container를 재사용할 수 있다.  

---

# Reference

- [[udemy lecture] Github Actions - The Complete Guide](https://www.udemy.com/course/github-actions-the-complete-guide/)
- [Manage workflow runs - skip workflow runs](https://docs.github.com/en/actions/managing-workflow-runs/skipping-workflow-runs)  
- [github actions - context](https://docs.github.com/en/actions/learn-github-actions/contexts)
- [Skipping workflow runs](https://docs.github.com/en/actions/managing-workflow-runs/skipping-workflow-runs)  
- [github actions events](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#pull_request)  
- [github actions cache](https://github.com/actions/cache)
