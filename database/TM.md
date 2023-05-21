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
 