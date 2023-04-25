# Concurrency
## Concurrency and Threads
### Thread를 사용하는 네 가지 이유
- Program structure: concurrent한 task를 논리적으로 표현
- Responsiveness: 작업을 background에서 실행되게 전환
- Performance: 여러 프로세서를 이용
- Performance: I/O devices 관리

### Thread Abstraction
- 무한한 프로세서
- 예측 불가능하고 다양한 속도로 실행됨

### Thread API
- void thread_create(thread, func, args)
    - func를 실행하기 위한 새 thread를 생성함
- void thread_yield()
    - 다른 thread에게 processor을 양보함
- int thread_join(thread)
    - Parent에서 forked thread가 끝나길 기다리고
    - 그 thread가 thread_exit으로 전달한 ret를 리턴함
- void thread_exit(ret)
    - 현재 thread를 종료함

### Fork/Join Concurrency  
다음의 경우에 thread 사이에 data가 안전하게 공유됨
- Child thread가 시작하기 전에 parent가 write한 data의 경우
- Child thread가 write하고 parent가 join 후에 읽는 경우

### Thread Data Structures
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Thread%20Structure.PNG" width="600" height="500"/>

### Linux: Processes일까 Threads일까?
Linux는 tasks를 사용해서 process와 thread를 표현함  
각 task는 unique한 task_struct를 가짐

### Implementing Kernel Threads
- Kernel threads
- Kernel threads와 single-threaded processes
    - Kernel에게 kernel thread와 single-threaded user process는 비슷하게 보임
- Kernel threads와 multithreaded processes (Linux, MacOS)
    - Syscall을 통해 kernel thread operations 사용가능
- User-level threads
    - Syscall 없이 thread operations 사용

구현에서 how는 mechanism이고 what은 policy이며 이 둘을 구분해야 함
### Thread Context Switch
현재 실행중인 thread를 멈추고 다른 thread를 실행시킴   
현재 실행중인 thread의 register를 그 thread의 TCB와 stack에 저장하고 새 thread의 register를 그 thread의 TCB와 stack에서 복구함
Trigger는 두 종류가 있음
- Voluntary
    - Tread_yield
    - Thread_join (child가 이미 끝났으면 일어나지 않음)
- Involuntary
    - Interrupt or processor exception
    - 높은 우선순위를 가진 다른 thread
### Implementing User-Level Threads without Kernel Support
- Green threads (early Java)
- Preemptive user-level threads
### Implementing User-Level Threads with Kernel Support
- Per-processor Kernel Threads
    - user-level thread를 kernel thread 위에 다중화
    - user-level library는 host machine의 각 processor마다 하나의 kernel thread를 생성
    - 각 kernel thread는 parallel하게 user-level schedular을 실행
- Schedular Activations
### Alternative Abstractions
- Asynchronous I/O와 event-driven programming
    - event-driven: 어떤 event가 발생했을 때 동작을 변경하는 방식
    - state가 계속 유지됨 (context switch가 없어서 TCB도 없음)
    - state 저장/복구를 application이 명시적으로 해야함 (thread system은 자동)
- Data parallel programming
## Synchronizing Access to Shared Objects 
Synchronization이 필요한 이유(Sequential하게 짜는게 어려운 이유)는 다음과 같음
- 프로그램 실행의 결과가 thread의 실행 순서에 따라 달라짐
- 프로그램 실행이 non-deterministic할 수 있음
    - Heisenbugs: 디버깅하려고 하면 없어지는 버그
- 컴파일러나 프로세서가 instruction의 순서를 바꿀 수 있음

Too much milk 문제에서 correctness property는 다음과 같음
- safety (mutual exclusion): 안전 영역을 벗어나지 말아야함
- liveness (progress): 결국 목적에 도달해야함
### Implementing Shared Objects
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Shared%20layers.PNG" width="600" height="500"/>

### Locks
Lock이란 mutual exclusion을 제공하는 synchronization variable  
BUSY와 FREE 상태가 있음
- Lock::aquire: lock이 FREE일때까지 기다렸다가 BUSY상태로 만들고 가져감
- Lock::release: lock을 FREE 상태로 만듬

Lock의 Formal Properties
- Mutual Exclusion: 최대 한개의 thread가 lock을 유지함
- Progress: 결국 어떤 thread가 acquire에 성공함
- Bounded waiting: lock을 얻기 위한 waiting은 유한함

