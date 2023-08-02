---
title: "MySQL의 쿼리 실행 절차, SELECT, join, subquery, SQL 내장 함수"
date: 2023-02-25T11:07:47+09:00
draft: true
summary: 
tags: ["DB"]
categories: "DB"
---

# 1. 실행계획과 query 작동 순서

## 실행계획

쿼리를 날리면 sql에서 parsing하고 최적화한다음에 storage engine에 쿼리를 넘기는데 내가 어떻게 최적화할지를 사용자에게 보여주는 것으로 다음 단계를 거친다.  

1. 쿼리문을 받으면
2. 어떻게 처리할지 최적화
3. 그리고 그 계획을 보여준다.  

위 이미지에서 selected_type과 Extra의 속한 종류들은 매우 많아서 다 학습하기는 어렵고, 흔히 보이는 것들을 위주로 학습하겠다.  

쿼리에 대한 실행계획을 확인하고자 한다면 다음과 같이 실행할 쿼리 앞에 `EXPLAIN`을 붙인다.  

```sql
EXPLAIN SELECT * FROM Employee
```

#

## query 작동 순서 - 면접질문

### 일반적인 쿼리의 작동 순서

1) 불필요한 조건 제거
2) 복잡한 연산 단순화
3) join 발생 시 read 순서 결정  
    - join 시 driving table과 driven table로 나눠지는데, driving table을 먼저 다 읽은 다음에 driving table row 하나 당 driven table을 다 훑는다.
    - 이 때 쿼리를 많이 날리는데, 이를 효율적으로 하기 위해서 인덱싱을 사용하지마나 인덱싱을 사용하지 못 하면 잠깐 메모리에 저장한다. 이를 join buffer라 한다.
4) 인덱스 결정  
5) 임시테이블 필요여부 결정

#

### SELECT 문 작동 순서

**_FROM -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY -> LIMIT_**

- FROM: 어떤 테이블을 query 할지 결정
- WHERE: 어떤 조건을 만족하는 지 결정  
- GROUP BY & HAVING: 어떻게 분류할건지 결정  
- SELECT: 위 단계들로 추려진 row들 중 어떤 column들을 선택할 지 결정  
- ORDER BY: 어떤 순서로 보여줄지 결정
- LIMIT: 몇 개를 보여줄지 결정  

### 예시

그러면 query 작동 순서를 이해하기 위해 예시 2가지를 보자.  

예시를 들기 위해 현재 가지고 있는 데이터를 확인해보자.  

```sql
SELECT FirstName, LastName FROM Employee;
```

위 쿼리를 실행한 결과 다음과 같은 데이터를 확인할 수 있다.  

|FirstName | LastName |
| ---- | ---- |
|Robert|King|
|Manoj|Edwards|
|Laura|Callahan|
|Julia|Adams|

아래 1번과 2번 중 하나만 실행이 가능하다.

- 예시 1번

    ```sql
    SELECT CONCAT(first_name, last_name) AS full_name 
    FROM Employee
    ORDER BY full_name;
    ```

- 예시 2번

    ```sql
    SELECT CONCAT(first_name, last_name) AS full_name
    FROM Employee
    WHERE full_name = "RobertKing"; 
    ```

그리고 위 예시 쿼리들 중 아래 부분만 실행하면 다음과 같은 결과를 확인할 수 있다.

```sql
SELECT CONCAT(first_name, last_name) AS full_name 
FROM Employee
```

|full_name|
|----|
|RobertKing|
|ManojEdwards|
|LauraCallahan|
|JuliaAdams|

그러면 각 예시 query를 확인해보자.  

예시 1번에서 ORDER BY 는 SELECT가 실행되고 나서 SELECT 된 것들 중에서 full_name을 기반으로 정렬을 한다.  

예시 2번에서 WHERE 는 SELECT를 실행하기 전에 먼저 실행된다. 그런데 `full_name` 이란 alias는 CONCAT으로 만들어진 것으로 실제 TABLE에는 존재하지 않는다.  

