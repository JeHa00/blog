---
title: "[TIL] Python basic 3: numeric data"
date: 2022-02-28T18:51:39+09:00
draft: false
summary: 첫 번째, python의 자료형 종류에는 무엇이 있는지 알아본다. 두 번째, 숫자형 데이터 타입의 선언, 연산, 그리고 형 변환에 대해 중점적으로 알아보겠다.
tags: ["TIL", "python"]
categories: ["Python"]
---

# 1. 문자열 생성, 출력 그리고, 길이

- 문자열 생성하기(선언하기) 및 출력하기

```python
# 문자열 생성
> str1 = "I am Python"
> str2 = 'Python'

# 빈 문자열 생성
> str_t1 = ''
> str_t2 = str()

# 문자열 출력
> print(str1, type(str1))
I am Python <class 'str'>

> print(str2, type(str2))
Pyton <class 'str'>

> print(str_t1, type(str_t1))
  <class 'str'>

> print(str_t2, type(str_t2))
  <class 'str'>
```

- 문자열 길이 측정: `len()` 을 많이 사용한다. 길이에는 공백을 포함한다.

```python
> print(len(str1))
11

> print(len(str2))
6
```

<br>

### 결론

- 문자열 선언에는 "", '', """""", '''''' 을 사용한다.
- 문자열 출력에는 다른 출력과 동일하게 print를 사용한다.
- 문자열 길이 측정에는 len() 함수를 사용한다. 길이에는 공백을 포함한다.

---

<br>

# 2. Escape, raw string and multi line

## 2.1 Escape code

```yaml
\n: 개행(줄바꿈)
\t: 탭키를 누른 만큼 벌어짐
\\: 문자 삽입
\': 문자 삽입
\": 문자 삽입
```

- 위에 escape code 예시를 작성해보자.

```python
## 문자 삽입 escape code

> escape_str1 = "do you have a \"retro games\"?"
> print(escape_str1)
do you have a "retro games?"

# 홀따옴표 사용
> escape_str2 = 'What\'s on TV??'
# 쌍따옴표 사용
> escape_Str3 = "what's one TV??"
> print(escape_str2)
> print(escape_str3)
What's on TV??

## 개행, 탭
# tab 누른 만큼 떨어져서 출력
> t_s1 = "Click \t Start!"
> print(t_s1)
Click   Start!

# 줄 바껴서 출력
> t_s2 = "New Line\n Check!"
> print(t_s2)
New Line
Check!

```

## 2.2 Raw String

- 소문자 r을 붙이면 Escape 표현을 무시하고 그대로 다 출력한다.
- Escape 표현을 사용하지 않기 위해 선언!
- 이런 게 있다는 정도만 알고 있기

```python
# raw string 미포함
> raw_s1 = "\\x\y\z\q"
> print(raw_s1)
\x\y\z\q

# raw string 포함
> raw_s1 = r"\\x\y\z\q"
> print(raw_s1)
\\x\y\z\q

```

## 2.3 Multi Line

- 여러 줄 출력하는 방법으로 `\(역슬러쉬)` 를 사용한다.
- 역슬러쉬를 사용하여 파이썬에게 어떤 변수를 binding 한다는 걸 의미한다. 그래서 다음 줄에 변수를 선언한다는 걸 의미한다.
- 콤마는 ```,""" 처럼 3개 이상을 사용한다.

```python


>  multi_str1 =  \
    """
    문자열
    멀티라인 입력
    테스트

    """

> print(multi_str1)
문자열
멀티라인 입력
테스트

```

### 결론

- 이스케이프 코드를 사용하여 개행, 탭만큼 띄우기, 따옴표 문자 삽입이 가능하다.
- 이스케이프 코드를 사용하고 싶지 않을 때는 string code를 사용한다.
- 한 줄로는 너무 길어서 여러 줄로 표현하고 싶을 때, 백슬러쉬와 따옴표 3개를 사용하여 멀티라인으로 출력한다.

---

<br>

# 3. 문자형 연산

- 문자형 연산으로는 **_문자형끼리_** 덧셈, 문자형을 반복해서 출력하기 위한 곱셈이 가능하다.

```python
> str_o1 = "python"
> str_o2 = "Apple"
> str_o3 = "How are you doing"
> str_o4 = "Korea Japan America"

