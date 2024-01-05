---
title: "[TIL] HTTP basic"
date: 2022-03-09T17:56:30+09:00
draft: false
summary: HTTP란 무엇이고, HTTP의 특징인 클라이언트 서버 구조, stateless, connectionless, HTTP mesage에 대해 알아본다.
tags: ["TIL", "Network", "HTTP"]
categories: "Network"
---

# 0. Introduction

- HTTP 학습내용의 기본 출처: 김영한님의 [모든 개발자를 위한 HTTP 웹 기본지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC) 이다.
- 강의를 듣고 정리한 내용과 모르는 부분에 대한 추가 내용을 합쳐 올린다.
- 이 강의는 HTTP에 대한 웹 기본지식을 설명하는 강의이므로, 내용이 간략할 수 있다.

- 학습 이유: 프레임워크를 사용하여 웹 개발을 배우기 전에, HTTP에 대해 기본적인 지식을 알고자 HTTP 공부를 시작한다. 이 강의에 대해 공부 후, 네트워크 전반에 대해 공부한다.

&nbsp;

---

# 1. 모든 것이 HTTP

## 1.1 HTTP란? (**지금은 HTTP 시대!**)

: 링크를 통해 HTML 같은 문서를 연결할 수 있는 프로토콜을 의미한다.
하지만, 이제는 문서 뿐만 아니라 **HTTP 메세지에 모든 것을 전송**한다.

- HTML, TEXT
- IMAGE, 음성, 영상, 파일
- JSON, XML (API)
- 거의 모든 형태의 데이터를 저장하여 전송 가능하다.
- 서버 간에 데이터를 주고 받을 때도 대부분 HTTP를 사용한다.

&nbsp;

## 1.2 HTTP 역사 (HTTP/1.1을 기준으로 학습)

- HTTP/0.9 1991년: GET 메서드만 지원, HTTP 헤더X
- HTTP/1.0 1996년: 메서드, 헤더 추가
- **HTTP/1.1 1997년: 가장 많이 사용, 우리에게 가장 중요한 버전**
  - HTTP 강의는 이 버전을 기준으로 설명한다.
  - RFC2068 (1997) -> RFC2616 (1999) -> RFC7230~7235 (2014)
  - 현재는 RFC7230 버전부터 본다.
- HTTP/2 2015년: 성능 개선
- HTTP/3 진행중: TCP 대신에 UDP 사용, 성능 개선

&nbsp;

## 1.3 기반 프로토콜

- `TCP` 기반으로 작동하는 프로토콜은 `HTTP/1.1`, `HTTP/2` 다.
- `UDP` 기반으로 작동하는 프로토콜은 `HTTP/3` 다.
  - 기본적으로 HTTP/1.1에서 개선된 것이므로 우리는 HTTP/1.1을 공부한다.
- 현재 HTTP/1.1 주로 사용한다.
  - HTTP/2, HTTP/3도 점점 증가하고 있다.

> [HTTP/3가 UDP 기반인 이유]  
> 기본 TCP는 3 way handshake로 신뢰성이나 연결성이 보장되지만, 속도가 떨어진다. 그래서 UDP를 애플리케이션 레벨에서 재설계되어 나온게 `HTTP/3`다.

&nbsp;

- 사이트에서 기반 프로토콜을 확인하려면 검사(F12)에 들어가 `Network tab`을 클릭 한다.
- 하단에 Name tab을 오른쪽 마우스 클릭하여 Protocol을 체크한다.
- h3는 http/3 고, h2는 http/2 를 의미한다.
- 구글은 h3를 사용하고, 네이버는 h2를 사용한다.

