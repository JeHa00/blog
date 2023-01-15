---
title: "Study"
date: 2023-01-13T21:36:57+09:00
draft: true
summary: 
tags: [""]
categories: [""]
---

# 2023-01-13


https://docs.celeryq.dev/en/stable/getting-started/first-steps-with-celery.html#first-steps


## Message broker

celery는 _message broker_ 로 message를 보내고, 받는다.

message broker의 종류에는 RabbitMQ, Redis 등등이 있다.

- RabbitMQ: feature-complete, stable, durable, easy to install
    - a excellent choice for a production environment. 
    - sudo apt-get install rabbitmq-server
    - or, docker run -d -p 5672:5672 rabbitmq
    - [Using RabbitMQ](https://docs.celeryq.dev/en/stable/getting-started/backends-and-brokers/rabbitmq.html#broker-rabbitmq)

- Redis: feature-complete, more susceptible to data loss in the event of abrupt termination or power failures (왜 데이터 손실에 취약한가?)
    - docker run -d -p 6379:6379 redis
    - [Using Redis](https://docs.celeryq.dev/en/stable/getting-started/backends-and-brokers/redis.html#broker-redis)


## Application

- Installing Celery
    - `pip install celery`

- celery로 할 것들 중 예: creating tasks / managing workers



## django-celery-beat 설치 중 발생된 에러

`Failed building wheel for backports.zoneinfo`

확인한 글들
- https://stackoverflow.com/questions/71712258/error-could-not-build-wheels-for-backports-zoneinfo-which-is-required-to-insta
    - 파이썬 3.9이상부터는 지원하지 않는다고 하니 requirements.txt를 수정해야한다고 한다. 하지만 난 python 3.8.9 이므로 해당 안되는데, 왜 안될까?

팀 프로젝트에서 파이썬 버전 3.10.8을 사용하므로 이 버전으로 설치하여 다시 진행하니 문제 없이 진행되었다.
