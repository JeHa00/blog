---
title: "DOM 생성을 위한 자바스크립트 학습 및 함수 객체화"
date: 2022-11-10T18:25:52+09:00
draft: true
summary: 
tags: ["DOM"]
categories: ["Project"]
---

# 함수 객체화

렌더링 시 자주 사용되는 함수들은 별도로 정의하여 코드 수를 줄일려고 노력

그리고, 공통적으로 사용되는 함수라면 common.js에 모아놨다.

<br>

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
```