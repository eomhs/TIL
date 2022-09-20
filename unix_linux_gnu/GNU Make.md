# Make
GNU Make는 compiler driver로 소스 코드가 수정되었으면 다시 프로젝트을 빌드한다   
`make` 명령어를 사용해 실행이 가능하다    
 현재 디렉토리에 `Makefile`이 있으면 그 파일의 명령들을 읽고 실행한다
# Makefile
`Makefile`은 프로젝트를 빌드하는데 사용되는 규칙들을 설명한다    
변수 선언은 다음과 같이 한다
```make
VARIABLE=value
```
이러면 `$(VARIABLE)`을 통해 `value`를 참조할 수 있다    

규칙들은 다음과 같은 형식을 가진다 
```make
target : dependencies
    command
    ...
```
dependencies에 해당하는 파일이 수정되면 `make` 실행시 command들이 실행되어 target이 다시 빌드된다   
Automatic variables이 존재한다
- `$@` : target을 의미한다
- `$^` : dependencies를 의미한다