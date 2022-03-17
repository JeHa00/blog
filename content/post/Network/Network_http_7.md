---
title: "[TIL] Network HTTP Header 1"
date: 2022-03-17T20:17:50+09:00
draft: true
summary: representation, 콘텐츠 협상, 전송 방식, 일반 정보, 특별 정보, 인증에 대해 알아본다.
tags: ["TIL", "python"]
categories: ["개발-dev"]
---

# Intro

> 1. [표현](#1-표현)
> 2. [콘텐츠 협상](#2-콘텐츠-협상)
> 3. [전송 방식](#3-전송-방식)
> 4. [일반 정보](#4-일반-정보)
> 5. [특별한 정보](#5-특별-정보)
> 6. [인증](#6-인증)
> 7. [쿠키](#7-쿠키)

<br>

- HTTP 학습내용의 기본 출처: 김영한님의 [모든 개발자를 위한 HTTP 웹 기본지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC)
- 강의를 듣고 정리한 내용과 모르는 부분에 대한 추가 내용을 합쳐 올린다.
- 이 강의는 HTTP에 대한 웹 기본지식을 설명하는 강의이므로, 내용이 간략할 수 있다.

<br>

- 학습 이유: 프레임워크를 사용하여 웹 개발을 배우기 전에, HTTP에 대해 기본적인 지식을 알고자 HTTP 공부를 시작한다. 이 강의에 대해 공부 후, 네트워크 전반에 대해 공부한다.

<br>

- 이번 chapter에서는 HTTP header 여러 종류에서 주로 사용하는 헤더에 대해 알아보겠다.

- HTTP header의 용도는 [[TIL] Network HTTP basic](https://jeha00.github.io/post/network/network_http_3/#52-http-header)을 참고한다.

### RFC2616(과거) - HTTP Header 분류

- HTTP header 종류에 대해 알아보기 전, 과거 'RFC2616' 일 때 헤더 분류를 살펴보자.

<p align="center"> <image src ="https://mdn.mozillademos.org/files/13821/HTTP_Request_Headers2.png"/></p>

- General 헤더: 메시지 전체에 적용되는 정보, 예) Connection: close
- Request 헤더: 요청 정보, 예) User-Agent: Mozilla/5.0 (Macintosh; ..)
- Response 헤더: 응답 정보, 예) Server: Apache
- **_Entity 헤더_**: 엔티티 바디 정보, 예) **Content-Type**: text/html, **Content-Length**: 3423

<br>

### RFC2616(과거) - HTTP body

<p align="center"> <image src ="https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F703b2736-8fe6-4cd6-8af8-200754ed916f%2FUntitled.png&blockId=e02a97a0-124b-4aae-a81c-c6a27d8c9c7b"/></p>

- 메세지 본문(message body)은 엔티티 본문(entity body)을 전달하는데 사용
- entity body는 요청이나 응답에서 전달할 실제 데이터
- **entity header**는 **entity 본문**의 데이터를 해석할 수 있는 정보를 제공한다.
  - 데이터 유형(html, json), 데이터 길이, 압축 정보 등등

<br>

### RFC2616 폐지 그리고, RFC7230~7235 등장

- RFC2616이 폐지되고, RFC7230~7235가 등장하면서
- Entity 라는 표현이 Representation 으로 바꼈다.
- 그리고, Representation 이란 representation Metadata와 Representation Data를 합친 걸 의미한다.
- `엔티티(Entity)` -> `Representation`
  - `Representation` = `Representation Metadata` + `Representation Data`

<br>

### RFC7230 - HTTP Body

<p align="center"> <image src ="https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc3b31baa-c5b8-40be-b317-286dd27d3c8a%2FUntitled.png&blockId=fb4c7c57-1f47-4027-a849-131dd8ca043c"/></p>

- 메시지 본문(message body)을 통해 표현 데이터를 전달한다.
- 메시지 본문을 다른 말로 `페이로드(payload)`라 한다.
- `representation`은 요청이나 응답에서 전달할 실제 데이터
- `representation header`는 `Representation Data`를 해석할 수 있는 정보를 제공한다.
  - 데이터 유형(html, json), 데이터 길이, 압축 정보 등등
- 참고: Representation header는 representation metadata 와 payload message를 구분해야 하지만, 여기서는 생략한다.

<br>

- 그러면 이 `representation`이 뭔지 알아보자.

---

# 1. 표현(representation)

<p align="center"> <image src ="https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc0fbdc83-b061-40a5-86bf-4df7184db205%2FUntitled.png&blockId=8ca73a3f-e079-4d94-9460-2c06876fe5b1"/></p>

## 1.0 Representation header 란??

> client와 server 간에 주고 받는 resource의 data를 어떻게 표현할지 결정하는 header

- 예) DB에 있는 binary data를 바로 서버에 전송하는 게 아니라, HTML 또는 XML 또는 JSON 형태로 전달한다.

- Representation header는 전송, 응답 둘 다 사용한다.

- 그래서 representation header 에는 여러 정보들이 담긴다.
  - Content-Type: 표현 데이터의 형식 설명
  - Content-Encoding: 표현 데이터의 압축 방식
  - Content-Language: 표현 데이터의 자연 언어
  - Content-Length: 표현 데이터의 길이

<br>

## 1.1 Content-Type

> 표현 데이터의 형식 설명

<p align="center"> <image src ="https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fabcd2b4a-f779-4a3b-a4c2-935e1bb390e7%2FUntitled.png&blockId=ba10c03c-2825-46f3-a462-bf44663d3d3c"/></p>

- 미디어 타입, 문자 인코딩
- 예)
  - text/html; charset =utf-8
  - application/json
  - image/png

<br>

---

## 1.2 Content-Encoding

> 표현 데이터의 압축 방식 설명

<p align="center"> <image src ="https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fca2848bb-cf2e-4d9a-adb7-4b5656ac3c72%2FUntitled.png&blockId=1a501a0c-1d9b-4561-9679-d7ef4c0f2b9b"/></p>

- 표현 데이터를 압축하기 위해 사용
- 데이터를 전달하는 곳에서 압축 후 인코딩 헤더 추가
- 데이터를 읽는 쪽에서 인코딩 헤더의 정보로 압축 해제
- 예)
  - gzip
  - deflate
  - identity

<br>

---

## 1.3 Content-Language

> 표현 데이터의 자연어 설명

<p align="center"> <image src ="https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fad9247a8-4844-40ec-bf7f-2048792f0a22%2FUntitled.png&blockId=030f6bec-8fe3-4822-aab5-0339fea2e68d"/></p>

- 표현 데이터의 자연 언어를 표현
- 예)
  - ko
  - en
  - en-US

