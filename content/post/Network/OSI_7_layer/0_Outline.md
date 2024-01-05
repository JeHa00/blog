---
title: "[TIL]Web Application Basic study: OSI 7 layer outline"
date: 2022-08-30T18:50:58+09:00
draft: false
summary: OSI 7 layer는 전체적으로 무슨 층으로, 무슨 프로토콜로, 각 계층의 데이터 타입은 무엇이고, 각 주요 기기들은 무엇인지 통합적으로 알아본다.   
tags: ["TIL", "Network"]
categories: "Network"
---
# 0. Introduction: 웹 애플리케이션 기초

---

사용자가 눈에 보이는 부분을 개발하는 부분을 **프론트엔드** , 사용자 눈에 보이지 않는 부분을 개발하는 부분을 **백엔드** 라 한다.

프론트엔드는 코드 실행 결과를 눈으로 직접 바로바로 확인이 가능하다.  

하지만, **백엔드는 코드의 결과를 눈으로 확인하기 어렵다. 그래서 오류의 원인을 찾는 디버깅이 어렵다**.

**이 디버깅을 하기 위해서는 기반지식이 필요하다.** 입문자가 생각하는 개발은 코드라고 생각하지만, **실제 개발에 필요한 내용은 기반지식이다.**

- 기반지식
  - 클라이언트 - 서버 구조
  - 웹 서버 구조 설계
  - 네트워크

이번에는 네트워크에 대해 학습해본다.  

&nbsp;

# 네트워크 기초

서로 다른 컴퓨터 회사들의 컴퓨터들이 자유롭게 통신할 수 있도록, 그리고 네트워크 통신 중 어느 부분에서 문제가 발생했는지를 알 수 있도록 ISO(International Organization for Standardization, 국제표준화기구)에서 OSI (Open System Interconnection) Model이라는 표준 규격을 만들었다. 이 모델이 바로 OSI 7 계층이다.  


## 왜 표준 Model이 필요할까?
---

### Agnostic application 측면

이 Model 도 또한 거대한 네트워크 application이다. 단 다른 것들과 다른 것은 바로 agnostic application으로 모든 곳에서 작동가능한 애플리케이션이다.  

그러면 왜 네트워크와 관련하여 이 OSI 표준 모델(standard)을 왜 만든 것이고, 왜 필요한 것일까?

표준 모델이 없다면 애플리케이션을 만들 때 이 네트워크 영역까지 설계해서 만들어야 한다. 그러면 지금 세상에서는 당연하게 생각하는 Wi-Fi, LTE, fiber 를 통해서 통신하는 것이 어려울 수도 있다.

kernel 이 있는 이유와 동일하다. kernel이 존재하지 않는다면 모든 application은 hardware 관리 측면까지 설계해야 한다. 

대신해서 kernel이 하드웨어 관리를 해주는 거고, OSI 7 Model이 있어서 네트워크 통신을 구체적으로 신경쓰지 않아도 쉽게 사용할 수 있다.  


### 네트워크 장비 관리 측면

네트워크 장비를 업데이트하는데 있어서, 표준 모델이 다르면 업데이트하는데 있어서 매우 어려워진다. 달라진 모델에 맞춰서 호환성을 위해 다 업데이트를 여러 버전으로 해야하기 때문에 어렵다. 

하지만 표준 모델이 있고, 이를 다 준수하기 때문에 장비를 쉽게 사용할 수 있다.  

### layer마다 개별적인 개선 가능  (Decoupled Innovation)  

Model은 여러 Layer 별로 구축되어 있기 때문에, 한 layer만 개선해도 다른 layer에 영향을 미치지 않는다.  

예를 들어서 1계층에서 보다 더 빠른 방식의 전송 매체를 알게되어 이를 바꿔도 2계층과의 interface만 동일하게 유지하면 전혀 문제가 없다.  


## OSI 7 layer 개요
---

OSI 7 layer의 구성은 다음과 같다. 그리고, 이 **OSI 7 layer** 를 4개의 계층으로 단순화하여 표현한 것을 **TCP/IP 4 layer** 라 한다. 설명은 OSI 7 layer를 기준으로 한다.

그리고, 각 계층마다 통신하기 위해 사용되는 형식, 약속, 규약들이 존재하는데 이를 **Protocol(프로토콜)** 이라 한다.  

