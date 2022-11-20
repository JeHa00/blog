---
title: "Project: svg icon이 인식되지 않는 문제와 js import 관련 문제"
date: 2022-11-13T16:21:20+09:00
draft: true
summary: DOM을 js로 직접 생성하는 과정에서 svg icon을 .setAttribute()로 path tag에 속성 d를 추가해도 icon이 인식되지 않는 문제점과 js가 import되지 않는 문제점에 대해서 각 문제점에 대한 해결책을 공유해본다.
tags: ["svg"]
categories: ["Project"]
---
# Introduction

- 이 repository는 현재 [러닝스푼즈 나노디그리 - django backend 부트캠프](https://learningspoons.com/course/detail/django-backend/)에서 팀 프로젝트를 진행하면서 다음과 같은 내용들에 대해 정리하고자 만들었습니다. 
    - 팀 정책을 이것으로 정한 이유 
    - 개발하면서 부딪힌 문제들에 대한 원인, 해결방안, 해결과정, 그리고 그 이유들

- 이번 포스팅에서는 dom을 js로 직접 생성하는 과정에서 svg icon에 관하여 부딪힌 문제점에 대해 공유하고자 합니다. 


<br>

---



svg가 setAttribute로 path d 속성을 추가했지만, 생성되지 않은 문제  -> 결국 i tag로 교체 


svg로 하는 방식을 찾았다.

selectedTag를 `let`으로 선언한다. 

`let`으로 선언한 이유는

aTag 는 selectedTag가 밑에 들어갈 태그다.

aTag.insertAdjacentHTML('beforeend', selectedTag)로 입력한다. 

insertAdjacentHTML method에 대한 설명은 아래 공식문서를 참고한다. 

https://developer.mozilla.org/ko/docs/Web/API/Element/insertAdjacentHTML 

setAttribute로 하면 인식되지 않았지만, html를 통채로 입력하니 가능했다. 