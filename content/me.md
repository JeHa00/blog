---
title: "About Me."
layout: "me"
url: "/me/"
summary: about
---

# 서버 개발자 김제하

<img src='https://user-images.githubusercontent.com/78094972/208803550-2c7b4f61-cdaf-4003-9e0e-3931f9de1035.png' width='100' height='100'>

저는 `_________` 엔지니어입니다.
- ‘기술은 세상을 이롭게 하기 위해 존재한다’는 가치관을 가진
- 기록, 정리 그리고 공유를 통해 혼자보다 동료와 '함께' 성장하고 싶은
- 커뮤니케이션을 잘하기 위해 '재질문과 문서화' 를 사용하는
- 학습 속도가 빠른


## Channel & Contact.

- Blog. 지금 보고 계신 블로그!
- Github. [https://github.com/JeHa00 🔗](https://github.com/JeHa00)
- Email. rudtls0611@gmail.com 

---

# Skill.

_프로젝트에 사용한 기술들입니다._

### Backend

- Python, Django, Django DRF, PostgreSQL 
- Docker, Docker-compose, AWS EC2, AWS RDS, AWS S3, AWS IAM 

### Frontend

- Javascript, HTML, CSS

### Etc

- jira, figma, notion

---
# Projects.

## [나노디그리 팀 프로젝트: Devket 🔗](https://github.com/backendnanodegree/Devket)
> **_실제 제품인 pocket 사이트를 클론 코딩 및 개선하기_**

- **_기간 : 2022.11.15 - 2022.12.14 (1개월)_**

### pocket 사이트 설명
- 사용자가 보관 및 읽고 싶은 타 웹 사이트 컨텐츠를 크롤링으로 저장
- 크롤링한 컨텐츠에 하이라이트 표시로 원하는 부분을 강조하는 기능
- 결제에 따라 저장할 수 있는 컨텐츠 수와 하이라이트 수 제한

### Fast learning ability
- 한 달보다 적은 기간 동안 JS와 DRF를 학습하여 다음과 같은 성과 성취

### [Frontend : vanilla js만을 사용 🔗](https://jeha00.github.io/post/project/js/dom_fetch_issue/)
- 저장한 웹 사이트 목록 렌더링  
- 저장한 각 웹 사이트 하단에 뜨는 하단툴바 렌더링  
- 구축한 api로 하단툴바를 통한 즐겨찾기, 삭제 기능 구현  
- fetch를 사용하여 api로부터 데이터를 받아 아임포트에 전송  

### Backend
- DB modeling 설계 및 ERD 작성
- 전체 목록 및 특정 조건을 만족하는 목록 조회를 위해 ORM filter와 Q를 사용하여 조회 api 구축
- 하단 툴바를 통한 저장된 웹 사이트의 즐겨찾기 값 변경, 저장한 웹 사이트 삭제 api 구축
    - Serializer를 통해 저장한 웹 사이트의 즐겨찾기 값 부분 수정
- 개발 기간과 확장성을 고려하여 결제 모듈로 아임포트를 선정하여 결제 기능 구현: 결제 api 구축
    - [Project: Iamport 선정 이유, 그리고 아임포트 javascript SDK를 사용하여 구축한 결제 흐름 🔗](https://jeha00.github.io/post/project/django/01_payment_overall_flow/)
    - Payment만의 objects를 사용하기 위해 ModelManager를 사용
    - 중복되지 않은 주문 번호를 생성하기 위해 hashlib 사용
- AWS EC2, RDS, S3와 docker-compose를 사용한 배포

### Team-mind and Communication
- 팀 전체 성장과 커뮤니케이션 효율을 위해 주도적으로 PR template 도입 추진 및 제작
    - [Project: Pull Request templates를 도입한 이유 🔗](https://jeha00.github.io/post/project/01_why-pr-template/)

&nbsp;

## 기술 블로그 만들기

> **_[Window에서 Hugo로 github page 만들고 배포하기 🔗](https://jeha00.github.io/post/dev-contents/hugo%EB%A1%9C-github-page-%EB%A7%8C%EB%93%A4%EA%B3%A0-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0/)_**  

- **_기간 : 2022.01.15 - 2022.02.16 (1개월)_** 
- Window 환경에서 Hugo SSG를 사용하여 기술 블로그를 만든 정보가 Jekyell에 비해 부족한 상황
- 다른 분들에게 도움이 되고자 만든 과정을 공유하기 위해 기술 블로그에 정리

&nbsp;

## 졸업 논문 project

> **_[유연부가 있는 진동형 heat pipe를 이용한 노트북 PC 방열 설계: 3D 모델링 설계 및 실험 진행 🔗](https://dog-lightyear-010.notion.site/020263bee9df472d944ad7df7a1fdc9b)_**

- **_기간 : 2018.09.03 - 2019.06.30 (1년)_**
- 교내 종합설계 프로젝트 16개팀 중 열유체 분야 동상 수상
- 1차, 2차 모델로 나눠서 점진적으로 진행한 결과, 1차 모델보다 10℃ 낮은 방열 성능으로 개선
- flexible display 방열을 주제로 한 대학원 연구의 기반

---

# Education. 

### [원티드 프리온보딩 백엔드 챌린지:  MySQL '잘' 사용하기🔗](https://www.wanted.co.kr/events/pre_challenge_be_4)

- **_2023.02.06 - 2023.02.19 (2주)_** 
- 학습내용
    - CAT Theorem,  RDBMS와 NoSQL의 차이
    - Transaction, ACID, Lock, Isolation level
    - Query, Join, Subquery, SQL functions
    - Indexing, Normalization, Partitioning, Cache

&nbsp;

### [나노디그리 백엔드 코스 1기 🔗](https://learningspoons.com/course/detail/django-backend/)

- **_2022.08 - 2022.12 (4.5개월)_** 
- 러닝스푼즈에서 진행한 백엔드 개발자 양성 교육 과정
- Django, Docker, AWS  등 학습  
- 팀 프로젝트를 통한 협업 경험  

&nbsp;

### 한국항공대학교 항공우주 및 기계공학부

- **_2017.03 - 2020.02 (졸업)_**
- 학점: 3.51 / 4.5
- 졸업 논문 프로젝트 16팀 중 열유체 분야 동상 수상  

---

_감사합니다._

_Backend Engineer,  김제하(jeha)_  

_Latest updated    2023.02.08_  