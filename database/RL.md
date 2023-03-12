# Relational Languages
## Introduction
Data란 기록할 가치가 있는 formal description  
Database란 integrated collection of persistent data   
Physical data independence에 의해 pysical level의 추상화는 알지 않아도 schema 수정 가능   
- ***Schema***: database의 논리적 구조
- ***Instance***: 특정 시간에 database의 실제 content

Database Languages
- Data Definition Language (DDL)
    - create table 등
- Data Manipulation Language (DML)
    - retrieve, insert, delete, change
- Query
    - 정보의 반환을 요구하는 statement
    - DML의 일부이며 동어로 쓰기도 함
## Relational Model
Data Model이란 data와 그들의 관계를 나타내는 framework   
Relational Model은 attributes (collumns)와 tuples (rows)로 이루어짐   
Attribute values는 atomic해야 함   
Tuple의 순서는 상관없음   
Key는 attribute들의 부분집합으로 다음의 종류가 있음
- Superkey
    - Relation에서 unique tuple을 찾을 수 있는 key
    - 하나의 attribute가 될 수도 있고 모든 attribute가 될 수도 있음
- Candidate key
    - Superkey중 minimal한 key
- Primary key
    - Candidate key중 table을 대표하는 key
- Foreign key
    - 다른 table의 primary key로, 주로 참조할때 쓰임

Query Language는 다음의 두 종류로 구분할 수 있음
- Procedural
    - 무슨 data가 필요한지, 그리고 어떻게 가져오는지를 정함
    - ex) Relational Algebra
- Nonprocedural
    - 무슨 data가 필요한지만 정하고 방법은 시스템에 맡김
    - ex) SQL

Relational Algebra의 operand는 relation이고 operator는 다음의 종류들이 있음
- σ (Selection): 조건에 맞는 row들을 가져옴
- π (Projcetion): 조건에 있는 atrribute들만 가져옴
    - 이때 중복된 row는 없앰
- ∪ (Union): 합집합이고 두 relation은 schema가 같아야 함
- ㅡ (Set Difference): 차집합이고 두 relation은 schema가 같아야 함 