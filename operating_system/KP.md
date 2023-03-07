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

***Privileged instructions***:   
kernel mode에서 쓸 수 있는 instructions   
Ex)
- privilege level 변경
- memory access 조정
- disable/enable interrupts   

user program이 privileged instruction을 실행하려 할 시 exception 발생 및 kernel에 의해 handling (주로 process 중단) 

***Limits on memory accesses***   
simple memory protection: base and bounds 그러나 많은 문제 발생   
따라서 Virtual Addresses를 사용


