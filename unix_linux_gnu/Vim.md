# Vim
# vimtutor
## 1장
- `h`(좌) `j`(하) `k`(상) `l`(우) 키로 움직일 수 있음   
- `:q!` 로 나가기
- `x`로 현재 커서 문자 삭제
- `i` 와 `A`로 텍스트 추가
- `:wq`로 파일 저장하고 나가기
- `ESC`키로 노말 모드로 변경
## 2장
- `dw`로 현재 단어 삭제
- `d$`로 현재 커서부터 줄 끝까지 삭제
- `dd`로 현재 줄 삭제
- `w`로 다음 단어 앞으로 이동
- `e`로 다음 단어 끝으로 이동
- `u`로 마지막 커맨드 undo
- `U`로 전체 줄 원래대로 복구
## 3장
- `p`로 현재 커서 아래에 바로 전에 삭제된 텍스트 삽입
- `rx`로 현재 커서에 있는 문자 x로 교체
- `ce` 또는 `cw`로 현재 커서부터 단어 끝까지 교체
## 4장
- `CTRL-G`로 현재 줄과 총 줄 수를 확인
- `G`로 파일 가장 아래로 이동, `gg`로 파일 시작 위치로 이동
- 숫자 + `G`로 원하는 줄로 이동
- `/` + 키워드로 검색, `n`으로 계속 검색
- `CTRL-O`로 이전 검색 위치로 이동
- `%`로 (, [, { 에 대응하는 ), ], } 위치로 이동
- `:s/old/new`로 커서 뒤에서 가장 가까운 old를 new로 변경   
 `:s/old/new/g`로 모든 파일에서 변경 가능
## 5장
- `:!`로 쉘 커맨드 실행 가능   
 ex) `:!ls`
- `:w NAME` 으로 NAME 이름의 파일로 저장
- `v`로 Visual selection을 시작하고 커서로 범위를 선택한 뒤 저장 가능
## 6장
- `o`로 현재 커서 아래줄에 입력   
`O`로 현재 커서 윗줄에 입력
- `a`로 현재 커서 뒤에 입력 
- `R`로 현재 커서부터 여러 문자 교체
- `y`로 복사, `p`로 붙여넣기
- `:set` 키워드로 환경 설정 가능   
`:set ic` : 대소문자 구분 없이 검색   
`:set is` : 검색할 때마다 일치하는 문자열 하이라이트    
`:set hls` : 검색된 부분이 하이라이트로 처리됨
## 7장
- `:help` + 커맨드로 커맨드에 대한 help system 호출
- `vimrc` 파일을 수정해서 환경설정 가능
- :e를 친 상태로 `CTRL-D`를 입력하면 e로 시작하는 커맨드의 리스트가 나옴
-  `<TAB>`으로 유일하다면 입력어 완성   
ex) :ed 에서 `<TAB>`을 누르면 :edit으로 완성
