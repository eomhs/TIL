# Transaction Management
## Transactions
Transaction이란
- 작업의 논리적 단위 (user view)
- 다양한 data item에 access하고 update하는 프로그램 단위 (system view)
- 특징으로 ACID가 있음

**ACID**
- Atomicity
    - All or nothing
    - 중간에 crash가 생기면 roll back 혹은 roll forward
- Consistency
    - Consistent state에서 시작했으면 결과가 consistent state임을 보장
- Isolation
    - 다른 transaction에게 방해받으면 안됨 (concurrency)
- Durability
    - Transaction이 완료됐으면 durable과 public

Transaction States  
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Transaction%20states.PNG" width="600" height="400"/>  

Schedule  
Transaction의 implified view로, read와 write만 신경씀  
Transaction의 모든 instructions를 실행 순서대로 나열한 것

Serializability  
어떤 schedule이 serial schedule과 **equivalent**하면 serializable임  

Conflict Serializability  
- Transaction T1과 T2의 instruction I1과 I2는 같은 item에 대해서는 둘 다 read가 아닌 경우 **conflict**임  
- Non-conflict instructions의 순서를 바꿔서 다른 schedule을 만들 수 있으면 **conflict equivalent**하다고 함
- Serial schedule과 conflict equivalent하면 **conflict serializable**임

Precedence graph
- Transaction을 정점으로 함
- Conflict가 생기면 먼저 실행된 instruction의 transaction에서 다른 transaction으로 arc를 그림
- Schedule은 precedence graph가 acyclic일 때 conflict serializable임

Recoverable Schedule
- Transaction T2가 T1이 전에 write한 item을 read했다면
- T1이 먼저 commit하고 T2가 commit 해야함
- 반드시 지켜야함

Cascading rollback이란 하나의 transcation 실패가 그 transaction의 영향을 받는 여러 transaction까지 모두 rollback시키는 것  

Cascadeless Schedule
- Cascading rollback을 방지하기 위한 schedule
- T2가 T1이 전에 write한 item을 read해야 한다면
- T1이 commit할 때까지 read를 미룸
- 따라서 recoverable함
- 지키는 것이 권장됨