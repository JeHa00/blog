---
title: "[TIL] Python basic 9: 흐름 제어문 (if 조건문)"
date: 2022-03-01T11:52:55+09:00
draft: false
summary: 조건문의 기본 형식, 연산자를 통해서 제어하는 방법, 참거짓을 의미하는 값들, 다중 및 중첩 조건문, in & not in 으로 제어하는 방법을 알아본다.
tags: ["TIL", "python"]
categories: ["Python"]
---

## 1. 조건문 기본 형식

- `콜론(:)`으로 끝나는 부분을 `헤더(Header)`라고 한다.
- 헤더의 마지막 콜론은 바로 뒤에 `스위트`가 이어진다.
- `스위트`는 헤더와 한 세트로 따라다니는 `실행문`을 의미한다.
- if - elif - else
  > `if 식: 스위트` => if 문으로, 반드시 1개 필요하다.  
  > `elif 식: 스위트` => elif 문으로, 없어도 되며 있으면 n개 가능 (여러개 가능)  
  > `else 식: 스위트` => else 문으로, 없어도 되며 있으면 별다른 조건문 없이 `else:`로 끝난다. 1개만 가능하다.


- 반드시 `True` 여야 제어문이 실행된다.

```yml

> print(type(True))
> print(type(False))
<class 'bool'>

> if True:
>    print("Good")
Good

> if False:
>   print("Bad")
실행 X
```

- 그러면 언제 `True`이고, `False`인지 알아보자.


---

## 2. 연산자

- 연산자에는 지난 번에 봤던 산술 연산자 그리고, 관계 연산자, 논리 연산자가 있다.
- **관계 연산자** 에는 `>`, `>=`, `<`, `<=`, `==`, `!=` 가 있다.

```yml
> x = 15
> y = 10

# ==  양 변이 같을 때 참
> print(x == y)
False

# !=  양변이 다를 때 참
> print(x != y)
True

# >  왼쪽이 클 때 참
> print(x > y)
True

# >= 왼쪽이 크거나 같을 때 참
> print(x >= y)
True

# <  오른쪽이 클 때 참
> print(x < y)
False

# <=  오른쪽이 크거나 같을 때 참
> print(x <= y)
False
```

- **논리 연산자**에는 `and`, `or`, `not`이 있다.

```yml
> a = 75
> b = 40
> c = 10

> print('and : ', a > b and b > c)
and : True

> print('or : ', a > b or b > c)
or : True

> print('not : ', not a > b)
not : False

> print('not : ', not b > c)
not : False
```

- 산술, 관계, 논리 우선순위
  - 산술 > 관계 > 논리 순서로 적용한다.

```yml
> print('e1 : ', 3 + 12 > 7 + 3)
e1 :  True

> print('e2 : ', 5 + 10 * 3 > 7 + 3 * 20)
e2 :  False

> print('e3 : ', 5 + 10 > 3 and 7 + 3 == 10)
e3 :  True

> print('e4 : ', 5 + 10 > 0 and not 7 + 3 == 10)
e4 :  False

```

---


## 3. 참거짓 판별 종류

- 참 : "values", [values], (values), {values}, 1
- 거짓 : "", [], (), {}, 0, None

```yml
# city가 공란이므로 False를 의미한다.
# 그래서 else 문을 출력한다.
> city = ""
>
> if city:
>     print("You are in:", city)
> else:
>     print("Please enter your city")
Please enter your city

# city에 값이 value가 있으므로 True를 의미한다.
# 그래서 if 문을 출력한다.
> city = "Seoul"
>
> if city:
>     print("You are in:", city)
> else:
>     print("Please enter your city")
You are in: Seoul

```

---


## 4. 다중 조건문, 중첩 조건문, in & not in


### 4.1 다중 조건문

- 동일한 syntax의 조건문이 여러 개인 조건문을 `다중 조건문`이라 한다.

```yml
> nume = 90

> if num >= 90:
>   print('Grade : A')
> elif num >= 80:
>   print('Grade : B')
> elif num >= 70:
>   print('Grade : C')
> else:
>   print('과락')
Grade : A

```

&nbsp;

### 4.2 중첩 조건문

- 한 syntax 조건문 하에 여러 개의 조건문을 `중첩 조건문`이라 한다.

```yml
> grade = 'A'
> total = 80

> if grade == 'A':
>   if total >= 90:
>        print("장학금 100%")
>   elif total >= 80:
>       print("장학금 80%")
>   else:
>       print("장학금 70%")
> else:
>   print("장학금 50%")
장학금 80%

```

&nbsp;

### 4.3 in & not in

- `A in B` : B 안에 A가 있으면 참
- `A not in B` : B 안에 A가 없으면 참

```yml
# list
> q = [10, 20, 30]

# sets
> w = {70, 80, 90}

# dictionary
> e = {"name": 'Lee', "city": "Seoul", "grade": "A"}

# tuple
> r = (10, 12, 14)

> print(15 in q)
False

> print(90 in w)
True


> print(12 not in r)
False


# key 검색. dictionary를 in 사용하여 검색할 때 default는 keys 다.
> print("name" in e)
True

# value 검색
> print("seoul" in e.values())
False

```


---

# Reference

- [Python tutorial](https://www.python-course.eu/python3_formatted_output.php)
- [프로그래밍 시작하기: 파이썬 입문 (Inflearn Original)](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%9E%85%EB%AC%B8-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)