<br>

---

## 1.4 Content-Length

> 표현 데이터의 길이 설명

<p align="center"> <image src ="https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F59ba7df8-58a4-42b1-942c-eba57a3480a8%2FUntitled.png&blockId=4889090d-a9fa-4580-9e63-2e4757a2e506"/></p>

- 바이트 단위
- Transfer-Encoding(전송 코딩)을 사용하면 Content-Length를 사용하면 안된다.

<br>

---

# 2. 콘텐츠 협상

> 클라이언트가 선호하는 표현을 서버에게 요청하는 것

- 서버에 요청 사항이 다양하다면, 우선 순위에 맞춰 서버에서 만든다.

- 클라이언트가 요청할 때 작성하기 때문에, 요청 시에만 사용한다.

- 협상 헤더 종류
  - Accept: 클라이언트가 선호하는 미디어 타입 전달
  - Accept-Charset: 클라이언트가 선호하는 문자 인코딩
  - Accept-Encoding: 클라이언트가 선호하는 압축 인코딩
  - Accept-Language: 클라이언트가 선호하는 자연 언어

<br>

## Accept-Language 적용 전과 후

- 적용 전

<p align="center"> <image src ="
https://user-images.githubusercontent.com/78094972/158811059-a9b0e5b0-e523-4b99-927d-6d806dabb014.PNG
"/></p>