Lock의 단점
- expensive (blocking)
- deadlock이 일어날 수 있음
### Bounded Buffer (Producer-Consumer)
size가 N이고 0... N-1 까지 접근한 뒤 다시 0으로 돌아오는 buffer  
Critical section에 lock을 걸어서 최대 한개의 thread만 접근하게 해야함  
Critical section이란 atomically하게 shared data에 접근이 필요한 code의 부분   
Producer는 생성하고 Consumer는 소모함  
### Readers-Writers problem
reader는 shared data를 읽지만 수정하지는 않는 thread  
writer는 shared data를 수정하는 thread  
reader는 한번에 여러 thread가 읽어도 되지만 writer는 하나만 활성화 가능  
### Condition Variables
Condition variable이란 Thread가 lock에 의해 보호받는 shared state에 일어난 변화를 효율적으로 기다리게 하는 synchronization object  
다음의 세 함수가 있음
- wait(Lock* lock)
    - atomically하게 lock을 release하고 processor을 반납
- signal()
    - condition variable의 waiting list에 있는 한 thread를 선택해 깨움(ready list에 넣음)
    - waiting list가 비어있으면 아무 일도 일어나지 않음 (memoryless)
- broadcast()
    - waiting list의 모든 thread를 깨움


위의 함수들을 호출할 때 항상 lock을 가지고 있어야 함  
wait는 무조건 **while** 안에 있어야 함  
Signal의 처리 방식에는 두 가지가 있음
- Mesa (위의 signal 함수에서 설명한 방식)
- Hoare
    - Signal은 waiter에게 lock과 processor을 줌
    - Waiter는 다시 실행됨
    - Waiter가 끝나면 lock과 processor가 signaler에게 돌아옴

### Semaphores
Semaphore란 non-negative integer value를 가진 synchronization variable  
- 무조건 초기화를 해줘야함
- P(): atomically하게 value > 0 을 기다리고 , 1 감소시킴
- V(): atomically하게 value를 1 증가시킴

Condition Variable과 Semaphore의 차이점
- Semaphore는 sticky함
    - memory가 있어서 P를 부르지 않았어도 V를 부르면 value가 증가
- Condition variable은 memoryless
    - wait하지 않았으면 signal해도 아무일도 일어나지 않음

### Monitors
Concurrent programming과 object-oriented programming이 접합되며 생김  
monitor procedures를 가진 객체와 한 thread만 활성화 될 수 있음  
ex) synchronized keyword in Java

### Implementing Locks
- Version 1: Disable interrupts
    - 장점: 간단함
    - 단점: disable_interrupt와 enable_interrupt는 privileged instruction이라 user program이 사용할 수 없음,   
    다른 interrupt도 못받아 긴 critical section에서는 사용 불가능
- Version 2: Software-based locks (Peterson's algorithm)
    - 장점: 하드웨어를 별로 요구하지 않음
    - 단점: 어렵고 instruction reordering이 일어나면 결과가 달라질 수 있음 (Memory barrier을 사용하면 방지할 수 있으나 expensive)
- Version 3: Hardware Instructions
    - test_and_set을 통해 하드웨어가 atomically하게 지원
    - 단점: spin-wait이라 CPU cycle 낭비
- Version 4: Sleep Locks
    - queue를 이용해서 lock을 얻을 수 없으면 wait하고 yield하게 함
    - 단점: Higher overhead, state 관리

### Concurrency Errors
- Deadlock
- Race 
    - Data race
        - ex) cnt++ (cnt가 여러 thread의 shared data일 때)
    - Atomicity bugs
        - ex) 조건을 확인했는데 다음 instruction 전에 다른 thread로 넘어감
    - Order bugs
        - ex) 다른 thread에서 선언하기 전에 먼저 참조하는 경우

## Multi-Object Synchronization
multi-object synchronization에 명확한 답은 없음  
trade-off와 non-modular  
multiprocessor lock은 performance가 안좋음
- Lock contention: 하나의 lock만 가질 수 있기 때문
- False sharing

Cache coherence란 system이 data의 copy가 하나만 있는 것처럼 행동하는 것  
Lock contention을 줄이기 위한 방법에는 다음의 네 가지가 있음
- Fine-grained locking
    - lock이 있는 hash table
    - subset으로 나눠서 각각이 own lock을 가짐
    - hash bucket 하나당 lock 하나
    - 다만 object의 complexity 증가
    - 구현: object 수가 증가하면 hash bucket의 수도 증가하므로 *resize* operation 이용
