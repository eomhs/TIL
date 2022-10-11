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