![image](https://user-images.githubusercontent.com/78094972/157368318-ca06aad3-5926-4e3f-a4c9-7168daa68fc1.PNG)

![image](https://user-images.githubusercontent.com/78094972/157368348-06f8e79f-3f69-40bc-a24c-2cac341bd340.PNG)

&nbsp;

## 1.4 HTTP 특징

- 클라이언트 서버 구조
- 무상태 프로토콜(stateless), 비연결성
- HTTP 메시지
- 단순함, 확장 가능
  - HTTP는 단순하고, 스펙도 읽어볼만 하다.
  - HTTP 메시지도 매우 단순하다.
  - 크게 성공하는 표준 기술의 하나의 예로, 단순하지만 확장 가능한 기술이다.

각 특징들에 대해 알아보자.

&nbsp;

---

# 2. 클라이언트 서버 구조

- HTTP는 클라이언트와 서버 구조로 되어있다.
- 클라이언트는 HTTP 메세지를 만들어서 서버에 요청(request)을 보낸 후, 서버로부터 응답(response)이 올 때까지 기다린다.
- 서버는 클라이언트로부터 온 요청(request)에 대한 결과를 만들어서 응답(response)한다.

- [클라이언트 서버 구조가 중요한 이유]  
  - 독립적 구조 -> 각자의 역할에 집중
  - 옛날에는 클라이언트와 서버가 분리되어 있지 않고, 합쳐져 있었다. 그후 비지니스 로직과 데이터는 서버가 담당하여 집중하고, 클라이언트는 UI 사용성에 집중했다.
  - 이로 인해서 클라이언트와 서버는 각각 **_독립적으로_** 진화할 수 있었다.  

---

# 3. Stateful, Stateless


## 3.1 Stateful

### 'Stateful' 이란??

**'한 서버'가 클라이언트의 이전 상태를 보존(기억)하기 때문에**, 클라이언트의 요청에 응답하는 서버가 **_항상 같은 서버로 유지_** 되어야 하는 상태를 말한다.

#

### 'Stateful(상태 유지)'의 문제점은 무엇일까??

서버가 멈추거나 하는 여러 이유로 해당 서버를 쓸 수가 없는 상황이 발생했다. 다른 서버를 이용해야 한다. 이런 경우, 새로운 서버에서 이전 서버에 가지고 있던 상태값들을 가지고 있지 않아 에러가 발생된다.

- 예시: 고객을 클라이언트, 점원을 서버라고 생각하자.

  - 서버가 문제 없이 유지되는 경우

  ```yml
     고객: 이 `노트북` 얼마인가요?
     점원: 100만원 입니다. (노트북 상태 유지)

     고객: `2개` 구매하겠습니다.
     점원: 200만원 입니다. 신용카드, 현금중에 어떤 걸로 구매 하시겠어요?
            (노트북, 2개 상태 유지)

     고객: `신용카드`로 구매하겠습니다.
     점원: 200만원 결제 완료되었습니다. (노트북, 2개, 신용카드 상태 유지)
  ```

  - 서버가 바뀔 경우

  ```yml
    고객: 이 `노트북` 얼마인가요?
    점원 A: 100만원 입니다.

    고객: `2개` 구매하겠습니다.
    점원 B: ? 무엇을 2개 구매하시겠어요? (상태유지 X)

    고객: `신용카드`로 구매하겠습니다.
    점원C: ? 무슨 제품을 몇 개 신용카드로 구매하시겠어요? (상태 유지 X)

  ```

한 서버에서만 클라이언트의 상태를 기억하기 때문에, 서버가 변경되면 기존 서버에 저장된 클라이언트의 상태를 기억하지 못하여 에러가 발생했다. 그래서 **_항상 같은 서버로 유지_** 되어야 한다.

&nbsp;

---

## 3.2 Stateless

### Stateless 란??

**서버가 클라이언트의 이전 상태를 보존(기억)하지 않고**, 클라이언트가 요청할 때마다 **_매번 모든 상태 값들을 전달_** 하기 때문에, **_서버 변경이 용이_** 하다.

### 예시

고객을 클라이언트, 점원을 서버라고 생각하자.

- 서버가 문제 없이 유지되는 경우

  ```yml
    고객: 이 `노트북` 얼마인가요?
    점원: 100만원 입니다.

    고객: `노트북 2개` 구매하겠습니다.
    점원: 노트북 2개는 200만원 입니다. 신용카드, 현금중에 어떤 걸로 구매 하시겠어요?

    고객: `노트북 2개`를 `신용카드`로 구매하겠습니다.
    점원: 200만원 결제 완료되었습니다.
  ```

- 서버가 바뀔 경우

  ```yml
    고객: 이 '노트북' 얼마인가요?
    점원A: 100만원 입니다.

    고객: '노트북 2개' 구매하겠습니다.
    점원B: 노트북 2개는 200만원 입니다. 신용카드, 현금중에 어떤 걸로 구매 하시겠어요?

    고객: '노트북 2개'를 '신용카드'로 구매하겠습니다.
    점원C: 200만원 결제 완료되었습니다.
  ```

클라이언트가 모든 상태 값을 서버에 전달하기 때문에, 서버가 중간에 바뀌어도 문제가 되지 않는다. 항상 같은 서버로 유지될 필요없다. 그래서 **서버 변경이 용이**하기 때문에, **`stateless`** 는 **무한한 서버 증설이 가능**하다.

### 무한한 서버 증설로 인한 이점

그러면 서버 증설이 무한히 가능하다면 어떤 이점이 있을까???

같은 기능을 하는 서버들 안에서 서버의 **_수평 확장(scale out)_** 에 유리하다.

![image](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fb6939ea2-0d68-4986-bc51-92a9eaf1b0a6%2FUntitled.png&blockId=8367b500-6305-4a9d-972d-babc19570513)

### 🔆 stateless의 한계와 실무 방식

좋은 이점들이 많지만 실무 한계가 존재하여, 모든 것을 `stateless(무상태)` 로 할 수 없다 실무 한계가 존재한다.

- 무상태 예시: 로그인이 필요 없는 단순한 서비스 소개 화면
- 상태 유지 예시: 로그인
  - 로그인한 사용자의 경우, 로그인 했다는 상태를 서버에 유지해야 한다.
  - 일반적으로 **_브라우저 쿠키와 서버 세션 등을 사용해서_** 상태를 유지한다.

그래서 상태유지는 최소한만 사용하고, 최대한 무상태로 서버를 설계한다.

❗️ 수평 확장(scale out)과 수직 확장(scale up)의 차이: [스케일 아웃과 스케일 업](https://m.blog.naver.com/islove8587/220548900044)

&nbsp;

## 3.3 🔆 정리

- http는 stateful, stateless 두 상태를 모두 사용한다.    

- Stateful (상태유지): 중간에 서버가 변경되면 안된다.

  - 만약 서버가 변경되야 한다면 상태 정보를 전부 다른 서버에게 미리 알려줘야 한다.

- Stateless (무상태): 중간에 서버가 바뀌어도 된다.
  - 그래서 서버는 수평적 확장에 유리한다. (scale out)
  - **_하지만 모든 걸 무상태로 할 수 없기 때문에, 무상태로 서버를 최대한 설계하며, 상태 유지로 서버를 최소한 설계한다._**

&nbsp;

---

# 4. 비연결성 (connectionless)과 지속 연결

- HTTP/1.0 에서는 지속 연결(persistent connection) 기능이 없었지만, HTTP/2.0 부터는 추가되었다.  

- 지속 연결을 사용하기 위해서는 `Connection: keep-alive` 을 HTTP 헤더에 추가한다.  

## 4.1 연결을 유지하는 모델

![image](https://user-images.githubusercontent.com/78094972/157386174-01e9379a-9c31-4423-8043-fb25905a794d.PNG)

- TCP/IP 연결로 새로운 클라이언트와 연결하면서 이전 클라이언트와의 연결을 유지한다.
- 연결된 클라이언트가 놀고 있어도 서버가 유지해야 하는게 단점이다.
- 왜냐하면 서버의 자원이 연결을 유지하는데 **_계속 소모_** 되기 때문이다.

&nbsp;

## 4.2 연결을 유지하지 않는 모델 (비연결성)

![image](https://user-images.githubusercontent.com/78094972/234801264-85af6185-60bf-466a-9027-1334306d70f9.png)

- TCP/IP 연결 후, 클라이언트와 서버의 단 하나의 요청 응답 흐름이 끝나면 연결을 바로 종료한다.
- 그리고 다른 클라이언트와 연결 시, 이전 클라이언트와의 연결은 유지하지 않는다.
- 즉, 서버는 연결 유지를 하지 않아 **_최소한의 자원만 사용_** 할 수 있다.

&nbsp;

### HTTP의 비연결성 장점

- HTTP는 기본이 연결을 유지하지 않는 모델이고,
- 일반적으로 초 단위 이하의 빠른 속도로 응답한다.
- 그래서 1시간 동안 수천명이 서비스를 사용해도 실제 서버에서 동시에 처리하는 요청은 수십개 이하로 매우 적다.
  - 예) 웹 브라우저에서 계속 연속해서 검색 버튼을 누르지 않는다.
- **_즉, 서버 자원을 매우 효율적으로 사용할 수 있다._**

&nbsp;

### 비연결성의 단점과 해결 방법

- 단점
  - TCP/IP 연결을 새로 맺어야 하기 때문에, 3 way handshake 시간이 추가된다.
  - 웹 브라우저로 사이트를 요청하면 HTML 뿐만 아니라 JavaScript, css, 추가 이미지 등 수많은 자원이 함께 다운로드된다.
- 해결 방법
  - 지금은 `HTTP 지속 연결(Persistent Connections)`로 문제 해결했다.
  - HTTP/2 와 HTTP/3에서 더 많은 최적화를 한다.

&nbsp;

## 4.3 HTTP 지속 연결: 비연결성의 한계 해결 방법

비연결성의 한계를 해결한 방법인 `HTTP 지속 연결`에 대해 알아보자.

HTTP 초기에는 모든 자료에 대해서 비연결성으로 '연결 -> 응답 -> 종료' 를 반복하여, 시간이 대략적으로 1초 가량 소모되었다고 한다.

아래 이미지를 참조하자.

![image](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F37024fde-ab57-4953-9fd1-62dda7951b1f%2FUntitled.png&blockId=1fca311e-5841-45d2-8770-a9821f766e86)

&nbsp;

그러면 HTTP 지속 연결로 어떻게 변했을까??

![image](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F46a04f27-85b1-4573-931a-88e7df14c211%2FUntitled.png&blockId=318906c9-6dfc-4a2e-a813-0d08920d69ac)

클라이언트는 서버와 연결을 한 다음, 필요한 자원들을 모두 다운받을 때까지 요청/응답이 반복된 뒤 종료된다. 

옛날에는 문서를 주로 주고 받았다면 시대가 발전해가면서 이미지, 동영상 이외의 파일을 보내는 작업들이 많아지면서 지속 연결이 필요해졌다.

또한, HTTP/2,3으로 오면서 더 빨라졌다. 특히, HTTP 3으로 오면서 UDP를 사용하여 연결 속도 자체도 줄어들었다.

지속 연결을 사용하고 싶다면 http header에 `connection: keep-alive`를 담아 보낸다. 

&nbsp;

### 4.4 🔆 실무에서 HTTP 지속 연결하는 경우

**_실무 상황에서 특정 시간에 발생하는 대용량 트래픽의 경우, 수만명이 동시 요청하기 때문에 무상태와 HTTP 지속 연결로 서버를 설계해야 대응할 수 있는 부분이 매우 많아진다._**

- 예) 선착순 이벤트, 명절 KTX 예약, 학과 수업 등록, 선착순 할인 이벤트

&nbsp;

---

# 5. HTTP 메시지

- HTTP 메시지 구조를 알아보자.
  - 공백 라인은 아래 순서로, 필수로 존재해야 한다.

![image](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1c1ade79-e244-4886-953b-ba3c12b4f115%2FUntitled.png&blockId=1f0cf7a3-cd02-4903-92ed-e9782fe18f93)

&nbsp;

## 5.1 시작 라인(start line)

- start line은 요청 메시지와 응답 메시지 로 나눠진다.

- start line = request - line (요청 메시지) / status - line (응답 메시지)
  - request-line = method SP(공백) request-target SP HTTP-version CRLF(엔터)
  - status-line = HTTP-version SP status-code SP reason-phrase CRLF

### 5.1.1 요청 메시지

![image](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F9963de80-40e6-4cf3-a0cb-b048ab516fbc%2FUntitled.png&blockId=e21b890c-1a01-4393-8025-55d95e9d0705)

- start line = **request - line (요청 메시지)** / status - line (응답 메시지)

  - **request-line** = method SP(공백) request-target SP HTTP-version CRLF(엔터)

  - HTTP method (**GET** /search?q=hello&hl=ko HTTP/1.1)

    - 종류: GET, POST, PUT, DELETE ...
    - 서버가 수행해야 할 동작 지정
      - GET: 리소스 조회 / POST: 요청 내역 처리

  - request-target (GET **/search?q=hello&hl=ko** HTTP/1.1)

    - absolute-path[?query] (절대경로[?쿼리])
    - 절대경로= "/" 로 시작하는 경로
    - 참고: \*, <http://...?x=y> 와 같이 다른 유형의 경로지정 방법도 있다.

  - HTTP verison (GET /search?q=hello&hl=ko **HTTP/1.1**)

&nbsp;

### 5.1.2 응답 메시지

![image](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F085e5d58-f33b-4406-999f-e07072372050%2FUntitled.png&blockId=cf01f06f-02d7-47a5-be2b-d21d8d042dcf)

- start line = request - line (요청 메시지) / status - line (응답 메시지)
  - status-line = HTTP-version SP status-code SP reason-phrase CRLF
  - HTTP version
  - HTTP 상태 코드: 요청 성공, 실패를 나타냄
    - 200: 성공
    - 400: 클라이언트 요청 오류
    - 500: 서버 내부 오류
  - 이유 문구: 사람이 이해할 수 있는 짧은 상태 코드 설명 글

&nbsp;

## 5.2 HTTP header

> **header - field = field - name ":" OWS field - value OWS**  
> (OWS: 띄어쓰기 허용)

- **field - name: 대소문자 구분 없음**
- field - value: 대소문자 구문 있음
- 용도
  - **HTTP 전송에 필요한 모든 부가정보가 담겨져 있다.**
    - 예) 메시지 바디의 내용, 크기, 압축, 인증
    - 예) 요청 클라이언트(브라우저) 정보, 서버 애플리케이션 정보, 캐시 관리 정보
  - 표준 헤더가 너무 많다. (<https://en.wikipedia.org/wiki/List_of_HTTP_header_fields>)
  - 필요한 경우, 임의의 헤더 추가 가능

&nbsp;

## 5.3 HTTP message body

- **실제 전송할 데이터**
- HTML 문서, 이미지, 영상, JSON 등등 byte로 표현할 수 있는 모든 데이터 전송 가능

&nbsp;

---

# HTTP 정리

- HTTP 메시지에 모든 것을 전송한다.
- HTTP 역사: HTTP/1.1을 기준으로 학습한다.
- 클라이언트 서버 구조이다.
- stateful과 무상태 프로토콜(stateless) 모두 사용한다.  
- HTTP 메시지
- 단순하며 확장 가능하다.
- 지금은 HTTP 시대다.

---

# Reference

- [모든 개발자를 위한 HTTP 웹 기본지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC)
- [HTTP 기본](h<https://catsbi.oopy.io/5c0b482c-b427-4052-9030-d2be0810eeb6)>
- [스케일 아웃과 스케일 업](https://m.blog.naver.com/islove8587/220548900044)
