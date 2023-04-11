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
ER 모델에서도 똑같이 keys가 있음  

Weak entity sets
- Primary key를 이룰 수 있는 attributes가 충분하지 않는 entity set
- 유일성이 identifying entity set에 의해 결정됨
- Identifying relationship은 두 줄 다이아몬드로 그림
- 같은 strong entity에 결합된 weak entity끼리 구분하는 attributes를 **discriminator**라고 함
- Identifying entity의 primary key와 weak entity의 discriminator를 합쳐서 primary key가 됨

### Extended E-R Features
- Specialization
    - entity set을 같은 특징을 지닌 entity sets로 subgrouping
- Generalization
    - 같은 특징을 지닌 entity sets를 높은 수준으로 결합
    - Specialization과 반대 개념
    - disjoint vs overlapping: 상위 entity가 완전히 나뉘는지 아니면 여러 하위 entity에 속할 수 있는지
    - total vs partial: 상위 entity가 모두 하위 entity에 있는지 혹은 아닌지

### Reducing ER schema to tables
Conceptual design을 Logical design으로 바꾸는 법  
- 각 entity set은 unique table로
- 각 relationship set도 unique table로
    - 연관된 entity sets의 primary key와 relationship set의 attributes를 가짐
    - 다만 one-to-many나 one-to-one의 경우 별도의 table을 만들지 않고 한 table의 primary key와 relationship set의 attributes를 다른 table에 추가하면 됨
- Generalization/Specialization은 상위 레벨과 하위 레벨을 각각 table로 만들거나 상위 레벨의 attributes를 하위 레벨에 포함시킴