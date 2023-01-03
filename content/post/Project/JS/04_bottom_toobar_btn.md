---
title: "Bottom_toobar_btn 함수 호출 순서"
date: 2022-11-17T16:23:39+09:00
draft: true
summary: 
tags: [""]
categories: ["Project"]
---
makeDeleteInToolBar(parentNode , site) -> makeModalActive(deleteButton, site): 삭제 버튼을 누르면 Modal 창을 활성화시키는 함수

makeModal() -> 창을 닫는 함수 
            -> deleteSite -> makeDeleteInToolBar로부터 입력된 site 데이터가 있어야 지정된 항목 삭제 가능 

makeModal()을 makeDeleteInToolBar() 안에 넣을 수 없는 이유는 모달창은 1개만 필요하기 떄문이다. 

안에 넣어놓으면 article의 갯수만큼 생성된다.

그래서 makdModalActive 안에 deleteSite를 추가했다. 모달 창의 삭제 버튼은 getElement 함수를 사용하여 가져왔다. 
