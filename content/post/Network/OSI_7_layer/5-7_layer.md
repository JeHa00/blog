---
title: "[TIL]OSI 7 layer: 5 ~ 7계층"
date: 2023-12-28T18:23:48+09:00
draft: False
summary: TCP/IP 4계층을 기준으로 맨 위 계층인 Application 계층에 대해 알아보자. 
tags: ["TIL", "Network"]
categories: "Network - OSI 7 layer"
---

# 네트워크 학습 내용 순서

1. [[TIL]Web Application Basic study: OSI 7 layer outline](<https://jeha00.github.io/post/network/osi_7_layer/0_outline/>)
2. [[TIL] Network OSI 7 layer: 1계층](<https://jeha00.github.io/post/network/osi_7_layer/1_layer/>)  
3. [[TIL] Network OSI 7 layer: 2계층](<https://jeha00.github.io/post/network/osi_7_layer/2_layer/>)  
4. [[TIL] Network OSI 7 layer: 3계층](<https://jeha00.github.io/post/network/osi_7_layer/3_layer/>)
5. [[TIL] Network OSI 7 layer: 4계층](<https://jeha00.github.io/post/network/osi_7_layer/4_layer/>)

이번 장에서는 OSI 7 계층에서 가장 최상위 계층에 속하며 이 챕터에서는 세션, 표현, 응용계층을 모두 포함한 것으로 통용하겠다.  




&nbsp;


# 1. Application 응용 계층 개요
---

응용 계층은 애플리케이션에 대한 서비스를 제공하기 위해서 클라이언트가 요청한 서비스를 통신 대상인 서버가 인식하도록 데이터를 변환하는 계층이다.  

컴퓨터 사용자라면 여러 서비스를 사용하고 싶어하는데, 이를 위해서 각 서비스에 맞는 프로토콜들을 사용해야 원하는 서비스를 제공하는 서버와 통신할 수 있다.  

응용 계층에서의 주요 프로토콜 종류에는 DHCP, DNS, HTTP, SMTP, POP3, IMAP, SNMP, FTP 등이 있다.  


- HTTP: 웹 사이트 접속
- DNS: 주소 해석
- SMTP: 메일 송신
- POP3: 메일 수신
- FTP: 파일 전송
- DHCP
- IMAP
- SNMP

이 계층에서의 데이터 단위는 **_메시지_** 다. 

- 메시지 = 응용 헤더 + user data

&nbsp;


# 2. DHCP (Dynamic Host Configuration Protocol)
---


## DHCP란?

컴퓨터의 IP 주소를 할당하는 방법에는 '정적 할당'과 '동적 할당' 이 있다.  

- 정적 할당: 수동 설정
- 동적 할당: 자동으로 주소를 할당하는 설정

여기서 동적 할당 방식에서 사용되는 프로토콜이 DHCP다. **_IP 주소, 서브넷 마스크, 기본 게이트웨이 등을 자동으로 설정하는 프로토콜_** 이다.  그래서 사용자가 직접 할당하지 않아도 된다.

## DHCP 동작 설명

> 임대 요청(DHCP DISCOVER) -> 임대 제공(DHCP OFFER) -> 임대 선택(DHCP REQUEST) -> 임태 확인(DHCP ACK)

DHCP 동작은 위 4가지로 설명할 수 있다. 여기서 '임대'라는 단어에 집중하면 동작 방식 이해가 쉬울 것이다.   

이 또한 프로토콜이므로 DHCP 클라이언트와 서버가 존재한다.

### 1) 임대 요청(DHCP DISCOVER)

IP 주소를 할당하기 전이기 때문에 IP 주소는 0.0.0.0 이다. 그리고 목적지 IP 주소는 255.255.255.255인데, 그 이유는 DHCP는 브로드캐스트 방식을 사용하기 때문이다.  


### 2) 임대 제공(DHCP OFFER)

그래서 이 임대 요청을 받은 DHCP 서버는 TCP/IP 설정을 응답으로 보낸다.  

IP 주소, 임대 기간, 서브넷 마스크 DNS 주소 등이 포함된다. 

그리고 브로드캐스트 방식으로 보낸다고 했는데, 여러 개의 DHCP 서버에게 응답이 왔다면 가장 먼저 도착한 응답 메시지를 선택한다.  

### 3) 임대 선택(DHCP REQUEST)

응답을 받은 DHCP 클라이언트는 이 설정을 사용하게 해달라는 요청을 보낸다.  

### 4) 임대 확인(DHCP ACK)

DHCP 서버가 확인 응답을 보내면서 IP 주소 동적 할당 과정이 종료된다.  


즉 위 4단계를 거쳐서 주소를 임대해주는 역할을 한다.  

&nbsp;


# 3. DNS (Domain Name Service)
---

## 도메인 이름과 IP 주소를 모두 사용하는 이유

평소 url 창에 주소를 입력할 때 우리는 숫자가 아니라 알파벳으로 구성되는 '도메인 이름'을 입력하여 이동한다. 

**_'도메인 이름'_** 이란 IP 주소에 매핑되는 텍스트 문자열을 말한다.

컴퓨터는 숫자가 친숙하지만 도메인 이름을 사용하는 이유는 사람에게 더 친숙하고 기억을 잘할 수 있는 방식이기 때문이다. 그러면 차라리 IP 주소 보다 이름으로 기억하면 더  좋지 않냐라는 생각을 할 수 있지만, IP 주소는 32bit 즉, 4 byte에 해당하는 갯수 밖에 없지만, 도메인 명은 최대 255 byte까지의 문자를 취급해야하기 때문에 그러면 그만큼 라우터가 부하되어 데이터 운반 동작에 시간이 더 걸려서 네트워크 속도가 느려진다.  

## Name resolution (이름 해석)

그래서 DNS를 통해 도메인 이름을 기반으로 IP 주소를 알아내는 **_name resolution(이름 해석)_** 이 필요하다.  

그러면 웹 브라우저에 네이버 주소를 입력하면 '이름 해석' 과정이 어떻게 이뤄질까?

1. 웹 브라우저 url 창에 네이버 주소를 입력하면 이 네이버 주소에 해당하는 IP 주소를 DNS에 요청한다.

2. DNS에서 웹 브라우저에게 네이버 주소에 해당하는 IP 주소를 알려준다.

3. 웹 브라우저는 DNS에서 받은 IP 주소로 접속한다.  

이 3가지 과정이 일어나서 네이버로 이동한다.  

## 정방향 조회와 역방향 조회

위와 같이 도메인 이름을 통해서 IP 주소를 조회하는 방식을 **_정방향 조회_** 라고 하고, 반대로 IP 주소를 통해서 도메인 이름을 얻는 방식을 **_역방향 조회_** 라고 한다. 일반적으로는 '정방향 조회'를 대부분 사용한다.  


## DNS 계층 구조 

DNS는 호스트를 식별하고 관리하기 위해서 domain name space(도메인 네임 공간)라는 계층 구조를 가진다. 이 계층 구조는 다음과 같이 **_트리형_** 이므로 여러 층 개념을 가진다. 그리고 이 계층은 DNS의 `.` 들이 계층을 구분한다.  예를 들어서 `www.lab.cyber.com` 이라고 하면 `com` 이라는 도메인 아래에 `cyber` 라는 도메인이 있고, 그 아래에 `lab` 이라는 도메인이 있다. 이 `lab` 도메인 안에 `www` 라는 도메인이 있는 것이다.  이처럼 계층적 구조를 가진다.  

![image](https://github.com/JeHa00/image/assets/78094972/e4a292eb-147c-4f50-9caa-43b70b018a5fㄴ)

### TLD, SLD 그리고 Sub domain

맨 위 층에 최상위 도메인은 **_Top Level Domain(TLD)_** 이라 부른다. 'TLD' 바로 밑에는 **_SLD(Second Level Domain)_** 이라 하여 '2차 도메인' 이라 한다. 그 아래부터는 **_sub domain_** 이라 칭한다. 


### 국가 도메인과 일반 도메인

국가마다 가지고 있는 고유의 도메인이 존재한다. 이 도메인을 '국가 도메인'이라 하며 다음과 같이 구성되어 있다. [국가 코드 최상위 도메인](https://ko.wikipedia.org/wiki/%EA%B5%AD%EA%B0%80_%EC%BD%94%EB%93%9C_%EC%B5%9C%EC%83%81%EC%9C%84_%EB%8F%84%EB%A9%94%EC%9D%B8)을 확인하면 알 수 있다. 그래서 위 이미지에 'kr'은 국가 도메인에 속함을 알 수 있다. 

이외에도 국가 기관이 아닌 일반 플랫폼들은 '일반 도메인'에 속한다.



## 반복적 쿼리와 재귀적 쿼리

'이름 해석' 단계가 총 3단계인 것으로 확인했다. 그러면 트리 구조로 된 DNS가 어떻게 IP 주소를 알려줄 수 있는 것일까?!

IP 주소를 알기 위해서 DNS client가 DNS 서버에 질의를 보낸다. 질의 방식에는 '재귀적 질의' 와 '반복적 질의'가 있다.  


### 반복적 질의

> local DNS 서버가 다른 DNS 서버에게 쿼리를 보내어, 쿼리를 받은 DNS 서버가 자신이 직접 관리하지 않는 질의 요청일 경우, 질의에 응답 가능한 DNS 서버 IP 주소 목록을 응답하는 방식   

1. 브라우저의 url 창에  www.lab.glasscom.com 을 입력하여 로컬 DNS 서버에 먼저 질의

- 로컬 dns server란 가장 가까운 dns 서버를 의미

2. 로컬 DNS 서버에서 모를 경우, Root DNS 서버에 질의한다. (`www.lab.glasscom.com`의 IP 주소는?) 그러면 Root DNS 서버에서 `com` 의 IP 주소를 로컬 DNS 서버에 응답한다.

3. 로컬 DNS 서버는 `com` TLD DNS 서버에 동일하게 질의한다. 그러면 `com` TLD DNS 서버에서 `glasscom`의 IP 주소를 로컬 DNS 서버에 응답한다.

4. 로컬 DNS 서버는 `glasscom` DNS 서버에 동일하게 질의한다. 그러면 `glasscom` DNS 서버에서 `lab`의 IP 주소를 로컬 DNS 서버에 응답한다.

5. 로컬 DNS 서버는 `lab` DNS 서버에 동일하게 질의한다. 그러면 `lab` DNS 서버에서는 `www.lab.glasscom.com`의 IP 주소를 로컬 DNS 서버에 응답한다.

6. 로컬 DNS 서버는 클라이언트에게 10번에서 얻은 IP 주소를 전달

![image](https://github.com/JeHa00/image/assets/78094972/9656ae46-6137-416e-8536-4eb55926c0da)

- 출처: 성공과 실패를 결정하는 1%의 네트워크 원리에서 그림 1-16


### 재귀적 쿼리

> 반복적 쿼리와 달리 local DNS 서버가 아닌 상위 DNS 서버가 직접 하위 DNS 서버를 탐색하여 결과값을 반환하는 방식    

재귀적 질의 동작은 다음과 같다. 

1. 클라이언트가 로컬 DNS 서버를 통해 루트 DNS 서버에 요청

2. 루트 DNS 서버에서 해당 도메인 이름이 등록되어 있는지 확인한다. 

3. 루트 DNS 서버에서 바로 해당되는 TLD 서버에 질의한다. 

4. 3번의 TLD 서버에서 가장 가까운 서브 도메인 서버에 질의한다.

5. 이렇게 계속 더 하위 계층으로 질의하여 진행되며, 최종적으로 원하는 IP를 얻을 경우 왔던 계층들로 다시 올라가서 루트 DNS 서버에서 로컬 DNS 서버에게 최종적으로 구한 IP 주소를 보낸다.

6. 로컬 DNS 서버가 클라이언트에게 이 IP 주소를 전달한다.


&nbsp;

# 4. HTTP
---

그러면 HTTP에 대해 간략히 알아보자. HTTP는 Hyper Text Transfer Protocol의 약어로, 웹에서 Hyper Text 문서를 요청하고 응답하기 위한 프로토콜이다. 
Hyper Text 문서라 함은 HTML(Hyper Text Markup Language)로 작성된 웹 문서를 말한다. 그래서 HTTP 요청과 응답을 주고 받으며 웹 사이트를 구성하는 HTML 파일을 전송한다. 이 때 사용하는 포트 번호는 80번이다.  

HTTP를 사용하기 위해서는 웹 url을 입력해야 한다. 이 url 구성은 간략하게 보자면 다음과 같다.

예를 들어 `https://www.inflearn.com/roadmaps`을 입력한다고 하자. 

- https: 어떤 프로토콜을 사용하는지
- www.inflearn.com: 호스트 이름을 의미하며, 어떤 웹 서버를 필요로 하는지 나타낸다  
- roadmaps: 경로명  


그리고 HTML 문서는 HTTP request와 response를 통해 보내지므로 HTTP request와 response에 대해 알아보자.

HTTP request 는 request line, message header, entity body 등의 부분으로 나눠지는데, 여기서 request line에 대해서 보자면 다음과 같다.

- request line = 사용되는 메서드 + URL + HTTP 버전  
- 메시지 헤더: 키 - 값 형식으로 여러 정보가 담겨져 있음  

HTTP response 는 response line, message header, entity body 등으로 나눠지는데, 여기서 response line에 대해서 보자면 다음과 같다.

- response line = HTTP 버전 + 상태 코드 + 상태 메세지  
  - 상태 코드: 웹 서버의 처리 결과를 나타낸 세 자리 숫자  


더 상세한 HTTP 내용은 내 블로그에서 학습한 HTTP 내용을 확인해보자.  


&nbsp;


# 5. 쿠키
---


&nbsp;


# 6. 프록시 서버
--- 



&nbsp;


# 7. 네트워크 실습

---


## ifconfig

> **_IP 주소를 확인하는 명령어_**

- terminal에 `ifconfig`를 입력해보고, 뜨는 것을 분석해보자.
- 이것이 네트워크의 전부다.  

- `lo0`: loop back을 의미하는데, 자기 자신에게 보내는 데이터를 처리하기 위한 가상 인터페이스 장치 이름
  - `inet 127.0.0.1`: localhost로 자신의 컴퓨터를 의미한다.
  - `netmask 0xff000000`: 255.0.0.0 /8
    - 11111111 00000000 000000000 00000000

- `en0`: ethernet을 의미한다
  - `inet6 fe80::1872:9ed:4de7:e8c`: IPv6 주소
  - `inet 172.30.1.1`: IPv4 주소로 내부 IP
  - `broadcast 172.30.1.255`: broadcast address
  - `netmask 0xffffff00`: 255.255.255.192 /26
    - 11111111 11111111 1111111 11000000

&nbsp;

## DNS server

> **_DNS(Domain Name System)이란? 웹 사이트의 IP 주소와 도메인 주소를 이어주는 시스템_**

- 터미널을 열어서 `nslookup www.naver.com`을 입력해보자.  
  - `Non-authoritative answer:` 아래 항목들 중에서 `Address`를 보면 `www.naver.com`의 IP 주소를 확인할 수 있다.
  - `Address: 223.130.200.104` 를 입력하면 네이버로 이동할 것이다.

- url을 입력하면 DNS server에서 이 url에 해당되는 IP 주소를 보내주고, 이 IP 주소로 입력되어 해당 서버에 요청하여 화면에 보여지는 것

### 맥에서 DNS 확인

> `cat /etc/hosts`을 입력

- 위 명령어를 입력하면 아래창을 확인할 수 있다.  

```yml
127.0.0.1 localhost
255.255.255.255 broadcasthost
::1             localhost
```

- `127.0.0.1`은 localhost다.  
- `broadcasthost` 는 `255.255.255.255` 다.
- DNS에다가 추가하고 싶은 IP 주소를 추가할 수 있다는 것

&nbsp;

## ping

> **_Ping(Packet INternet Groper)의 약자로, 네트워크 상태를 점검 및 진단하는 명령어_**

- 나중에 서버를 구출할 때, 그 서버에 ping을 날려보면 데이터를 보내고 받을 수 있는지 확인할 수 있다.

- 터미널 실행 후,  `ping google.co.kr`를 입력해보자.  

```yml
> ping google.co.kr
PING google.co.kr (172.217.26.227): 56 data bytes
64 bytes from 172.217.26.227: icmp_seq=0 ttl=114 time=45.149 ms
64 bytes from 172.217.26.227: icmp_seq=1 ttl=114 time=36.573 ms
64 bytes from 172.217.26.227: icmp_seq=2 ttl=114 time=44.751 ms
64 bytes from 172.217.26.227: icmp_seq=3 ttl=114 time=44.796 ms
64 bytes from 172.217.26.227: icmp_seq=4 ttl=114 time=40.381 ms
64 bytes from 172.217.26.227: icmp_seq=5 ttl=114 time=45.104 ms
64 bytes from 172.217.26.227: icmp_seq=6 ttl=114 time=37.562 ms
64 bytes from 172.217.26.227: icmp_seq=7 ttl=114 time=36.680 ms
^C
--- google.co.kr ping statistics ---
8 packets transmitted, 8 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 36.573/41.375/45.149/3.740 ms
```

- 총 8개의 packet을 보내고, 받았다는 걸 알 수 있다.  
- 이렇게 Ping을 보내어 해당 서버와 통신 유무를 확인할 수 있다.  

&nbsp;


---
# Reference

- [나노디그리 러닝스푼즈: Python & Django backend course](https://learningspoons.com/course/detail/django-backend/)  
- [네트워크, 그림으로 이해해자](https://www.inflearn.com/course/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EA%B7%B8%EB%A6%BC-%EC%9D%B4%ED%95%B4/dashboard)  
- [성공과 실패를 결정하는 1%의 네트워크 원리](https://product.kyobobook.co.kr/detail/S000000559868)