---
title: "[MySQL] 함수 정리"
date: 2022-10-21T01:43:36+09:00
draft: false
tags: ["DB"]
categories: ["mysql","leetcode"]
showToc: true
UseHugoToc: true
comments: true
---

코딩테스트 연습 할 때 Leetcode 플랫폼을 많이 이용합니다.남의 코드와 아이디어를 잘 볼 수 있어서 매우 좋아합니다.(영어라는 것만 빼면 완벽하다고 생각하고 있습니다..) discussion을 보면 문제에 대한 각자의 코드와 설명이 있는데 많은 사람들이 vote와 댓글 기능을 이용해 피드백을 줍니다. 저는 칭찬이 많이 달려있는 글을 위주로 참고합니다. 별로인 것에 대해서는 가차없는 피드백이 적혀있어서 더 도움이 되는 것 같습니다. 그리고 시간복잡도 등에 대해서 토론하는 댓글도 많아서 이 부분도 유용합니다. 

개인적으로 SQL을 공부 할 필요성을 느껴서 mysql 함수 중심으로 problem과 code를 정리해봤습니다. discussion을 꼼꼼히 읽어서 어떻게 코딩하는 것이 좋은 방향인지도 알아보려고 합니다. 

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

- `[SUBSTRING(***str***,***pos***)](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_substring)`
    - pos ~ 위치까지의 문자열 반환
- `[SUBSTRING(***str***,***pos***,***len***)](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_substring)`
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

- UNION vs. UNION ALL
    - UNION만 사용하면 중복된 열 제거 & 데이터 정렬
    - UNION ALL만 사용하면 중복된 열까지 출력
- 문제에서 ‘product_id’는 PK라고 했으므로 중복된 것이 존재할 수 없음. 따라서 UNION ALL을 사용해서 중복 제거 과정을 거치지 않는 것이 맞다고 판단함.

### 참고 자료
[1] [blog] ****[UNION과 UNION ALL 의 차이 및 주의 사항](http://intomysql.blogspot.com/2011/01/union-union-all.html)****