> print(3 * str_o1)
pythonpythonpython

> print(str_o1 + str_o2)
pythonApple
```

- `in` 예약어를 사용하여 문자열 안에 원하는 문자가 있는지도 확인할 수 있다.

```python
> print('y' in str_o1)
True

> print('n' in str_o1)
True

> print('P' not in str_o1)
True
```

<br>

---

# 4. 문자형 형 변환(Type conversion), 문자형 함수

<br>

## 4.1 Type Conversion

- 파이썬에서 type conversion은 자유롭게 가능하다고 생각하자.

```python
# 정수 -> 문자열
> print(str(66))


# 실수 -> 문자열
> print(str(10.1))

# boolean  -> string
> print(str(True), type(str(True)))
True <class 'str'>

# complex -> string
> print(str(complex(12)))
12 + 0j
```

<br>

## 4.2 문자열 함수

> `print(dir())` 함수에 변수를 입력하면 그 변수의 속성들을 보여준다.

- 이 속성들에는 변수가 사용할 수 있는 함수들도 포함된다.
- string type의 data를 넣으면 string이 사용할 수 있는 함수를 보여준다.
- list면 list가 사용할 수 있는 함수를 보여준다.

  ```python
  > im_str = "Good Boy"
  > print(dir(im_str))
  ['capitalize', 'encode', .....]
  ```

- 위 함수들에서 일부만 출력해보겠다.

  ```python
  > str_o1 = "python"
  > str_o2 = "Apple"
  > str_o3 = "How are you doing"
  > str_o4 = "Korea Japan America"
  ```

<br>

### Capitalize

- `Capitalize()`: 첫 글자를 대문자로 바꿔주는 함수

  ```python
  > print("Capitalize : ", str_o1.capitalize())
  Capitalize : Python
  ```

<br>

### upper, lower, endswith

- `upper()`: 모든 문자를 대문자로 바꿔주는 함수  
- `lower()`: 모든 문자를 소문자로 바꿔주는 함수  
- `endswith()`: 마지막 글자가 무슨 문자로 끝나는지 확인하는 함수

  ```python
  # 모든 문자를 대문자로 바꿔주는 함수
  > print("upper : ", str_o1.upper())
  upper : PYTHON

  # 모든 문자를 소문자로 바꿔주는 함수
  > print("lower : ", str_o2.lower())
  lower : apple

  # 마지막 글자가 s로 끝나는가?
  > print("endswith? : ", str_o1.endswith('s'))
  endswith : False
  ```

<br>


### split

- `split()`: 인자로 입력한 값을 기준으로 문자열을 분리해서 리스트로 반환하는 함수  

  ```python
  # 해당 문자열을 입력한 문자열을 기준으로 쪼개어 list로 만드는 함수
  > print("split : ", str_o4.split(' '))
  > print("split : ", str_o4.split())
  split : Korea Japan America

  > text = 'a A/B A.B.C'
  > a, b, c = text.split('.')
  > print(a)
  > print(b)
  > print(c)
  a A/B A
  B
  C
  ```

<br>


### join

- `join()`: 해당 문자열 앞뒤로 join하는 함수  
  - list에 있는 것을 합쳐서 문자열을 만들 때 주로 사용한다. 

  ```python
  > print("join str : ", str_o1.join(["I'm", "!"]))
  join str : I'm python!
  ```

<br>


### replace

- `replace(A,B)`: 해당 문자열 안의 A 문자열을 B 문자열로 바꿔주는 함수

  ```python
  > print("replace : ", str_o1.replace('thon', 'Good'))
  replace :  pyGood
  ```

<br>



### sorted

- `sorted()`: 최소 단위까지 쪼개어 알파벳 순으로 list로 만드는 함수

  ```python
  > print('sorted : ', sorted(str_o3))
  sorted :  [' ', ' ', ' ', '?', 'H', 'a', 'd', 'e', 'g', 'i', 'n', 'o', 'o', 'o', 'r', 'u', 'w', 'y']
  ```

<br>


###  strip

- `strip()`: 앞뒤의 특정 문자열을 제거하는 함수 
  - `lstrip()`: 앞의 특정 문자열을 제거하는 함수
  - `rstrip()`: 뒤의 특정 문자열을 제거하는 함수

  ```python
  > text = '****abcde****'
  > print(text.strip('*'))
  abcde
  
  > print(text.lstrip('*'))
  abcde****

  > print(text.rlstrip())
  ****abcde
  ```

<br>

### find와 index

- `find()`와 `index()` 모두 인자로 입력한 문자열의 첫 번째 index를 반환한다. 
  - 차이점은 find는 못 찾으면 `-1`을 반환하고, `index`는 `ValueError`를 일으킨다.  

- `rfind()`와 `rindex()`는 제일 마지막 index를 반환한다. 

  ```python
  > text = 'ABC ABC'
  > print(text.find('D'))
  -1
  
  > print(text.index('D'))
  ValueError: substring not found

  > print(text.find('A'))
  > print(text.index('A'))
  0
  
  > print(text.rfind('A'))
  > print(text.rindex('A'))
  4
  ```

<br>


### isalpha, isupper, islower

- `is`가 붙은 것은 반환값이 True 또는 False라고 이해해보자. 
- `isalpha`는 문자열에 알파벳이 있는지, `isupper`는 다 대문자인지, `islower`는 다 소문자인지 알려준다. 

  ```python
  > text = 'ABCdef'
  > print(text.isalpha())
  True 
  
  > print(text.isupper())
  False
  
  > print(text.islower())
  False
  
  > print(text.upper().isupper())
  True
  
  > print(text.lower().islower())
  True
  ```


<br>

## 4.3 reverse 와 reversed 의 차이

- reverse
  - string이 아닌 list를 **단순히 뒤집어서 저장해주는 역할**
  - 리턴값이 없어서 for문 같은 조건문 반복문에 사용하지 못 한다.
- reversed는 원본에서 뒤집어진 순서의 새로운 객체(reverse iterator)를 만든다.
  - `sorted`도 새로운 객체를 만들고, 기존 객체는 수정하지 않는다. 
  - 그래서 `id` 값으로 나온다.
  - 출력을 원하면 reverse 값을 원하면 형 변환을 해야 한다.
  - **_기존 리스트의 값을 변경하지 않으면서 return 값만을 원할 때 reversed 함수를 사용한다._**

  ```python
  > print('reversed1: ', reversed(str_o2))
  reversed1:  <reversed object at 0x000001ECEA15CFD0>

  # reversed는 return 값이 존재하기에 list로 출력이 가능하다.
  > print('reversed2: ', list(reversed(str_o2))
  reversed2:  ['e', 'l', 'p', 'p', 'A']
  ```


<br>

---

# 5. Slicing

> 문자열의 일부분을 원하는 대로 잘라오는 것을 말한다.

```python
# index[0] 부터 [3]까지가 아닌, [2]까지이기 때문이다.
> sl = 'Nice Python'
> print(sl[0:3])
Nic

# 아무런 값이 없으면 끝까지 또는 처음부터라는 의미다.
> print(sl[5:])
Python

# 끝부분의 index를 모를 때, len 함수를 사용한다. 끝까지 출력된다.
# 위에 [0:3] 처럼 끝에가 -1 이 되지 않는다.
> print(sl[:len(sl)])
Nice Python

> print(sl[:len(sl)-1])
Nice Pytho

# 처음과 끝을 의미한다.
> print(sl[:])
Nice Python

# index[1]부터 시작하여 2씩 증가하며, index[4] 미만까지 한다.
> print(sl[1:4:2])
ie

# index[-4]부터 시작하여 [-3] 미만까지 한다.
> print(sl[-4:-2])
th

# index를 역으로 해도 가능하다.
> print(sl[-5:])
Python

# index를 역으로 하는 것과 정방향으로 하는 것을 같이 해도 가능하다.
> print(sl[1:-2])
ice Pyth

# 역으로 출력된다.
> print(sl[::-1])
nohtyP eciN

```

- **결론: slicing을 사용하여 원하는 정보를 추출해야한다. 그렇기 때문에 slicing은 중요하다.**

---

<br>

# Reference

- [Python tutorial](https://www.python-course.eu/python3_formatted_output.php)
- [프로그래밍 시작하기: 파이썬 입문 (Inflearn Original)](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%9E%85%EB%AC%B8-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)
