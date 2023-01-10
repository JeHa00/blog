---
title: "Project: deployment issue - S3 CORS"
date: 2022-12-12T19:33:59+09:00
draft: false
summary: 배포 후 발생한 S3 CORS issue에 대해 알아본다. 이 과정에서 CORS란 무엇이고 이를 헤결하기 위해서 어떤 header를 어떻게 추가하는지 알아본다.
tags: ["deployment", "CORS"]
categories: ["Project"]
---
# 0. Introduction

이 repository는 현재 [러닝스푼즈 나노디그리 - django backend 부트캠프](https://learningspoons.com/course/detail/django-backend/)에서 팀 프로젝트를 진행하면서 다음과 같은 내용들에 대해 정리하고자 만들었습니다. 
- 팀 정책을 이것으로 정한 이유 
- 개발하면서 부딪힌 문제들에 대한 원인, 해결방안, 해결과정, 그리고 그 이유들

---
# 1. S3 CORS issue

web application에서 S3 RDS에 있는 js 파일을 가져올 때 CORS(Cross Origin Resource Sharing) issue가 발생했다. 

![iamge](https://user-images.githubusercontent.com/78094972/207022285-08e8b31d-fd0f-4178-b0a1-33d5eeb8e6fb.png)

위 이미지에 따르면 2가지 사실을 알 수 있다. 
- CORS 정책에 의해 스크립트에 접근하는 게 막혔다는 것  
- 'Access-Control-Allow-Origin' header가 http request header에 존재하지 않는다는 것 

해결책은 이 header를 추가하는 것이라는 걸 알 수 있다.

그렇다면 CORS란 무엇이고, 이 header는 어떻게 추가할 수 있는 것일까?

&nbsp;

---

# 2. CORS

## CORS 란?

**'Cross-Origin Resource Sharing'** 으로 **'교차 출처 리소스 공유 정책'** 으로 직역할 수 있다. 여기서 교차 출처란 '서로 다른 출처'를 의미한다.

그렇다면 origin의 기준 그리고, 서로 다른 지 판단하는 기준은 무엇일까?


## Origin이란?

> **_origin = protocol + host + port number_**

사용자가 사이트에 접속하는 방법은 URL 창에  URL 문자열을 입력하여 접근한다. 

URL 구성은 [URL 분석](https://jeha00.github.io/post/network/network_http_2/#12-url-%EB%B6%84%EC%84%9D) 을 보면 '프로토콜' - '호스트명' - 'Port 번호' - 'path' - 'query parameter'로 구성된다.

여기서 origin은 'Protocol + Host + Port' 까지 합친 URL을 의미한다.

javascript에서 `location.origin`이 바로 이 origin을 의미하기 때문에, 쉽게 알아낼 수 있다.

### Cross-origin
그렇다면 cross origin이란 이 Protocol + Host + Port 가 합쳐진 것이 서로 다른 출처임을 이해할 수 있다. 즉, 이 3가지 중 하나라도 다르면 출처가 다르다.

### Same-origin
반대로 same origin은 이 3가지 모두가 동일한 출처라는 걸 의미한다.


### 이외의 CORS의 기본 개념들
위 개념들 이외에 same-origin-policy, cross-origin-policy 등등 CORS에 관한 모든 내용이라고 할 정도로 내용이 좋은 블로그 [악명 높은 CORS 개념 & 해결법 - 정리 끝판왕](https://inpa.tistory.com/entry/WEB-%F0%9F%93%9A-CORS-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EB%B2%95-%F0%9F%91%8F)를 찾았기 때문에, 이 글에 추가하지 않는다.


&nbsp;


---

# 3. Solution

서버에서 `Access-Control-Allow-Origin` 헤더에 허용할 출처를 기재해서 클라이언트에 응답하면 된다.

이를 기재하는 방식은 알아보니 매우 다양했다.

nginx, django, AWS 모두 다 가능했다.

그중에서 AWS 방식을 선택했다. 

그 이유는 django의 경우 별도의 패키지를 설치해야 하기 때문이다. nginx는 nginx에서 설정을 해도 django나 AWS에서 그 설정이 제대로 되지 않으면 계속해서 뜨는 걸 이 블로그 [Django, Nginx CORS 설정](https://jay-ji.tistory.com/72)에서 확인했기 때문이다.


### AWS S3 > 권한 > CORS 편집

[aws - CORS configuration](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ManageCorsUsing.html)을 참고하여 작성했다.

```yml
[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "GET",
            "HEAD"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": [
            "x-amz-server-side-encryption",
            "x-amz-request-id",
            "x-amz-id-2"
        ],
        "MaxAgeSeconds": 3000
    }
]
```

위 내용을 추가하니 CORS error가 뜨지 않는다. 

위 값들에 대한 설명은 위 aws 문서를 참고한다.


&nbsp;

---

# Reference

- [악명 높은 CORS 개념 & 해결법 - 정리 끝판왕](https://inpa.tistory.com/entry/WEB-%F0%9F%93%9A-CORS-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EB%B2%95-%F0%9F%91%8F)
- [Django, Nginx CORS 설정](https://jay-ji.tistory.com/72)
- [Using cross-origin resource sharing (CORS)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/cors.html)
- [aws - CORS configuration](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ManageCorsUsing.html)