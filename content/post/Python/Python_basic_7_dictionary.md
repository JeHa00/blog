---
title: "[TIL] Python basic 7: dictionary "
date: 2022-03-01T11:52:48+09:00
draft: false
summary: dictionary type의 선언, 출력과 수정 그리고, dictionary에 사용되는 함수에 대해 알아본다.
tags: ["TIL", "python"]
categories: ["Python"]
---

# 0. Introduction

- **범용적으로 가장 많이 사용되는 기초 자료형**

- **형식**

  - `key : value`로 구성된다.
  - A가 key이고, B가 value라고 할 때,
  - 주로 중괄호 {A : B} 를 사용한다.
  - 또 다른 선언법으로는 dict([(A, B)]), dict(A = B)을 사용한다.

- **sequence X => key 값으로 index 접근한다. => key 값은 중복되면 안된다.**

  - key는 숫자, 문자 다 가능하다.
  - key만 존재하면 value는 어떤 자료 형태든 가능하다.
  - key가 중복되지 않기 때문에, value는 중복 가능하다.

- **immutable과 mutable이 공존**
  - `key` 는 `immutable` 이다.
    - 그래서 `key` 값으로 `list` data type은 불가능하고, `tuple` data type은 가능하다.
  - `value`는 `mutable`이다.

> 만약 mutable data type으로 key 값을 만들었을 경우, TypeError가 뜬다.

```yml
# key 를 list로 만들었을 경우
TypeError: unhashable type: 'list'
```

&nbsp;

---


## 1. dictionary 선언


- dictionary 선언에는 매우 다양한 방법이 있다.
- 이 다양한 방법들의 공통점은 { }, key, value 로 기본적으로 구성된다.

```yml
# key와 value 모두 string
> a = {'name' : 'Kim', 'phone': '01012345678', 'birth': '870124'}

# key는 integer 자료형, value는 string
> b = {0 : 'Hello Python!'}

# key는 string, value는 list
> c = {'arr': [1, 2, 3, 4]}

> d = {
    'Name' : 'Niceman',
    'City' : 'Seoul',
    'Age' : 33,
    'Grade' : 'A',
    'Status' : True
    }

# 하나 하나 튜플 형식으로 입력한다. 가독성이 좋지 않은 단점이 있다.
# 그래서 개선된 방법이 f 다.
> e = dict([
    ( 'Name', 'Niceman'),
    ('City', 'Seoul'),
    ('Age', '33'),
    ('Grade', 'A'),
    ('Status', True)
> ])


# 이 형태로 DB에 저장하거나, 엑셀이 쓰거나, 웹 서비스 형태에 사용된다.
# java에서는 map 이라고 한다.
# JSON 형태도 이런 방식으로 되어 있다.
> f = dict(
    Name = 'Niceman',
    City = 'Seoul',
    Age = '33',
    Grade = 'A',
    Status = True
> )

> print('a - ', type(a),a)
a -  <class 'dict'> {'name': 'Kim', 'phone': '01012345678', 'birth': '870124'}

> print('b - ', type(b),b)
b - <class 'dict'> {0: 'Hello Python!'}

> print('c - ', type(c),c)
c -  <class 'dict'> {'arr': [1, 2, 3, 4]}

> print('d - ', type(d),d)
d -  <class 'dict'> {'Name': 'Niceman', 'City': 'Seoul', 'Age': 33, 'Grade': 'A', 'Status': True}

> print('e - ', type(e),e)
e -  <class 'dict'> {'Name': 'Niceman', 'City': 'Seoul', 'Age': '33', 'Grade': 'A', 'Status': True}

> print('f - ', type(f),f)
f -  <class 'dict'> {'Name': 'Niceman', 'City': 'Seoul', 'Age': '33', 'Grade': 'A', 'Status': True}
```

&nbsp;

---


# 2. dictionary value 출력과 수정


## 2.1 dictionary value 출력

- value 출력에는 2가지 방법이 있다.
  - `print(a[key])` 로 출력하는 방법
  - `print(a.get(key))`로 출력하는 방법


- 첫 번째 방법은 key에 해당하는 value 값이 존재하지 않으면 `keyError`가 발생한다.
- 두 번째 방법은 key에 해당하는 value 값이 존재하지 않으면 `none`처리를 한다.
- `error`가 발생하면 중단되기 때문에, 실무에서는 두 번째 방법을 많이 사용한다.

> **_결론: Key에 해당하는 값을 사용할 때는 함수 `.get(key)` 를 사용하자._**

