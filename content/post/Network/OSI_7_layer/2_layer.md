---
title: "[TIL] Network OSI 7 layer: 2계층 데이터 링크 계층"
date: 2023-05-14T21:14:44+09:00
draft: false
summary: OSI 7 layer에서 2계층에 대해 더 깊이 알아보자.   
tags: ["TIL", "Network"]
categories: ["Network"]
---

# 네트워크 학습 내용 순서

1. [[TIL]Web Application Basic study: OSI 7 layer outline](<https://jeha00.github.io/post/network/osi_7_layer/0_outline/>)
2. [[TIL] Network OSI 7 layer: 1계층](<https://jeha00.github.io/post/network/osi_7_layer/1_layer/>)

이번 장에서는 데이터 링크(Data link) 계층인 2계층에 대해 알아보자.  

&nbsp;

# 1. 데이터 링크 계층(2계층) 개요  

---

네트워크 장치 간 데이터를 전송하는 기능과 절차를 제공하는 으로, 물리적 주소 설정, 흐름제어, 에러제어 등을 수행한다.

이 계층에서 '주요 네트워크 기기'는 **_스위치_** 이고, '주요 프로토콜'은 **_이더넷, CSMA/CD_** 다. '주요 데이터 단위'는 **_Frame_** 이다.

- Frame의 구성: 이더넷 헤더 + IP 헤더 + TCP 헤더 + 응용 헤더 + User data + 트레일러  

&nbsp;

# 2. 주요 프로토콜 첫 번째: 이더넷  

---

데이터를 전송하는 프로토콜, 네트워크를 구성하는 기술 방식  

- 대부분의 LAN은 이더넷 방식  
- ISO에서 국제 표준으로 지정
- 초기 네트워크 규격은 버스형이어서 **CSMA/CD** 기술이 사용되었다.
- 하지만 점차 데이터의 양이 매우 많아지면서 고속 네트워크 망이 필요하여 아래 기술이 현재 사용 중이다.
  - Gigabit Ethernet
  - 데이터의 전송속도가 1Gbps로 향상  
  - 기존의 이더넷과 호환
  - 스타넷(성형) 네트워크 토폴로지 이용  

### 이더넷 규격(참고)  

| 이더넷 규격 | 전송 속도 | 전송 매체 |기타 |
| ---- |---- |---- |---- |
| 10BASE5 | 10Mbps | 동축 케이블| 초기 이더넷의 규격|
| 10BASE2 | 10Mbps | 동축 케이블| 초기 이더넷의 규격|
| 10BASE-T | 10Mbps | UTP 케이블| |
| 100BASE-FX | 100Mbps | 광섬유 케이블||
| 1000BASE-SX | 1000Mbps | 광섬유 케이블||
| 1000BASE-LX | 1000Mbps | 광섬유 케이블||
| 10GBASE-T | 10Gbps | UTP 케이블|  |

- SX: Short wavelength / LX: Long ...
- SX와 LX는 전송 거리의 차이: 550m / 5Km

&nbsp;

# 3. 주요 데이터 단위: 프레임

---

## 3.1 이더넷 헤더와 트레일러

### 이더넷 헤더

이더넷 헤더에는 다음 정보가 포함되어 있다.

- 목적지 MAC 주소 + 출발지 MAC 주소 + 타입  
- 타입: 상위 계층의 프로토콜 종류를 구분하는 역할  (IPv4, IPv6..)

### 이더넷 트레일러

이더넷 트레일러에는 오류 발생 체큐 용도로 사용되어 **FCS(Frame Check Sequence)** 라 불린다.  이 프레임을 주고 받으면서 MAC 주소를 얻는다.

#

## 3.2 이더넷 프레임

### 프레임(Frame)이란?

이더넷 헤더와 트레일러가 붙으면서 캡슐화된 데이터를 **프레임** 이라 한다.  

### 캡슐화와 역캡슐화 시 프레임 변화

캡슐화 시 이더넷 헤더와 트레일러를 부착하지만, 역캡슐화 시 이더넷 헤더와 트레일러를 제거하면서 전송한 데이터를 받는다.

### 이더넷 프레임의 크기

이더넷 프레임의 크기는 64 ~ 1518 byte로, 이 이더넷헤더 + packet + 트레일러 3가지를 포함한 크기다.

각 부분의 크기는 다음과 같다.

- 이더넷 헤더: 14 Byte
  - 목적지 MAC 주소 (6 Byte) + 출발지 MAC 주소 (6 Byte) + 타입 (2 Byte)  
- packet: 46 ~ 1500 Byte
- 트레일러: 4 Byte

#

## 3.3 주요 식별자: MAC 주소

### MAC 주소란?

이더넷에서 특정 인터페이스를 식별하기 위한 48bit의 물리적인 주소다. 컴퓨터가 제조될 때 새겨지는 전 세계에서 유일한 번호로서, 장치의 실제 주소를 식별하기 때문에, **_MAC 주소 = 물리 주소 = 하드웨어 주소_** 로 생각하면 된다.

- 물리 주소, 하드웨어 주소라고도 불린다.  

### 사용 용도

허브 또는 스위치에 2대 이상의 컴퓨터가 연결되어 있을 때, 스위치는 어느 포트에는 어느 MAC 주소로 이어지는지에 대한 정보가 담겨 있는 **MAC 주소 테이블** 을 사용하여 무슨 컴퓨터에 데이터를 전달할지를 결정한다.  

- 스위치가 동작하는 과정에서 MAC 주소가 MAC 주소 테이블에 저장된다.  

- 표기: 16진수로 총 48bit
  - ex) 11-AA-11-AA-11-AA, 11:AA:11:AA:11:AA, 11AA. 11AA. 11AA  
  - 앞 24bit: 랜 카드 제조사 번호  
  - 뒤 24bit: 제조사가 붙인 일련 번호  

### MAC 주소와 IP 주소의 차이

||MAC(Media Access control) | IP(Internet Protocol) |
| ---- |---- |---- |
| 개념 | 미디어 접속 제어 주소 | 인터넷 프로토콜 주소 |
| 목적 | 장치의 실제 주소 식별   (**물리적 주소**) | 인터넷에서의 기기 간 연결을 식별(**논리적 주소**) |
| 주소 표기와 크기 | 16진수 표기, 48비트 | IPv4: 10진수, 32비트 // IPv6: 16진수 128비트 |
| 공급자 | NIC 카드 제조업체에서 지정 | 네트워크 관리자 또는 ISP   (Internet Service Provider) |  

&nbsp;

# 4. 주요 네트워크 기기: 스위치

---

### 스위치란?

스위치에 연결된 장치들끼리 이더넷을 이용한 '하나의 네트워크'를 구성하는 기기로, 기존 허브에서 MAC 주소 테이블을 활용한 스위칭 기능을 추가해서 Point to Point 접속이 가능해져 속도 저하를 개선했다.

- 더미 허브와 외관상 차이 거의 없다.  

- 스위치는 연결된 장치의 물리적인 주소인 **_MAC 주소를 구분해서 해당 주소가 있는 장치로 데이터를 보내주는 장비_** 다.  

### MAC 주소 테이블 원리  

더비 허브의 문제점을 극복한 방법은 'MAC 주소 테이블'이다. 각 port 별 연결된 장치의 Mac 주소를 이 테이블에 이미 저장해놓기 때문에 연결된 모든 포트에 데이터를 전달할 필요 없이 해당되는 Mac 주소에 연결된 포트로만 데이터를 보내면 된다.  

### 스위치 동작

- 플러딩(flooding): MAC 주소 테이블에 frame header에 있는 목적지 MAC 주소 정보가 없을 경우, 전송 받은 포트를 제외한 다른 모든 포트로 데이터를 보내는 동작  
- 필터링(filtering): 데이터의 도착지 MAC 주소와 MAC 주소 테이블의 MAC 주소와 비교한 후, 일치하지 않을 경우 전송하지 않는 동작  
- 포워딩(forwarding): 일치할 경우 전송하는 동작  

&nbsp;

# 5. 반이중 통신, 전이중 통신

---

| 통신 종류 | 반이중 통신 | 전이중 통신 |
| ---- | ---- | ---- |
| 정의 | 데이터의 송수신을 '번갈아가며' 하는 방식 | 데이터의 송수신을 '동시에' 하는 방식 |
| 이더넷 사용 시기 | 초기 | 현재 |

- 그래서 반이중 통신은 데이터 송수신이 동시에 불가능하다.  

&nbsp;

# 6. 주요 프로토콜 두 번째: CSMA/CD

---

CSMA/CD 란 Carrier Sense Multiple Access/Collision Detection 의 약어다.  

이 개념이 등장한 배경은 네트워크 망 형태가 버스형처럼 되어 있어서 여러 대의 기기가 동시에 데이터를 전송하면 충돌이 일어난다. 이를 해결하고자 나온 것이 CSMA/CD 다.

CS(Carrier Sense)는 '반송파 감지', MA(Multiple Access)는  '다중 접속', CD(Collision Detection)는 '충돌 탐지' 를 의미하는데 이를 해석하자면 처음에는 CS의 의미대로 케이블이 현재 사용 중인지 감지한다. 만약 케이블에 트래픽이 없으면 충돌 없이 데이터는 성공적으로 전송된다.

하지만, 다른 컴퓨터에서도 데이터를 보내서 충돌이 감지된다면 그 즉시 데이터의 전송을 중지하고 각 컴퓨터에서는 랜덤 시간이 설정되어 기다린 후 데이터를 전송한다. 컴퓨터 A, B에서 각각 서로 충돌되는 방향으로 데이터를 보내어 충돌이 감지된다면 A는 10초를, B는 20초를 기다린 후 데이터를 재송신한다.  

### CSMA/CD 알고리즘

![image](https://private-user-images.githubusercontent.com/78094972/240250282-34581795-5c15-4072-ac71-1e7095b00a0b.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJrZXkiOiJrZXkxIiwiZXhwIjoxNjg0ODQxMDUyLCJuYmYiOjE2ODQ4NDA3NTIsInBhdGgiOiIvNzgwOTQ5NzIvMjQwMjUwMjgyLTM0NTgxNzk1LTVjMTUtNDA3Mi1hYzcxLTFlNzA5NWIwMGEwYi5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBSVdOSllBWDRDU1ZFSDUzQSUyRjIwMjMwNTIzJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDIzMDUyM1QxMTE5MTJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1kMTc0ODkyYjFiZjRhYTc0OGVmNDFiMjFiMGMwYjJmMjBjMGI3ZDhiZjM5MjljYWJiMTE2NGMyMzMwNjYyMmI5JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.o6eALp3ZKoXN6plJd_fvwFCJZoZy8f2jOdDjl71O_Yg)

&nbsp;

# 7. 랜카드(NIC: Network Interface Card)

---

### NIC가 동작하는 계층  

NIC 카드는 랜카드라고도 하는데, NIC 제조 업체에서 MAC 주소를 지정하고 이 주소를 랜카드에 기입한다.  

그렇다면 NIC는 1계층에서 동작하는 건가?

그렇다.

랜카드는 1계층에서 작동하는데, 이를 더 상세히 풀어서 설명하자면 물리 계층으로부터 받은 디지털 정보를 전기 신호로 변화하는 역할을 수행하는게 랜카드다.  

또한, 랜카드 드라이버를 통해서 2계층인 데이터 링크 계층에서도 동작한다. 왜냐하면 위에서 언급한 대로 랜카드에는 MAC 주소가 기입되어 있고 이 MAC 주소를 2계층에서 사용하기 때문이다.  

그래서 **'속하였다'** 라고 생각하기보다는 **'이 계층들에서 동작할 수 있다'** 라고 생각하는 게 정확하다.

이를 운영체제와의 관계 관점에서 보자면 다음과 같다.

- **응용 프로그램 <-> 운영체제의 네트워크 스택 <-> 랜카드 드라이버**  

&nbsp;

---

# Reference

- [나노디그리 러닝스푼즈: Python & Django backend course](https://learningspoons.com/course/detail/django-backend/)  
- [네트워크, 그림으로 이해해자](https://www.inflearn.com/course/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EA%B7%B8%EB%A6%BC-%EC%9D%B4%ED%95%B4/dashboard)  