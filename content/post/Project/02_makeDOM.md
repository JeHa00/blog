---
title: "DOM 생성을 위한 자바스크립트 학습 및 함수 객체화"
date: 2022-11-10T18:25:52+09:00
draft: true
summary: 
tags: ["DOM"]
categories: ["Project"]
---

# 1. DOM 생성과 fetch 사용을 위한 js 학습

## 새로운 언어의 학습 방법 선택하기
파이썬 언어를 학습한 후, 또 다른 프로그래밍 언어를 학습하는 건 처음이어서 파이썬을 학습했던 것처럼 똑같이 하면 될지 고민이 되었다. 

만약 파이썬을 학습한 것처럼 한다면 짧은 프로젝트 기간을 고려하면 좋지 않다. 

이번 부트캠프에서 book study 시간에 읽었던 [함께 자라기](https://jeha00.github.io/post/bookstudy/agile/review01/)에서 본 내용이 기억났다. 프로그래밍 언어는 도구임을 기억하여 바로 사용하고, 막히면 문법 내용으로 돌아오는 이 전환과 피드백이 빠를수록 학습이 빠르다고 했다. 

그래서 해외 유튜브에서 DOM을 자바스크립트로 생성하는 영상을 따라하며 이해했다. 그리고 바로 프로젝트에 적용했고, 그 과정에서 막히는 부분과 에러 부분은 MDN 문서로 바로 돌아와 놓친 부분을 빠르게 매꿔서 진행했다. 

그 결과가 다음과 같다.

- [FEAT: Rendering mylist view and bottom-toolbar under each item #28](https://github.com/backendnanodegree/Devket/pull/28/files)

## DOM
처음 DOM을 조작하는 것이기 때문에, 저장된 사이트들을 목록에 보여주는 DOM을 생성하는 건 시간이 걸렸지만 이후에 '하단 툴바'를 렌더링할 때는 금방 DOM 생성 코드를 짤 수 있었다. 이후부터 DOM을 지정하고, 삭제하고, 수정하는 것에 어려움을 느끼지 못했다.

&nbsp;

## fetch 함수 사용하기

### Front와 Back 간의 흐름 이해

fetch 함수를 사용하기 전까지는 api를 통해서 프론트와 백엔드가 어떻게 통신을 하는지 코드적으로 이해할 수가 없었다. 즉, 눈에 보이지 않았다. 하지만 내가 구축한 api를 fetch를 사용해서 통신을 직접 해보니 확실히 이해할 수 있었다. 백엔드에서 구축한 api를 fetch를 사용하여 request를 보내면 django의 views.py의 view method(or function)에서 request로 받아 response를 반환하면 fetch 함수에서 response로 보낼 수 있다.

&nbsp;

### fetch 학습
DOM을 익히면서 유튜브와 공식문서를 왔다갔다하면서 개발을 진행하는 과정에서 공식 문서로 빠르게 피드백을 받은 경험을 통해서 fetch 함수를 익힐 때는 유튜브 영상은 안보고 공식 문서와 stackoverflow만을 사용하여 작성할 수 있었다. 하지만 fetch의 경우 비동기적으로 작동하기 때문에 이 부분에 관해서 어려움이 있었다.


&nbsp;

---


# 함수 객체화

렌더링 시 자주 사용되는 함수들은 별도로 정의하여 코드 수를 줄이고, 보다 직관적으로 만들기 위해 노력했다. 

```js
function createNode(tag) {
    /* tag를 생성하는 함수 */

    return document.createElement(tag);
}

function appendTag(parent, element) {
    /* parent tag에 child tag를 추가하는 함수 */

    return parent.appendChild(element);
}
```

계속해서 document.createElement를 사용해도 되겠지만, 이보다 createNode 함수를 만들어서 보다 코드를 짧고 직관적으로 만들었다. 

### appendTag

appendTag의 경우, js로 직접 저장되는 항목들을 렌더링하기 위해서 html tag를 생성하기 때문에 dot annotation보다 하나의 함수로 만드는 것이 직관적이라 이 방식을 선택했다.

그리고, 공통적으로 사용되는 함수라면 common.js에 모아놨다.

&nbsp;

# 렌더링 시 이슈, 

각 저장된 항목을 목록에 렌더링할 때, 전체 항목을 렌더링하고 나서, 각 항목에 하단 툴바를 렌더링할려고 했지만 그러면 반복문을 2번해야하기 때문에, O(2n)이 되므로, 각 항목을 렌더링할 때 하단 툴바도 같이 렌더링하여 O(n)이 되도록 결정했다.

```js
function renderItem(post) {
    /* 각 item의 tag를 rendering 하는 함수 */

    const article = createNode('article')
    article.className = 'c18o9ext grid hiddenActions noExcerpt'
    appendTag(root, article)

    const item = createNode('div')
    item.className = 'cardWrap'
    appendTag(article, item)

    const imgContainer = createNode('div')
    imgContainer.className = 'c97c5c media'
    appendTag(item, imgContainer)

    const itemLink = createNode('a')
    itemLink.href = post.thumbnail_url
    appendTag(imgContainer, itemLink)

    const img = createNode('img')
    img.src = post.thumbnail_url
    img.style = '--fallbackBackground:#1CB0A880; --fallbackColor:#1CB0A8; --fallbackLetter:&quot;T&quot;;">'
    appendTag(itemLink, img)

    const content = createNode('div')
    content.className = 'content'
    appendTag(item, content)

    const titleContainer = createNode('h2')
    titleContainer.className = 'title'
    appendTag(content, titleContainer)

    const title = createNode('a')
    title.innerText = post.title
    appendTag(titleContainer, title)

    const details = createNode('cite')
    details.className = 'details'
    appendTag(content, details)

    const publisher = createNode('a')
    publisher.className = 'publisher'
    publisher.href = 'https://www.theatlantic.com/technology/archive/2022/10/phone-call-greeting-smartphone-technological-error/671910/?utm_source=pocket_saves'
    publisher.innerText = 'The Atlantic'
    appendTag(details, publisher)

    // 각 항목(item)의 하단 툴바
    makeBottomToolbar(article)
}
```