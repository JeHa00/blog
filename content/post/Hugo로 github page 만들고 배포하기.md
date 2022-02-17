---
title: "Window에서 Hugo로 Github page 만들고 배포하기"
date: 2022-02-17T21:49:13+09:00
draft: false
summary: SSG의 한 종류 'Hugo'와 'git remote, git submodule'로 Github page를 만든다. 그 후, 컨텐츠 생성과 업로드를 한다. 마지막으로 utterances로 댓글 기능 추가, deploy.sh를 사용하여 업로드하는 방법을 다룬다.
tags: ["hugo", "github", "blog", "submodule"]
categories: ["개발-dev"]
---

# Window에서 Hugo로 Github page 만들고 배포하기

window 10 환경에서 Hugo 라는 SSG의 한 종류를 사용해서 `<user-id>.github.io` 주소의 github page를 만들고 배포한 후, contents를 업로드하는 것까지 내용을 다룬다. 이를 위해 총 5단계를 거쳐서 진행한다.

> 1. Static Site Generator 로 Hugo를 선택한 이유
> 2. Github blog 만들기 위한 local 환경 조성
> 3. 2개의 github repo 와 local 연결
> 4. Github page에 theme 적용
> 5. Contents 생성과 업로드, utterances로 댓글 기능 추가, deploy.sh로 자동화

위 과정들에서 필요한 개념들, 부딪혔던 error 및 해결책도 각 단계 마지막 부분에 작성했다.

> 동일한 주제로 다룬 블로그들을 보면 3단계와 4단계의 순서를 바꿔서 진행한다. 4단계 진행 후, 3단계를 진행해도 무방하다. 다만, 나는 그 과정에서 헷갈린 부분이 있어서 마지막에 theme 적용을 하기로 선택했다.

## 1. Static Site Generator 로 Hugo를 선택한 이유

`Github` page를 만들 때 SSG의 종류들로 `Jekyll`, `Hexo`, `Hugo` 가 많이 언급된다. 그러면 SSG란 무엇인가?? `정적 페이지(Static Site)`란 `HTML`,`CSS`,`JS`를 미리 올려서 서버가 바뀌지 않는 HTML page를 보여주는 것을 말한다. 이 정적 페이지를 보다 간편하게 만들어주는 것이 `SSG(Static Site Generator)`다. 이와 반대로 동적 페이지는 client에 반응하여 HTML page를 동적으로 만들어진 페이지를 말한다.

hugo를 선택하기에 앞서 각 SSG의 특징들에 대해 알아야 하기 때문에, `Jekyll`, `Hexo`, `Hugo`의 각 특징들은 다음과 같다.

```
 Jekyll
  -루비 기반
  -현재 가장 인기 있음(깃헙 별 수 제일 많음)
  -한글 레퍼런스도 제일 많음
  -느리다는 제보가 많음(몇 십개의 포스팅 뿐인데도 빌드 하는데 5분씩 걸린다고)
  -윈도우 공식 지원 안됨

Hexo
  -자바스크립트(Node.js) 기반
  -한글 레퍼런스 꽤 많음
  -메인 개발자가 손을 놓은 듯
  -개발자가 중국인? 이라 구글링하면 중국어 글이 많이 나옴

Hugo
  -Golang 기반
  -빌드 빠름
  -문서화 잘돼있음
  -깃헙 별 수가 헥소보다 많음
  -한글 레퍼런스가 거의 없음

출처: http://tadakichi.tistory.com/188
```

그래서 4가지 이유로 Hugo를 선택했다.

> - 남들이 안해본 걸 해보자.
> - 한글 레퍼런스가 거의 없기 때문에, 내가 기여할 수 있는 부분이 다른 것보다 있을 것이다.
> - 내가 원하는 디자인 대부분이 Hugo였다.
> - 앞으로 계속해서 기술 블로그를 작성할 것이기 때문에, 빠른 빌드를 원했다.

두 번째 이유의 경우, 맨 처음에 Hugo의 존재를 모른 상태에서 jekyll이 많이 사용된다고 하여, 처음에 jekyll로 github page를 만들었다. 그래서 Hugo와 비교했을 때, 훨씬 Jekyll이 한글 레퍼런스가 많다는 걸 알았다.

## 2. Github page 만들기 위한 local 환경 조성

> - git을 설치한다. 그리고, github 에 가입한다.
> - Visual Studio Code (VSC), Atom 같은 에디터를 설치한다.
> - window 환경에 Hugo를 설치한다.

첫 번째, github 가입은 [Github](https://github.com/) 에 들어가서 오른쪽 상단에 있는 `Sign up`을 클릭하여 진행한다. 그러면 최종적으로 `https://github.com/user-name/` 을 갖는다. 이것이 github blog다. github page는 `[user-name].github.io` 로 주소를 갖는다.

두 번째, 나는 visual studio code를 사용한다. [visual studio code](https://code.visualstudio.com/) 여기에 들어가 설치한다.

세 번째, window 환경에 hugo를 설치한다. [window에서 Hugo 설치하기](https://gohugo.io/getting-started/installing#windows) 이 영상 하나 따라하면 쉽다. 하지만, 글로 보고 싶은 분들을 위해 작성한다.

> - [hugo 다운로드](https://gohugo.io/getting-started/installing#windows) 로 들어가서 아래로 scroll을 내리면 window 버전을 다운받아 `C:\Hugo\bin` 디렉토리를 생성해서 다운받은 압축 파일을 해제
>
> - 어느 위치에서나 Hugo가 실행할 수 있도록 `윈도우 검색`으로 `시스템 환경 변수 편집`을 검색하여 들어간다. `고급` 탭의 `환경 변수` 로 들어간다. `사용자 변수` 란의 `path`를 클릭 후, `편집` 을 클릭한다. `새로 만들기`를 클릭하여 `C:\Hugo\bin` 경로를 추가한다.
> - cmd에 `echo %PATH%` 를 입력하여 추가한 경로가 있는지 확인한다.
> - 해제한 압축 파일에서 hugo를 실행하여 설치 후, cmd에 `hugo version` 으로 동작 환인한다.

## 3. 2개의 github repo 와 local 연결

> - github에 2개의 repository를 만든다.

## 4. Github page에 theme 적용

## 5. Contents 생성과 업로드, utterances로 댓글 기능 추가, deploy.sh로 자동화

## Reference

- [Jekyll, Hexo, Hugo 차이점 설명](http://tadakichi.tistory.com/188)

- [Hugo로 Github.io 블로그 만들기](https://github.com/Integerous/Integerous.github.io)

- [How to Set Up a Hugo Site on Github Pages - with Git Submodules!](https://www.adamormsby.com/posts/000/how-to-set-up-a-hugo-site-on-github-pages-with-submodules/)

- ***

- **We'll be using `yml/yaml` format for all examples down below, I recommend using `yml` over `toml` as it is easier to read.**
- You can find any [YML to TOML](https://www.google.com/search?q=yml+to+toml) converters if necessary.
