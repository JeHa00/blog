---
title: "Svg_problem"
date: 2022-11-13T16:21:20+09:00
draft: true
summary: 
tags: [""]
categories: [""]
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