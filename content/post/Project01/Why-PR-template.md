---
title: "Project: Pull Request templates를 도입한 이유"
date: 2022-11-03T13:37:52+09:00
draft: false
summary: 프로젝트 진행에 Pull Request templates를 사용하는 이유와 적절한 PR size는 얼만큼 정했는지에 대해 공유해본다.
tags: ["github"]
categories: ["Project"]
---

# Introduction

- 이 repository는 현재 [러닝스푼즈 나노디그리 - django backend 부트캠프](https://learningspoons.com/course/detail/django-backend/)에서 팀 프로젝트를 진행하면서 다음과 같은 내용들에 대해 정리하고자 만들었습니다. 
    - 팀 정책을 이것으로 정한 이유 
    - 개발하면서 부딪힌 문제들에 대한 원인, 해결방안, 해결과정, 그리고 그 이유들

- 이번 포스팅에서는 왜 PR template을 팀 프로젝트에 도입하려한 이유를 설명하고자 합니다.
    - [Add template for pull request #5](https://github.com/backendnanodegree/Devket/pull/5)에 정리된 내용에 대해 설명하겠습니다. 
    - 선택한 PR template은 [PULL_REQUEST_TEMPLATE](https://github.com/backendnanodegree/Devket/blob/main/.github/PULL_REQUEST_TEMPLATE.md) 입니다.


<br>

---


# Why we use PR template? 

> **1. Template을 사용하여 모두의 가독성을 높이고 시간을 절약하기 위해서**  
> **2. PR을 작성하는 과정에서 작성해야할 것을 놓치지 않기 위해서**  
> **3. commit message로는 부족한 부분을 설명하기 위해서**  

템플릿을 반드시 사용해야하는 이유는 없습니다. 

하지만 템플릿을 사용하여 얻을 수 있는 이점들은 있습니다.

위 두 가지 이점에 대해 설명해보겠습니다.

## 모두의 가독성을 높이고 시간을 절약하기 위해서

template 이라는 형식이 없다면 각자 자유롭게 작성하기 때문에, **눈에 익숙하지가 않아** 글이 손쉽게 읽혀지지 않습니다.  

그리고 이 문단에서 또는 이 위치에서 말하는 바가 **내가 이해한 그 의도가 맞는지 확신을 갖기 어렵습니다.** 왜냐하면 평소에 각자 작성하는 글의 스타일이 달라서 어느 사람은 문두에 단지 이유를, 다른 사람은 바로 결론을 말하기 때문입니다. 이를 이해하기 위해서 작성자에게 다시 질문을 해야하고, 이를 위해서 또 시간을 할애해야 합니다. 

그래서 **템플릿을 통해 형식을 정한다면 계속해서 일정한 형식에 노출되면 이해하려는데 쏟는 시간이 단축되어 절약할 수 있습니다.** 시간도 자원이기 때문에 이를 절약하여 개발에 시간을 더 쏟기 위함입니다. 저희는 현재 부트캠프로 짧은 시간에 완성된 프로덕트를 만들기 위해 스프린트 기간을 1주일로 잡아 진행하고 있어서 시간을 절약하는게 중요하다고 생각합니다.

<br>


## 작성해야할 것을 놓치지 않기 위해서  

**템플릿은 작성자가 읽는 대상들이 반드시 이해해야하는 부분을 놓치지 않고 작성하도록 도와주는 가이드입니다.** 
저희는 PR을 작성하는 내용에 무엇이 들어가야하는지 정확히 알지 못하는 것뿐만 아니라, 체화되지 않았기 때문에 놓칠 수 있다고 판단했습니다. 
그래서 템플릿 '가이드'의 도움을 받아서 작성하는 게 없이 작성하는 것보다 개발자 준비생으로서 큰 도움이 될 거라 판단했습니다. 


<br>


## commit message로는 부족한 부분을 설명하기 위해서

commit message를 작성할 때, commit convention에 맞춰서 header와 body 부분을 작성합니다. 

팀원들끼리 의논한 결과, commit message body 부분에 상세히 적는 것보다 body 부분도 개략적으로 짧게 적은 후 PR template에 상세히 적는 것을 다들 선호하여 PR template에 보다 자세히 작성하기로 결정했습니다. 

<br>

---

# What size is good for PR?

> **한 개의 PR 당 code size를 최대 300줄로 유지하자.**  
> **단, css와 html은 이 줄에 포함시키지 않는다.** 

아래 reference를 참고하면 다음과 같은 그래프를 확인할 수 있습니다.

![image](https://i0.wp.com/smallbusinessprogramming.com/wp-content/uploads/2017/10/optimal_pull_request_size.png?w=566&ssl=1)

**사이즈가 너무 작아도 너무 커도 cost가 증가하는 걸 확인할 수 있습니다.** 저희가 프로젝트를 진행하면서 너무 PR이 크면 모든 코드를 보기 힘들고, 너무 작게 여러 번하는 것도 불편하고 시간이 많이 드는 걸 느꼈습니다. 

그래서 여러 멘토님들과 여러 블로그를 참조하고, 저희가 진행하면서 느낀 결론은 **한 개의 PR 당 code size를 최대 300줄로 유지하자** 는 결론을 내렸습니다. 


<br>

---
# Reference

- 참고한 PR template
    - [좋은 Pull Request를 만드는 방법과 PR Template 구성](https://2jinishappy.tistory.com/337)
    - 러닝스푼즈에서 VOD로 제공된 github 강의

- PR 크기에 대한 근거 자료: [SMALL BUSINESS PROGRAMMING](https://smallbusinessprogramming.com/optimal-pull-request-size/)