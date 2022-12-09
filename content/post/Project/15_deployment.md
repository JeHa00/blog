---
title: "Project: Docker를 사용하여 django app 배포하기"
date: 2022-12-08T14:43:44+09:00
draft: false
summary: docker compose를 사용하여 nginx, AWS S3, AWS RDS와 연결된 django application을 배포한다. 이때, AWS S3에 접근하기 위해서 AWS IAM을 사용한다. static files에 연결하는 방식 3가지를 거치면서 왜 S3를 사용하는지도 정리했다.
tags: ["deployment"]
categories: ["Project"]
---
# 0. Introduction

- 이 repository는 현재 [러닝스푼즈 나노디그리 - django backend 부트캠프](https://learningspoons.com/course/detail/django-backend/)에서 팀 프로젝트를 진행하면서 다음과 같은 내용들에 대해 정리하고자 만들었습니다. 
    - 팀 정책을 이것으로 정한 이유 
    - 개발하면서 부딪힌 문제들에 대한 원인, 해결방안, 해결과정, 그리고 그 이유들


- 1차적으로 구현한 기능을 마치고, Docker 내용을 복습한 후 배포 작업을 시작했다. 배포 작업 순서, 에러, 해결방안, 그리고 여러 이유들에 대해 정리해본다.

<br>

---


# 1. git clone하여 가져오기

- `~/development/devket` directory를 생성하여 git clone으로 가져온다.

- 이에 따라 requirements.txt를 docker compose를 실행하기 위해서 위치를 바꾼다. `Devket` 안이 아니라 `Dockerfile`과 동일한 레벨로 옮긴다.


<br>

---

# 2. file directory 구조

익숙하지 않아서 먼저 구조를 잡기 위해 파일들을 생성했다.

```yml
# ~/deployment

./
├── devket
│   ├── devket
│   │   ├── config
│   │   ├── manage.py
│   │   ├── pocket
│   │   ├── static
│   │   │   ├── css
│   │   │   └── js
│   │   └── templates
│   ├── Dockerfile
│   └── requirements.txt
├── docker-compose.yml
└── nginx
    ├── Dockerfile
    └── default.conf
```

<br>

---

# 3. Dockerfile 생성

### django app(devket) Dockerfile

```yml
FROM python:3.10.8

WORKDIR /usr/src/app

COPY . .

RUN python -m pip install --upgrade pip
RUN pip install -r requirements.txt
RUN pip install boto3
RUN pip install django-storages

WORKDIR ./devket

CMD python manage.py runserver 0:8000

EXPOSE 8000
```

- [boto3를 사용하는 이유](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html#boto3-documentation): 파이썬 언어를 사용하여 EC2, S3 같은 AWS 서비스를 구성하고, 관리하려면 boto3를 사용해야 한다.
- [django-storages를 설치하는 이유](https://django-storages.readthedocs.io/en/latest/): 장고 프로젝트가 특정 storage를 사용하기 위해서 설치해야 한다.


<br>

### nginx Dockerfile

```yml
FROM nginx
RUN rm /etc/nginx/conf.d/default.conf
COPY default.conf /etc/nginx/conf.d
CMD ["nginx", "-g", "daemon off;"]
```

<br>

---

# 4. docker-compose.yml 생성

```yml
version: "3"
services:
    devket:
      build: ./devket
      container_name: devket
      restart: always
      expose:
        - "8000"

    devket-nginx:
      build: ./nginx
      container_name: devket-nginx
      restart: always
      ports:
        - "80:80"
      depends_on:
        - devket
```

### 502 Bad Gateway


`docker logs <container id>` 로 nginx와 django app을 확인해보자.


- django error

```yml
python: can't open file '/usr/src/app/devket/Devket/manage.py': [Errno 2] No such file or directory
```

- nginx error

```yml
nginx: [emerg] host not found in upstream "devket" in /etc/nginx/conf.d/default.conf:6 
```

위 두 에러 모두 django Dockerfile의 WORKDIR을 수정하여 해결했다.

<br>

---

# 5. RDS 생성 및 연결

RDS 생성 시 설정 세팅은 [RDS 연결하기](https://jeha00.github.io/post/django/deployment-with-nginx-uwsgi-ec2_3/#2-rds-%EC%97%B0%EA%B2%B0%ED%95%98%EA%B8%B0)를 참고하여 진행했다.

```python
# config/settings.py

DATABASES = { 
    "default": {
        "ENGINE": "django.db.backends.postgresql_psycopg2",     

            # 아래는 예시일 뿐, 리스트말고 문자열로 입력한다.
            “HOST": [RDS 엔드포인트],
            "NAME": [DB 이름], 
            "PORT": "5432",
            "USER": [마스터 사용자 이름],
            "PASSWORD": [비밀번호], 
        }
}
```

- RDS endpoint 는 `연결 & 보안`에서 확인 가능
- DB 이름, 마스터 사용자 이름은 `구성`에서 확인 가능  

`super user`가 생성가능하면 DB 연결이 된 것이다.

<br>

### ❗️ RDS 생성할 때 DB 이름의 기본값 
RDS 생성 시, 추가사항 탭을 클릭하여 DB 이름을 설정하지 않았다면 기본값으로 'postgres'가 입력된다. DB를 하나만 사용하고 있는 상황에서는 반드시 DB 이름을 입력할 필요는 없다고 판단되나, 프로젝트의 첫 배포 과정으로서 DB에 이름을 부여하고 싶어 입력했다.


<br>

---

# 6. S3 연결하기

S3에 연결하기 전에 이전에 학습해던 [3가지 방식](https://jeha00.github.io/post/django/deployment-with-nginx-uwsgi-ec2_3/#1-static-file-serving)을 다 사용하면서 왜 S3를 사용하는지 되새겨본다.

## 첫 번째 방법: ’location /static/’ 추가
> **_문제점: admin에 적용되는 css를 확인할 수 없다._**

```yml
# 경로: nginx의 default.conf
# 추가할 파일 내용

location /static/ { 
        alias   /devket/static/;
}
```

<br>

## 두 번째 방법: collectstatic
> **_장점: 첫 번째 방법에 대한 문제점 해결_**
> **_문제점: 프로젝트 내부에 정적 파일들을 모아놓기 때문에, 서버 부하를 피할 수 없다._**

`python manage.py collectstatic` 명령어를 사용하여 모든 static 파일들을 public directory 안에 모으기 위해서 location 설정을 바꾼다.

-  settings.py 설정 변경

    ```yml
    # 경로: cofig/settings.py
    # 추가할 파일 내용

    # STATIC
    STATIC_URL = "static/"
    STATICFILES_DIRS = [BASE_DIR / "static"]

    # 위에 두 줄은 이미 입력되어 있기 때문에, 밑에 한 줄만 입력한다.
    STATIC_ROOT = os.path.join(BASE_DIR, "public")
    ```


- `python manage.py collectstatic 실행`: public 폴더 생김
    - 먼저 `docker compose up -d --build`를 실행하여 변경사항이 반영된 container를 실행한다.
    - django 명령어를 입력할 때는 해당 image에 접속하여 입력하면 된다.



- nginx/default.conf 설정 변경
    ```yml
    # 경로: nginx의 default.conf
    # 추가할 파일 내용

    location /static/ { 
            alias   /devket/public/;
    }
    ```

<br>

## 세 번째 방법: S3에 연결하기
> **_내부가 아닌 외부 AWS S3에 모아놓은 정적 파일들을 올려서 서버 부하를 분산시킨다._**


- AWS S3 bucket 생성하기  
    - bucket 명: devket
    - 객체 소유권: ACL 활성화 + 객체 소유권: 객체 라이터
    - 퍼블릭 액세스 차단 설정
    ![image](https://user-images.githubusercontent.com/78094972/206662633-05848dcc-79ba-49f8-924a-97a6fa6103d5.png)

- AWS IAM에서 다운 받은 key를 settings.py에 반영하기
    - [이 링크](https://jeha00.github.io/post/django/deployment-with-nginx-uwsgi-ec2_3/#3-aws-iam%EC%97%90%EC%84%9C-%EB%8B%A4%EC%9A%B4-%EB%B0%9B%EC%9D%80-key%EB%A5%BC-settingspy%EC%97%90-%EB%B0%98%EC%98%81%ED%95%98%EA%B8%B0)를 따라서 생성한다.

- config/storages.py 추가하기

    ```python
    from storages.backends.s3boto3 import S3Boto3Storage

    class S3DefaultStorage(S3Boto3Storage): 
            location = "media"

    class S3StaticStorage(S3Boto3Storage): 
            location = "static"
    ```

- 현재 과정에서 file directory 구조

    ```yml
    # ~/deployment

    ./
    ├── devket
    │   ├── Dockerfile
    │   ├── devket
    │   │   ├── config
    │   │   │   ├── __init__.py
    │   │   │   ├── asgi.py
    │   │   │   ├── settings.py
    │   │   │   ├── storages.py
    │   │   │   ├── urls.py
    │   │   │   └── wsgi.py
    │   │   ├── manage.py
    │   │   ├── pocket
    │   │   ├── static
    │   │   │   ├── css
    │   │   │   ├── images
    │   │   │   └── js
    │   │   └── templates
    │   └── requirements.txt
    ├── docker-compose.yml
    └── nginx
        ├── Dockerfile
        └── default.conf
    ```


- static file들을 S3로 옮기기: `python manage.py collectstatic`
    - ❗️**python manage.py collectstatic 시 발생된 Error**
        - 첫 번째 Error: botocore.errorfactory.NoSuchBucket: An error occurred (NoSuchBucket) when calling the PutObject operation: The specified bucket does not exist
            - 지정한 bucket이 존재하지 않는다는 의미다. S3의 bucket name이 storages.py의 BUCKET_NAME과 동일한지 비교한다.

        - 두 번째 Error: botocore.exceptions.ClientError: An error occurred (AccessControlListNotSupported) when calling the PutObject operation: The bucket does not allow ACLs
            - bucket 인식문제는 해결되었지만, 이 문제가 새롭게 발생했다. bucket 생성 시, 아래 설정대로 했는지 확인해보자.
            - ![image](https://user-images.githubusercontent.com/78094972/206662633-05848dcc-79ba-49f8-924a-97a6fa6103d5.png)


- nginx의 location url로 경로 바꾸기
    - 이 단계까지 수행하면 css file들이 AWS S3 bucket으로 연결되지 않은 걸 확인할 수 있다. 그래서 nginx의 default.conf 설정을 수정해야한다. 
    - 생성한 bucket에 들어가서 새로고침을 하면 `static/`이 생긴 걸 확인할 수 있다. 이를 선택하면 `URL 복사`가 활성화되는데, 이 버튼으로 복사해서 

    ```yml
    location /static/ {
                    alias https://devket.s3.ap-northeast-2.amazonaws.com/static/;
    }
    ```

<br>

### Error: Django amazon s3 SuspiciousOperation

위 nginx의 location url 경로를 수정해도 이와 같은 error가 발생했다. 

`django S3 연결 SuspiciousOperation`을 검색하니 아래와 같은 문서가 떴다.

읽어보면 해결 방법은 총 2가지다. 

첫 번째 방법은 storages.py 의 class 내용을 수정하는 방식이지만, 두 번째 방법은 static tag 뒤에 파일 경로의 시작 부분 `/`를 삭제하는 방식이다. 

현재 단계에서는 static tag로 연결된 부분이 많지 않기 때문에, 코드를 추가 작성하는 것보다 두 번째 방법으로 하는 게 시간적으로 금방 해결되기 때문에 후자를 선택하여 진행했다.

출처: [Django amazon s3 SuspiciousOperation](https://stackoverflow.com/questions/25456420/django-amazon-s3-suspiciousoperation)


<br>

### AWS IAM을 사용하는 이유

> _IAM 역할을 사용하면 일반적으로 조직의 AWS 리소스에 대한 액세스 권한이 없는 사용자나 서비스에 액세스 권한을 위임할 수 있습니다.  ... 그러면 애플리케이션이 이러한 자격 증명을 사용해 Amazon S3 버킷 또는 Amazon DynamoDB 데이터 등의 리소스에 액세스할 수 있습니다._   
> 출처: [AWS - manage-roles](https://aws.amazon.com/ko/iam/details/manage-roles/)

AWS S3 bucket을 데이터 저장소로 사용한다면 IAM을 사용해서 액세스 권한을 위임받아야한다는 내용이므로, 반드시 IAM을 사용해야 한다.

<br>

---
# Reference

- [boto3를 사용하는 이유](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html#boto3-documentation)
- [django-storages를 설치하는 이유](https://django-storages.readthedocs.io/en/latest/)
- [RDS 연결하기](https://jeha00.github.io/post/django/deployment-with-nginx-uwsgi-ec2_3/#2-rds-%EC%97%B0%EA%B2%B0%ED%95%98%EA%B8%B0)
- [staticfiles 연결 3가지 방식](https://jeha00.github.io/post/django/deployment-with-nginx-uwsgi-ec2_3/#1-static-file-serving)
- [AWS IAM에서 다운 받은 key를 settings.py에 반영하기](https://jeha00.github.io/post/django/deployment-with-nginx-uwsgi-ec2_3/#3-aws-iam%EC%97%90%EC%84%9C-%EB%8B%A4%EC%9A%B4-%EB%B0%9B%EC%9D%80-key%EB%A5%BC-settingspy%EC%97%90-%EB%B0%98%EC%98%81%ED%95%98%EA%B8%B0)
- [Django amazon s3 SuspiciousOperation](https://stackoverflow.com/questions/25456420/django-amazon-s3-suspiciousoperation)  
- [AWS - manage-roles](https://aws.amazon.com/ko/iam/details/manage-roles/)  