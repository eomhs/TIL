# Kernels and Processes
## Introduction
운영체제란?   
정확히 정의하긴 어려우나 User와 application을 위해 컴퓨터의 자원을 관리하는 software   
운영체제가 하는 일
- Refree:
    - User와 application에 자원 할당
    - 다른 user와 application 분리 및 communication
- Illusionist:
    - 각 application이 전체 machine을 사용하는 것처럼 보여지게 함
- Glue:
    - 라이브러리 등을 제공

Kernel space는 protected memory space이고 hardware에 바로 접근 가능함   
User space는 resource의 일부만 볼 수 있고 system call을 통해서만 hardware에 접근 가능함
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Relationship%20between%20spaces.PNG" width="600" height="500"/>

Kernel의 종류에는 Monolithic과 Micro가 있으나 대부분 Monolithic (Linux kernel)  
Linux는 hardware을 ccNUMA machine으로 생각함
## The Kernel Abstraction
운영체제의 보편적인 정의는 없음   
Kernel은 "컴퓨터에서 항상 실행중인 프로그램"임   
운영체제의 주요 역할은 Protection   
### Process Abstraction
Process: instance of a program
- 역할: Execution과 Protection
- Address space: process의 set of rights
### Dual-Mode Operation
Kernel mode
- 하드웨어에 대한 full privilege

User mode
- 제한된 privilege

Hardware support: kernel이 protect를 잘 하고 user code가 processor에서 바로 실행되게 하기 위해서는 다음의 hardware support가 필요
- Privileged instructions
- Limits on memory accesses
- Timer
- Safe way to switch mode

### Privileged instructions:   
kernel mode에서 쓸 수 있는 instructions   
Ex)
- privilege level 변경
- memory access 조정
- disable/enable interrupts   

user program이 privileged instruction을 실행하려 할 시 exception 발생 및 kernel에 의해 handling (주로 process 중단) 

### Limits on memory accesses
simple memory protection: base and bounds 그러나 많은 문제 발생   
따라서 Virtual Addresses를 사용

### Hardware Timer
user process가 무한루프 등으로 계속 실행되면 kernel이 다시 control을 가져와야 함  
이런 경우 Hardware device가 Interrupt를 통해 kernel handler에 control을 돌려줌   
Interrupt는 kernel에 의해 frequency가 정해지고 일시적으로 미뤄질 수도 있음

### Mode Switch
user mode에서 kernel mode로 변경되는 원인
- Interrupts
- Exceptions
- System calls

kernel mode에서 user mode로 변경되는 원인   
- 새로운 process나 thread가 시작됨
- Interrupt, Exception, System call에서 리턴함
- Context switch
- User-level upcall (UNIX signal)

Interrupts를 안전하게 처리하기 위한 방법은 다음과 같음
- Interrupt vector table을 통해 Limited entry로 kernel에 접근
- Atomic transfer of control (PC, SP 등이 모두 변하거나 모두 변하지 않거나)
- Transparent re-startable execution (User program은 interrupt가 처리되고 다시 그 지점에서 실행돼서 일어났는지 모름)   

Interrupt Vector Table   
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Interrupt%20Vector%20Table.PNG" width="600" height="400"/>

Interrupted process의 state는 Interrupt Stack에 저장됨   
Interrupt Stack
- kernel memory에 위치 (user process의 stack은 신뢰할 수 없기 때문)
- process/thread는 보통 kernel stack과 user stack 둘 다 보유
- kernel을 위해서는 per-processor interrupt stack

Interrupt Masking
- Interrupt handler는 실행중일때 interrupt를 받지 않음
    - Non-blocking
- OS kernel도 interrupt를 미룰 수 있음

Interrupt handler의 실행이 끝나면 
- Interrupt Stack에 저장된 레지스터 값들을 복원함
- Atomically하게 interrupted process/thread로 return함
    - PC, SP 복원, user mode로 switch
## The Programming Interface
System call은 다음의 주요 세 가지 목적이 있음
- User space를 위한 추상화된 interface 제공
- Security
- Process에게 virtualized system을 제공

OS가 제공하는 Programming Interface는 다음의 종류가 있음
- <U>Process management</U>
- <U>I/O</U>
- Thread management
- Memory management
- File systems and storage
- <U>Communicating between processes</U>
- Authentication and security