- Per-processor data structures 
    - processor 하나당 하나의 hash table
- Ownership desing pattern
    - thread가 container에서 object를 제거하고 lock 없이 접근함
- Staged architecture
    - System을 stage라 불리는 여러 subsystem으로 나눔
    - 각 stage는 stage에 private한 state를 포함하고 그 state에서 작동하는 한개 이상의 thread를 가짐

### Lock Contention
위의 방법들을 사용해도 lock이 여전히 busy할 수 있음  
다음의 두 방법이 better할 수 있음
- MCS Locks
- RCU (read-copy-update)
- 둘 다 Linux kernel에 쓰이고, atomic한 read-modify-write instruction에 의존함  

test_and_set (spinlock)의 경우 많은 processor가 lock을 얻으려고 하면 sequential하기 때문에 시간이 오래 걸림  
MCS lock
- 각 waiting thread에 spin할 수 있는 separate memory location을 할당
- lock을 waiting하는 list가 있고 front가 lock을 가지고 tail이 마지막 thread
- CompareAndSwap
    - memory word의 value를 확인해서
    - 바뀌었으면 false를 리턴
    - 바뀌지 않았으면 new value로 바꾸고 true를 리턴

Read-Copy-Update  
- 목표: shared data에 대한 매우 빠른 read
    - read는 lock이 필요 없음
    - write는 조금 느려도 괜찮음
- 제한된 update
    - writer는 new version의 data structure을 만듬
    - new version을 atomic memory write로 publish함
- Reader는 old version 혹은 new version을 볼 수 있음
- 모든 reader가 grace period를 지나면 old version을 garbage collect함

### Multi-Object Atomicity
A->subtract(100)   
B->add(100)  
처럼 여러 object에 대한 operations가 다른 threads에게 atomic해야 하는 경우가 있음  
이를 위해 다음의 방법들이 있음
- Careful class design
    - 다만 한계가 있어서 multiple locks가 필요
- Acquire-All/Release-All
    - Request들이 들어왔을 때 동시에 acquire할 수 있는 모든 lock을 aquire하고 동시에 release
    - Request를 처리하기 전에 어떤 lock을 필요로 하는지 아는게 어려움
- Two-Phase Locking
    - Request를 처리하기 전에 acquire하는 것이 아니라 operation이 필요로 할 때 lock을 acquire함
    - 다만 operation이 아닌 request가 끝날 때 release함

### Deadlock
Resource: thread가 작업을 수행하기 위해 필요한 어떤 것  
Starvation: thead가 영원히 기다릴 수 있음  
Deadlock: resources를 circular하게 기다림  

Deadlock의 필요조건
- 한정된 resources
- No preemption
    - thread가 권한을 자발적으로 놓기 전에 뺏을 수 없음
- 자원을 가진 채로 waiting
- Circular waiting
    - 각 thread가 다른 thread가 가진 자원을  원형으로 기다림

Deadlock을 방지하는 법
- Explit or limit behavior
    - 위의 네 조건을 없앰
- Detect and recover

Resource allocation graph를 통해 deadlock과 potential deadlock을 detect  
- lock과 thread를 node
- owned by와 wait for을 edge
- cycle이 있으면 deadlock
- lock order만 봤을 때 cycle이 있으면 potential deadlock

Banker's algorithm
- 예시 참조

Deadlock은 희귀하기 때문에 방지하지 않고 일어났을 때 복구하는 식으로 overhead를 줄이기도 함  
- 그 자원 없이 진행
- Rollback하고 다시 실행

## Scheduling
Resource의 type
- Preemptible: OS가 resource를 가져갈 수 있음
- Non-preemptible: OS가 가져갈 수 없고 자발적으로 반납하는걸 기다림
### Basic Scheduling Algorithms
First-Come, First-Served (FCFS)  
- 가장 간단한 scheduling algorithm
- Non-preemptive
- FIFO
- 장점: 간단함
- 단점: 도착 순서에 waiting time이 의존함

Shortest Job First (SJF)
- 가장 짧은 수행 시간 순서대로 schedule
- 수행 시간이 같으면 FIFO
- 장점: 평균 wait time을 최소화 (preemption이 허용되지 않으면 아마 최적)
- 단점: 수행 시간을 예측하기 어려움, starve할 수 있음

