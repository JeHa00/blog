---
title: "Django study: nginx와 uwsgi를 사용한 django application deployment 02 - 502 Bad Gateway Error"
date: 2022-10-15T03:28:59+09:00
draft: false
summary: nginx와 uwsgi를 사용한 배포과정에서 발생한 502 Bad Gateway Error에 대한 해결과정과 해결책을 정리해본다.
tags: ["Django"]
categories: "Django"
---

# ❗️ 502 Bad Gateway Error 해결

위 작업을 다 완료했음에도 불구하고도, `sudo service nginx reload` 후, 502 Bad Gateway Error가 뜬다면 무엇이 원인일까?

더 정확하게 원인을 알고자 log에 접근했다.

- `vi /etc/nginx/nginx.conf` 로 들어가서 error_log 경로를 찾아보자. 
    - 찾은 경로로 `tail -f <찾은 경로>` 를 입력하여 error 확인하기

    ```yml
    2022/10/15 13:18:01 [error] 19512#19512: 
    *71 upstream prematurely closed connection while reading response header from upstream, 
    client: 27.34.20.255, 
    server: _, 
    request: "POST / HTTP/1.1", 
    upstream: "uwsgi://unix:///tmp/app.sock:", 
    host: "13.125.216.246"
    ``` 

위에 메세지에서도 HTTP response message의 header를 읽는 동안 연결이 완전히 닫혀졌다는 걸 알 수 있다. 

그러면 무엇과 무엇 사이가 닫힌 것일까? 

nginx와 uwsgi 사이일까?  

- 그래서 django과 nginx에서 '502 Bad Gateway'로 검색해봤지만, 마땅한 결과가 뜨지 않아 stackoverflow에 검색한 결과 다음과 같은 결과를 알 수 있었다.

출처: [django with nginx uwsgi bad gateway 502](https://stackoverflow.com/questions/42063718/django-with-nginx-uwsgi-bad-gateway-502)

- nginx는 들어온 request를 전달했지만, nginx와 연결된 대상이 없어서 response를 받지 못한 상황이라고 한다. 즉, nginx와 uwsgi 와의 연결이 끊어져있다는 상황이다. 

- 그래서 uwsgi 가 제대로 설치되고 있는지 확인하라고 했다.
    - `which uwsgi` 를 입력해보니 확실한 경로가 나와서 설치된 걸 확인할 수 있다.

- 그러면 uwsgi가 실제로 실행되고 있는지를 확인해보자.
    - 단지 명령어만 입력하면 실행될거라 생각했지만, 그렇지 않았던 것이다. 

    ```
    $ tail -f uwsgi.log
    ...
    --- no python application found, check your startup logs for errors ---
    ...
    ```

    - python application을 발견할 수 없다고 뜬다. 

- 혹시 모르니 `uwsgi`가 작동이 잘되는지도 확인해보자. 

    ```yml
    $ tail -f /var/log/nginx/error.log;
    2022/10/16 17:47:11 [notice] 36887#36887: signal process started
    ```
    - 또는 `sudo service nginx status`를 실행하여 running을 확인한다.


그러면 uwsgi 를 설치하는 것에 문제가 생긴 것으로 판단된다. 직접 확인해보자.

- `ls -al ./venv/lib/python3.9/site-packages` 를 입력하여, `uWSGI-2.0.20.dist-info` 를 확인해보자. 그러면 존재하지 않는다는 걸 알 수 있다.

- 원인을 분석해보면 위에 pip install uwsgi 명령어를 입력하여 오류가 떠서, 구글링을 통해 `sudo apt install uwsgi`가 이를 대체할 수 있다고 판단하여 설치했으나 정확하게 설치되지 않은 걸로 판단된다.

뜬 Error를 면밀히 살펴보면 `Exception: you need a C compiler to build uWSGI` 내용을 확인할 수 있다. 

- 위 Error가 떴다면 [Install package](#51-install-package) 과정의 명령어들을 다 실행하지 않은 것이니, 다시 실행해보자. 


- 그럼에도 안된다면 아래 명령어를 다시 실행해하여 `Public IPv4/`를 입력하자. 

```yml
# 경로: www/ls-django/.conf
$ uwsgi --ini uwsgi.ini
```

&nbsp;

---

# Reference

- [django with nginx uwsgi bad gateway 502](https://stackoverflow.com/questions/42063718/django-with-nginx-uwsgi-bad-gateway-502)
