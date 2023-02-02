---
title: "Project: Docker를 사용하여 django app 배포하기"
date: 2022-12-08T14:43:44+09:00
draft: false
summary: docker compose를 사용하여 nginx, gunicorn, AWS S3, AWS RDS와 연결된 django application을 배포한다. 이때, AWS S3에 접근하기 위해서 AWS IAM을 사용한다. static files에 연결하는 방식으로 3가지를 사용했다.
tags: ["deployment"]
categories: ["Project"]
---
# 0. Introduction

이 repository는 현재 [러닝스푼즈 나노디그리 - django backend 부트캠프](https://learningspoons.com/course/detail/django-backend/)에서 팀 프로젝트를 진행하면서 다음과 같은 내용들에 대해 정리하고자 만들었습니다. 
- 팀 정책을 이것으로 정한 이유 
- 개발하면서 부딪힌 문제들에 대한 원인, 해결방안, 해결과정, 그리고 그 이유들

### 배포 작업 시작
1차적으로 구현한 기능을 마치고, Docker 내용을 복습한 후 배포 작업을 시작했다. 배포 작업 순서, 에러, 해결방안, 그리고 여러 이유들에 대해 정리해본다.


### 배포 관련 이전 학습 내용들
배포에 관련해서 배운 내용은 다음과 같다.    
- django live 강의에서는 docker를 사용하지 않고, AWS EC2 서버에서 nginx, uwsgi와 django app을 연결 후, AWS RDS, IAM, S3와 연결하여 배포했다.
- Docker 강의에서는 AWS EC2 서버에서 Docker container만을 사용하여 nginx, gunicorn, django app, postgreSQL image들을 사용하여 배포했다. 

### 위 내용을 바탕으로 구성한 배포 구조    
그래서 이번 프로젝트 배포에서는 django live 강의 마지막에 학습한 배포와 docker 강의에서 학습한 docker를 활용한 배포 내용을 정리한 것을 보고 합하여 진행해본다.
- AWS EC2 서버에 docker-compose를 사용하여 nginx, django의 각 custom image를 만든다. django app에는 AWS RDS(postgreSQL), IAM, S3 연결 세팅을 해놓은 후 build 실행한다. 

### uWSGI와 gunicorn 중 후자를 택한 이유

uWSGI 와 gunicorn 중 gunicorn을 선택했다. 

지난 강의 때 들었던 내용을 정리한 [WSGI의 종류](https://jeha00.github.io/post/network/webapplicationbasic01/#wsgi%EC%9D%98-%EC%A2%85%EB%A5%98)를 참고하면 gnicorn은 입문자가 사용하기에 편리하고, 성능을 내는 데에는 uWSGI가 좋다고 판단했다. 

하지만, 이외의 여러 블로그 의견들을 취합했고 많은 공통된 의견들을 발견했다. 

한 예로 [python개발자 uWSGI를 버리고 gunicorn으로 갈아타다.](https://discord.com/channels/1018414416510861382/1031916397137231955/1051371601402265671)을 근거로 전달하자면 uWSGI는 다른 WSGI에 비해서 무거워 자원 소모가 큰 문제점이 있다. 

하지만, gunicorn은 가볍고 큰 프로젝트가 아닌 경우 uWSGI보다 빠른 성능을 낸다는 장점이 있다. 

현재 프로젝트는 규모가 크지 않아서 uWSGI보다는 gunicorn을 사용하는 것이 적절하다고 판단했다. 


### root 계정으로 하지 않기 

root 계정으로 배포를 하게 되면 일반 user는 로그인하지 못하기 때문이다.


&nbsp;

---


# 1. git clone 및 file directory 구조

### git clone 하여 가져오기 

`~/development/devket` directory를 생성하여 git clone으로 가져온다.

이에 따라 requirements.txt를 docker compose를 실행하기 위해서 위치를 바꾼다. `Devket` 안이 아니라 `Dockerfile`과 동일한 레벨로 옮긴다.


### file directory

익숙하지 않아서 먼저 구조를 잡기 위해 파일들을 생성했다.

아래 파일 구조를 출력하기 위해 `sudo apt-get install tree`을 설치한 후, `tree ./`을 입력한다.

```yml
# ~/deployment

./
├── devket
│   ├── Devket
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

&nbsp;

---

# 2. Dockerfile 생성

## 2.1 django app(devket) Dockerfile

```yml
FROM python:3.10.8

WORKDIR /usr/src/app

COPY . .

RUN python -m pip install --upgrade pip \
  && pip install -r requirements.txt \
  && pip install boto3 \
  && pip install django-storages \
  && pip install gunicorn

# git
WORKDIR ./Devket

RUN python manage.py collectstatic --noinput

CMD sh -c "python manage.py makemigrations \
    && python manage.py migrate \
    && python manage.py runserver 0:8000"

CMD gunicorn config.wsgi:application --bind 0:8000

EXPOSE 8000
```

- [boto3를 사용하는 이유](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html#boto3-documentation): 파이썬 언어를 사용하여 EC2, S3 같은 AWS 서비스를 구성하고 관리하려면 boto3를 사용해야 한다.
- [django-storages를 설치하는 이유](https://django-storages.readthedocs.io/en/latest/): 장고 프로젝트가 특정 storage를 사용하기 위해서 설치한다.
- [docs.docker - CMD](https://docs.docker.com/engine/reference/builder/#cmd)
    - 위 문서에 따르면 기본 명령어 형식으로 CMD는 여러 개를 입력할 수 없다. 그래서 shell에 입력하듯이 하는 방식으로 CMD를 입력했다.


### ❗️ EOFError
`python manage.py collecstatic`은 input 값을 기본적으로 필요로 하는 명령어다. 

오랫동안 입력값이 없으면 EOFError가 발생한다. 그래서 옵션으로 `--noinput`을 하면 input 값을 기다렸다가 yes로 자동적으로 입력되어 진행된다. 

Dockerfile이 진행되는 동안 입력값을 받지 못하고, 처음 image build 시 collectstatic을 반드시 실행해야하기 때문에 이 옵션을 사용했다.

- 출처: [Where to run collectstatic when deploying django app...using Docker](https://stackoverflow.com/questions/59719175/where-to-run-collectstatic-when-deploying-django-app-to-heroku-using-docker)

&nbsp;


## 2.2 django app Dockerfile version 입력

위 Dockerfile에서 다음 명령어를 삭제 또는 수정하기로 했다. 

```yml
&& pip install boto3 \
&& pip install django-storages \
&& pip install gunicorn 
```

왜냐하면 정확한 버전이 적혀있지 않아서 설치할 때 최신 버전을 설치하게 되는데, 이런 방식으로 설치하는 건 지금은 문제가 없을 수 있다.

하지만, **그 당시 최신 버전에서 작동되었던 코드들이 업데이트 되면서 작동되지 않을 수 있기 때문이다.** 

그래서 **삭제 후, requirements.txt에 버전 명과 함께 구체적으로 입력한다.**

```txt
...
boto3==1.26.27
django-storages==1.13.1
gunicorn==20.1.0 
...
```

또는 위 명령어를 삭제하지 않고, 아래와 같이 정확한 버전 명을 입력하는 것도 또 하나의 방법이다.

```yml
&& pip install boto3==1.26.27 \
&& pip install django-storages==1.13.1 \
&& pip install gunicorn==20.1.0
```

&nbsp;

## 2.3 nginx Dockerfile

```yml
FROM nginx
RUN rm /etc/nginx/conf.d/default.conf
COPY default.conf /etc/nginx/conf.d
CMD ["nginx", "-g", "daemon off;"]
```


&nbsp;

---
# 3. nginx의 default.conf

- 80 port로 트래픽을 받아서 web application에 전달한다. 

- proxy_pass에서 devket은 

```yml
server {
	listen 80;
	server_name localhost;

	location /{
		proxy_pass http://devket:8000;
	}
}
```

&nbsp;

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

### ❗️ 502 Bad Gateway


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

&nbsp;

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

🔆 `super user`가 생성가능하면 DB 연결이 된 것이다.

<br>

### ❗️ RDS 생성할 때 DB 이름의 기본값 

RDS 생성 시, 추가사항 탭 클릭하여 DB 이름을 설정하지 않았다면 기본값으로 'postgres'가 입력된다. 

DB를 하나만 사용하고 있는 상황에서는 반드시 DB 이름을 입력할 필요는 없다고 판단되나, 프로젝트의 첫 배포 과정으로서 DB에 이름을 부여하고 싶어 입력했다.


&nbsp;

---

# 6. S3 연결하기

S3에 연결하기 전에 이전에 학습해던 [3가지 방식](https://jeha00.github.io/post/django/deployment-with-nginx-uwsgi-ec2_3/#1-static-file-serving)을 다 사용하면서 왜 S3를 사용하는지 되새겨본다.

## 6.1 첫 번째 방법: ’location /static/’ 추가
> **_문제점: admin에 적용되는 css를 확인할 수 없다._**  

```yml
# 경로: nginx의 default.conf
# 추가할 파일 내용

location /static/ { 
        alias   /devket/static/;
}
```

&nbsp;

## 6.2 두 번째 방법: collectstatic
> **_- 장점: 첫 번째 방법에 대한 문제점 해결_**  
> **_- 문제점: 프로젝트 내부에 정적 파일들을 모아놓기 때문에, 서버 부하를 피할 수 없다._**  

`python manage.py collectstatic` 명령어를 사용하여 모든 static 파일들을 public directory 안에 모으기 위해서 location 설정을 바꾼다.

-  settings.py 설정 변경

    ```yml
    # 경로: cofig/settings.py
    # 추가할 파일 내용

    # STATIC
    STATIC_URL = "/static/"
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

&nbsp;

## 6.3 세 번째 방법: S3에 연결하기

> **_admin에 적용되는 css도 확인할 수 있으면서, 내부가 아닌 외부 AWS S3에 모아놓은 정적 파일들을 올려서 서버 부하를 분산시키기 때문에 이 방식을 최종적으로 선택한다._**  


### 6.3.1 AWS S3 bucket 생성하기  

- bucket 명: devket
- 객체 소유권: ACL 활성화 + 객체 소유권: 객체 라이터
- 퍼블릭 액세스 차단 설정
![image](https://user-images.githubusercontent.com/78094972/206662633-05848dcc-79ba-49f8-924a-97a6fa6103d5.png)

<br>

### 6.3.2 AWS IAM을 사용하는 이유

> _IAM 역할을 사용하면 일반적으로 조직의 AWS 리소스에 대한 액세스 권한이 없는 사용자나 서비스에 액세스 권한을 위임할 수 있습니다.  ... 그러면 애플리케이션이 이러한 자격 증명을 사용해 Amazon S3 버킷 또는 Amazon DynamoDB 데이터 등의 리소스에 액세스할 수 있습니다._   
> 출처: [AWS - manage-roles](https://aws.amazon.com/ko/iam/details/manage-roles/)

AWS S3 bucket을 데이터 저장소로 사용한다면 IAM을 사용해서 액세스 권한을 위임받아야한다는 내용이므로, 반드시 IAM을 사용해야 한다.

<br>

### 6.3.3 AWS IAM에서 다운 받은 key를 settings.py에 반영하기

- [이 링크](https://jeha00.github.io/post/django/deployment-with-nginx-uwsgi-ec2_3/#3-aws-iam%EC%97%90%EC%84%9C-%EB%8B%A4%EC%9A%B4-%EB%B0%9B%EC%9D%80-key%EB%A5%BC-settingspy%EC%97%90-%EB%B0%98%EC%98%81%ED%95%98%EA%B8%B0)를 따라서 생성한다.

- 하지만, config/settings.py 에 추가되는 설정은 아래 코드를 사용한다. 기존에는 KEY가 오픈되었지만, `get_secret()`을 사용하여 드러나지 않도록 했다.
- `secret.json` 에도 추가하도록 한다.

```python
AWS_ACCESS_KEY_ID = get_secret("AWS_ACCESS_KEY_ID")
AWS_SECRET_ACCESS_KEY = get_secret("AWS_SECRET_ACCESS_KEY")
AWS_REGION = "ap-northeast-2"
AWS_STORAGE_BUCKET_NAME = "devket"
AWS_S3_CUSTOM_DOMAIN = f"{AWS_STORAGE_BUCKET_NAME}.s3.{AWS_REGION}.amazonaws.com"
AWS_DEFAULT_ACL = "public-read"
DEFAULT_FILE_STORAGE = "config.storages.S3DefaultStorage"
STATICFILES_STORAGE = "config.storages.S3StaticStorage"
```


<br>

### 6.3.4 config/storages.py 추가하기

```python
from storages.backends.s3boto3 import S3Boto3Storage

class S3DefaultStorage(S3Boto3Storage): 
        location = "media"

class S3StaticStorage(S3Boto3Storage): 
        location = "static"
```

<br>

### 6.3.5 현재 과정에서 file directory 구조

```yml
# ~/deployment

./
├── devket
│   ├── Dockerfile
│   ├── Devket
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


### 6.3.6 static file들을 S3로 옮기기:`python manage.py collectstatic

❗️**python manage.py collectstatic 시 발생된 Error**

- **첫 번째 Error** : botocore.errorfactory.NoSuchBucket: An error occurred (NoSuchBucket) when calling the PutObject operation: The specified bucket does not exist
    - 지정한 bucket이 존재하지 않는다는 의미다. S3의 bucket name이 storages.py의 BUCKET_NAME과 동일한지 비교한다.

- **두 번째 Error** : botocore.exceptions.ClientError: An error occurred (AccessControlListNotSupported) when calling the PutObject operation: The bucket does not allow ACLs
    - bucket 인식문제는 해결되었지만, 이 문제가 새롭게 발생했다. bucket 생성 시, 아래 설정대로 했는지 확인해보자.
    - ![image](https://user-images.githubusercontent.com/78094972/206662633-05848dcc-79ba-49f8-924a-97a6fa6103d5.png)


<br>

### 6.3.7 nginx의 location url로 경로 바꾸기

- 이 단계까지 수행하면 css file들이 AWS S3 bucket으로 연결되지 않은 걸 확인할 수 있다. 그래서 nginx의 default.conf 설정을 수정해야한다. 
- 생성한 bucket에 들어가서 새로고침을 하면 `static/`이 생긴 걸 확인할 수 있다. 이를 선택하면 `URL 복사`가 활성화되는데, 이 버튼으로 복사해서 alias 옆 경로를 붙여넣는다.

```yml
location /static/ {
                alias https://devket.s3.ap-northeast-2.amazonaws.com/static/;
}
```

&nbsp;

### ❗️Error: Django amazon S3 SuspiciousOperation

위 nginx의 location url 경로를 수정해도 이와 같은 error가 발생했다. 

`django S3 연결 SuspiciousOperation`을 검색하니 아래와 같은 문서가 떴다.

읽어보면 해결 방법은 총 3가지다. 

- 첫 번째 방법은 storages.py 의 class 내용을 수정하는 방식 
- 두 번째 방법은 static tag 뒤에 파일 경로의 시작 부분 `/`를 삭제하는 방식 
- 세 번째 방법은 img의 src에 static template tag를 사용하는 방식

기존에 알고 있던 django template tag를 사용하는 방식이 보다 django 를 잘 활용하는 개발 방식이라 판단하여 세 번째 방식을 선택했다. 그래서 img src에도 django template tag를 적용했다. 

그 전에는 이를 적용하지 않았던 이유는 django static template tag를 head tag 안에 import 시에만 사용하고, body 안에는 사용한다는 생각을 못 했기 때문이다. 

위 방식을 알아낸 것은 아래 문서를 통해서 인지했다.

출처: [Django amazon s3 SuspiciousOperation](https://stackoverflow.com/questions/25456420/django-amazon-s3-suspiciousoperation)

&nbsp;

### S3 CORS issue

S3 CORS issue는 해당 링크 [Project: deployment issue - S3 CORS](https://jeha00.github.io/post/project/deployment/02_deployment_cors/)를 참고한다.


---
# 7. Django image size 줄이기: slim

`docker image ls`로 확인한 결과 django image의 size는 다음과 같다. 

```yml
REPOSITORY                TAG       IMAGE ID       CREATED          SIZE
deployment-devket         latest    059f304f1f5d   11 minutes ago   1.09GB
```

1GB를 넘기 때문에 이를 줄이고자 python 버전을 slim으로 다운받아서 비교해본다. 

현재 django Dockerfile에서 `FROM python:3.10.8`으로 python 설치를 시작하지만, `FROM python:3.10.8-slim`으로 수정했다.

docker compose build를 실행한 결과, 설치 실패가 떴다. 

에러 내용은 다음과 같다. 

```yml
Error: pg_config executable not found.

pg_config is required to build psycopg2 from source.  
Please add the directory containing pg_config to the $PATH 
or specify the full executable path with the
    option: python setup.py build_ext --pg-config /path/to/pg_config build ... 
    or with the pg_config option in 'setup.cfg'
```

[stackoverflow - pg_config executable not found](https://stackoverflow.com/questions/11618898/pg-config-executable-not-found)에 모든 답변을 따라서 시도했지만 계속해서 같은 오류가 떴다. 그래서 `pip install psycopg2-binary=2.9.5`을 설치하기로 한다. 


psycopg2 와 psycopg2-binary의 차이는 다음 글을 참고했다. 
- 먼저 공식문서 [psycopg2 vs psycopg2-binary](https://www.psycopg.org/docs/install.html#psycopg-vs-psycopg-binary)와 [what is the different about psycopg2 and psycopg2-binary python package](https://stackoverflow.com/questions/70330567/what-is-the-different-about-psycopg2-and-psycopg2-binary-python-package) 를 참고했다.

`docker image ls`로 확인해보면 다음과 같은 크기로 줄어들었다.

```yml
REPOSITORY                TAG       IMAGE ID       CREATED         SIZE
deployment-devket         latest    2c0746fca459   2 minutes ago   308MB
```

동일한 기능을 낸다면 가벼운 것과 상대적으로 무거운 것 중 가벼운 것으로 가는 방향이 cost가 덜 나가기 때문에 size를 줄이는 방향을 선택했다. 

&nbsp;

---
# Reference


- [WSGI의 종류](https://jeha00.github.io/post/network/webapplicationbasic01/#wsgi%EC%9D%98-%EC%A2%85%EB%A5%98)
- [python개발자 uWSGI를 버리고 gunicorn으로 갈아타다.](https://discord.com/channels/1018414416510861382/1031916397137231955/1051371601402265671)
- [boto3를 사용하는 이유](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html#boto3-documentation)
- [django-storages를 설치하는 이유](https://django-storages.readthedocs.io/en/latest/)
- [docs.docker - CMD](https://docs.docker.com/engine/reference/builder/#cmd)
- [Where to run collectstatic when deploying django app...using Docker](https://stackoverflow.com/questions/59719175/where-to-run-collectstatic-when-deploying-django-app-to-heroku-using-docker)
- [RDS 연결하기](https://jeha00.github.io/post/django/deployment-with-nginx-uwsgi-ec2_3/#2-rds-%EC%97%B0%EA%B2%B0%ED%95%98%EA%B8%B0)
- [staticfiles 연결 3가지 방식](https://jeha00.github.io/post/django/deployment-with-nginx-uwsgi-ec2_3/#1-static-file-serving)
- [AWS IAM에서 다운 받은 key를 settings.py에 반영하기](https://jeha00.github.io/post/django/deployment-with-nginx-uwsgi-ec2_3/#3-aws-iam%EC%97%90%EC%84%9C-%EB%8B%A4%EC%9A%B4-%EB%B0%9B%EC%9D%80-key%EB%A5%BC-settingspy%EC%97%90-%EB%B0%98%EC%98%81%ED%95%98%EA%B8%B0)
- [Django amazon s3 SuspiciousOperation](https://stackoverflow.com/questions/25456420/django-amazon-s3-suspiciousoperation)  
- [AWS - manage-roles](https://aws.amazon.com/ko/iam/details/manage-roles/)  
- [stackoverflow - pg_config executable not found](https://stackoverflow.com/questions/11618898/pg-config-executable-not-found)
[psycopg2 vs psycopg2-binary](https://www.psycopg.org/docs/install.html#psycopg-vs-psycopg-binary)
- [what is the different about psycopg2 and psycopg2-binary python package](https://stackoverflow.com/questions/70330567/what-is-the-different-about-psycopg2-and-psycopg2-binary-python-package)