- 적용 후

<p align="center"> <image src ="https://user-images.githubusercontent.com/78094972/158811065-38f049ee-b265-40c8-82ac-b9d6c4205f74.PNG"/></p>

- 복잡한 예시

<p align="center"> <image src ="https://user-images.githubusercontent.com/78094972/158811068-5b5cf032-2459-481e-aff0-70c0164dcd3d.PNG"/></p>

## 협상과 우선순위 (Quality Values(q))

<br>

### 협상과 우선순위 첫 번째

> 첫 번째: Quality Values(q)가 높을 수록 우선순위가 높다.

- Quality Values(q) 값 사용

  - 0~1, 클수록 높은 우선순위
  - 생략하면 1

- Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7

  - 1. ko-KR;q=1 (q생략)
  - 2. ko;q=0.9
  - 3. en-US;q=0.8
  - 4. en:q=0.7

- 인터넷 창 -> 검사 -> Network -> Headers -> Request Headers 창에 들어가면 사용하는 도메인의 우선순위를 볼 수 있다.

<p align="center"> <image src ="https://user-images.githubusercontent.com/78094972/158811071-64ccf151-192e-48c3-a048-94ead11a9dcb.PNG"/></p>

### 협상과 우선순위 두 번째

> 두 번째: 구체적인 것이 우선된다.

- Accept: **text/_, text/plain, text/plain;format=flowed, _/\***

  - text/plain;format=flowed
  - text/plain
  - text/\*
  - \*/\*

- Media Type 우선도
  - Accept: text/\*;q=0.3, text/html;q=0.7, text/html;level=1,text/html;level=2;q=0.4, \*/\*;q=0.5

<p align="center"> <image src ="https://user-images.githubusercontent.com/78094972/158812968-7956f625-7bdc-45a7-ac1a-86c1463e6201.PNG
"/></p>

<br>

---

## 3. 전송 방식

- 전송 방식에는 4 종류가 있다.
  - 단순 전송(Content-Length)
  - 압축 전송(Content-Encoding)
  - 분할 전송(Transfer-Encoding)
  - 범위 전송(Range, Content-Range)

### 3.1 단순 전송(Content-Length)

<p align="center"> <image src ="https://user-images.githubusercontent.com/78094972/158811074-b3b8b9c0-543d-4a94-939e-2a91d723d6bc.PNG
"/></p>

- content의 길이를 알 수 있을 때 사용한다.
- 한 번에 요청하고,한 번에 받는다.

<br>

### 3.2 압축 전송(Content-Encoding)

<p align="center"> <image src ="https://user-images.githubusercontent.com/78094972/158811077-719df75e-0a38-4c55-aa3c-4a3a97052871.PNG"/></p>

<br>

### 3.3 분할 전송(Transfer-Encoding)

<p align="center"> <image src ="https://user-images.githubusercontent.com/78094972/158811080-d9a60710-66b7-4a83-8282-8e7e4453d9e9.PNG"/></p>

- 용량이 커서 한 번에 보내면 받는데 시간이 걸리기 때문에, 분할하여 보내서 오는 대로 바로 구현한다.
- 이 때는 content-length를 넣으면 안된다. 전체 길이를 알 수 없기 때문이다.
- 5 byte 씩 나눠서 보내고, 마지막에는 보낼 게 없어서 0이다.

<br>

### 3.4 범위 전송(Range, Content-Range)

<p align="center"> <image src ="https://user-images.githubusercontent.com/78094972/158811083-61b33632-0c48-4caa-b02f-0fab727c2c7f.PNG"/></p>

<br>

---

## 4. 일반 정보

- From: 유저 에이전트의 이메일 정보

  - 일반적으로 잘 사용되지 않음
  - 검색 엔진 같은 곳에서, 주로 사용
  - 요청에서 사용