```yml
> print('a - ', a.get('name1'))
None

> print('a - ', a['name1'])
# error 발생으로 중단

> print('b - ', b[0])
> print('b - ', b.get(0))
Hello Python!

> print('c - ', c['arr'])
> print('c - ', c.get('arr'))
[1, 2, 3, 4]

# value 값이 여러 원소를 가지고 있을 경우, index를 사용하여 출력할 수 있다.
> print('c - ', c['arr'][3])
> print('c - ', c.get('arr')[3])
4

> print('d - ', d.get('Age'))
33

> print('e - ', e.get('Status'))
True

> print('f - ', f.get('City'))
Seoul

```

&nbsp;

## 2.2 dictionary 수정

- index에 key 값을 입력했을 때, 기존에 있던 key 면 수정이 되고, 없는 key면 추가된다.

```yml

# 수정
> b = {0 : 'Hello Python!'}
> b[0] = 'Good backend developer'
> print(b)
{0: 'Good backend developer'}

# 추가
> b[2] = 'Python Python'
> print(b)
{0: 'Good backend developer', 2: 'Python Python'}

```

&nbsp;

---


# 3. dictionary 함수

- `len` : key의 갯수 구하기
- `.keys()`: key 값만 출력
- `.values()`: value 값만 출력
- `.items()`: key, value 다 출력
- `.pop()`: list에서 배운 것처럼 한 성분을 빼서 제거하여 저장하는 함수이지만, list는 순서가 있기 때문에 항상 맨 마지막 성분이 제거된다. 하지만, dictionary는 순서가 없어서 `key` 값을 인자로 입력해야 한다.
- `.popitem()`: key와 value 중 아무거나 하나를 임의로 도출하여 없앤다.
- `.update()`: 다른 리스트를 넣어서, 키가 똑같은 것을 수정할 수 있다.
- `in` 연산자를 사용해서 해당하는 키 값이 있는지 알 수 있다.

<dr>

```yml
> a = {'name' : 'Kim', 'phone': '01012345678', 'birth': '870124'}
> d = {
    'Name' : 'Niceman',
    'City' : 'Seoul',
    'Age' : 33,
    'Grade' : 'A',
    'Status' : True
    }


# len
> print(len(a))
3

> print(len(d))
5

# .keys()
> print(a.keys())
dict_keys(['name', 'phone', 'birth'])

> print(d.keys())
dict_keys(['Name', 'City', 'Age', 'Grade', 'Status'])


# .values()
> print(a.values())
dict_values(['Kim', '01012345678', '870124'])

> print(d.values())
dict_values(['Niceman', 'Seoul', 33, 'A', True])


# .items()
> print(a.items())
dict_items([('name', 'Kim'), ('phone', '01012345678'), ('birth', '870124')])

> print(d.items())
dict_items([('Name', 'Niceman'), ('City', 'Seoul'), ('Age', 33), ('Grade', 'A'), ('Status', True)])


# list 안에 넣으면 key 값들만, value 값들만, item 값들만으로 list를 만든다.
> print(list(a.keys())
['name', 'phone', 'birth']

> print(list(a.values())
['Kim', '01012345678', '870124']

> print(list(a.items())
[('name', 'Kim'), ('phone', '01012345678'), ('birth', '870124')]

# .pop()
> print(a.pop('birth'))
870124

> print(a)
{'name': 'Kim', 'phone': '01012345678'}

> print(d.pop('Age'))
33

> print(d)
{'Name': 'Niceman', 'City': 'Seoul', 'Grade': 'A', 'Status': True}

# .popitem(): 추첨기에 사용할 수 있다.
> print(d.popitem())
('Status', True)

> print(d)
{'Name': 'Niceman', 'City': 'Seoul', 'Grade': 'A'}

> print(d.popitem())
('Grade', 'A')

> print(d)
{'Name': 'Niceman', 'City': 'Seoul', 'Grade': 'A'}

```


- `in` 연산자 사용하여 key 값 존재유무 확인

```yml
> a = {'name' : 'Kim', 'phone': '01012345678', 'birth': '870124'}
> print('name' in a)
True

> print('addr' in a)
False

```

&nbsp;

❗ 다른 data type을 dictionary로 만들 때 유의사항: 갯수가 맞아야 한다.

```python
> a = ['ab', 'cd', 'ef']
> print(dict(a))
{'a': 'b', 'c': 'd', 'e': 'f'}

# 하지만 갯수가 안맞으면??
# 길이가 맞지 않아 Error가 발생
> a = ['ab', 'cd', 'eff']
> print(dict(a))
ValueError: dictionary update sequence element; 

> a = [['a', 1], ['b', 2], ['c', 3]]
> print(a)
{'a': 1, 'b': 2, 'c': 3}

> a = [['a', 1], ['b', 2], ['c', 3, 4]]
> print(a)
# 위와 똑같은 Error가 발생된다.
```

&nbsp;

---

# Reference

- [Python tutorial](https://www.python-course.eu/python3_formatted_output.php)
- [프로그래밍 시작하기: 파이썬 입문 (Inflearn Original)](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%9E%85%EB%AC%B8-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)
