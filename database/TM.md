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

## Concurrency Control
Isolation의 구현   
Lock-Based Protocol
- exclusive (X) mode: read와 write 가능 (lock-X)
- shared (S) mode: read만 가능 (lock-S)
- Lock을 쓴다고 항상 serializability를 보장하는 것은 아님

Two-Phase Locking Protocol (2PL)
- Phase 1: Growing Phase
    - Transaction이 lock을 얻기만 하고 release하지 않는 단계
    - lock-S 또는 lock-X를 얻거나 lock-S를 lock-X로 upgrade
- Phase 2: Shrinking Phase
    - Transaction이 lock을 release하는 단계
    - lock-S 또는 lock-X를 release하거나 lock-X를 lock-S로 downgrade
- Serializability를 보장하나 deadlock이나 cascading rollback이 발생할 수 있음

Strict/Rigorous 2PL
- Strict 2PL
    - Transaction이 commit/abort할 때까지 exclusive lock을 release하지 않음
- Rigorous 2PL
    - Transaction이 commit/abort할 때까지 모든 lock을 release하지 않음

## Recovery System
Atomicity와 Durability의 구현  
Shadow database scheme
- 한번에 하나의 transaction만 active라 가정
- db_pointer가 현재 database의 copy를 가리킴
- Transaction이 작업 전에는 원래 database를 가리키다 partially commit이고 disk로 flush되면 바뀐 database를 가리킴 
- 매우 비효율적 (transaction마다 database 전체를 copy)

Failure 종류
- Transaction failure
- System crash
- Disk failure
- Fail-stop 가정: System crash가 일어나도 non-volatile storage는 오염되지 않는다고 가정 

Storage 구조
- Volatile storage
    - System crash가 일어나면 정보가 모두 날아감
    - main memory, cache memory
- Non-volatile storage
    - System crash에도 정보 유지
    - disk tape, flash memory
- Stable storage
    - 모든 failure 종류에도 살아남는 이론적인 storage
    - 여러 non-volatile storage들을 이용해 근사

Data Access    

<img src = "https://github.com/eomhs/TIL/blob/main/figures/Data%20access.PNG" width="600" height="400"/>  

Recovery 알고리즘
- 두 가지 파트로 나뉘어짐
- 첫번째로 normal transaction동안 log를 기록
- 두번쨰로 failure 이후 log를 통해 복구

**Log-Based Recovery**
- Log란 일련의 log records
- Log records
    - Transaction이 시작할 때: \<T start>
    - T가 X에 write 하기 전: \<T, X, V1, V2>
    - T의 마지막 statement가 끝날 때: \<T commit>
    - T가 roll back하고 끝날 때: \<T abort>
- Undo(T): T의 마지막 log record로부터 돌아가며 old value로 update
- Redo(T): T의 처음 log record부터 시작해서 new value로 update
- Checkpoint
    - Log를 처음부터 끝까지 searching하는것은 비효율적이기에 도입됨
    - 현재 main memory에 있는 모든 log record를 stable storage에 output하고
    - 수정된 모든 buffer block들을 disk에 output하고
    - \<checkpoint L> log record 작성 

Concurrent transactions에 대한 recovery
- \<checkpoint L>형태의, checkpoint 당시 active한 transactiont list 기록
- Redo phase
    - 마지막 checkpoint의 L을 undo-list에 넣음
    - 앞으로 가며 redo함
    - 새로 start한 trasaction이 있으면 undo-list에 넣음
    - abort하거나 commit한 transaction이 있으면 undo-list에서 제거
- Undo phase
    - Log를 마지막에서 시작해 뒤로 가며
    - undo-list에 있는 transaction에 대한 log record를 undo
    - 해당 \<T start>를 만나면 \<T abort>를 log에 기록하고 undo-list에서 제거
    - undo-list가 비면 종료

Write-Ahead Logging
- Log record는 생성된 순서대로 stable storage에 output됨
- T는 \<T commit>이 stable storage에 output된 이후에 commit state로 바뀜
- Block of data가 disk로 output되기 전에 그 data들에 대한 log records가 먼저 stable storage로 output 되어야 함
