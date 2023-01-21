---
title: "[database] Re-introduction to DB"
date: 2022-10-06T00:50:00+09:00
draft: false
tags: ["DB"]
categories: ["DB"]
showToc: true
UseHugoToc: true
comments: true
---

데이터베이스 프로그래밍 수업의 내용을 정리한 문서입니다. 시간이 조금 지나고나서야 중요한 내용 임을 깨달아서 예전에 굿노트에 정리했던 것들을 다시 글로 기록하려고 합니다. 더 알고 싶은 부분은 조금 더 파고 들어볼 예정입니다. 

---

## 1. DB 기본 개념

### 1) DB vs. DBMS
- **DB**는 저장된 데이터의 모임. 데이터 그 자체 컨텐츠를 의미함.
- **DBMS**는 데이터 접근을 위한 프로그램 set임.
    - oracle
    - MS SQL server
    - MySQL
    - MariaDB

### 2) Typical web-based application architecture
{{< figure src="/images/db_programming_1/1.png">}}

[[출처](https://docs.aws.amazon.com/whitepapers/latest/serverless-multi-tier-architectures-api-gateway-lambda/three-tier-architecture-overview.html)]
- Client Tier - Presentation Layer
    - 응용 프로그램의 최상단임
    - UI 제공 (인터넷 브라우저의 정적 데이터 제공)
    - 주로 web server를 뜻 함 (물리적 WEB server)
    - HTML, Javascript, CSS, Image
- Application Tier - Business Logic Layer
    - 클라이언트 요청에 대해 마치 서버처럼 행동함
    - 정보 처리의 규칙 존재(주로 동적 데이터임)
    - middleware, backend
    - 주로 application server 뜻함(물리적 WAS server)
- Data Tier - Data Access Layer
    - DB와 엑세스해서 읽고 쓰고 관리하는 것을 포함
    - DB server (물리적 DB server)
    - MySQL

### 3) Rational DB
- Domain : attribute가 가질 수 있는 값의 집합
- Attribute(column)
- Tuple(record, row)
- Relation(table)
- DB - set of relations

### 4) Schema, Instance
- Schema : DB의 논리적 구조, 뼈대
- Instance : DB의 실제 컨텐츠, DB의 상태

### 5) Key
- Key
    - tuple을 구별하기 위한 attribute 집합
- Superkey
    - relation에서 unique하게 tuple을 식별할 수 있는 attribute 집합
- Candidate Key
    - superkey 중에서 minimal한 key
- Primary Key
    - candidate key 중 하나 (relation을 정의할 때 선택)
    - entity Integrity : Null이 될 수 없음.
- Foreign Key
    - 타 relation을 참조하는 attribute
    - referential integrity : 반드시 참조된 relation의 PK 값에 존재하거나 NULL이어야 함.

### 6) Entity-Relation model
- ERD
    - entity : 업무상 관리가 필요한 관심사에 해당함. 저장이 되기 위한 어떤 것임.
    - DB design에 사용됨.

### 7) DB Language
- DB language는 절차적 언어와 비절차적 언어로 구성되어 있음.
    - Procedural (Relational algebra)
    - Non-procedural (SQL)
- DDL (data definition language) : db schema를 정의하는 언어
- DML (data manipulation language) : db의 data를 조작하는 언어
    - query
- DCL (data control language) : db의 constraint를 제어하는 언어
    
    

### 8) SQL (structed query language)
- relational db의 표준
- DDL, DML
- Integrity(무결성) : 데이터 정확성, 일관성, 유효성이 유지되는 것을 의미함.
- TCL (Transaction Control Language)
- view definition
- authorization

---

### 출처

- 데이터베이스 프로그래밍 수업 메모
- [what is programming:티스토리](https://prinha.tistory.com/entry/WEB-3-Tier-Architecture-3계층-구조)