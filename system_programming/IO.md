# I/O
## I/O란?
Input/Output의 줄임말로 메인 메모리와 외부 장치(디스크, 터미널, 네트워크 등)가 데이터를 복사하는 프로세스 

UNIX I/O를 알아야 하는 이유
- 시스템 개념을 이해하는 데 도움을 준다
- UNIX I/O를 사용해야만 하는 경우도 존재
## UNIX I/O
모든 UNIX I/O는 consistent 하고 uniform way로 처리된다   
리눅스 파일이란 일련의 bytes이다   
모든 I/O 장치들은 **파일**로 모델링이 되어있다   
- Opening files
    - 애플리케이션이 I/O 장치에 접근하고 싶어 커널에게 대응하는 파일을 열어달라고 요청
    - 커널은 descriptor이라고 불리는 음수가 아닌 정수를 리턴하는데, 이는 그 파일을 식별한다
    - 리눅스 쉘에 의해 생성된 프로세스는 세 open file을 가진다
        - standard input (descriptor 0)
        - standard output (descrpitor 1)
        - standard error (descriptor 2)
- Changing the current file position
    - 커널은 open file에 대해 file position을 기억하고 있다
    - file position이란 몇 바이트까지 읽었는지 위치
    - 애플리케이션은 seek를 통해 file position을 변경 가능
- Reading and writing files
    - read operation은 file position부터 시작해 n bytes를 파일에서 메인 메모리로 복사
    - write operation은 file position부터 시작해 n bytes를 메인 메모리에서 파일로 복사
- Closing files
    - 애플리케이션이 I/O 장치에 접근이 끝나서 파일을 닫아달라고 커널에게 알림
    - 커널은 파일에 할당된 메모리와 descriptor을 다시 사용가능하게 해제
## Files
리눅스 파일에는 타입이 존재
- Regular file
    - user/app data를 가지고 있음
    - OS는 포맷에 대해 모름(텍스트인지, 바이너리인지 등)
- Directory
    - 다른 파일들의 이름과 위치를 포함하는 파일
- Socket
    - 네트워크 간 다른 프로세스와 상호작용하기 위한 파일
- 이 외에 pipe,character, block devices 등도 있음

리눅스 시스템에서는 '/' 라는 루트 디렉토리에서 시작되는 파일 시스템을 가짐   

hard link는 해당 파일의 메모리 위치를 같이 가리키는 파일로 hard link의 수가 0이 되어야 해당 메모리가 반환됨    

soft link는 해당 파일의 메모리 위치를 참조하는 파일로 해당 파일의 hard link들이 모두 삭제되면 파일을 읽을 수 없음    

각 파일은 user와 group에 소유됨   
- ### ACL (Access Control Lists)
    - drwxrw-r-- 와 같은 권한 존재
    - 맨 앞은 flag로 directory, link 등을 의미
    - 그 다음부터 3자리씩은 각각 user, group, others의 권한을 의미
    - r은 read, w은 write, x는 execute

## Opening and Closing Files
open 함수를 통해 파일을 오픈함     
이때 여러가지 flag와 umask가 존재   
close 함수를 통해 파일을 닫음   
error일 경우 -1을 리턴하고 아닐 경우 descriptor을 리턴
## Reading and Writing Files
read 함수를 통해 read operation   
write 함수를 통해 write operation   
error일 경우 -1을 리턴하고 아닐 경우 descriptor을 리턴
## Standard I/O
C에는 UNIX I/O보다 higher-level의 input/output function들을 제공하는 라이브러리가 존재하는데 이게 standard I/O library   

fopen, fclose, fread, fwrite, fgets, fputs, scanf, printf 등이 있음   

open file을 stream으로 모델링했고 모든 C 프로그램은 세 open streams로 시작됨
- stdin (standard input에 대응 : descriptor0)
- stdout (standard output에 대응 : descriptor1)
- stderr (standard error에 대응 : descriptor2)
## Reading File Metadata
애플리케이션은 stat과 fstat 함수를 호출해서 파일에 대한 Metadata(데이터에 대한 데이터)를 얻는다   
stat이란 struct 안에 Metadata가 들어있고 stat과 fstat함수는 이러한 stat을 채워서 리턴한다
## Reading Directory Contents
opendir 함수를 통해 DIR(directory stream) 포인터를 리턴    
readdir 함수를 통해 DIR 포인터를 인풋으로 받아서  directory stream에서 다음 directory entry에 대한 포인터를 리턴   
closedir 함수를 통해 stream을 닫음
## Sharing Files





