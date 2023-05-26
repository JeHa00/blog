---
title: "[TIL] HTML & CSS study - CSS 02"
date: 2022-08-12T22:35:43+09:00
draft: false
summary: Block와 Inline의 차이는 무엇이고, box의 속성 3가지인 border, padding, margin에 대해 학습한다. 
tags: ["TIL", "CSS"]
categories: "HTML_CSS"
---

# 1. CSS의 속성: Block 과 Inline


아래 설명을 보기 전 예시 코드로 [CSS의 display 속성: inline, block, inline-block](https://www.daleseo.com/css-display-inline-block/)을 참고하면 이해하기 보다 쉬울 것이다. 

<br>

## 1.1 block과 inline이란?? 

> **_box들은 대부분 block 속성을 가지는데 옆에 다른 요소(element)가 못 오는 속성을 'block', 옆에 다른 요소가 올 수 있는 속성을 'inline (in the same line)'이라 한다._**       

css의 여러 가지 속성들 중 'block' 과 'inline' 속성에 대해 알아보자.  


### box

html은 box들로 디자인된다고 생각해도 무방하다. 큰 box들이 작은 box 들로 구성된다.  

BOX의 종류는 다음과 같다.

- 가장 기본적인 BOX로는  `<div>` tag를 생각해볼 수 있다.  

- 이외에도 div, header, main, section, footer, article 태그 등이 있다.   

- 그래서 `<span>`, `<a>`, `<image>` 같은 block이 아닌 종류를 기억하는 게 편하다.  


### inline

그리고, box 외에 'inline' 속성이 존재한다.  

- **_'inline' 속성은 아주 작은 글, 링크 그림 등이 이에 속한다._**  

### box와 inline의 차이 

그렇다면 **_'block'과 'inline'의 차이_** 는 무엇일까??  

- block: 옆에 다른 요소가 못 오는 속성  
    - 옆에 다른 요소가 못 오도록 **block** 한다고 생각하자.
    - **높이와 너비를 가지고 있다.**  

- inline: 옆에 다른 요소가 올 수 있는 속성
    - **line** 한 줄에 다 올라온다고 생각하자. 
    - **높이와 너비를 가지고 있지 않다.**  

### box와 inline 속성을 바꾸는 방법 

css의 `display` property로 block을 inline으로, inline을 block으로 바꿀 수 있다.  

❗ [property와 attribute의 차이](https://velog.io/@kysung95/%EC%A7%A4%EB%A7%89%EA%B8%80-attribute%EC%99%80-property%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90): 링크를 클릭하여 참고하자. 

<br>

---
## 1.2 Block과 Inline의 속성

| display attribute | Block | Inline | Inline -block | 
| ----|----| ----| ---- | 
| height and width | O | X | O |
| other elements near | X(one element on a row) | O | O |  


- **Block의 속성**
    - 이 tag를 block으로 인지하는지를 알기 위해서는 검사에 들어가서 확인하면 된다. 

    - 높이와 너비를 가지고 있다. 
    - 또한, box이기 때문에 **3가지 속성(margin, padding, border)** 을 가진다.    
    - 원래 block인 것을 inline으로 바꾸면 높이와 너비 요소들은 사라진다.

- **Inline의 속성**
    - inline은 높이와 너비를 가질 수 없지만, 폭과 너비를 가지게 하고 싶으면 CSS 속성인 `display`를 사용한다. 
        - `display: 'inline - block;`을 입력한다.  



❗ inline-block으로 했을 때는 여러 문제점들이 있다.   
- 첫 번째, 기본적으로 element 사이에 빈 공간이 기본으로 생긴다.  
- 두 번째, 반응형 디자인(responsive design)을 지원하지 않아서 PC 모니터 사이즈에 따라 달라진다.   

<br>

---
## 1.3 Box의 속성: border, margin, padding

| box attribute | margin | padding |
| ---- | ---- | ---- | 
| definition | space from the border of the box to the outside | a spce from the border of the box to the inside |
| directions On inline | right, left | top, bottom, right, left|  

**Box는 3가지 속성: border, margin, padding을 가진다.**  

즉, block도 이 3가지 속성을 가진다는 것이다.  

### border

- 단어 명칭 그대로 **_a border of the box_**: box의 경계선을 의미한다.  
- 많은 property를 가지고 있지만, 이쁘지 않아서 사용되지 않는다.  
- 'border style mdn'을 구글링하면 나온다.
- inline과 block에 다 적용된다. 



### margin

- 'padding'의 반대 개념으로, 정의는 다음과 같다. 
- **_space from the border of the box to the outside_** : box의 border 바깥에 있는 공간  
- box의 크기에 따라 margin의 크기도 달라진다. 


❗ Collapsing margins 현상    
    - 2개의 box가 있을 때, 경계선이 같은 부분에 있다면 이 두 margin은 하나로 취급된다.   
    - 위, 아래쪽만 일어난다. 경계가 닿아서 margin이 같아졌다.  


### padding

- 'margin'의 반대 개념으로, 정의는 다음과 같다.  
- **_a spce from the border of the box to the inside_**: box의 border 안쪽에 있는 공간  

### Inline에서의 padding과 margin

- padding은 사방으로 가능하나, margin은 좌우로만 가능하다.  
- margin을 위아래로도 적용하고 싶다면, inline을 block으로 바꿔야 한다.  


##  

🔅 브라우저에서 box 속성 확인하기  
    - 마우스 우클릭하여 검사 (or inspection)에 들어가면 `Styles` 항목에 'user agent stylesheet' 가 있다.      
    - 이 'user agent stylesheet'를 보면 border, margin, padding의 크기를 확인할 수 있다.     
    - 또한, border, margin 그리고 padding에 마우스 커서를 대면 브라우저 상에서 어디인지 확인할 수 있다. 



<br>

---
# Reference

- [노마드코더 - 코코아톡 클론 코딩](https://nomadcoders.co/kokoa-clone) 
- [CSS의 display 속성: inline, block, inline-block](https://www.daleseo.com/css-display-inline-block/)
- [property와 attribute의 차이](https://velog.io/@kysung95/%EC%A7%A4%EB%A7%89%EA%B8%80-attribute%EC%99%80-property%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90)