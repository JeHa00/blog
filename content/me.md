---
title: "About Me. 🌳"
layout: "me"
url: "/me/"
summary: about
---

#

**SW backend engineer로 오기까지의 결정들과 그 이유**

대학교 입학 후 문제를 해결하는 직무인 `엔지니어`에 관심이 생겼습니다. 하지만 해당 domain은 제가 원하는 domain이 아니었고,  제가 원하는 `문제 해결 domain`을 찾기 위해 편입에 도전했습니다. 편입 및 졸업 후 플랜트 엔지니어(Plant Engineer)로 `약 9개월 동안 3024시간`을 몰입했습니다. 하지만 근무하면서 제가 해결하고 싶은 문제는 `사용자의 생활에 밀첩하게 물려있는 문제`임을 깨달았습니다. 이를 알기까지 짧지 않은 시간이 걸렸지만 제가 원하는 문제를 푸는 엔지니어는 SW engineer임을 확신할 수 있었습니다.

#

**저는  ‘`_________`’  엔지니어입니다.**

- 코드 신뢰성을 위해 **`테스트 코드`** 를 중요하게 생각하는
  - 현재 진행 중인 프로젝트의 테스트 코드 수: `105개`
- 체계적인 기록과 커뮤니케이션을 위해 **`문서화`** 를 좋아하는
  - 2022-02 ~ 2022-12: `141개`를 작성
  - 2023-01 ~ 현재 : `5개`를 작성

#

### Channel & Contact

- Github. [https://github.com/JeHa00 🔗](https://github.com/JeHa00)
- Email. [rudtls0611@gmail.com 🔗](rudtls0611@gmail.com)

---

## Skill

_최근 프로젝트에 사용한 기술들입니다._

### Backend  

- Python 3.10.8, FastAPI, MySQL 8.0
- Docker-compose

### Frontend

- Javascript, HTML, CSS

---

# Projects

## SNS 백엔드 개발 및 운영

> **_cat 사료 정보 특화 sns (Frontend: 1명 / Backend: 1명)_**

- **_기간: 2023.03 ~ (진행 중)_**

- **Development**
  - 현재까지 완료된 요구사항
    - `DDD` layered architecture 적용  
    - Pytest를 사용하여 `41개`의 unit test와 `64개`의 EndToEnd test 작성
    - FastAPI의 background task를 사용하여 가입 인증 이메일, 임시 비밀번호 발급 이메일 전송 속도 `100배 이상` 향상 (2900ms -> 0.33ms)
    - Oauth2를 사용하여 `JWT` authorization 적용  
    - Redis caching 을 적용하여 좋아요 갯수 조회 속도 `6배` 향상 (6ms -> 1ms)  
  - 진행 중인 요구사항
    - 비동기 통신 방법인 `SSE(Server Sent Event)`를 사용하여 알림 기능 구현

- **Deployment(예정)**
  - docker-compose 적용
  - Github Action을 통한 자동배포 적용  

&nbsp;

## [나노디그리 팀 프로젝트: Devket 🔗](https://github.com/backendnanodegree/Devket)
>
> **_실제 제품인 pocket 사이트를 클론 코딩 및 개선하기_**

- **_기간 : 2022.11.15 - 2022.12.14 (1개월 / 4명)_**

- **pocket 사이트 설명**
  - 사용자가 보관 및 읽고 싶은 타 웹 사이트 컨텐츠를 크롤링으로 저장
  - 크롤링한 컨텐츠에 하이라이트 표시로 원하는 부분을 강조하는 기능
  - 결제에 따라 저장할 수 있는 컨텐츠 수와 하이라이트 수 제한

- [**Frontend: vanilla js만을 사용 🔗**](https://jeha00.github.io/post/project/devket/js/dom_fetch_issue/)
  - 저장한 웹 사이트 목록 렌더링  
  - 저장한 각 웹 사이트 하단에 뜨는 하단툴바 렌더링  
  - fetch를 사용하여 api로부터 데이터를 보내고 받기

- **Backend**
  - DB modeling 설계 및 ERD 작성
  - 전체 목록 및 특정 조건을 만족하는 목록 조회 api 구축
  - 하단 툴바를 통한 저장된 웹 사이트의 즐겨찾기 값 변경, 저장한 웹 사이트 삭제 api 구축
  - 개발 기간과 확장성을 고려하여 결제 모듈로 아임포트를 선정하여 결제 기능 구현: 결제 api 구축
    - [Project: Iamport 선정 이유, 그리고 아임포트 javascript SDK를 사용하여 구축한 결제 흐름 🔗](https://jeha00.github.io/post/project/devket/django/01_payment_overall_flow/)
    - [Project: Payment 개발 과정에서의 고려사항들과 개발 이슈들 🔗](https://jeha00.github.io/post/project/devket/django/02_payment_issues/)
  - [AWS EC2, RDS, S3와 docker-compose를 사용한 배포 🔗](https://jeha00.github.io/post/project/devket/deployment/01_deployment/)

- **Team and Communication**
  - 팀 전체 성장과 커뮤니케이션 효율을 위해 주도적으로 PR template 도입 추진 및 제작
    - [Project: Pull Request templates를 도입한 이유 🔗](https://jeha00.github.io/post/project/devket/docs/why-pr-template/)

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

# Education

### [원티드 프리온보딩 백엔드 챌린지:  MySQL '잘' 사용하기🔗](https://www.wanted.co.kr/events/pre_challenge_be_4)

- **_2023.02.06 - 2023.02.19 (2주)_**
- 학습내용
  - CAT Theorem,  RDBMS와 NoSQL의 차이
  - Transaction, ACID, Lock, Isolation level
  - Query, Join, Subquery, SQL functions
  - Indexing, Normalization, Partitioning, Cache

&nbsp;

### 한국항공대학교 항공우주 및 기계공학부

- **_2017.03 - 2020.02 (졸업)_**
- 학점: 3.51 / 4.5
- 졸업 논문 프로젝트 16팀 중 열유체 분야 동상 수상  

---

_감사합니다._

_Backend Engineer,  김제하(jeha)_  

_Latest updated    2023.06.18_  
