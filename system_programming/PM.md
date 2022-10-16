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