그래서 예시 1번만 실행되고, 예시 2번은 실행되지 않는다.  

- 예시 1 결과

    |full_name|
    |----|
    |JuliaAdams|
    |LauraCallahan|
    |ManojEdwards|
    |RobertKing|

- 예시 2 결과

    ```sql
    ERROR 1054 (42S22): Unknown column 'full_name' in 'where clause'
    ```

그러면 각 예시의 실행계획을 확인해보자. 예시 2의 경우 실패했으므로 실행 계획을 확인하지 않는다.  

| id | select_type | partitions | type | possible_keys | key | key_len | ref | rows | filtered | Extra |
| ---- |---- |---- |---- |---- |---- |---- |---- |---- |---- |---- |
| 1 | SIMPLE | NULL | ALL | NULL | NULL |NULL |NULL | 4| 100.0| | Using filesort |  

위 실행계획의 각 의미는 다음과 같다.

- id 1번: table 하나 당 select 하나이므로  
- select_type SIMPLE: 별다른 조건 없이 그냥 READ 했다는 의미  
- type ALL: full table scan으로 효율 생각 안하고 전체를 다 훑어보았다는 의미  
- rows: 4개의 row를 훑어보았다.  
- filtered 100.0: 그중에 100%를 다 사용했다는 의미
- extra Using filesort: ORDER BY에 사용된 방식으로 인덱스를 사용할 수 없어서 filesort라는 방식으로 정렬함을 의미  

계속 query에 대해 학습해가면서 실행계획을 확인해보자.

&nbsp;

---

# 2. LIMIT

**_LIMIT은 조건이 맞으면 바로 쿼리를 멈춘다._**

```sql
EXPLAIN SELECT * FROM reservation LIMIT 0, 10;
EXPLAIN SELECT * FROM reservation GROUP BY requested_at LIMIT 0, 10;
EXPLAIN SELECT DISTINCT num_customer FROM reservation LIMIT 0, 10;
```

- GROUP BY requested_at 은 날짜 별로 정렬한다는 것이다. 기존 테이블은 날짜 별로 정리가 되어 있지 않으므로, 임시로 다른 저장소에 날짜 별로 저장한 다음에 불러온다. 그래서 이 query의 실행계획에서 Extra를 확인해보면 Using temporary임을 알 수 있다.  
  - 임시 테이블에서의 작업은 메모리 상에서 수행되고, 메모리가 부족하면 디스크로 넘어간다.  

- LIMIT 을 추가하면 모든 row를 훑는 게 아닌 해당 LIMIT 조건을 만족하면 바로 중단하기 때문에 빠르다.  
  - LIMIT 0, 10은 0번째부터 10개를 의미한다.  
  - LIMIT 10, 10은 10번째부터 10개를 의미한다.

- DISTINCT 조건과 LIMIT 0, 10 조건이 있으므로 중복되지 않는 10개가 채워지면 조회가 바로 중단된다.

&nbsp;

---

# 3. DISTINCT

- Tuple 이다.  
- GROUP BY와 유사할 수 있다.  

    ```sql
    select distinct num_customer from reservation where id < 10;
    select num_customer from reservation where id < 10 group by num_customer; 
    ```

  - 위 두 sql은 결과가 동일하다.  

&nbsp;

---

# 4. JOIN 사용법

여러 테이블의 데이터를 한 번에 가져오는 것으로 다음과 같은 종류가 있다.

![image](https://i2.wp.com/www.datascienceexamples.com/wp-content/uploads/2019/12/join_types.png?w=1280&ssl=1)

위 종류들 중 아래 6가지에 대해서 알아보고자 한다.  

- 1) INNER JOIN
- 2) LEFT (OUTER) JOIN
- 3) RIGHT (OUTER) JOIN
- 4) FULL (OUTER) JOIN
- 5) CROSS JOIN
- 6) SELF JOIN

먼저 `DESC {table name}` 을 사용하여 table 구조를 확인해보자.  

- DESC는 DESCRIBE의 약어다.  

