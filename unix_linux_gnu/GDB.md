# GDB
## tutorial
### Compile
- GDB를 사용하기 위해서는 컴파일 과정에서 -g 플래그를 사용해야함
- ex) gcc [other flags] -g -o \<output file> \<source file>
### Start up GDB
- 다음 중 한 가지 방법으로 실행
- gdb 
    - 실행 후 file \<output file>로 디버깅할 파일 설정
- gdb \<output file>
- gdb \<output file> \<core file>
- gdb \<output file> \<running pid>
## Run
- (gdb) run
- 처음부터 종료까지 프로그램을 실행
- Breakpoint를 정하지 않았고 오류가 없다면 실행결과가 출력됨
- 오류가 있었다면 관련 정보가 출력됨
## Quit
- (gdb) quit 또는 (gdb) q
## Setting breakpoints
- 다음의 방식으로 breakpoint 설정
- (gdb) break \<linenumber>
    - ex) (gdb) break 10
- (gdb) break \<source file>:\<linenumber>
    - ex) (gdb) break test.c:10
- (gdb) break \<func>
- (gdb) break \<source file>:\<func>
- break 대신 b 로도 가능
## Continue, step, next
- (gdb) continue
    - 다음 breakpoint까지 프로그램을 진행
    - continue 대신 c 로도 가능
- (gdb) step
    - 코드의 다음 줄을 실행
    - 함수를 만나면 함수 안으로 들어감(next와 차이점)
    - step 대신 s 로도 가능
- (gdb) next
    - 코드의 다음 줄을 실행
    - 함수를 만나면 다음 줄로 건너뜀 (step과 차이점)
    - next 대신 n 으로도 가능
- **TIP** : ENTER키로 가장 최근에 실행한 명령 반복 가능
## Print
- (gdb) print \<variable>
- \<variable> 을 출력함
- print 대신 p 로도 가능
- p/\<format> 형식으로 포맷 설정
    - d : 10진수
    - x : 16진수
    - s : 문자열
    - a : 주소 (address)
    - f : floating point
    - 등이 있음
## Setting watchpoints
- Breakpoint가 특정 줄이나 함수 또는 조건에 의해 작용했다면 watchpoint는 변수에 작용함
- (gdb) watch \<variable>
- \<variable> 이 수정될 때마다 정지하고 old value와 new value를 출력함
## Deleting breakpoints and watchpoints
- (gdb) delete
    - 모든 breakpoints와 watchpoints를 삭제
- (gdb) delete \<breakpoint number>
    - 해당하는 번호의 breakpoint 또는 watchpoint 삭제
    - **TIP** : (gdb) info breakpoints 또는 (gdb) info watchpoints로 번호 확인 가능
## Layout
- (gdb) layout src
    - source code를 layout
- (gdb) layout asm
    - assembly code를 layout
- (gdb) layout regs
    - registers를 layout