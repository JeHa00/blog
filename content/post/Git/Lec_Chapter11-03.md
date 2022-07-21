---
title: "[TIL] Git study: Lecture Chapter 11 - Git blame & GitLens"
date: 2022-07-18T15:16:52+09:00
draft: false
summary: git blame 명령어와 VSC의 extension program인 GitLens를 통해 각 라인의 작성자를 확인하는 법을 알아본다.  
tags: ["TIL", "git"]
categories: ["git"]
---


# 0. Introduction

- 해당 내용은 [제대로 파는 Git & GitHub - by 얄코 ](https://www.inflearn.com/course/%EC%A0%9C%EB%8C%80%EB%A1%9C-%ED%8C%8C%EB%8A%94-%EA%B9%83/dashboard)을 통해서 공부한 내용입니다.

<br>

- Chapter 11에서는 분석하고 디버깅하는 것에 대해 다룬다.  
- 그래서 이번 소단원에서는 `git blame` 명령어를 통해 각 라인의 작성자를 확인하는 법에 대해 알아본다.  

<br>

---
# git blame

🔅 코드에 대한 책임을 따지는 것을 넘어서 이 코드를 수정해도 되는지, 왜 이렇게 했는지를 묻기 위해서도 필요하다.  

- **파일의 부분별로 작성자 확인하기**
    
    
    - `git blame (파일명)`  
    - 파일명을 CLI에 입력할 때는 첫 글자를 입력하고 `tab`을 누르면 자동완성된다.   

    ```yml

    $ git blame whoCodedThis.txt
    ```

    - 그러면 왼쪽 칼럼 부분에 괄호 치고 작성자가 누군지 알 수 있다. 
    - `git config user.name` 을 바꿔가면서 작성자를 바꿨다. 
    - 맨 오른쪽 칼럼 부분은 코드의 각 줄을 의미하는데, 각 줄마다 누가 작성했는지를 알 수 있다. 

- **특정 부분 지정해서 작성자 확인하기**


    - `git blame -L (시작줄) (끝줄, 또는 +줄수) (파일명)`

    ```yml
    $ git blame -L 10,12 whoCodedThis.txt
    65f63a2d (pikachu 2022-01-04 13:31:27 +0900 10) 
    d1ef31c6 (mito    2022-01-04 13:31:45 +0900 11) 나 미친토끼 미토에요.
    d1ef31c6 (mito    2022-01-04 13:31:45 +0900 12)

    # 또는 다음과 같이 입력할 수 있다. 

    $ git blame -L 10,+3 whoCodedThis.txt
    65f63a2d (pikachu 2022-01-04 13:31:27 +0900 10) 
    d1ef31c6 (mito    2022-01-04 13:31:45 +0900 11) 나 미친토끼 미토에요.
    d1ef31c6 (mito    2022-01-04 13:31:45 +0900 12)
    ```


<br>

---
# GitLens 

- git 명령어로 하는 방법보다 권장되는 방법으로, 플러그 인 프로그램인 `GitLens`를 사용하는 것이다.  
- source tree로도 볼 수 있지만, `GitLens`를 사용하는 걸 추천한다.  

<br>

---

# Reference

- [제대로 파는 Git & GitHub - by 얄코](https://www.inflearn.com/course/%EC%A0%9C%EB%8C%80%EB%A1%9C-%ED%8C%8C%EB%8A%94-%EA%B9%83/dashboard)