## alias 사용하기  

sql을 작성할 때 SELECT부터 작성하는 사람이 있는 반면 실행 순서에 맞춰서 작성하는 분이 있다.  

그럴 경우 이처럼 alias를 사용하면 보다 짧게 sql을 작성할 수 있다.

```sql
SELECT                                              SELECT
    Customer.CustomerId,                                c.CustomerId,   
    Customer.FirstName,                                 c.FirstName, 
    Customer.State,                                     c.State, 
    Employee.County                                     e.County
FROM Customer                                       FROM Customer c
JOIN Employee                                       JOIN Employee e
ON Employee.FirstName=Customer.FirstName            ON e.FirstName=c.FirstName
WHERE Customer.State="IL";                          WHERE c.State="IL";   
```

## 1) INNER JOIN

**_두 테이블 모두에 일치하는 값이 있는 record만 리턴_**  

```sql
SELECT Customer.FirstName, Customer.LastName Invoice.Total, DISTINCT Customer.CustomerId 
FROM Customer JOIN Invoice ON Customer.CustomerId=Invoice.CustomerId LIMIT 10;
```

각 테이블에는 훨씬 많은 값이 있지만 두 테이블 모두에 일치하는 값만 10개를 가져온다.  

```sql
EXPLAIN SELECT count(*) FROM Customer c JOIN Invoice i ON c.CustomerId=i.CustomerId;
```

| id | select_type | table| partitions| type| possible_keys| key | key_len | ref | rows | filtered | Extra|
| ---- | ---- | ---- |---- |---- |---- |---- |---- | ---- |---- | ---- |---- |
|1 | SIMPLE | c | NULL | index | PRIMARY | IFK_CustomerSupportRepId| 5 | NULL | 6 | 100.00 | Using index |
|1 | SIMPLE | i | NULL | ref | IFK_InvoiceCustomerId | IFK_InvoiceCustomerId| 4 | chinook.c.CustomerId | 6 | 100.00 | Using index |  

- id 1: 두 개여도 SELECT가 하나이기 때문에  
- table: c, i로 되어 있는데 alias를 의미한다. 즉, Customer와 Invoice table을 의미한다.  
- type:
  - index: index full scan
  - ref: join
- possible_keys: key로 사용될 수 있었던 옵션들  
- key: 최종 실행계획에서 사용된 인덱스  
- ref: 접근 방법에 대한 예시로, join된 컬럼  
- rows: 효율성을 판단하기 위해 예측한 row의 수 (부정확)
- filtered: 필터링 되고 남은 비율 (부정확)
- extra: Using index

그 다음으로 `where c.CustomerId < 30` 을 추가하여 쿼리를 날려본다.  

| id | select_type | table| partitions| type| possible_keys| key | key_len | ref | rows | filtered | Extra|
| ---- | ---- | ---- |---- |---- |---- |---- |---- | ---- |---- | ---- |---- |
|1 | SIMPLE | c | NULL | range | PRIMARY | PRIMARY | 4 | NULL | 2 | 100.00 | Using where; Using index |
|1 | SIMPLE | i | NULL | ref | IFK_InvoiceCustomerId | IFK_InvoiceCustomerId| 4 | chinook.c.CustomerId | 6 | 100.00 | Using index |  

- id: 1
- type: Range - index를 범위만 조회했다.  
- possible_keys: key로 사용될 수 있었던 옵션들  
- key: 최종 실행 계획에서 사용된 인덱스  
- ref: 접근 방법에 대한 예시 - join된 칼럼  
- rows: 효율성을 판단하기 위해 예측한 row의 수 (부정확)
- filtered: 필터링 되고 남은 비율 (부정확)
- extra:
  - Using Index: 인덱스 사용  
  - Using where: 추가 작업을 했다.  

## 2) LEFT (OUTER) JOIN

**_Left에 있는 값을 중심으로 join을 실행하는데, Left에는 있고 Right에는 없으면 NULL이다._**

