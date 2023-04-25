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

## Relational DB Design
중복이 있는 design은 다음의 문제들을 야기함
- Insertion anomaly 
- Deletion anomaly 
- Update anomaly: single record를 update해도 중복되는 모든 record를 같이 update 해야함

따라서 이러한 경우 schema를 decompose 해야함 

First Normal Form
- 모든 attributes의 domain이 atomic한 경우임
- 모든 relation이 first normal form이라고 가정함

특정한 relation R이 good form인지 판단하고, 아닌 경우 good forms로 decompose하는걸 relational theory라고 함  
Functional dependancies에 기반함  

Functional Dependencies
- A, B가 R의 attribute 일 때
- A -> B 의 필요충분조건은 다음과 같음
- 모든 legal relations r(R)에 대해
- 어느 tuple t1과 t2에 대해서도
- t1[A] = t2[A] => t1[B] = t2[B]
- 즉 A에 의해 B가 결정되는 경우(A가 같으면 B도 무조건 같음)

FD set F에 의해 유도될 수 있는 모든 FD를 closure of F라고 함  
Amstrong's Axioms에 의해 closure를 구할 수 있음
- B ⊆ A 이면 A -> B
- A -> B 이면 CA -> CB
- A -> B 이고 B -> C 이면 A -> C

Boyce-Codd Normal Form (BCNF)
- BCNF이면 good form
- Key가 아닌 FD가 없는 경우
- Relation schema R에서 Closure of F가 모두 다음 중 하나를 만족하는 경우
    - A -> B가 trivial
    - A가 R의 superkey

Lossless-join Decomposition
- 정보의 손실이 없는 decomposition
- r(R)을 {R1, R2} 로 decompose 했을 때
- r = πR1(r) ⋈ πR2(r) 인 경우
- R1 ∩ R2 -> R1 또는 R1 ∩ R2 -> R2 인 경우 (공통된 attribute가 최소한 둘 중 하나의 key인 경우)

Dependancy Preservation
- Decomposition 하기 전의 FD 보존
- R을 R1, R2, ... , Rn 으로 decompose 했을 때
- F의 closure가 F1, F2, ... , Fn의 합집합의 closure과 같은 경우
- 이를 통해 Join 하지 않고 원래 FD들을 알 수 있음
- ex) R = (A, B, C), F = { A -> B, B -> C }
- ex) R1 = (A, B), R2 = (B, C) : lossless join & dependancy preservation 
- ex) R1 = (A, B), R2 = (A, C) : lossless join & not dependancy preservation

디자인 목표
- Lossless join decomposition와
- Dependancy preservation을 통해 (가능하다면: 불가능한 경우도 있음) 
- BCNF로 만든다

Attribute set에 의해 functional dependencies를 가지는 attribute set을 closure of attribute sets라고 함  
Attribute closure을 이용해 다음을 할 수 있음
- Superkey 테스트: 해당 attribute set의 closure가 R인지 확인
- Functional dependencies 테스트: A -> B인지 확인하려면 B가 A의 closure 안에 있는지 확인
- F의 closure 계산: R의 모든 부분집합 attribute set에 대해 closure을 구함
- A -> B가 BCNF 조건을 위배하는지 확인: A의 closure가 R인지 확인


R이 BCNF인지 확인하려면 F의 closure가 아닌 F에 대해서만 확인해도 됨  
그러나 R의 decomposition들에 대해 BCNF를 확인할 때는 closure of F를 사용해야 함  

