# Linux Kernel
## Syscall 추가하는법
1. `kernel/ `에 새로 만든 syscall의 .c 파일 추가
2. Syscall table에 새로 만든 syscall 등록
    - ARM64의 경우 `include/uapi/asm-generic/unistd.h`
3. `kernel/sys_ni.c`에도 새로 만든 syscall 추가 
4. `include/linux/syscalls.h`에 syscall과 필요한 struct의 원형 선언
5. `kernel/Makefile`의 `obj-y` 변수에 새로 만든 syscall의 .o 파일 이름 추가 
