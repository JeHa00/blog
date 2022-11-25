---
title: "Project: svg icon이 인식되지 않는 문제와 js import 관련 문제"
date: 2022-11-14T16:21:20+09:00
draft: true
summary: DOM을 js로 직접 생성하는 과정에서 svg icon을 .setAttribute()로 path tag에 속성 d를 추가해도 icon이 인식되지 않는 문제점과 js가 import되지 않는 문제점에 대해서 각 문제점에 대한 해결책을 공유해본다.
tags: ["svg"]
categories: ["Project"]
---
# Introduction

- 이 repository는 현재 [러닝스푼즈 나노디그리 - django backend 부트캠프](https://learningspoons.com/course/detail/django-backend/)에서 팀 프로젝트를 진행하면서 다음과 같은 내용들에 대해 정리하고자 만들었습니다. 
    - 팀 정책을 이것으로 정한 이유 
    - 개발하면서 부딪힌 문제들에 대한 원인, 해결방안, 해결과정, 그리고 그 이유들

- 이번 포스팅에서는 다음 2가지 문제점에 대해 공유하고자 합니다.
    - dom을 js로 직접 생성하는 과정에서 svg icon에 관하여 부딪힌 문제점
    - js import 관련 문제점


<br>

---

# Svg icon이 인식되지 않는 문제

각 항목에 mouse를 hover 시, 하단에 뜨는 하단 툴바를 만들기 위해서 DOM을 생성하는 과정에 부딪힌 문제점이다. 

우리 프로젝트의 클론 코딩 대상이 되는 사이트는 아이콘을 `<i>` tag보다 `<svg><path d=""></path></svg>` 를 사용하여 삽입한다. 

js를 학습하여 DOM을 생성해보는 과정 중에

그 이후에 sv

svg가 setAttribute로 path d 속성을 추가했지만, 생성되지 않은 문제  -> 결국 i tag로 교체 


svg로 하는 방식을 찾았다.

selectedTag를 `let`으로 선언한다. 

`let`으로 선언한 이유는

aTag 는 selectedTag가 밑에 들어갈 태그다.

aTag.insertAdjacentHTML('beforeend', selectedTag)로 입력한다. 

insertAdjacentHTML method에 대한 설명은 아래 공식문서를 참고한다. 

https://developer.mozilla.org/ko/docs/Web/API/Element/insertAdjacentHTML 

setAttribute로 하면 인식되지 않았지만, html를 통채로 입력하니 가능했다. 



<br>

---

# Js가 import 되지 않는 문제  



외부에서 import 해서 js를 사용하는 경우 - Debug 순서

위와 같은 상황에서 가져온 js가 작동되지 않을 경우

1. 작성한 js를 연결시킨 html의 script tag 안에 작성하여 한 파일로 실행해본다. 

2. 1번으로도 에러가 발생하면 함수 내부 코드를 처음부터 하나 하나 주석처리 하며 실행해본다. 

3. 1번으로 에러가 발생되지 않는다면 import 과정에서 생기는 문제 
- button에 부착된 함수를 가져올 경우, event가 필요한 함수의 경우 type="module"을 작성하면 가져오지 못 한다.
- tag에 부착된 것 없이 가져오면 자동적으로 실행되는 경우에는 type="module"을 작성한다. 


ncaught SyntaxError: Unexpected token 'export' (at make-active.js:176:1)