Shortest Remaining Time First (SRTF)
- 새 process의 수행 시간이 현재 process의 남은 수행 시간보다 짧다면 새 process를 schedule (preemption이 허용되는 SJF)
- 장점: 평균 waiting time을 줄임 (아마 최적)

Round-Robin (RR)
- Time-sharing을 지원하는 practical approach
- Time slice만큼 process를 수행하고 남은 부분을 FIFO queue의 끝에 넣음
- 항상 FIFO보다 좋다고 할 수는 없음
- 장점
    - Interactivity가 좋음
    - Process간 CPU의 fair allocation
    - 수행시간이 매우 넓게 분포하면 평균 대기 시간이 짧음
- 단점
    - 수행시간이 비슷하면 평균 대기 시간이 나쁨 (FIFO 보다도)
    - Time slice에 performance가 의존함: 너무 높으면 FCFS와 같아짐, 너무 낮으면 context switch가 너무 많아짐

다만 Round-Robin이 항상 fair한건 아님  
 ex) 짧은 I/O bound와 긴 CPU bound workload가 섞여 있는 경우  

Max-Min Fairness
- Equal share보다 적은 resource를 원하는 task가 있으면 그만큼 먼저 schedule함
- 남은 time을 max-min으로 나눔
- 다른 task들이 모두 equal share 이상을 원하면 equal share만큼 나눔

모든 상황에 맞는 scheduler는 없음 (workload와 environment가 다르기 때문)

Multi-level Feedback Queue (MFQ)
- ready queue가 여러 queue로 나뉘어짐
- 각 queue는 각각 scheduling algorithm을 가짐 (RR, FCFS 등)
- queue들 사이에 schedule을 위한 scheduling algorithm을 골라야 함
- 각 queue는 priority가 있고 Aging (priority 높임) 과 Monopolizing resources (priority 낮춤)을 통해 조정

### Real-time Scheduling
- Hard real time: 실제로 정해진 시간 안에 task를 수행해야함
- Soft real time: 중요한 process가 다른 process보다 priority가 높아야 함 (Linux)

Priority는 statically하게 할당될 수 있으나 starvation의 문제가 생길 수 있고, OS가 dynamically하게 바꿀 수도 있음  

Priority Inversion  
- 높은 priority가 낮은 priority에 의존하는 현상
- ex) P1 (low) lock, P2 (high) lock, P3 (medium) while(...){}
- P3가 끝나야 P1이 lock을 풀고 P2가 lock을 얻으므로 priority inversion
- Waiting process의 가장 높은 priority를 잠깐 상속하는 priority inheritance로 해결 
- 위의 예시의 경우 P1에게 high priority를 상속

Hard Real-time Scheduling  
- Deadline 안에 수행되는걸 보장함
- 일정한 간격으로 CPU를 요구하는 process를 periodic하다고 함

Rate Monotonic Scheduling
- Periodic process들에게 적용가능함
- Burst length에 관계없이 주기가 짧을수록 높은 priority를 가짐
- Optimal static scheduling policy임
- 다만 CPU 이용률이 한계를 넘으면 deadline 안에 수행을 보장하지 못함

Earliest Deadline Fisrt Scheduling (EDF)
- Deadline이 빠를수록 높은 priority를 가짐
- Dynamic priorities (process의 priority가 시간에 따라 바뀜)

### Multiprocessor Scheduling
Shared-memory Multiprocessor 환경  

Global queue of processes
- 모든 CPU가 공유하는 ready queue를 사용
- 장점: CPU 사용률이 높음, 모든 process에게 fair함
- 단점: scalable하지 않음, cache가 느려짐(poor locality)

Per-CPU queue of processes
- CPU들에게 processes를 static partition함
- 장점: 구현하기 쉬움, scalable
- 단점: 각 CPU마다 process 수가 다를 수 있음 (Load-imbalance), 따라서 CPU 사용률이 낮아지고 fair하지 않음

Per-Processor Affinity Scheduling
- 각 processor가 ready list를 가짐
- Thread를 thread가 가장 최근에 수행됐던 ready list의 마지막에 넣음
- 쉬는 processor가 다른 processor로부터 대기중인 process를 가져올 수 있음
