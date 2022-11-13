# Concurrent Programming
Concurrent program이란 programs with more than a single flow of control
Operating system level의 concurrency 개념과는 다름   
**Data parallelism**과 **Task parallelism**에 유용함
- Data parallelism
    - 같은 연산을 여러 threads에 분배
    - ex) vector addition C = A + B
- Task parallelism
    - 다른 task를 여러 threads에 분배
    - ex) webserver

Concurrent program은 세 가지 classic problem이 있음
- **Races**
    - 실행 결과가 arbitrary scheduling decision에 의해 여러 가지가 나올 수 있음
- **Deadlock**
    - 부적절한 resource 할당으로 프로그램 진행이 막힘
- **Livelock/Starvation/Fairness**
    - 프로그램의 일부는 진행 가능하지만 일부는 막힘

Concurrent program을 생성하기 위해서는 세 가지 접근법이 있음
- Processes
    - Kernel이 자동적으로 여러 logical flows를 배치함
    - 각 flow는 고유한 address space를 가짐
- Threads
    - Kernel이 자동적으로 여러 logical flows를 배치함
    - 각 flow는 address space를 공유함
- I/O multiplexing with select()
    - 프로그래머가 수동적으로 여러 logical flows를 배치함

Process 기반 디자인의 경우 무엇을 공유하는지 명확하다는 장점이 있지만, context switch와 process control의 overhead가 비교적 높음   
## Concurrent Programming with Threads
Process는 다음과 같이 여러 view로 볼 수 있음   
Process: Traditional View    
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Traditional%20view.PNG" width="600" height="300"/>   

Process: Single Thread View   
<img src = "https://github.com/eomhs/TIL/blob/main/figures/SingleThread%20view.PNG" width="600" height="400"/>   

Process: Multi-Threaded View     
<img src = "https://github.com/eomhs/TIL/blob/main/figures/MultiThreaded%20view.PNG" width="600" height="500"/>   

Thread란 process 내에서 실행의 단위임   
각 thread는 address space와 file descriptor을 공유하며 고유한 PC와 stack, register을 가짐   
Thread와 process의 공통점과 차이점은 다음과 같음
- 공통점
    - 고유한 logical control flow를 가짐
    - concurrently하게 실행가능
    - kernal에 의해 context switched
- 차이점
    - Thread는 code와 data를 공유하고, process는 (보통) 그러지 않음
    - 위의 이유로 thread는 process보다 less expensive
        - Process control (creating and reaping)이 thread control보다 더 expensive
        - Context switch도 process가 thread보다 expensive
    - Process는 tree hierarchy이고 thead는 pool 형태임
        - 각 thread는 다른 thread를 kill할 수 있음
        - 각 thread는 다른 thread가 terminate되기까지 기다릴 수 있음
        - Main thread: process에서 처음 실행된 thread

Thread 기반 디자인의 경우 thread간 data structures의 sharing이 쉽고 process에 비해 효율적이라는 장점이 있지만, 의도하지 않은 sharing이 문제를 일으킬 수 있음
### POSIX thread(Pthread) API
- pthread_create()
    - thread를 생성함
- return    
pthread_exit()    
pthread_cancel()
    - thread를 terminate함
- pthread_join()
    - thread를 기다림
- pthread_self()
    - TID를 리턴함
- pthread_detach()
    - 해당 TID를 "detached" mode로 설정
- 등등 man 7 pthreads 참조   

Thread는 joinable 혹은 detached mode 둘 중 하나임   
- Joinable thread는 다른 thread에 의해 reaped or killed 가능
    - must be reaped to free memory resources     
- Detached thread는 다른 thread에 의해 reaped or killed 불가능
    - 자동적으로 reaped 되거나 terminated됨
## Races and Synchronization
C 프로그램에서 여러 thread가 변수 x의 객체를 참조한다면 x는 공유되었다고 정의함   
개념적으로와는 달리 실제 작동상 thread는 다른 thread의 stack에 접근할 수 있어 문제가 생길 수 있음
### Mutual Exclusion
cnt++ 같은 하나의 동작도 어셈블리 레벨로 보면 Load cnt, Update cnt, Store cnt의 과정으로 나뉘어짐   
만약 thread 1에서 Store cnt하기 전에 thread 2에서 Load cnt를 한다면 원하는 결과가 아님   
프로그램이 잘 실행되고 있는지는 Progress Graph에서 trajectory가 unsafe region을 지나는지 여부로 확인할 수 있음(safe trajectory)   
Safe trajectory를 보장하기 위해서는 unsafe trajectory를 가지지 않게 thread들의 execution을 synchronize해야함   
Classic solution으로는 Semaphores가 있음   
다른 방법으로는 Mutex and condition variables (Pthreads), Monitors (Java) 등이 있음   
### Semaphores
Semaphore란 non-negative global integer synchronization variable   
P와 V operation에 의해서 조작됨   
- P(s): [ while (s == 0) wait(); s--; ]
- V(s): [ s++; ]

kernal이 [ ]사이에 있는 operation들이 각자 execute 되는걸 보장함(atomic operation)   

Semaphore을 이용해 Mutual exclusion을 하는 기본적인 방법은 각 shared variable에 unique semaphore mutex를 연관시키고, critical한 section 부근에 P(mutex)와 V(mutex) operation을 이용함   
Terminology
- Binary semaphore: 값이 항상 0 또는 1인 semaphore
- Mutex: mutual exclusion에 사용되는 binary semaphore
    - P operation: "locking" the mutex
    - V operation: "unlocking" or "releasing" the mutex
    - "Holding" a mutex: locked and not yet unlocked
- Counting semaphore: used as a counter for set of available resources   

Semaphore은 Progress Graph에 unsafe region을 감싸는 forbidden region을 생성해 trajectory가 들어가는 것을 막음