# Linux Kernel
## Syscall 추가하는법
1. `kernel/ `에 새로 만든 syscall의 .c 파일 추가
2. Syscall table에 새로 만든 syscall 등록
    - ARM64의 경우 `include/uapi/asm-generic/unistd.h`
3. `kernel/sys_ni.c`에도 새로 만든 syscall 추가 
4. `include/linux/syscalls.h`에 syscall과 필요한 struct의 원형 선언
5. `kernel/Makefile`의 `obj-y` 변수에 새로 만든 syscall의 .o 파일 이름 추가 
## Scheduler 추가하는법 (wrr)
수정한 파일
- `kernel/sched/sched.h`
    - struct wrr_rq 추가
- `kernel/sched/wrr.c`
    - struct sched_class에서 필요한 함수만 추가
    - trigger_load_balance_wrr() 함수 추가
- `kernel/sched/core.c`
    - scheduler_tick()에서 trigger_load_balance_wrr() 호출 추가
    - pick_next_task()에서 wrr에 맞게 수정
    - sched_init()에서 wrr이 작동하게 수정
    - 기타 등은 프로젝트 레포 참고
- `kernel/sched/Makefile`
    - wrr.c 추가
- `kernel/sched/rt.c`
    - 다음 level의 scheduling policy로 CFS대신 WRR로 변경
- `kernel/sched/debug.c`
    - 디버깅용 함수 추가