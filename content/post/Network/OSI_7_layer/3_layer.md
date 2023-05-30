---
title: "[TIL]OSI 7 layer: 3계층 네트워크 계층"
date: 2023-05-24T15:14:44+09:00
draft: true
summary: OSI 7 layer에서 한 네트워크에서 다른 네트워크와 통신하기 위한 IP 주소를 결정하는 3계층에 대해 더 알아보자.     
tags: ["TIL", "Network"]
categories: "Network"
---


# 네트워크 학습 내용 순서

1. [[TIL]Web Application Basic study: OSI 7 layer outline](<https://jeha00.github.io/post/network/osi_7_layer/0_outline/>)
2. [[TIL] Network OSI 7 layer: 1계층](<https://jeha00.github.io/post/network/osi_7_layer/1_layer/>)  
3. [[TIL] Network OSI 7 layer: 2계층](<https://jeha00.github.io/post/network/osi_7_layer/2_layer/>)  

이번 장에서는 네트워크(Network) 계층인 3계층에 대해 알아보자.  

&nbsp;

# 1. IP(Internet Protocol) 네트워크 계층 개요

---

네트워크 계층은 한 네트워크와 다른 네트워크 간 통신하기 위해 IP를 결정하는 계층으로, 사용하는 '주요 프로토콜'은 **_IP(Internet Protocol)_** 를 사용한다. '주요 네트워크 기기'는 **_라우터_** 이며, '주요 데이터 단위' 는 **_packet_**  이다.

네트워크 계층의 특징은 아래 4가지로 요약할 수 있다.  

### 비신뢰성

데이터가 송신지까지 도달하는 걸 보장하지 않는다. 그래서 인터넷 계층의 윗 계층인 4계층 전송 계층에서 이를 갖춘다. 그래서 데이터 순서가 바뀔 수도 있고, 서로 다른 경로를 통해 전송될 수 있다.  

### 비연결형

4계층 전송 계층과는 달리 연결설정 과정 없이 데이터를 전송한다.

### 주소 지정

IP 주소를 제공하여 각 기기 장치가 식별될 수 있도록 하는 역할을 한다. MAC 주소는 컴퓨터의 물리적인 식별자라면 IP 주소는 인터넷 연결 간 논리적인 식별을 하는데 사용된다.

### 경로 결정

이 IP 주소를 통해 라우팅의 경로 설정도 가능하다.

&nbsp;

# 2. IP 주소

---

### IP 주소란? 그리고 IP 주소의 사용 목적

각 기기들을 식별하기 위한 논리적인 주소를 말한다. 네트워크에 연결되어 있는 기기(호스트나 라우터)들은 자신의 고유한 주소를 갖고 있어야 데이터를 주고 받을 수 있다. 이를 위해서 인터넷 프로토콜은 논리적인 주소인 IP 주소를 제공하여 데이터를 서로 송수신할 수 있게 한다.  

#

### IPv6 버전은 IPv4를 대체하기 위해 등장  

| | IPv4 | IPv6 |
| ---- |---- |---- |
| 주소 형식 | 192.168.10.1 | FE80:CD00:0000:0CDE:1257:0000:211E:729C |
| 주소 표현 | 10진수 | 16진수 |
| 크기 | 32bit(4 bytes) | 128bit(16 bytes) |
| 최대 표현 개수 | 약 43억개 범위: 0.0.0.0 ~ 255.255.255.255 | 무한대에 가깝다. |  
| 서비스 품질 | 제한적 품질 | 확장된 품질 |
| 보안 기능 | IPSec 별도 설치 | 기본 제공 |

- 크기
  - IPv4 기준으로 각각 8bit씩 총 4 부분 * 8bit = 32bit를 이룬다.  
  - IPv6 기준으로 각각 8bit씩 총 8 부분 * 16bit = 128bit를 이룬다.  

- 8bit를 1 옥텟(octet)이라 부른다.  

#

### Network ID와 Host ID

범위에 따라 Network ID와 Host ID로 구분할 수 있다.

- 네트워크 ID: 전체 중 작은 네트워크를 식별하는 ID이자 호스트의 집합 대표 주소  

- 호스트 ID: 호스트를 식별  

{{<figure src="https://user-images.githubusercontent.com/78094972/241970333-5b71f22d-e266-49b0-a189-a8d5c90c2f34.png" width="60%">}}

#

### IP 주소 클래스 (IPv4 기준)

| 클래스 | IPv4 (2진수) | IPv4 (10진수) | 네트워크 ID | 호스트 ID | 비고 |
| ---- |  ---- |  ---- |  ---- | ---- | ---- |
| A|00000001. 00000000. 00000000. 00000000 | 0.0.0.0 ~ 127.255.255.255 | 8 bit | 24 bit|
| B|10000000. 00000000. 00000000. 00000000 | **128** .0.0.0 ~ 191.255.255.255 | 16 bit | 16 bit|
| C|11000000. 00000000. 00000000. 00000000 | **192** .0.0.0 ~ 223.255.255.255 | 24bit | 8 bit|
| D|11100000. 00000000. 00000000. 00000000 | **224**.0.0.0 ~ 239.255.255.255 |||멀티캐스트용|
| E|11110000. 00000000. 00000000. 00000000 | **240**.0.0.0 ~ 239.255.255.255 ||| 특수용도 예약주소 |

- docker IP는 B 클래스에 해당된다.

### MAC 주소와 비교

- MAC 주소: 48 bit, 16진수  
- IP 주소 (IPv4): 32 bit, 2진수
  - 실제로는 2진수로 되어 있고, 사람이 보기 편하게 10진수로 되어 있다.  

&nbsp;

---

# Reference

- [나노디그리 러닝스푼즈: Python & Django backend course](https://learningspoons.com/course/detail/django-backend/)  
- [네트워크, 그림으로 이해해자](https://www.inflearn.com/course/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EA%B7%B8%EB%A6%BC-%EC%9D%B4%ED%95%B4/dashboard)  
- [네트워크 기초(4) - OSI 7계층 - 3계층: 네트워크 계층](https://losskatsu.github.io/os-kernel/network-basic04/#2-%EB%9D%BC%EC%9A%B0%ED%84%B0)  
