---
title: "Js Module"
date: 2022-11-16T03:13:43+09:00
draft: true
summary: 
tags: [""]
categories: [""]
---


외부에서 import 해서 js를 사용하는 경우 - Debug 순서

위와 같은 상황에서 가져온 js가 작동되지 않을 경우

1. 작성한 js를 연결시킨 html의 script tag 안에 작성하여 한 파일로 실행해본다. 

2. 1번으로도 에러가 발생하면 함수 내부 코드를 처음부터 하나 하나 주석처리 하며 실행해본다. 

3. 1번으로 에러가 발생되지 않는다면 import 과정에서 생기는 문제 
- button에 부착된 함수를 가져올 경우, event가 필요한 함수의 경우 type="module"을 작성하면 가져오지 못 한다.
- tag에 부착된 것 없이 가져오면 자동적으로 실행되는 경우에는 type="module"을 작성한다. 


ncaught SyntaxError: Unexpected token 'export' (at make-active.js:176:1)