---
title: "[MySQL] 활용"
date: 2022-11-01T16:21:10+09:00
draft: false
tags: ["DB","MySQL"]
categories: ["MySQL"]
showToc: true
UseHugoToc: true
comments: true
---

### 0) Second Highest Salary ([problem](https://leetcode.com/problems/second-highest-salary/))

```sql
Input:
Employee table:
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
Output:
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```

- solution 1
    
    ```sql
    SELECT max(Salary) as SecondHighestSalary
    FROM Employee
    WHERE Salary < (SELECT max(Salary) FROM Employee);
    ```
    
- solution 2 (n-th rank 구할 때 적절할 듯)
    
    ```sql
    WITH CTE AS
    			(SELECT Salary, RANK () OVER (ORDER BY Salary desc) AS RANK_desc
    			   FROM Employee)
    SELECT MAX(salary) AS SecondHighestSalary
      FROM CTE
     WHERE RANK_desc = 2
    ```
    
    - with 절
        - 동일한 SQL 문이 반복되어 성능을 높이기 위해 사용됨. table을 만들지 않아도 만든 것 같은 효과를 냄. → 메모리를 차지함.
        - option
            - materialize : 기본 with절 사용 시 적용. temp 사용함.
            - inline : temp 테이블을 생성하지 않고 inline view로 수행함.
    - view
        - DB에 존재하는 가상 테이블임. 실제 행과 열은 있지만 데이터를 저장하고 있진 않는다. 테이블처럼 물리적으로 저장되어 있는 것은 아님.
    - with 절 vs. view
        - with 절
            - 장점 : temp라는 임시 테이블을 사용해서 장시간 걸리는 쿼리의 결과를 저장해놓고 저장해놓은 데이터를 엑세스해서 성능이 좋음.
            - 단점 : 메모리를 차지함.
        - view
            - 장점 : 특정 사용자에게 테이블 전체가 아닌 필요한 필드만 보여줄 수 있음. 복잡한 쿼리를 단순화해서 사용할 수 있음.
            - 단점 : 삽입, 삭제, 갱신 작업에 많은 제한 사항을 가짐. 자신만의 인덱스를 가질 수 없음.
        

## 참고 자료

- [[blog] [MYSQL] 📚 WITH (임시 테이블 생성)](https://inpa.tistory.com/entry/MYSQL-%F0%9F%93%9A-WITH-%EC%9E%84%EC%8B%9C-%ED%85%8C%EC%9D%B4%EB%B8%94)
- [[blog] [MySQL] 뷰 생성하기(VIEW 생성하기)](https://kkamikoon.tistory.com/entry/MySQL-%EB%B7%B0-%EC%83%9D%EC%84%B1%ED%95%98%EA%B8%B0VIEW-%EC%83%9D%EC%84%B1%ED%95%98%EA%B8%B0)