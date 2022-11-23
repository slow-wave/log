---
title: "[MySQL] 문법/함수 정리"
date: 2022-10-21T01:43:36+09:00
draft: false
tags: ["DB","MySQL"]
categories: ["mysql","leetcode","SQL"]
showToc: true
UseHugoToc: true
comments: true
---

코딩테스트 연습 할 때 Leetcode 플랫폼을 많이 이용합니다. discussion을 보면 문제에 대한 각자의 코드와 설명이 있는데 많은 사람들이 vote와 댓글 기능을 이용해 피드백을 줍니다. 보통은 칭찬이 많이 달려있는 글을 위주로 참고합니다. 별로인 것에 대해서는 가차없는 피드백이 적혀있다는 것도 도움이 되는 것 같습니다. 또 댓글로 시간복잡도 등에 대해서 토론하는 부분도 있어서 유용합니다. 

개인적으로 SQL을 공부 할 필요성을 느껴서 MySQL의 문법을 중심으로 problem과 code를 정리해봤습니다. discussion을 꼼꼼히 읽어서 어떻게 코딩하는 것이 좋은 방향인지도 알아보려고 합니다. 

### 0) SUBSTRING ([problem](https://leetcode.com/problems/fix-names-in-a-table/?envType=study-plan&id=sql-i))

```sql
# 1667. Fix Names in a Table
/*
Input: 
Users table:
+---------+-------+
| user_id | name  |
+---------+-------+
| 1       | aLice |
| 2       | bOB   |
+---------+-------+
Output: 
+---------+-------+
| user_id | name  |
+---------+-------+
| 1       | Alice |
| 2       | Bob   |
+---------+-------+
*/

SELECT user_id, 
CONCAT(UPPER(SUBSTRING(name,1,1)), LOWER(SUBSTRING(name,2,LENGTH(name)))) as name
FROM Users
ORDER BY user_id;
```

- [SUBSTRING(***str***,***pos***)](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_substring)
    - pos ~ 위치까지의 문자열 반환
- [SUBSTRING(***str***,***pos***,***len***)](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_substring)
    - pos ~ len 위치까지의 문자열 반환

### 1) GROUP_CONCAT ([problem](https://leetcode.com/problems/group-sold-products-by-the-date/))

```sql
/*
Input: 
Activities table:
+------------+------------+
| sell_date  | product     |
+------------+------------+
| 2020-05-30 | Headphone  |
| 2020-06-01 | Pencil     |
| 2020-06-02 | Mask       |
| 2020-05-30 | Basketball |
| 2020-06-01 | Bible      |
| 2020-06-02 | Mask       |
| 2020-05-30 | T-Shirt    |
+------------+------------+
Output: 
+------------+----------+------------------------------+
| sell_date  | num_sold | products                     |
+------------+----------+------------------------------+
| 2020-05-30 | 3        | Basketball,Headphone,T-shirt |
| 2020-06-01 | 2        | Bible,Pencil                 |
| 2020-06-02 | 1        | Mask                         |
+------------+----------+------------------------------+
*/

SELECT 
    sell_date,
    COUNT(DISTINCT product) AS num_sold,
    GROUP_CONCAT(DISTINCT product ORDER BY product) AS products
FROM Activities
GROUP BY sell_date;
```

### 2) LIKE ([problem](https://leetcode.com/problems/patients-with-a-condition/?envType=study-plan&id=sql-i))

```sql
/*
Input: 
Patients table:
+------------+--------------+--------------+
| patient_id | patient_name | conditions   |
+------------+--------------+--------------+
| 1          | Daniel       | YFEV COUGH   |
| 2          | Alice        |              |
| 3          | Bob          | DIAB100 MYOP |
| 4          | George       | ACNE DIAB100 |
| 5          | Alain        | DIAB201      |
+------------+--------------+--------------+
Output: 
+------------+--------------+--------------+
| patient_id | patient_name | conditions   |
+------------+--------------+--------------+
| 3          | Bob          | DIAB100 MYOP |
| 4          | George       | ACNE DIAB100 | 
+------------+--------------+--------------+
*/

SELECT * FROM Patients
WHERE conditions LIKE 'DIAB1%' or conditions LIKE '% DIAB1%';
```

