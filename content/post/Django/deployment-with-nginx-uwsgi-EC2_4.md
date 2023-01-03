---
title: "Django study: nginx와 uwsgi를 사용한 django application deployment 04 - DB 나누기"
date: 2022-10-20T18:45:16+09:00
draft: false
summary: uwsgi, nginx 를 연결한 후, R3에 연결하여 static file을 적용했다. 그 다음으로 DB를 develop 단계와 product 단계로 나눠서 적용하기 위해 DB를 나누는 작업을 해본다. 
tags: ["Django"]
categories: ["Django"]
---
# 0. Introduction 

- 해당 강의는 [러닝스푼즈 - 나노디그리 Python & Django backed course](https://learningspoons.com/course/detail/django-backend/)의 김형종 강사님의 django 강의를 학습한 내용입니다.

- 만든 프로젝트를 EC2에 올리고, S3에는 STATIC 파일들을 저장하고, RDS를 연결해보는 실습을 해보고 나서 정리한 것을 올린다.

- 실제 현업에서는 **STATIC 파일을 올리는 방법들 중 S3를 사용하는 방식을 사용** 한다고 한다. 

- DB를 나눠서 적용해보는 걸 실습해본다.

&nbsp;

---
# 개발자 DB 와 운영 DB로 나누기 

### 첫 번째: config/settings 생성하기

- `config/settings` directory 생성 후, 그 안에 다음과 같이 생성하기
    - `__init__.py`
    - base.py
    - develop.py
    - production.py

### 두 번째: 기존 내용 base.py에 담기

기존 settings.py의 내용은 base.py에 담는다. 그리고, 기존 settings.py는 삭제한다.


### 세 번째: ALLOWED_HOSTS 내용 옮기기

기존에 있던 ALLOWED_HOSTS는 develop.py 와 production.py 에 옮긴다. 

- `config/settings/develop.py` 

    ```python
    from .base import * 

    DEBUG = TRUE

    ALLOWED_HOSTS = ["*"]
    ```

- `config/settings/production.py`

    ```python
    # config/settings/production.py
    from .base import *

    DEBUT = False

    ALLOWED_HOSTS = ["127,0,0,1"]
    ```

### 네 번째, settings option을 사용하여 runserver 실행 

그러면 settings를 나눴기 때문에, 옵션을 사용하여 적절한 설정을 택하여 서버를 실행시켜보자.

```yml
## terminal에 입력하기 

# develop setting 적용하기
$ python manage.py runserver --settings=config.settings.develop

# production setting 적용하기
$ python manage.py runserver --settings=config.settings.production
```

하지만 `FileNotFoundError`가 발생된다. 그 이유는 `settings` directory가 생기면서 기존 설정 파일의 디렉토리 레벨이 한 단계 깊어져서, `BASE_DIR`의 값이 달라졌기 때문이다. 

&nbsp;

### 다섯 번째, BASE_DIR 수정하기

처음에는 `BASE_DIR = Path(__file__).resolve().parent.parent` 이랬지만,


이처럼 `BASE_DIR = Path(__file__).resolve().parent.parent.parent` 수정하자.

그 후, 다시 아래 명령어를 실행하여 결과를 확인해보자. 

```yml
python manage.py runserver 0:8000 --settings=config.settings.production
```

그 결과, 화면에 `Disallowed Error`가 발생하는데, 정상적으로 잘 되었다는 걸 의미한다.  

&nbsp;

### 🔆 DB를 production과 develop를 나눈 이유

> **_develop 단계에 사용한 DB는 테스트를 위한 DB이기 때문에, production 단계에서의 DB와 다를 수 밖에 없다._**

- RDS를 연결하면 그 때 DB는 production 단계에서 사용한다.  

- 코드로 확인해보자.
    - production.py 에는 RDS DATABASES를 적용한다.   
    - develop.py 에는 기존 장고 프로젝트 생성 시, DB 내용을 적용한다고 하자.  

&nbsp;

- develop.py

```python
from .base import *

ALLOWED_HOSTS = ["*"]

DATABASES = { 
    "default": {
        "ENGINE": "django.db.backends.sqlite3",     
        "NAME": BASE_DIR / "db.sqlite3"
        }
}
```

&nbsp;


- production.py

```python
from .base import *

ALLOWED_HOSTS = ["127.0.0.1"]

DATABASES = { 
    "default": {
        "ENGINE": "django.db.backends.postgresql_psycopg2",     
        "HOST": "ls-django.cvhmktue5rnw.ap-northeast-2.rds.amazonaws.com",
        "NAME": "postgres", 
        "PORT": "5432",
        "USER": "learningspoons",
        "PASSWORD": [비밀번호], 
    }
}
```

&nbsp;

---

# Reference

- [러닝스푼즈 - 나노디그리 Python & Django backed course](https://learningspoons.com/course/detail/django-backend/)
