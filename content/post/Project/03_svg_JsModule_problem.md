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

## 첫 번재 article에만 svg path tag가 다 들어간 상황

관련 함수 코드
- 즐겨찾기 만드는 함수 외에 카테고리 함수, 태그 함수, 삭제 함수 모두 다 발생된 문제이지만, 예로서 즐겨찾기만 가져왔다.

```js
function makeFavoriteInToolBar(parentNode, site) {
    /* 하단 툴바의 즐겨찾기 버튼 Dom을 만드는 함수 
        - 특정 문자열로 className에 할당되는 값들은 클론코딩으로 가져오는 css를 반영하기 위한 것입니다.
    */

    const favoriteButtonContainer       = createNode('span')
    appendTag(parentNode, favoriteButtonContainer)

    const favoriteButton                = createNode('button')
    favoriteButton.className            = site.favorite == false ? 'm11fpiro t1221eea pmdugmx d1mp5exd favorite' : 'm11fpiro t1221eea pmdugmx d1mp5exd favorite active'
    favoriteButton.setAttribute('data-tooltip', site.favorite == false ? '즐겨찾기' : '즐겨찾기 해제')
    appendTag(favoriteButtonContainer, favoriteButton)

    const favoriteIconContainer         = createNode('span')
    favoriteIconContainer.className     = 'i1qqph0t icon'
    appendTag(favoriteButton, favoriteIconContainer)

    let favoriteIconSvgHTML             = `<svg class='favorite-icon-svg' fill="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"aria-hidden="true"></svg>`
    favoriteIconContainer.insertAdjacentHTML('beforeend', favoriteIconSvgHTML)
    const favoriteIconSvg               = getElement('.favorite-icon-svg')

    let favoriteIconPathHTML            = '<path fill-rule="evenodd" clip-rule="evenodd" d="M12 1a1 1 0 0 1 .897.557l2.706 5.484 6.051.88a1 1 0 0 1 .555 1.705l-4.38 4.268 1.034 6.027a1 1 0 0 1-1.45 1.054L12 18.13l-5.413 2.845a1 1 0 0 1-1.45-1.054l1.033-6.027-4.379-4.268a1 1 0 0 1 .555-1.706l6.051-.88 2.706-5.483A1 1 0 0 1 12 1Zm0 3.26L9.958 8.397a1 1 0 0 1-.753.548l-4.567.663 3.305 3.221a1 1 0 0 1 .287.885l-.78 4.548 4.085-2.147a1 1 0 0 1 .93 0l4.085 2.147-.78-4.548a1 1 0 0 1 .287-.885l3.305-3.22-4.567-.664a1 1 0 0 1-.753-.548L12 4.26Z"></path>'
    favoriteIconSvg.insertAdjacentHTML('beforeend', favoriteIconPathHTML)

    changeFavoriteValue(favoriteButton, site)
}
```

위 코드에서 아래 부분이 문제의 원인이다. 개발자 도구로 태그를 타고 들어가 설계한 것과 달리 많은 태그가 들어간 것을 확인했다. 

이 코드를 추가하고 나서 확인하지 못 했던 이유는 그 당시 내 목록에 추가했던 항목이 1개 밖에 없었기 때문이다.

팀원한테 이 문제를 듣고서 해결 시도를 시작했다. 

### 문제

위 함수 실행 결과 아래 첫 번째 항목(article tag) 하단 툴바의 svg tag에 저장된 항목의 갯수만큼 path 태그가 추가되어, 다른 항목들의 하단 툴바 이미지 아이콘이 보이지 않는 문제가 발생했다.

아래 이미지는 두 항목 모두 hover 된 상태이지만, 첫 번째에만 이미지가 뜨고 있다.

![image](https://user-images.githubusercontent.com/78094972/205566677-2b0d0707-94b0-4c0a-a913-b332158d2241.png)

### 원인

전체 document에서 해당되는 첫 번째 tag를 가져오기 때문에 발생했다. 
[Document.querySelector()](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector)의 Note 부분을 확인하니 이 내용을 확인할 수 있었다. 

이 문제는 결제 기능을 완성 후, 배포 과정을 학습하는 과정에서 발견했다. 결제 기능처럼 공식문서를 볼 때 항상 유의사항을 반드시 확인하여, 이 부분까지 다루도록 코드를 작성해야 한다는 걸 다시 느꼈다.

document.querySelector() 에서 document의 범위를 좁히든지, getElement() 의 매개변수에 들어가는 부분을 보다 정확하게 타겟팅하든지 2가지 방법으로 접근하면 될 것으로 판단된다. 

만약 첫 번째 방법을 사용한다면 별도로 정의한 getElement를 사용할 수가 없어서 통일성이 깨진다. 그래서 매개변수에 각 항목에 대한 데이터가 들어가거, getElement를 사용하는 방향으로 개발할려고 한다.



```js
function getElement(elemVal) {
    /* 단일 요소 가져오는 함수 */

    return document.querySelector(elemVal);
}

function makeFavoriteInToolBar(parentNode, site) {
    ...
    const favoriteIconSvg               = getElement('.favorite-icon-svg') 
    ...
}

```
### 해결 과정

그래서 아래 코드로 수정해서 화면을 확인하니, console에 입력한 id값은 타당하지 않은 선택자라고 한다.

```js
const favoriteIconSvg               = getElement(`'#${site.id}' .favorite-icon-svg`)
```

만약 getElementById로 가져올 수도 있지만, querySelector를 사용하는 방식에서 벗어날 필요는 느끼지 못하여, getElement는 사용하지 못 해도, querySelector를 사용하는 통일성을 지키는 게 낫다는 판단을 했다.

그래서 후자 방식은 사용하지 않고 첫 번째 방식으로 사용하기로 했다.

svg tag의 상위 태그가 span tag인데, 이를 Container로 명명했다.

document 대신에 이 컨테이너를 사용하여 범위를 좁혀서 접근하는 방식으로 해결했다.

```js
const favoriteIconContainer         = createNode('span')
favoriteIconContainer.className     = 'i1qqph0t icon'
appendTag(favoriteButton, favoriteIconContainer)

let favoriteIconSvgHTML             = `<svg class='favorite-icon-svg' fill="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"aria-hidden="true"></svg>`
favoriteIconContainer.insertAdjacentHTML('beforeend', favoriteIconSvgHTML)
const favoriteIconSvg               = favoriteIconContainer.querySelector('.favorite-icon-svg')
```

![image](https://user-images.githubusercontent.com/78094972/205588915-a5022426-8374-4390-b67f-c63413565708.png)

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