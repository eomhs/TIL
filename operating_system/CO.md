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
### Bounded Buffer (Producer-Consumer)
size가 N이고 0... N-1 까지 접근한 뒤 다시 0으로 돌아오는 buffer  
Critical section에 lock을 걸어서 최대 한개의 thread만 접근하게 해야함  
Critical section이란 atomically하게 shared data에 접근이 필요한 code의 부분 