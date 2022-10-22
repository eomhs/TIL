# Process Management
## Processes
Process란 실행중인 프로그램의 인스턴스   
Process는 각 프로그램에게 다음의 두 가지 추상화를 제공함
- Logical control flow
    - 각 프로그램이 CPU를 독점적으로 사용하는 것처럼 보임
- Private virtual adress space
    - 각 프로그램이 main memory를 독점적으로 사용하는 것처럼 보임
### Process States
Process states에는 6종류가 있음
- **new**: process가 생성됨
- **running**: Instruction이 실행중
- **waiting**: process가 어떤 event가 발생하기를 기다림
- **ready**: process가 processor에 할당되기를 기다림
- **terminated**: process가 실행을 끝냄 
### Process Control Block (PCB)
Process control block에는 process에 관한 정보가 들어있음
- Process state
- Program counter
- CPU registers
- CPU scheduling information
- Memory management information
- Accounting information
- I/O status information
### Process Creation
Process는 항상 parent process에 의해 생성됨   
따라서 트리 모양의 hierarchy를 가짐   
Process는 **process identifier (PID)** 에 의해 식별되고 관리됨
### Context Switching
Process는 kernel에 의해 관리됨   
**Context switching**을 통해 한 process에서 다른 process로 control flow가 일어남
- 이전 process의 정보를 저장하고 다음 process의 저장된 정보를 불러옴(PCB)
- Context switch에 걸리는 시간은 overhead임   
   
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Context%20switch.PNG" width="600" height="400"/>

### Concurrent Processes
두 process의 flow가 겹치면 concurrent이고 아니면 sequential   
다음의 그림에서
- Concurrent: A&B, A&C
- Sequential: B&C   

<img src = "https://github.com/eomhs/TIL/blob/main/figures/Control%20flow.PNG" width="600" height="400"/>

multi core system의 경우 실제로 concurrent process들이 parallel하게 실행됨
## Process Control
### Process Management API
- fork()
    - 새 process를 생성함
    - parent에게 child의 PID, child에게 0을 리턴(실패시 -1 리턴)
    - child는 parent의 file descriptor table을 복사해서 가짐
    - virtual memory의 경우 일단 page table만 복사하고 write할 일이 있을때만 actual memory를 변경함 (COW: copy on write)
- exit()
    - process를 종료함
    - parent process가 reaping하기 전까지 exit status와 PCB가 남아있음: zombie 라고 부름
    - child process보다 parent process가 먼저 종료하면 child process의 PPID는 1이 됨
- execv()
    - 현재 process의 context에서 application을 실행함
    - v는 argv, e는 envp를 의미(execv, execve 등에서)
- waitpid()
    - child process를 기다림
## Exceptions
Exception이란 system state의 변화가 생겨 control flow에 생긴 급격한 변화    
Exception 발생 시 exception table의 해당하는 exception number에 있는 instruction을 통해 handling   
해결 후 세 가지 옵션 중 하나로 리턴
- return to I_current (현재 instruction을 다시 실행)
- return to I_next (다음 instruction을 실행)
- abort (프로세스 종료)   

Exception은 다음의 네 가지 종류가 있음 

<img src = "https://github.com/eomhs/TIL/blob/main/figures/Exception%20classes.PNG" width="600" height="200"/>

## Signals
Signal이란 process에게 시스템에서 어떤 이벤트가 일어났음을 알려주는 메세지임   
Exception과 비슷하지만 higher-level software form임   
보통 1-31의 integer로 표현됨   
Signal에 대해 kernel은 **default action**을 정의해놓음
-  terminate/core
- ignore
- stop / continue

Process는 signal에 어떻게 반응할지 정할 수 있음
- **catching** the signal (custom signal handler을 정의함)
- **blocking** the signal (나중에 unblocked될때까지 받지 않음)
- **ignoring** the signal
- executing **default action** of the signal
- 특정 signal은 catch, block, ignore이 불가능함 (SIGKILL, SIGSTOP)

Signal은 kernel이나 request of a program에 의해 생성됨   

Signal 전달과 받는 과정은 다음과 같음
- Signal Delivery
    - kernel은 process의 PCB의 internal signal state를 업데이트시켜 signal을 전달함
- Pending Signals
    - 전달되었으나 받지 않은 상태
    - 최대 한개의 pending signal만 존재
- Signal Reception
    - 나중에, kernel은 signal state를 확인하고 선택된 action을 실행함

Signal reception은 **asynchronous** to delivery : Signal은 context switch에서 return하기 전에 즉시 전달됨
### Signal API
- kill(pid_t pid, int sig)
    - process에 signal을 보냄
    - pid의 값에 따라 받는 process의 범위가 달라짐
    - 실패시 <0의 integer을 리턴함
- sigaction(int signum, const struct sigaction *act, struct sigaction *oldact)
    - signal을 catch하게 signal handler을 등록하거나, ignore하거나 default action을 하게 등록함 
    - 실패시 <0의 integer을 리턴함
- signal(int signum, sighandler_t handler)
    - sigaction과 유사하고 간단함
    - 다만 UNIX 버전마다 약간 다른점이 존재해 현재는 사용하지 않는 함수
    - 실패시 <0의 integer을 리턴함
- sigprocmask(int how, const sigset_t *set, sigset_t *oldset)
    - set에 있는 signal들을 block 하거나(SIG_BLOCK), unblock 하거나(SIG_UNBLOCK), blocked signal mask를 set으로 설정함(SIG_SETMASK)
    - 실패시 <0의 integer을 리턴함
- sigpending(sigset_t *set)
    - pending된 모든 signal의 set을 리턴함
    - 실패시 <0의 integer을 리턴함
