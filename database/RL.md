# Relational Languages
## Introduction
Data란 기록할 가치가 있는 formal description  
Database란 integrated collection of persistent data   
Physical data independence에 의해 pysical level의 추상화는 알지 않아도 schema 수정 가능   
- **Schema**: database의 논리적 구조
- **Instance**: 특정 시간에 database의 실제 content

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
- x (Cartesian-Product)
- ρ (Rename): relation의 이름을 변경함
- ∩ (Set-Intersection): 교집합이고 두 relation은 schema가 같아야 함
- ⋈ (Natural-Join): 두 relation을 합치고 겹치는 attribute가 같은 row들을 가져옴
- ← (Assignment): 일시적인 relation variable처럼 사용

## SQL
Structured Query Language의 줄임말  
Table 생성 문법은 **create table** r (A1 D1, A2 D2, ...)  
ex) create table instructor(  
 　　   ID  　　char(5),  
  　　  name 　varchar(20) not null,  
   　　 salary 　numeric(8,2),  
   　　 primary key(ID))  
Domain types에는 다음의 종류 등이 있음
- char(n)
- varchar(n)
- int
- numeric(p, d)  

Create table에서 integrity constraints에는 다음의 종류가 있음
- not null
- primary key(A1, ...)
- foreign key(Am, ...)

Table r을 삭제하거나 attribute를 수정하려면
- drop table r
- alter table r add A D
    - A는 attribute의 이름이고 D는 domain
- alter table r drop A

SQL query는 다음의 형태를 가짐  
　　**select** A1, A2, ...  
　　**from** r1, r2, ...  
　　**where** P  
기본적으로 query result로 중복을 허용함   
중복을 제거하려면 **select** 뒤에 **distinct**를 추가  
또한 **select**절은 연산을 허용함  

**as**를 통해 rename 가능
- ex)select column from table **as** T

String operation중 % 기호는 pattern matching에 쓰이며 substring을 의미
- ex) select * from course where title **like** '%data%'
- title에 data라는 substring이 포함된 tuple들을 받아옴

**order by**를 통해 오름차순(default) 또는 내림차순(desc)으로 tuple 정렬  
- ex) select * from instructor **order by** name **desc**

Set Operation도 사용가능
- union, intersect, except
- ex) (select name from instructor) **union** (select name from student)

Tuple들은 null value를 가질 수 있음  
- unknown으로 취급됨
- **is null**을 통해 null인지 검사 가능
- ex) select name from instructor where salary **is null**
- null과 연산을 하면 null임
- Aggregate function은 null을 무시함

Aggregate Functions
- avg: 평균값
- min: 최솟값
- max: 최댓값
- sum: 값들의 합
- count: 값들의 수
- ex) select count(*) from teaches

**group by**를 통해 같은 attribute를 가진 집단끼리 묶을 수 있음
- ex) select dept_name, avg(salary) from instructor **group by** dept_name **having** avg(salary) > 50000
- group by로 묶이지 않은 attribute는 aggregate function을 써야만 select절에 사용가능
- **having**을 통해 조건 설정

Query 안에 subquery 사용가능  
주로 set membership, set comparison, set cardinality에 사용됨
- where A **in (not in)** <subquery\>
- where A > **some (all)** <subquery\>
- where **exists (not exists)** <subquery\>

From 절에도 subquery 사용가능  
Select 이외에 DML로는 delete, insert, update가 있음  

Deletion은 다음의 형태를 가짐  
　　**delete from** table [where clause]  
ex) **delete from** course where dept_name = 'Appl.Math'  

Insertion은 다음의 형태를 가짐   
　　**insert into** table **values** (values)  
　　**insert into** table <select subquery\>  
ex1) **insert into** course **values** ('CS-437', 'Database Systems', 4)  
ex2) **insert into** table1 select * from table1  

Update는 다음의 형태를 가짐  
　　**update** table **set** <assignment_statements\> [where clause]  
ex) **update** instructor **set** salary = salary * 1.05 where salary < (select avg(salary) from instructor)  

## Intermediate SQL
Join의 type과 Condition은 다음 그림과 같음  
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Join%20types.PNG" width="600" height="300"/>  
주로 from절에 쓰이고, table1 [natural] join table2 [on/using]과 같이 사용  

Integrity Constraints에는 다음의 종류들이 있음
- not null
- unique
    - ex) unique(ID)
- primary key
- check(P)
    - ex) check(semester in ('Spring', 'Summer', 'Fall', 'Winter'))
- Referential integrity
    - references 키워드, foreign key
    - on delete cascade, on delete set null, on delete set default 사용가능

Authorization을 주는 grant문은 다음과 같은 형식임  
　　**grant** <privilege list\>  
　　**on** <relation name or view name\>  
　　**to** <user list\>  
반대로 authorization을 뺏는 revoke문은   
　　**revoke** <privilege list\>  
　　**on** <relation name or view name\>  
　　**from** <user list\>  
Role이란 user들의 group과 privileges를 표현하기 위한 것으로 다음과 같이 생성함  
　　**create role** grader  
Privilege를 role에게 grant할 수 있고, role을 user나 다른 role에게 grant 할 수 있음
- grant select on student to grader
- grant grader to Kim
- grant grader to prof