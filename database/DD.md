# Database Design
## Database Design Using the E-R Model
Database Design은 세 phase가 있음
- Conceptual design
    - ER schema 등의 개념적 설계
- Logical design
    - 실제 DBMS의 모델로 map
- Physical design
    - physical feature을 명시(index나 db의 순서 등)
### Entity-Relationship Model
- Entity
    - 다른 객체와 구분가능한 객체
- Attributes
    - Entity가 가진 속성
- Entity set
    - 같은 특징을 지닌 Entity들의 집합
- Relationship
    - 2개 이상의 entity들의 관계
    - attribute를 가질 수도 있음
### E-R Diagrams
직사각형은 entity sets를 의미  
다이아몬드는 relationship sets를 의미  
Attributes는 직사각형 안에 리스트로 써있음  
- Simple attribute: atomic한 attribute
- Composite attribute: 여러 part로 구성된 attribute
- Single-valued attribute: 하나의 entity에 하나만 있는 attribute
    - ex) name, dept
- Multivalued attribute: 하나의 entity에 여러개 있을 수 있는 attribute
    - ex) children, phone
- Null

밑줄은 primary key attributes  

Degree of Relationship Set
- Relationship이 몇 개의 entity set과 관련되어 있는지
- 대부분 binary
- 정의하기에 따라 달라짐: ternary 등  

Relationship cardinality
- Relationship set을 통해 entity와 다른 entity가 관련되는 수
- 일반적으로 다음의 종류들이 있음
- 1 : 1
- 1 : m
- m : 1
- m : n

다이어그램에서 화살표가 가리키는 entity가 one 이고 화살표가 없으면 many  
줄이 두개면 total participation을 의미하며 entity set의 모든 entity가 relationship set에 있는 최소한 한개의 relationship을 가짐을 의미  
선 위에 min .. max 로도 cardinality를 나타낼 수 있음
 