```sql
SELECT c.CustomerId, c.FirstName, c.State, e.Country, e.Email FROM Customer c LEFT JOIN Employee e ON e.FirstName = c.FirstName;
```

위 쿼리를 실행하면 NULL 값을 확인할 수 있다.  

| id | select_type | table| partitions| type| possible_keys| key | key_len | ref | rows | filtered | Extra|
| ---- | ---- | ---- |---- |---- |---- |---- |---- | ---- |---- | ---- |---- |
|1 | SIMPLE | c | NULL | ALL | NULL | NULL | NULL | NULL | 6 | 100.00 | NULL |
|1 | SIMPLE | e | NULL | ALL | NULL | NULL | NULL | NULL | 8 | 100.00 | Using where; Using join buffer (hash join) |  

- extra
  - Using join buffer: 사용할 수 있는 인덱스가 없는 경우 메모리에 옮기는 것  
  - Using Where: 별도로 작업  

#

## 3) RIGHT (OUTER) JOIN

**_Right에 있는 값을 중심으로 join을 실행하는데, Right에는 있고 Left에는 없다면 NULL이다._**

```sql
SELECT c.FirstName, c.LastName, i.InvoiceDate, i.Date, i.Total FROM Customer c RIGHT JOIN Invoice i ON c.CustomerId = i.CustomerId WHERE i.TOTAL > 13;
```

i가 붙은 것은 값이 다 들어오고, c에 해당되는 값들에는 NULL이 있음을 알 수 있다.  

| id | select_type | table| partitions| type| possible_keys| key | key_len | ref | rows | filtered | Extra|
| ---- | ---- | ---- |---- |---- |---- |---- |---- | ---- |---- | ---- |---- |
|1 | SIMPLE | i | NULL | ALL | NULL | NULL | NULL | NULL | 412 | 33.33 | Using where |
|1 | SIMPLE | e | NULL | eq_ref | PRIMARY | PRIMARY | 4 | chinook.i.CustomerId | 1 | 100.00 | NULL |

- type
  - ALL: total에 index가 없고, CustomerId는 다 고유값
  - eq_ref: 각 CustomerId에 해당하는 Customer의 Row가 1개  
- possible_keys: key로 사용될 수 있었던 옵션들  
- key: 최종 실행계획에서 사용된 인덱스  
- ref: 접근 방법에 대한 예시 - join된 칼럼  
- rows: 효율성을 판단하기 위해 예측한 row의 수 (부정확)
  - 412개의 1/3인데 쿼리 결과 rows를 확인하면 정확하게 맞지 않는 걸 확인할 수 있다.  
- filtered: 필터링 되고 남은 비율 (부정확)
  - i.TOTAL > 13 조건  
- extra: Using Where - 뭔가 한 번 추가 작업이 필요했다.  

#

## 4) FULL (OUTER) JOIN

**_두 테이블에서 모든 값을 리턴한다._**

이 부분은 현재 db에 저장된 값에 형태가 맞지 않아서 인터넷 사례를 가져왔다.  

## 5) CROSS JOIN

## 6) SELF JOIN

&nbsp;

---

# Subquery 활용법  

- Query 안에 query를 중첩(nested)하는 것으로 Parent query 와 Child query가 존재한다.  
- 복잡한 query를 손쉽게 작성할 수 있다.  
- 가독성 향상  
- SELECT, FROM, WHERE에 사용  

### subquery 활용법 - SELECT

subquery는 하나의 값만 리턴해야 한다

#

### subquery 활용법 - FROM

subquery는 하나의 값만 리턴해야 한다

#

### subquery 활용법 - WHERE

&nbsp;

---

# SQL 내장함수

기본으로 제공하는 함수로 자주 사용되는 aggregation에 대해 알아보겠다.  

- COUNT(), MAX(), MIN(), AVG()
- CASE: 조건문으로 사용되는 statement  

&nbsp;

---

# Reference

- [원티드 백엔드 챌린지 2월: MySQL '잘' 사용하기](https://www.wanted.co.kr/events/pre_challenge_be_4)