### 3) UNION ALL ([problem](https://leetcode.com/problems/rearrange-products-table/))

```sql
/*
Input: 
Products table:
+------------+--------+--------+--------+
| product_id | store1 | store2 | store3 |
+------------+--------+--------+--------+
| 0          | 95     | 100    | 105    |
| 1          | 70     | null   | 80     |
+------------+--------+--------+--------+
Output: 
+------------+--------+-------+
| product_id | store  | price |
+------------+--------+-------+
| 0          | store1 | 95    |
| 0          | store2 | 100   |
| 0          | store3 | 105   |
| 1          | store1 | 70    |
| 1          | store3 | 80    |
+------------+--------+-------+
*/

SELECT product_id, 'store1' store, store1 price
FROM Products
WHERE not store1 is null
UNION ALL
SELECT product_id, 'store2' store, store2 price
FROM Products
WHERE not store2 is null
UNION ALL
SELECT product_id, 'store3' store, store3 price
FROM Products
WHERE not store3 is null;
```

### 4) NOT IN, NOT EXISTS ([problem](https://leetcode.com/problems/customer-who-visited-but-did-not-make-any-transactions/))
- UNION vs. UNION ALL
    - UNION만 사용하면 중복된 열 제거 & 데이터 정렬
    - UNION ALL만 사용하면 중복된 열까지 출력
- 문제에서 ‘product_id’는 PK라고 했으므로 중복된 것이 존재할 수 없음. 따라서 UNION ALL을 사용해서 중복 제거 과정을 거치지 않는 것이 맞다고 판단함.

```sql
/* Input: 
Visits
+----------+-------------+
| visit_id | customer_id |
+----------+-------------+
| 1        | 23          |
| 2        | 9           |
| 4        | 30          |
| 5        | 54          |
| 6        | 96          |
| 7        | 54          |
| 8        | 54          |
+----------+-------------+
Transactions
+----------------+----------+--------+
| transaction_id | visit_id | amount |
+----------------+----------+--------+
| 2              | 5        | 310    |
| 3              | 5        | 300    |
| 9              | 5        | 200    |
| 12             | 1        | 910    |
| 13             | 2        | 970    |
+----------------+----------+--------+
Output: 
+-------------+----------------+
| customer_id | count_no_trans |
+-------------+----------------+
| 54          | 2              |
| 30          | 1              |
| 96          | 1              |
+-------------+----------------+ */

# ANSWER1 - NOT IN
SELECT customer_id, COUNT(visit_id) as count_no_trans 
FROM Visits
WHERE visit_id NOT IN (
	SELECT visit_id FROM Transactions
	)
GROUP BY customer_id

#ANSWER2 - NOT EXISTS
SELECT customer_id, COUNT(visit_id) as count_no_trans 
FROM Visits v
WHERE NOT EXISTS (
	SELECT visit_id FROM Transactions t 
	WHERE t.visit_id = v.visit_id
	)
GROUP BY customer_id
```

- in vs. exists
    - in 연산자 : sub query → main query 순서로 처리
        - sub에서 main의 정보를 가져올 수 없기 때문에 조건을 각각 설정함.
    - exists 연산자 : main query → sub query 순서로 처리
        - sub에서 main의 정보를 가져와 모든 조건을 한번에 설정함.

### 참고 자료

- [blog] [UNION과 UNION ALL 의 차이 및 주의 사항](http://intomysql.blogspot.com/2011/01/union-union-all.html)
- [blog] [[MS SQL Server] #11_ IN / EXISTS / NOT IN / NOT EXISTS 비교](https://doorbw.tistory.com/222)
- [blog] [[MySQL] in 연산자와 exists 연산자 의 차이](https://junco.tistory.com/71)