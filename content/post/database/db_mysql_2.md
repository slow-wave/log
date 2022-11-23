---
title: "[MySQL] joins"
date: 2022-11-01T16:24:19+09:00
draft: false
tags: ["DB","MySQL"]
categories: ["mysql","leetcode","SQL"]
showToc: true
UseHugoToc: true
comments: true
---

Table간 Join을 수행해야할 때는 다음과 같이 **집합**의 개념을 활용해서 생각하면 좋습니다. **MySQL** 언어로 아래의 모든 집합 관계에 대해서 표현해보려고 합니다. 

{{< figure src="/images/database/db_mysql_2/0.png">}}

# MySQL JOINS

## 0. Full Outer Join

### [0] [problem](https://leetcode.com/problems/employees-with-missing-information/)

```sql
/* Input: 
Employees table:
+-------------+----------+
| employee_id | name     |
+-------------+----------+
| 2           | Crew     |
| 4           | Haven    |
| 5           | Kristian |
+-------------+----------+
Salaries table:
+-------------+--------+
| employee_id | salary |
+-------------+--------+
| 5           | 76071  |
| 1           | 22517  |
| 4           | 63539  |
+-------------+--------+
Output: 
+-------------+
| employee_id |
+-------------+
| 1           |
| 2           |
+-------------+ */

select Employees.employee_id
from Employees left join Salaries
on Employees.employee_id = Salaries.employee_id
WHERE Salaries.employee_id is NULL

union

select Salaries.employee_id
from Employees right join Salaries
on Employees.employee_id = Salaries.employee_id
WHERE Employees.employee_id is NULL 
order by employee_id;
```

- MySQL에는 FULL OUTER JOIN 없음

## 1. LEFT OUTER JOIN

### [0] [problem](https://leetcode.com/problems/combine-two-tables/)

```sql
/*
Input: 
Person table:
+----------+----------+-----------+
| personId | lastName | firstName |
+----------+----------+-----------+
| 1        | Wang     | Allen     |
| 2        | Alice    | Bob       |
+----------+----------+-----------+
Address table:
+-----------+----------+---------------+------------+
| addressId | personId | city          | state      |
+-----------+----------+---------------+------------+
| 1         | 2        | New York City | New York   |
| 2         | 3        | Leetcode      | California |
+-----------+----------+---------------+------------+
Output: 
+-----------+----------+---------------+----------+
| firstName | lastName | city          | state    |
+-----------+----------+---------------+----------+
| Allen     | Wang     | Null          | Null     |
| Bob       | Alice    | New York City | New York |
+-----------+----------+---------------+----------+
*/

SELECT p.firstName, p.lastName, a.city, a.state
FROM Person as p
    LEFT OUTER JOIN Address as a 
    ON p.personId = a.personId;
```

### [1] [problem](https://leetcode.com/problems/customer-who-visited-but-did-not-make-any-transactions/)

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

# ANSWER1 - 처음에는 full outer join 해야한다고 생각함. 
SELECT d.customer_id, count(d.visit_id) as count_no_trans
FROM 
    ( SELECT v.customer_id, v.visit_id
    FROM Visits as v LEFT JOIN Transactions as t
    ON v.visit_id = t.visit_id
    WHERE t.visit_id is NULL

    UNION

    SELECT v.customer_id, v.visit_id
    FROM Transactions as t RIGHT JOIN Visits as v
    ON v.visit_id = t.visit_id
    WHERE t.visit_id is NULL) as d
GROUP BY d.customer_id;

#ANSWER2 (final)
SELECT customer_id, COUNT(Visits.visit_id) AS count_no_trans
FROM visits LEFT JOIN Transactions
ON Visits.visit_id = Transactions.visit_id
WHERE Transactions.visit_id IS NULL
GROUP BY customer_id;
```

## 참고 자료

- [[blog] **Mysql Join 해부(Left, Right, Outer, Inner Join)**](http://rapapa.net/?p=311)