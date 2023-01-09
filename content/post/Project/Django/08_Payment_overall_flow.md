---
title: "Project: Iamport를 선택한 이유, 사용 시 결제 과정, 그리고 얻는 장점, JS SDK를 사용한 결제 흐름"
date: 2022-11-23T14:56:16+09:00
draft: false
summary: 기존 결제 과정과 iamport를 사용 시 변한 결제 과정을 정리하고, iamport 사용 시 얻는 장점들에 대해 정리해본다. 마지막으로 프로젝트에서 아임포트 javascript SDK를 사용한 결제 흐름을 정리해본다.
tags: ["iamport"]
categories: ["Project"]
---

# 0. Introduction

- 이 repository는 현재 [러닝스푼즈 나노디그리 - django backend 부트캠프](https://learningspoons.com/course/detail/django-backend/)에서 팀 프로젝트를 진행하면서 다음과 같은 내용들에 대해 정리하고자 만들었습니다. 
    - 팀 정책을 이것으로 정한 이유 
    - 개발하면서 부딪힌 문제들에 대한 원인, 해결방안, 해결과정, 그리고 그 이유들

- 이번 포스팅에서는 제가 맡은 결제 기능의 전체적인 프로세스와 아임포트를 사용하면서 이 과정이 어떻게 변하는 지에 대해 설명하겠습니다.

<br>

---

# 1. 기존 결제 과정

- 결제의 기본 진행 과정: `인증 -> 결제` 순서로 진행

- 하지만, 한국에서는 이 인증 부분에서 차이가 있다. 한국에서 카드 결제 과정은 다음과 같다. 왜냐하면 **한국에서는 카드 정보(카드 번호 / 유효기간 / cvc )를 일부 가맹점 또는 PG 사를 제외하고는 저장할 수 없기 때문이다. 카드 정보는 오로지 카드사에서만 저장 가능하다.**

- 카드사 서버가 구매자 브라우저로부터 직접 카드 정보를 전달받아 인증처리하는 게 다른 나라와 가장 큰 차이이다. 

- 이 카드 인증된 결과를 바탕으로 결제 프로세스가 진행된다. 결제 프로세스는 3번부터 8번까지의 과정을 말한다.

![image](https://docs.iamport.kr/static/images/tech/webhook/pg-flow.svg)
출처: [아임포트 공식문서 - webhook](https://docs.iamport.kr/tech/webhook)

<br>

### PG 사에서 저장할 수 없도록 만든 방법

![image](https://github.com/iamport/iamport-manual/raw/master/%EC%9D%B8%EC%A6%9D%EA%B2%B0%EC%A0%9C/screenshot/background/authentication.png)

그래서 결제를 진행할 때 PG 사의 결제 모달 창이 뜬 후, 카드사를 클릭하면 또 다른 모달 창이 뜬다. 

이것이 PG 사에서 카드 정보를 저장하지 못하도록 하기 위함이다.  

그래서 PG 결제 모듈 창을 띄우고 나서 결제 과정의 첫 시작인 '인증' 과정이 시작될 수 있다.  


<br>

---

# 2. Iamport 선택 이유와 장점
### Iamport 사용 시 결제 과정

![image](https://docs.iamport.kr/static/images/tech/webhook/iamport-flow.svg)
## 아임 포트를 선택한 이유

### PG 사와의 복잡한 연동 과정 해소

> **_직접 api로 만들어야하는 많은 부분을 아임포트가 대신 해주기 때문에, 짧은 기간 안에 결제 기능을 추가하기 좋은 라이브러리로서, 경험이 없는 학습자가 사용하기에 보다 퀄리티에 집중할 수 있다._**      

직접 PG 서버와 통신해야 했던 가맹점 서버의 역할을 대신한다. PG 서버와 직접 통신하기 위해서 PG가 지원하는 제한적인 개발환경을 사용해야하고, PG 모듈을 설치하는 등 절차가 복잡했지만, 아임포트를 사용하면서 간결하고  REST API를 통해 쉽게 결제를 연동할 수 있다. 

위 이미지의 1, 2, 5, 7번 과정만 신경쓰면 된다.  카드정보가 잘못되었다면 카드사 모듈 창에서 입력 시, 인증 오류가 날 것이다. 결제 정보가 잘못되었다던가, 카드 한드가 초과되었다던가, 사용자 변심으로 결제가 취소했다면 결제 단계에서 에러가 난다.  


### 결제 기능 확장성

토스에서도 결제 API를 제공한다. 하지만, 나중에 '구독'이란 정기 결제를 도입할 상황에서 아임포트는 정기 결제 기능을 제공하고 토스에서는 그렇지 않기 때문에, 추후 결제 기능 확장성을 고려할 때 아임포트를 선택했다.


### 편리한 복수 PG 사용  

연동되는 PG 선택도 [iamport admin](https://admin.iamport.kr/auth/signin)에서 로그인하여 테스트 버전으로 자유롭게 선택할 수 있다.  

### 잘 정리된 api 문서

[아임포트 api 문서](https://api.iamport.kr/) 를 보면 아임포트 API가 체계적으로 정리되어 있다.

적절한 api url에 필요한 정보만 보내면 결제 정보를 조회할 수 있다.  



<br>

---

# Reference 

- [한국 전자결제 서비스 흐름](https://github.com/iamport/iamport-manual/blob/master/%EC%9D%B8%EC%A6%9D%EA%B2%B0%EC%A0%9C/background.md)  
- [Django에서 아임포트를 통해 결제 구현하기 - (1) 아임포트 세팅 및 결제 구조](https://skyseven73.tistory.com/17)  
- [iamport admin](https://admin.iamport.kr/auth/signin)
- [아임포트 api 문서](https://api.iamport.kr/)   