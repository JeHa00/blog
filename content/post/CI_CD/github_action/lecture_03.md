---
title: "CI/CD를 위한 github action study: workflow와 job 실행 제어하기 & Jobs 와 Docker containers"
date: 2023-08-11T18:29:20+09:00
draft: true
summary: 
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

&nbsp;

---

# 2.Github actions with Docker container  

---

### Docker container 안에서 github action을 실행하는 이유

github actions는 여러 개의 job을 가질 수 있는데, 하나의 job마다 실행 환경 runners를 입력해야만 한다. 이는 우분투, 윈도우, 리눅스, MacOS가 있지만 이는 이미 만들어져있는데 기성품 같은 것이기 때문에 우리가 모든 환경을 조절할 수 없다. 그래서 docker container 너를 각 job의 실행 머신 os로 지정한다면 docker image를 정의한 작성자가 훨씬 더 많은 걸 조절할 수 있기 때문에, docker container를 runners로 지정하는 걸 추천한다.  

기존에 제공하는 os를 사용하면 이에 따라 여러 가지 세팅들을 제공하기 때문에 충분할 수 있으나, 그렇지 않은 상황에서는 container를 추천한다.  

그리고 container를 사용하면 container를 재사용할 수 있다.  
