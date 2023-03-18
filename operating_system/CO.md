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