- Referer: 이전 웹 페이지 주소

  - 유입 경로 분석을 위해 많이 사용한다.
  - 현재 요청된 페이지의 이전 웹 페이지 주소
  - A -> B로 이동하는 경우 B를 요청할 때 Referer: A 를 포함해서 요청
  - Referer를 사용해서 유입 경로 분석 가능
  - 요청에서 사용
  - 참고: referer는 단어 referrer의 오타

- User-Agent: 유저 에이전트 애플리케이션 정보

  - 검사(F12) -> Network -> Headers -> User-Agent 확인
  - user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/
    537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36
  - 클리이언트의 애플리케이션 정보(웹 브라우저 정보, 등등)
  - 통계 정보
  - 어떤 종류의 브라우저에서 장애가 발생하는지 파악 가능
  - 요청에서 사용

- Server: 요청을 처리하는 ORIGIN 서버의 소프트웨어 정보

  - Server: Apache/2.2.22 (Debian)
  - server: nginx
  - 응답에서 사용
  - Origin 서버란??
    - 여러 node들을 거치고,최종적으로 나의 요청을 처리하는 서버를 ORIGIN 서버라 한다.

- Date: 메시지가 생성된 날짜
  - Date: Tue, 15 Nov 1994 08:12:31 GMT
  - 응답에서 사용

<br>

---

## 5. 특별 정보

- Host: 요청한 호스트 정보(도메인)
- Location: 페이지 리다이렉션
- Allow: 허용 가능한 HTTP 메서드
- Retry-After: 유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간

### 5.1 Host

> GET /search?q=hello&hl=ko HTTP/1.1  
> Host: www.google.com

- 요청에서 사용
- **_필수_**
- 하나의 서버가 여러 도메인을 처리할 때
  - 하나의 IP 주소에 여러 도메인이 적용되어 있을 때

<p align="center"> <image src ="https://user-images.githubusercontent.com/78094972/158818365-4b9908e6-c36f-470c-99cd-8a41016d9312.PNG"/></p>

<br>

### 5.2 Location

> 페이지 리다이렉션

- 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, Location 위치로 자동 이동
  (리다이렉트)
- 응답코드 3xx에서 설명
- 201 (Created): Location 값은 요청에 의해 생성된 리소스 URI
- 3xx (Redirection):

<br>

### 5.3 Allow

> 허용 가능한 HTTP 메서드를 명시한다.  
> 하지만, 실제로 많이 구현되어 있지 않으므로 이런 게 있다 정도만 알자.

- 405 (Method Not Allowed) 에서 응답에 포함해야 한다.
- Allow: GET, HEAD, PUT

<br>

### 5.4 Retry-After

> 유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간  
> 하지만, 실제로는 사용하기 어렵다.

- 503 (Service Unavailable): 서비스가 언제까지 불능인지 알려줄 수 있음
- Retry-After: Fri, 31 Dec 1999 23:59:59 GMT (날짜 표기)
- Retry-After: 120 (초단위 표기)

<br>

---

## 6. 인증

- Authorization: 클라이언트 인증 정보를 서버에 전달한다.

  - Authorization: Basic xxxxxxxxxxxxxxxx
  - 인증 관련해서 여러 매커니즘이 있다. 각 매커니즘마다 넣는 헤더가 다르다. 추가적으로 알아보자.

- WWW-Authenticate: 리소스 접근시 필요한 인증 방법 정의한다.

  - 401 Unauthorized 응답과 함께 사용한다.
  - WWW-Authenticate: Newauth realm="apps", type=1,
    title="Login to \"apps\"", Basic realm="simple"
    - 인증할려면 : 이후의 내용들을 참고해서 인증 방법을 만들라는 의미다.

<br>

---

## 7. 쿠키

<br>

---

# Reference

- [모든 개발자를 위한 HTTP 웹 기본지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC)
- [HTTP 헤더1- 일반 헤더](https://catsbi.oopy.io/39d2f2f6-4f69-4c93-a4b1-6af1d5afc3bb)