| OSI 7 layer | TCP/IP 4 layer | 사용되는 프로토콜 | 설명 | PDU(Protocol Data Unit) | 주요 네트워크 기기 |
| ---- | ---- | ---- | ---- | ---- | ---- |
|응용 계층| 4. 응용 계층| HTTP, FTP, SMTP, DNS | 애플리케이션 서비스 제공 | 메시지 혹은 데이터 | - |
|표현 계층| 4. 응용 계층| HTTP, FTP, SMTP, DNS | 문자코드, 압축, 암호화 방식으로 데이터 변환 | 메시지 혹은 데이터  | -  |
|세션 계층| 4. 응용 계층| HTTP, FTP, SMTP, DNS | 세션 연결/설정/해제와 통신 방식 결정 | 메시지 혹은 데이터  | - |
|[전송 계층](https://jeha00.github.io/post/network/osi_7_layer/4_layer/)| 3. 전송 계층|TCP, UDP | 신뢰성 있는 통신 구현 | segment | - |
|[네트워크 계층](https://jeha00.github.io/post/network/osi_7_layer/3_layer/)| 2. 인터넷 계층 | IP | 한 네트워크에서 다른 네트워크와 통신하기 위한 IP 주소 결정 | packet | 라우터 |
|[데이터링크 계층](https://jeha00.github.io/post/network/osi_7_layer/2_layer/)| 1. 네트워크 인터페이스 계층 | 이더넷, CMSA/CD | '네트워크 기기' 간의 데이터 전송과 물리 주소 결정 | frame | 스위치 |
|[물리 계층](https://jeha00.github.io/post/network/osi_7_layer/1_layer/)| 1. 네트워크 액세스 계층 | RS-232, RS-449 | 물리적 연결과 데이터를 전기 신호로 변환 | | 리피터, 허브 |

![image](https://t1.daumcdn.net/thumb/R1280x0/?fname=http://t1.daumcdn.net/brunch/service/user/axm/image/lEbuexy5CZ5WK6Xq5yZuwg_8tR4.png)

- 위 각 계층에 대한 설명에 추가 설명  

  - 1계층 ~ 2계층: 한 네트워크 내에서 데이터를 주고 받는 과정
  - 3계층부터는 네트워크 간 데이터를 주고 받는 과정  
  - 3계층: 여러 네트워크 간 데이터 전송을 위해 타 네트워크 주소 결정
  - 4계층: 3계층에서 데이터가 유실되거나 더 정확하게 데이터를 전송하기 위한 정보 추가  
  - 5 ~ 7 계층: 특정 애플리케이션까지 도달하기 위한 데이터 정보 추가  

&nbsp;

### 송신 및 수신 순서

- 송신할 때 순서: 하위 계층으로 보내는 과정
  - 응용 계층 -> 표현 계층 -> 세션 계층 -> 전송 계층 -> 네트워크 계층 -> 데이터링크 계층 -> 물리 계층

- 수신할 때 순서는 송신 순서의 역방향으로 진행된다. (상위 계층으로 보내는 과정)

&nbsp;

### 헤더(Header), 캡슐화(Encapsulation) 그리고 역캡슐화(Decapsulation)

![image](https://user-images.githubusercontent.com/78094972/236663376-e5648e74-186b-4fbe-b305-3ae6c6d0a48f.png)

- from: [예술하는 개발자 - 캡슐화, 역캡슐화, PDU](https://www.youtube.com/watch?v=rBw_e7zQLMQ)  

우리가 현실세계에서 우편을 보낼 때 보낼 물건을 바로 보내는 게 아니라 우편 봉투에 넣고 주소 태그를 붙이는 것처럼 소프트웨어 세계에서도 데이터가 각 계층을 통해 보내질 때, 계층을 한 번 이동할 때마다 각 계층에 해당되는  **header(헤더)** 가 붙여진다. 이 헤더에는 목적지 정보, 출발지 정보, 에러 체크 등 필요한 정보들이 담겨져 있다.

이 헤더는 전송되는 원본 데이터 앞에 추가적으로 붙는다.  

이렇게 송신 과정에서 데이터 앞에 헤더를 붙이는 것을 **Encapsulation(캡슐화)** 라고 한다.

그리고 이와 반대로 헤더를 제거하는 것을 **Decapsulation(역캡슐화)** 라고 한다.

캡슐화는 송신 과정에서 각 계층을 통과할 때마다 각 계층마다 일어나며, 역캡슐화는 수신 과정에서 각 계층을 통과할 때마다 각 계층마다 일어난다.  

&nbsp;

---

# Reference

- [나노디그리 러닝스푼즈: Python & Django backend course](https://learningspoons.com/course/detail/django-backend/)
- [osi 7 layer](https://brunch.co.kr/@lars/1)
- [네트워크 기초(1) - OSI 7계층이란?](https://losskatsu.github.io/os-kernel/network-basic01/#1-osi-7%EA%B3%84%EC%B8%B5%EC%9D%B4%EB%9E%80)  
- [예술하는 개발자 - 캡슐화, 역캡슐화, PDU](https://www.youtube.com/watch?v=rBw_e7zQLMQ)  
- [Udemy - Fundamentals of Networking](https://www.udemy.com/course/fundamentals-of-networking-for-effective-backend-design/)