---
title: "Project: DOM 생성과 fetch 사용을 위한 Javascript 학습 후기 및 각 과정에 일어난 개발 issues"
date: 2022-11-20T18:25:52+09:00
draft: false
summary: 첫 번째, DOM 생성과 fetch 사용을 위한 Javascript 학습 후기를 남겨본다. 두 번째, DOM을 조작하여 개발한 과정과 fetch를 사용하여 개발한 각 과정에서 일어난 여러 개발 issue들에 대해 정리해본다.
tags: []
categories: ["Project"]
---

# 1. DOM 생성과 fetch function 사용을 위한 Javascript 학습 후기

## 1.1 새로운 언어의 학습 방법 선택하기
파이썬 언어를 학습한 후, 또 다른 프로그래밍 언어를 학습하는 건 처음이어서 파이썬을 학습했던 것처럼 똑같이 하면 될지 고민이 되었다. 만약 파이썬을 학습한 것처럼 파이썬 자체만을 깊이 파는 문법 강의를 통해서 한다면 짧은 프로젝트 기간을 고려하면 좋지 않다. 

고민 중에 이번 부트캠프에서 book study 시간에 읽었던 [함께 자라기](https://jeha00.github.io/post/bookstudy/agile/review01/)에서 본 내용이 문득 기억났다. 프로그래밍 언어는 도구임을 기억하여 바로 사용하고, 막히면 문법 내용으로 돌아오는 이 전환과 피드백이 빠를수록 학습이 빠르다고 했다. (독서를 할 때 단지 읽은 것에서 끝내지 않고, 정리하는 것과 함께 읽는 것이 필요하며 나에게 맞는 학습 방법임을 느낀 계기였다.)

그래서 짧은 기간 안에 만들어야 하기 때문에 해외 유튜브에서 DOM을 자바스크립트로 생성하는 외국 엔지니어의 영상을 따라하며 이해했다. 그리고 바로 프로젝트에 적용했고, 그 과정에서 막히는 부분과 에러 부분은 MDN 문서로 바로 돌아와 놓친 부분을 빠르게 매꿔서 진행했다. 

그 결과가 다음과 같다. PR 날짜만을 보고 판단했을 때 event를 추가하는 것 없이 렌더링 부분만 대략 5일 정도 걸린 것으로 판단된다. 멘토링을 해줬던 멘토님이 처음 프로젝트를 진행하는데 이 속도로 진행한 건 많이 한 것이라는 평가를 들어서 기분이 좋았다.

- [FEAT: Rendering mylist view and bottom-toolbar under each item #28](https://github.com/backendnanodegree/Devket/pull/28/files)

## 1.2 자발적으로 더 맡아서 진행하기

![image](https://user-images.githubusercontent.com/78094972/210824475-9a956489-df5c-42dc-a9ef-6b5414199934.png)

이 평가가 기분이 유독 좋았던 이유는 다음과 같다. 팀원들을 위해서 스스로 더 압박감을 느끼며 진행했지만, 그 결과 팀 전체 도움과 내 자신의 성장을 이끌 수 있었기 때문이다.

이 화면단 개발은 우리 프로젝트에서 화면적으로 반드시 개발되어야하는 부분이었다. 하지만 각자 맡은 메인 기능들에 대해 완성된 후 위 이미지의 사이드바 부분을 나눠서 개발하기로 했기 때문에, 자신이 맡은 개발 기능들에 연구 개발을 하느라 팀원들은 시간을 할애하지 못하는 상황이었다. 

이런 상황에서 내 자신에게 도전적인 과제를 던져주기로 결정했다. 
### 첫 번째 이유
이런 결정을 한 첫 번째 이유는 프로젝트 기한을 고려했을 때 누군가가 더 맡아서 이 부분을 빠르게 구현한다면 프로젝트를 진행하는데 있어서 진행률이 많이 빨라지고, 다른 팀원들도 시각적으로 작동되는 게 보이기 때문에 프로젝트를 이해하고 개발하는데 도움이 될거라 판단했기 때문이다. 왜냐하면 위 이미지의 '내 목록'에 각 저장된 사이트들과 하단툴바가 나타나는 것처럼 사이드바 부분의 각 탭에서 나오는 방식이 다 동일하기 때문이다. 단지 ORM을 사용하여 filter로 가져오는 값들이 다를 뿐이다.

### 두 번째 이유
또한, 전체 팀원 4명 중 개발 경험이 거의 없는 인원이 나 포함 3명이었기 때문에, 최악의 경우로 각자 맡은 기능들을 개발하다가 시간적인 여유가 없어서 이 부분을 개발하지 못하는 상황까지 고려했다.

그래서 스스로 도전 과제를 주어 기한을 1주일 정도 정하여 내 자신에게 압박감을 주면서 진행했다. 그 결과, 기본적으로 저장된 사이트를 렌더링하는 DOM은 다 형성했다. 사이드 바에서는 즐겨찾기, 아티클, 동영상 부분의 ORM filter 부분을 짜서 끝냈다.




&nbsp;

## 1.3 DOM 학습 후기
처음 DOM을 조작하는 것이기 때문에, 저장된 사이트들을 목록에 보여주는 DOM을 생성하는 건 시간이 걸렸다. 그 이유는 진행한 프로젝트는 클론 코딩이기 때문에, html 구조와 css는 거의 그대로 사용하기 위해 html의 class name과 id name을 브라우저의 개발자 도구로 분석하며 가져오느라 오래 걸렸다.  

또한, html 구조와 css는 거의 그대로 사용할 것이기 때문에 class name이 직관적이지 않아도 가져온 이유다. 직접 수정할 수도 있겠지만, 이 프론트 영역까지 파고들고 싶지 않았으며 그 시간에 백엔드 구축에 집중하고 싶었다.

이후에 '하단 툴바'와 modal 창을 렌더링할 때는 금방 DOM 생성 코드를 짤 수 있었다. 이후부터 DOM을 지정하고, 삭제하고, 수정하는 것에 어려움을 느끼지 못했다.

하지만, React를 사용하지 않고 vanilla js로 다 하다보니 코드 길이가 많이 길어졌다. 하지만 React를 사용하면 손쉽게 생성된다고 한다. 프레임워크의 중요성을 느낀 계기가 되었다.

&nbsp;

## 1.4 fetch 함수 학습 및 사용 후기

### Front와 Back 간의 흐름 이해

fetch 함수를 사용하기 전까지는 api를 통해서 프론트와 백엔드가 어떻게 통신을 하는지 코드적으로 이해할 수가 없었다. 즉, 눈에 보이지 않았다. 하지만 내가 구축한 api를 fetch를 사용해서 통신을 직접 해보니 확실히 이해할 수 있었다. 백엔드에서 구축한 api를 fetch를 사용하여 request를 보내면 django의 views.py의 view method(or function)에서 request로 받아 그 과정에서 짜놓은 로직에 따라서 CRUD 상황이 이뤄지고, response를 반환하면 fetch 함수에서 response로 그 결과 값을 반영할 수 있다. 이 response 값에 따라서 DOM 조작이 이뤄진다.

그래서 Django가 MTV (Model - Template - View) pattern에서 View가 controller의 역할을 한다는 걸 느꼈다. Django의 MTV 는 기본적으로 MVC 패턴과 동일하다.

![image](https://lunayyko.github.io/public/img/MVT.png)

> 출처: [루나의 TIL 기술 블로그](https://lunayyko.github.io/wecode/2021/10/27/interview1/)


MVC에 대응되는 MTV 설명은 [Django - MVC 패턴과 MTV 패턴](https://tibetsandfox.tistory.com/16)을 참조한다.

&nbsp;

### fetch 학습

DOM을 익히면서 유튜브와 공식문서를 반복하여 개발하는 동안, 공식 문서로 빠르게 피드백을 받은 경험을 통해서 fetch 함수를 익힐 때는 유튜브 영상은 안보고 공식 문서와 stackoverflow만을 사용하여 작성할 수 있었다. 하지만 fetch의 경우 비동기적으로 작동하기 때문에 어려움이 있었다. 하지만 이 부분에 대해서도 글로 정리한다.


&nbsp;

---

# 2. DOM 렌더링 코드 작성 시 issue 사항

## 2.1 Custom 함수 만들어 팀 공용으로 사용하기

렌더링 시 자주 사용되는 함수들은 별도로 정의하여 코드 수를 줄이고, 보다 직관적으로 만들기 위해 노력했다. 그 이유는 React가 아닌 vanilla JS로 렌더링하는 것이기 때문에 코드 양이 상대적으로 매우 많아지기 때문이다. 계속해서 document.createElement를 사용해도 되겠지만, 이보다는 createNode 함수를 만들어서 보다 코드를 짧고 직관적으로 만들었다. 

아래 함수로 끝나는 것이 아닌 계속해서 추가했으며, `common.js`라는 이름의 별도 파일을 생성하여 앞으로 팀 공용으로 사용하면 좋은 함수들에 대해 PR 승인 후 합쳐서 같이 사용하는 방식으로 팀 방식을 진행했다.

```js
// common.js

function createNode(tag) {
    /* tag를 생성하는 함수 */

    return document.createElement(tag);
}

function appendTag(parent, element) {
    /* parent tag에 child tag를 추가하는 함수 */

    return parent.appendChild(element);
}
```

## 2.2 함수 기능 분리

크롤링으로 가져온 사이트들을 hover 시, 하단에 뜨는 툴바를 렌더링하는 함수다. 하단 툴바에는 즐겨찾기 기능, 카테고리 분류 기능, 태그 추가 기능, 삭제 기능이 있어서 이를 위한 태그 렌더링이 필요하다.

처음에는 `makeBottomToolbar` function에 위 기능을 제공하는 태그 렌더링 코드를 다 작성했다.  
하지만, 코드의 길이가 너무 길어지고 내부 내용을 구분하기 어렵기 때문에, 다음과 같이 `makeFavoriteInToolBar`, `makeCategoryInToolBar` 등등 별도의 함수로 만들어서 `makeBottomToolbar` 를 통해 실행되도록 설계했다. 그리고 주석을 작성하여 다시 한번 구분했다. 

```js
function makeBottomToolbar(parentNode) {
    /*각 사이트마다 하단 툴바를 만드는 함수*/

     // bottom toolbar container - footer
     const footer = createNode('footer')
     footer.className = 'footer'
     appendTag(parentNode, footer)

     const itemActionsContainer = createNode('div')
     itemActionsContainer.className = 'i18uycg6 actions'
     appendTag(footer, itemActionsContainer)

     const itemActions = createNode('div')
     itemActions.className = 'item-actions'
     appendTag(itemActionsContainer, itemActions)

     // bottom toolbar - favorite
     makeFavoriteInToolBar(itemActions)

     // bottom toolbar - category
     makeCategoryInToolBar(itemActions)

     // bottom toolbar - tag
     makeTagInToolBar(itemActions)

     // bottom toolbar - delete
     makeDeleteInToolBar(itemActions)
}
```

&nbsp;

## 2.3 렌더링 시 시간 복잡도 고려 issue

각 저장된 사이트(post)를 내 목록에 렌더링할 때, 전체 항목을 렌더링하고 나서 각 항목에 하단 툴바를 렌더링했다. 즉 크롤링해서 가져온 사이트들을 먼저 각각 렌더링 후, 각 사이트마다 하단툴바를 렌더링을 시도했다. 

실행결과를 보면서 이럴 경우 저장된 사이트의 수 만큼 다시 또 반복하는 것이기 때문에, 파이썬으로 생각하자면 for문을 두 번 돌리는 것과 동일하다고 판단하여 시간 복잡도가 `O(2n)`이 되므로, 각 항목을 렌더링할 때 하단 툴바도 같이 렌더링하여 `O(n)`이 되도록 수정했다. 또한, 현재는 DB에 저장된 사이트 수가 적어서 문제가 없겠지만, 저장된 데이터량이 많아진다면 렌더링 속도에 가시적으로 영향을 줄 거라 판단했기 때문이다.

그래서 다음 아래와 같이 `renderItem` function에 `makeBottomToolbar` function을 포함시켰다.

```js
function renderItem(post) {
    /* 각 post의 tag를 rendering 하는 함수 */

    const article = createNode('article')
    article.className = 'c18o9ext grid hiddenActions noExcerpt'
    appendTag(root, article)

    const item = createNode('div')
    item.className = 'cardWrap'
    appendTag(article, item)

    ...

    // 각 항목(item)의 하단 툴바
    makeBottomToolbar(article)
}
```

&nbsp;

## 2.4 svg icon이 인식되지 않는 문제

### 문제점

**[Pull requests]**
- [Edit html about premium, mylist and Refactor bottom-toolbar.js #51](https://github.com/backendnanodegree/Devket/pull/51)
- [Rendering mylist view and bottom-toolbar under each item #28](https://github.com/backendnanodegree/Devket/pull/28)  


각 항목에 mouse를 hover 시, 하단에 뜨는 하단 툴바를 만들기 위해서 DOM을 생성하는 과정에서 부딪힌 문제점이다. **svg icon을 사용하기 위해서 `<svg>` node를 생성해야 하는데, 생성되지 못한 문제점이 있었다.** 

프로젝트의 클론 코딩 대상 사이트는 아이콘을 `<i>` tag보다 `<svg><path d=""></path></svg>` 를 사용한다. 그래서 다른 태그를 생성하는 것과 동일한 방식을 사용했다. 
 - `createNode`와 `appendTag` function을 사용하여 svg tag와 path tag를 생성 
 - `.className`을 사용하여 class name을 추가
 - 속성명에 `-`가 있는 건 dot annotation으로 추가 못하기 때문에 `setAttribute()` 사용

하지만 아이콘이 브라우저에 형성되지 않는 문제를 발견했다. 클론 코딩 대상의 프로젝트 tag 속성을 개발자 도구로 분석하면서 관련 속성들을 하나씩 지워보고, 순서도 바꿔보고, 새로운 html을 생성하여 해당 태그들만 추가해보고, stackoverflow와 여러 구글링을 통해 피드백 받은 방안들을 적용해봤지만 svg icon이 형성되지 않았다. 

이 문제로 2~3일을 소비했기 때문에, 임시로 i tag를 사용하기로 결정하여, fontawesome site를 선택하여 유사한 icon을 선택하여 사용했다. 

마지막으로, 팀원들이 이 시행착오를 겪지 않도록 하기 위해 코드 리뷰 시간에 공유했다.

### 해결책

다른 팀원 또한 렌더링하는 과정에서 svg icon 문제에 똑같이 부딪혔고, 내가 공유한 과정들을 통해 다른 방식을 적용하여 문제를 해결했다. 

**해결한 방법은 setAttribute로 하면 인식되지 않았지만 html를 통채로 입력하니 가능했다.** 

이 때 변수를 `insertAdjacentHTML`을 사용한 이유는 다음과 같다. 

className과 속성들을 별도로 입력하는 방식이 작동되지 않기 때문에, 통째로 입력하는 방법을 선택했다. 그래서 HTML을 통째로 원하는 DOM tree 안에 node들을 추가하기 위해 함수 'insertAdjacentHTML' 를 사용했다.

사용 방식은 `element.insertAdjacentHTML(position, text)`와 같다. 이 함수의 사용 방법 출처는 [MDN - Element.insertAdjacentHTML()](https://developer.mozilla.org/ko/docs/Web/API/Element/insertAdjacentHTML)을 참고했다.

'text'에 들어갈 svg를 담은 변수를 `let`으로 선언한 이유는 재정의할 수도 있기 때문이다. 클론 코딩의 상단 툴바의 경우 아이콘이 변하는 경우가 있어서 let으로 선언했지만, 하단 툴바의 경우에는 그렇지 않기 때문에 아래 코드의 `let`은 `const`로 수정해야하는 부분이다. 

- let & const
||재선언|재할당|
|----|----|----|
|let|X|O|
|const|X|X|

- position
| position | 설명 |
| ---- | ---- |
| beforebegin | element 앞에|
|afterbegin| element 안에 가장 첫번째 chid|
|beforeend| element 안에 가장 마지막 child |
|afterend|element 뒤에|


클론 코딩의 대상 사이트의 svg DOM 구조가 다음과 같았다.

```html
<span>
    <svg>
        <path></path>
    </svg>
</span>
```

그래서 favoriteIconContainer이 `<span>`에 `<svg>`가 들어가기 위해서 `favoriteIconContainer.insertAdjacentHTML('beforeend', favoriteIconSvgHTML)`을 한 후, `<svg>` 안에 `<path>`를 넣기 위해서 `favoriteIconSvg.insertAdjacentHTML('beforeend', favoriteIconPathHTML)`으로 작성했다.

여기서 `beforeend`를 선택한 이유는 `<path>`의 경우, svg icon의 종류에 따라서 복수로 존재하기도 했다. 그래서 `<path>` tag 순서를 _작성한대로 순서를 원래의 것과 유지하여 직관적으로 순서를 바로 확인할 수 있도록_ 이를 사용했다.

```js
const favoriteButton = createNode('button')
...

const favoriteIconContainer         = createNode('span')
favoriteIconContainer.className     = 'i1qqph0t icon'
appendTag(favoriteButton, favoriteIconContainer)

let favoriteIconSvgHTML             = `<svg class='favorite-icon-svg' fill="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"aria-hidden="true"></svg>`
favoriteIconContainer.insertAdjacentHTML('beforeend', favoriteIconSvgHTML)
const favoriteIconSvg               = getElement('.favorite-icon-svg')

let favoriteIconPathHTML            = '<path fill-rule="evenodd" clip-rule="evenodd" d="M12 1a1 1 0 0 1 ... 1-.753-.548L12 4.26Z"></path>'
favoriteIconSvg.insertAdjacentHTML('beforeend', favoriteIconPathHTML)
```

❗️ let을 const로 수정하기

&nbsp;

---
## 2.5 Modal 창을 통한 삭제 기능 issue

### 발생된 issue

**[Pull requests]**
- [Modifiy api-url and add delete function by bottom-toolbar #42](https://github.com/backendnanodegree/Devket/pull/42)

**[Issue 배경]**  
저장된 사이트의 하단 툴바 삭제 기능을 클릭하여, 모달 창이 뜨면 이 모달 창으로 삭제를 진행하는 방식으로 개발했다.
모달 창을 통해서 삭제를 진행하는 이유는 삭제하면 이 사이트 저장을 되돌릴 수 없기 때문에, 경고를 하는 게 유저 편의성을 높이는 방향이라고 생각했기 때문이다.   

**[발생된 issue]**  
한 사이트의 하단 툴바 삭제 아이콘을 클릭하여 모달 창의 삭제버튼으로 삭제를 진행할 때, 하단툴바가 달려있던 한 사이트만 삭제가 되야하는데 저장된 모든 사이트가 다 삭제되는 이슈가 발생했다.

### issue 발생했을 때 삭제 기능 함수 호출 순서 

원인 코드와 해결과정을 작성하기에 앞서 먼저 하단 툴바의 삭제 버튼을 클릭하여, 모달창이 띄어지고, 삭제를 실행하는 전체 흐름 함수를 확인해보자.

저장된 사이트(site)의 정보를 토대로 목록에 렌더링하는 함수인 renderItem(site) 에서 시작된다. 

- renderItem(site) -> makeBottomToolbar(, site) -> makeDeleteInToolBar(, site) -> openModal() -> Modal 창 오픈

- makeModal() -> deleteSite() -> closeModal -> window.location.reload()

makeModal에 의해서 모달창은 브라우저 창이 load 시, 기본적으로 만들어지지만 화면에 나타나지 않도록 설계했다. 미리 만든 이유는 기본적으로 만들어서 있는 게 버튼을 클릭 이벤트 때마다 렌더링하는 것보다 속도가 빠르다고 생각했기 때문이다. 그래서 openModal()은 기존 class와 tag에 class name을 추가하여 모달창이 보이도록 한다.

또한, makeModal()을 makeDeleteInToolBar() 안에 넣을 수 없는 이유는 모달창은 1개만 필요하기 떄문이다. 만약 makeDeleteInToolBar 안에서 makeModal이 호출된다면 저장된 사이트 수만큼 하단 툴바가 생성되고, 그만큼 모달창이 생성된다.


### 원인

저장된 사이트들을 화면에 렌더링할 때 태그 속성을 식별자로 만들기 위해서 태그 속성 id의 값으로 각 site의 DB에 저장된 id 값으로 정했다.

❗️ 태그 속성 id의 값으로 db의 id 값을 입력한 것은 보안적으로 좋지 않기 때문에, 프로젝트 완성을 위해 다시 개발할 때 수정할 예정이다.

리액트의 경우, 자동적으로 이 태그들을 구분하기 때문에 식별자가 필요하다고 한다. 하지만 우리는 바닐라 js로 하기 때문에 필요했다. 

하지만 모달 창을 통해 삭제할 때 이 모든 site의 id를 가져오기 때문에 발생된 issuse다. 식별을 위해서 입력했지만, 정작 코드 상 식별의 역할을 하지 못했다.


### 해결 과정

이 issue를 해결하는 초기 과정에서는 무엇이 원인인지 알 수 없어서, 다음과 같이 views.py의 해당 method를 print 문을 통해서 확인해보았다.


```python
def delete(self, request, pk):

    site = self.get_object(pk)
    
    print(f'before delete site: {site}')

    site.delete()
    print(f'after delete site: {site}')

    return Response({'msg': 'Deleted successfully'}, status=status.HTTP_200_OK)
```

실행한 결과 터미널에서 모든 항목들이 하나씩 하나씩 출력되었다. 의도치 않게 자바스크립트에서 전체 사이트들이 포함된 배열로 보내져서 이것이 하나씩 삭제되는 건가 추측했지만, 터미널의 결과를 보니 그렇지 않았다. 한 항목의 하단 툴바 삭제 기능을 눌러 실행한 것이지만, 각 항목의 하단 툴바 삭제 버튼을 모든 항목에 대해서 클릭하여 일어난 것과 동일한 결과이기 때문이다. 

```python
before delete site: ....
before delete site: ....
```


그래서 이번에는 javascript 함수인 `makeDeleteInToolBar`, `makeModalActive`, `deleteSite` 에 각각 `console.log(site)` 를 입력하여 브라우저 콘솔 창에 확인하니 삭제 버튼을 실행하지 않아도 위 각 함수에서 모든 항목이 출력되는 걸 확인했다. 이는 js 문제이며 식별자 문제임을 확인했다.

만약 리액트로 이를 구현했다면, 리액트에서는 각 항목들을 구분하는 별도의 값을 자동적으로 만들어주기 때문에 이런 문제에 부딪히지 않는다고 멘토님을 통해 확인했다. 

그래서 각 항목들을 확실하게 구별할 수 있는 식별자를 만들어, 이 식별자에 접근하여 삭제하는 방식을 권하셨다. 이에 대한 구체적인 방안으로 각 항목의 하단 툴바 삭제 버튼을 클릭하면 해당 항목의 class에 `selected` 를 추가한다. 

```js
function makeDeleteInToolBar() {
    deleteButton.addEventListener('click', () => {
    const article = document.getElementById(`${site.id}`)

    // 추가
    article.classList.add('selected')
    openModal(deleteButton)


    })}
```

그리고 `selected`가 클래스 값에 있는 태그를 가져와, 이 태그의 id 값을 fetch에 입력하여 view 단에 보내는 흐름으로 구성했다.

```js
function deleteSite() {
    /* DELETE http message를 보내는 함수 */

    const site      = getElement('.c18o9ext.grid.hiddenActions.noExcerpt.selected')
    const csrftoken = getCookie('csrftoken');
    const data      = {
        method: 'DELETE',
        headers: {
            'content-type': 'application/json',
            'X-CSRFToken' : csrftoken,  
        },
    }

    fetch(`/api/sites/${site.id}`, data)
    .then(response => {
        const status = response.status

        if (status === 200) {
            console.log('삭제 완료했습니다.')
        } else if (status === 404) {
            console.log('해당 항목이 존재하지 않습니다.')
        } return response.json() 
    })
    .catch(error => console.log('Error:', error))
}
```


삭제 후, 화면을 다시 불러오기 위해서 `window.location.reload()`를 사용한다.


### 최종 호출 순서

문제 상황을 해결하고, 다른 팀원들도 공통으로 사용할 수 있도록 모달창을 모듈화한 최종 함수 호출 흐름은 다음과 같다.

- issue 발생 시 함수 호출 흐름: renderItem(site) -> makeBottomToolbar(, site) -> makeDeleteInToolBar(, site) ->

- 최종 함수 호출 흐름: renderItem(site) -> makeBottomToolbar(, site) -> makeDeleteInToolBar(, site) -> openModal(param) -> makeModal(param) -> deleteSite()

내가 코드를 작성했을 때 모달창 렌더링은 하단 툴바에서만 사용하는 경고 텍스트, 버튼 이름이었지만, 이를 상단 툴바나 이외의 곳에서 팀원들이 같이 사용하기 위해서 팀원들 모두 동의 하에 모듈화를 진행했다. 그래서 param의 역할은 모듈로 사용되기 위해서 다음과 같이 만들어진 객체다. 

```js
// 하단 툴바의 모달창 Parameter
let modalParam  = {
    func        : deleteSite,
    type        : 'bottom',
    alarm_txt   : `이 항목을 삭제하겠습니까? 삭제한 항목은 복원할 수 없습니다.`,
    title       : '항목 삭제',
    buttonName  : '삭제', 
    args        : '',
}

// 상단 툴바의 즐겨찾기 추가 복수 작업
let modalParam = {
    func        : bulkFavorite,
    type        : 'bulk', 
    alarm_txt   : alarm_txt,
    title       : '선택 항목 즐겨찾기',
    buttonName  : '추가', 
    args        : favorite,
}
```

또한, 추가로 `article.classList.add('selected')` 였지만 코드 리뷰 시간에 `.toggle()`이란 걸 사용하면 없으면 추가하고, 있으면 빼는 작업을 자동적으로 해주는 걸 알게되어 `article.classList.toggle('selected')`로 수정했다.


&nbsp;

---


## 2.6 Document.querySelector() issue


### 발생된 issue 와 원인 코드

> **_첫 번째 article tag에 저장된 모든 사이트 수만큼 svg, path tag가 다 들어간 상황_**

아래 첫 번째 article tag의 하단 툴바 svg tag에 저장된 사이트의 갯수만큼 path 태그가 추가되어, 다른 항목들의 하단 툴바 이미지 아이콘이 보이지 않는 문제가 발생했다.

아래 이미지는 두 항목 모두 hover 된 상태이지만, 첫 번째에만 하단툴바 아이콘 이미지가 뜨고 있다.

![image](https://user-images.githubusercontent.com/78094972/205566677-2b0d0707-94b0-4c0a-a913-b332158d2241.png)


즐겨찾기 만드는 함수 외에 카테고리 함수, 태그 함수, 삭제 함수 모두 다 발생된 문제이지만 예로서 즐겨찾기만 가져왔다.

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
    
    // issue가 발생된 원인
    const favoriteIconSvg               = getElement('.favorite-icon-svg')

    let favoriteIconPathHTML            = '<path fill-rule="evenodd" clip-rule="evenodd" d="M12 1a1 1 0 0 1 .897.557l2.706 5.484 6.051.88a1 1 0 0 1 .555 1.705l-4.38 4.268 1.034 6.027a1 1 0 0 1-1.45 1.054L12 18.13l-5.413 2.845a1 1 0 0 1-1.45-1.054l1.033-6.027-4.379-4.268a1 1 0 0 1 .555-1.706l6.051-.88 2.706-5.483A1 1 0 0 1 12 1Zm0 3.26L9.958 8.397a1 1 0 0 1-.753.548l-4.567.663 3.305 3.221a1 1 0 0 1 .287.885l-.78 4.548 4.085-2.147a1 1 0 0 1 .93 0l4.085 2.147-.78-4.548a1 1 0 0 1 .287-.885l3.305-3.22-4.567-.664a1 1 0 0 1-.753-.548L12 4.26Z"></path>'
    favoriteIconSvg.insertAdjacentHTML('beforeend', favoriteIconPathHTML)

    changeFavoriteValue(favoriteButton, site)
}
```

위 코드에서 `getElement` function이 문제의 원인이다. 이 function은 공통으로 사용하고자 custom function이다. 

```js
function getElement(elemVal) {
    /* 단일 요소 가져오는 함수 */

    return document.querySelector(elemVal);
}
```

이 함수를 사용했지만 해당 issue를 확인하지 못 했던 이유는 그 당시 내 목록에 추가했던 항목이 1개 밖에 없었기 때문이다. 팀원한테 이 issue를 듣고서 해결하기 시작했다.


`querySelector` 는 전체 document에서 해당되는 태그들 중 첫 번째를 가져오기 때문에 발생한 이슈였다. `getElement`를 사용할 때 공식문서를 확인했지만, 놓친 부분이 있었던 것이다. 이는 [Document.querySelector()](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector)의 설명 부분을 보니 알 수 있었다.

### 해결과정

그래서 아래 2가지 방법을 생각해냈다. 

- document.querySelector() 에서 document의 범위를 좁히기 
- getElement() 의 매개변수에 들어가는 부분을 보다 정확하게 타겟팅하기

만약 첫 번째 방법을 사용한다면 만든 getElement를 사용할 수가 없어서 통일성이 깨지기 때문에, 먼저 getElement를 사용하는 방향을 선택했다.


```js
function getElement(elemVal) {
    /* 단일 요소 가져오는 함수 */

    return document.querySelector(elemVal);
}

function makeFavoriteInToolBar(parentNode, site) {
    ...
    const favoriteIconSvg = getElement('.favorite-icon-svg') 
    ...
}
```

`site.id`를 id 값으로 입력한 이유는 저장한 각 항목들을 구분하기 위한 식별자로 각 사이트를 렌더링할 때마다 속성값으로 입력했기 때문이다. 

React라면 각각의 식별자가 자동적으로 추가된다고 하지만, vanilla JS를 사용했기 때문에 수동적으로 입력하는 게 필요했다.  그래서 아래 코드로 수정해서 화면을 확인하니 console에 입력한 id값은 타당하지 않은 선택자라고 한다.

```js
// 변경 전
const favoriteIconSvg = getElement('.favorite-icon-svg')

// 변경 후
const favoriteIconSvg = getElement(`'#${site.id}' .favorite-icon-svg`)
```

그래서 getElementById로 가져올 수도 있지만, 코드 통일성을 위해서 getElement는 사용하지 못 해도 querySelector를 사용하는 게 낫다는 판단으로 두 번째 방법은 사용하지 않고 첫 번째 방법으로 사용하기로 했다. 

svg tag의 상위 태그인 span tag를 Container로 명명하여 document 대신에 이 컨테이너를 사용하여 범위를 좁혀서 접근하는 방식으로 해결했다.

```js
const favoriteIconContainer     = createNode('span')
favoriteIconContainer.className = 'i1qqph0t icon'
appendTag(favoriteButton, favoriteIconContainer)

let favoriteIconSvgHTML         = `<svg class='favorite-icon-svg' fill="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"aria-hidden="true"></svg>`
favoriteIconContainer.insertAdjacentHTML('beforeend', favoriteIconSvgHTML)

// 수정된 부분
const favoriteIconSvg           = favoriteIconContainer.querySelector('.favorite-icon-svg')
```

그 결과, 다음 이미지와 같이 하단 툴바가 몰리는 issue를 해결할 수 있었다.

![image](https://user-images.githubusercontent.com/78094972/205588915-a5022426-8374-4390-b67f-c63413565708.png)

&nbsp;

---

# 3. Fetch 함수 사용 시 issue 사항

## 3.1 Custom 함수 만들어 팀 공용으로 사용하기

DOM rendering을 위해 커스텀 함수를 만든 것처럼 fetch 함수의 두 번째 인자로 보낼 HTTP message를  그 때 그 때마다 다 작성하는 건 효율적이지 못하다고 판단하여 method와 Http body에 담을 data를 인자로 받는 `setFetchData`를 작성했다. 여기서 `content-type`을 `application/json`으로 정한 이유는 MDN을 통해 [iana](https://www.iana.org/assignments/media-types/media-types.xhtml)을 들어가니 위 content-type에 대한 설명이 있었고, 구글링을 해도 application에서 json 타입으로 데이터를 주고 받을 때는 content-type에 위 형식을 사용한다고 나와서 이 방식으로 선택했다.


```js
...

function setFetchData(method, body){
    /* Fetch data 셋팅 */

    let csrftoken   = getCookie('csrftoken');

    const data = {
        method: method,
        headers: {
            'content-type': 'application/json',
            'X-CSRFToken' : csrftoken,        
        },
        body: JSON.stringify(body)
    }

    return data
}
```

&nbsp;

## 3.2 아임포트 Javascript SDK에 fetch 사용하기

아임포트 결제 연동 서비스를 사용하기 위해서는 아임포트 Javascript SDK(Software Development Kit)를 사용해야 한다. 이 SDK를 통해서 내가 생성한 주문번호와 금액을 아임포트에 전달하고, 아임포트는 결제 번호를 나에게 전달한다.

그래서 이 SDK를 통해 결제 각 단계에서의 데이터 처리를 js function을 통해서 django의 view로 전달하고, DB에 결제 상태와 정보를 저장한다. 그리고, django의 view에 전달하기 위해서 fetch를 사용했다.

🔆 아임포트 Javascript SDK는 정확하게는 Ajax 문법으로 작성되어 있다. 구글링을 해도 SDK에 사용되는 function을 Ajax로 작성되어 있어서 javascript만 알고 있기 때문에, 이를 function은 js로 다 바꾸고, IMP.request_pay 부분은 다른 아임포트 라이브러리 ajax파일을 호출하기 때문에, 이 부분을 제외한 나머지 부분들도 js로 바꿨다.


🔆 이 문서에서는 Javascript SDK와의 전체적인 흐름이 아닌 fetch 함수의 비동기적 특징에 대해서만 다룬다.
- [아임포트 Javascript SDK를 사용한 결제 흐름](http://localhost:1313/post/project/django/08_payment_overall_flow/#3-%EC%95%84%EC%9E%84%ED%8F%AC%ED%8A%B8-javascript-sdk%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%9C-%EA%B2%B0%EC%A0%9C-%ED%9D%90%EB%A6%84)



### 발생된 issue와 원인 코드

아래 코드에 나와있다시피 결제 금액과 결제 방식을 입력받은 함수가 주문 번호(merchant_id)를 반환하는 함수 과정에서 11번째 줄에는 merchant_id가 확인되지만, 15번째줄 console.log(result)에서는 merchant_id가 확인되지 않는 issue가 있었다.


```js
// IMP.request_pay를 호출하기 위한 merchant_id 반환
function passMerchantId(e, amount, type) {
    e.preventDefault(); 
    const data = setFetchData('POST', {
        amount: amount, 
        type: type, 
    })
    const result = fetch(`/api/payment/checkout`, data)
    .then(response => response.json())
    .then(result => {
        console.log(result.merchant_id) //58b9dea713
        merchant_id = result.merchant_id
        return merchant_id
    }).catch(err => alert(`${err}`))
    console.log(result) // ''
    return result
}
```


그래서 아임포트 IMP.request_pay()에 주문번호가 undefined로 전달되어 결제가 실패했다. 이를 방지하고자 아래와 같이 `merchant_id !== undefined` 또는 `merchand_id !== ''` 를 했지만, 올바른 값이 아니기 때문에 결제가 진행되지 못 했다.


```js
import {getElement ,setFetchData} from './common.js';

window.onload = function () {
    const IMP = window.IMP;
    IMP.init('imp62676076');
    const paymentButton = getElement('.order')
    paymentButton.addEventListener('click', (e) => {
        ...
        const merchant_id = passMerchantId(e, amount, type);
        if (merchant_id !== '' && merchant_id !== undefined) {
            IMP.request_pay({
                pg: 'html5_inicis',
                merchant_uid: merchant_id,
                name: 'Devket Premium 서비스',
                pay_method: 'card',
                amount: amount,
            },
            // 결제창 오픈
            function (response) {
                // 결제한 유저의 이름과 이메일 확인하고 결제 버튼 누른 후, 진행되는 로직 
                if (response.success) {
                   ...
                } else {
                  ...
                }
            });
        }
        return false;
    })
};
```

&nbsp;

### 해결과정


**[첫 번째 고려사항: Scope 상의 문제]**

[fetch의 기본적인 사용법과 scope 극복](https://velog.io/@along/fetch%EC%9D%98-%EA%B8%B0%EB%B3%B8%EC%A0%81%EC%9D%B8-%EC%82%AC%EC%9A%A9%EB%B2%95%EA%B3%BC-scope-%EA%B7%B9%EB%B3%B5)을 참고하여 다음 코드와 같이 fetch scope 외부에 `merchant_id`를 선언했고, 값이 재할당 되기 때문에 `let`을 사용했다.

```js
// IMP.request_pay를 호출하기 위한 merchant_id 반환
function passMerchantId(e, amount, type) {
    e.preventDefault(); 
    
    // 추가된 코드
    let merchant_id = ''

    const data = setFetchData('POST', {
        amount: amount, 
        type: type, 
    })
    const result = fetch(`/api/payment/checkout`, data)
    .then(response => response.json())
    .then(result => {
        console.log(result.merchant_id) //58b9dea713
        merchant_id = result.merchant_id
        return merchant_id
    }).catch(err => alert(`${err}`))
    console.log(result) // ''
    return result
}
```

하지만, 그럼에도 결과는 동일했다. 

#

**[두 번째 고려사항: 비동기]**

[JavaScript - 자바스크립트 fetch와 async/await](https://chanhuiseok.github.io/posts/js-6/) 블로그에 따르면 merchant_id 값이 다르게 출력되는 이유는 fetch가 비동기로 작동하기 때문이다. **_이전 작업이 완료될 때까지 기다리지 않고, 따로 처리하며 다른 코드들도 먼저 실행할 수 있도록 하는 작업_** 이다. 그러므로 fetch 밖에 정의한 merchant_id에는 할당되지 않는 상황인 **_순서가 정확히 보장되지 못하는 상황_** 에 처할 수 있다.

그래서 순서를 보장받고자 async와 await을 사용한다. 

await은 [MDN - await](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/await)에 따르면 Promise 객체를 리턴하는 부분 앞에만 작성될 수 있고, await 연산자는 `async function` 내부에서만 사용할 수 있다고 하여 다음과 같이 async와 await을 추가했다. 왜냐하면 `Promise`가 `await` 연산자에 넘겨지면, `await`은 `Promise`가 완전히 실행되기를 기다렸다가 해당 값을 반환하기 때문이다.

```js
// async 추가
async function passMerchantId(e, amount, type) {
    e.preventDefault(); 
    let merchant_id = ''
    const data = setFetchData('POST', {
        amount: amount, 
        type: type, 
    })
    // await 추가
    await fetch(`/api/payment/checkout`, data)
    .then(response => {
        const result = response.json();
        if (response.status === 200) {
            console.log('result.mechant_id: ', result.merchant_id)
            merchant_id = result.merchant_id
        } else if (response.status === 401) {
            alert('로그인 해주세요.')
        } else {
            alert('문제가 발생했습니다. 다시 시도해주세요.')
        }
    })
    return merchant_id
};
```

하지만 그럼에도 merchant_id는 빈 값으로 반환되었다. 


#

**[세 번째 고려사항: 정확한 사용방법이 맞는가?]**

[MDN - await](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/await)와 위 블로그들을 보면서 await과 then을 같이 사용하는 코드를 발견하지 못하여 이에 대해 구글링을 했다. 

구글링을 하는 과정에서 [async/ await 사용방법 (feat. then과 함께 쓰기)](https://appdevelopmaster.tistory.com/491), [Promise 정리: async/await 사용법 & then과의 차이](https://one-it.tistory.com/entry/Promise-%EC%A0%95%EB%A6%AC-asyncawait-%EC%82%AC%EC%9A%A9%EB%B2%95-then%EA%B3%BC%EC%9D%98-%EC%B0%A8%EC%9D%B4) 문서뿐만 아니라 여러 문서들을 보면서 잘못된 방식으로 사용했다는 걸 알았다. 또한, 프로젝트 멘토님도 then과 await은 같이 사용하는 게 아니라는 의견을 주셨다.

그래서 then을 사용하지 않고 `const`로 변수를 선언하여 사용하기로 했다. `result.works`는 아임포트에서 success, fail를 판단할 때 사용하는 변수이기 때문에 그 외 함수 분기처리에도 사용하는 게 통일성 있는 방식으로 생각하여 아래와 같이 선택했다. 

```js
// IMP.request_pay를 호출하기 위한 merchant_id 반환
async function passMerchantId(e, amount, type) {
    e.preventDefault(); 
    let merchant_id = ''
    const data = setFetchData('POST', {
        amount: amount, 
        type: type, 
    })
    const response = await fetch(`/api/payment/checkout`, data)
    const result   = await response.json() 
    if (result.works) {
        merchant_id = result.merchant_id
        console.log('merchant_id after then:', merchant_id)
        return merchant_id 
    } else if (result.status === 401) {
        alert('로그인 해주세요.')
    } else {
        alert('문제가 발생했습니다. 다시 시도해주세요.')
    }
};
```

[Javascript Promise 배경부터 then catch 그리고 async await까지](https://velog.io/@dishate/Javascript-Promise-%EB%B0%B0%EA%B2%BD%EB%B6%80%ED%84%B0-then-catch-%EA%B7%B8%EB%A6%AC%EA%B3%A0-async-await%EA%B9%8C%EC%A7%80) 문서 뿐만 아니라 여러 문서들에서 보면 then ~ catch 이후에 새롭게 나온 방식이 async 와 await이라고 한다. then을 계속해서 사용하면 코드가 길어져 가독성이 좋지 않기 때문에, async 와 await 방식을 권장한다고 나온다.

---

# Reference

- [FEAT: Rendering mylist view and bottom-toolbar under each item #28](https://github.com/backendnanodegree/Devket/pull/28/files)
- [루나의 TIL 기술 블로그](https://lunayyko.github.io/wecode/2021/10/27/interview1/)
- [Django - MVC 패턴과 MTV 패턴](https://tibetsandfox.tistory.com/16)  
- [MDN - Element.insertAdjacentHTML()](https://developer.mozilla.org/ko/docs/Web/API/Element/insertAdjacentHTML)
- [iana](https://www.iana.org/assignments/media-types/media-types.xhtml)
- [fetch의 기본적인 사용법과 scope 극복](https://velog.io/@along/fetch%EC%9D%98-%EA%B8%B0%EB%B3%B8%EC%A0%81%EC%9D%B8-%EC%82%AC%EC%9A%A9%EB%B2%95%EA%B3%BC-scope-%EA%B7%B9%EB%B3%B5)  
- [JavaScript - 자바스크립트 fetch와 async/await](https://chanhuiseok.github.io/posts/js-6/)  
- [MDN - await](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/await)  
- [async/ await 사용방법 (feat. then과 함께 쓰기)](https://appdevelopmaster.tistory.com/491)  
- [Promise 정리: async/await 사용법 & then과의 차이](https://one-it.tistory.com/entry/Promise-%EC%A0%95%EB%A6%AC-asyncawait-%EC%82%AC%EC%9A%A9%EB%B2%95-then%EA%B3%BC%EC%9D%98-%EC%B0%A8%EC%9D%B4)  
- [Javascript Promise 배경부터 then catch 그리고 async await까지](https://velog.io/@dishate/Javascript-Promise-%EB%B0%B0%EA%B2%BD%EB%B6%80%ED%84%B0-then-catch-%EA%B7%B8%EB%A6%AC%EA%B3%A0-async-await%EA%B9%8C%EC%A7%80)  