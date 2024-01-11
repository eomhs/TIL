## Go Slices: usage and internals (2011)
### Introduction
슬라이스는 다른 언어의 배열과 비슷하지만, 몇 가지 특이한 속성을 가지고 있다.
### Arrays
- 배열은 길이와 원소의 타입이 지정되어 있다.   
    - 예를 들어, `[4]int`는 네 개의 int를 가진 배열을 의미한다.  
- 배열의 크기는 고정되어 있다.  
    - (`[4]int`와 `[5]int`는 다른, 양립 불가능한 타입이다)    
- 배열은 명시적으로 초기화하지 않아도 된다. 
    - 초기화 하지 않으면 0이 들어가 있다.    

- Go에서 배열은 value이다.
    - 첫 번째 원소를 가리키는 포인터가 아니다. (즉, C와 다르다)
    - 따라서 배열을 전달하면 복사본을 만든다.
    - 마치 필드가 없고 인덱스가 있는 구조체처럼 생각할 수 있다.

### Slices
- 배열과 다르게, 타입만 지정되고 길이가 지정되어 있지 않다.
- 주로 `make([]T, len, cap)`의 형태로 생성된다.
    - capacity가 없으면 len과 cap이 같게 생성된다.
- zero value는 `nil`이다.
- 슬라이싱이 가능하다.

### Slice internals
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Slice%20Struct.PNG" width="600" height="300"/>  

- 슬라이스는 배열에 대한 포인터, length, capacity를 가지고 있는 array에 대한 descriptor이다.
    - length는 slice에 의해 참조된 원소의 수이다.
    - capacity는 slice pointer가 가리키는 원소부터 underlying array의 마지막 원소까지의 수이다.
- 슬라이싱은 슬라이스의 데이터를 복사하지 않고 original array를 가리키는 새로운 slice를 생성한다.
    - 따라서, 슬라이싱된 슬라이스의 원소를 변경하면 원래 슬라이스의 원소도 변경된다.

### Growing slices (the copy and append functions)
- 슬라이스의 capacity를 늘리기 위해서는 새로운 더 큰 슬라이스를 생성하고(일반적으로 원래 cap의 두배) 원래 슬라이스의 원소들을 새 슬라이스에 복사한다.
- 주로 append를 사용한다.
- 슬라이스를 다른 슬라이스에 append하려면 ...을 붙인다.
    - ex) `a = append(a, b...)`

### A possible "gotcha"
- 앞서 말했듯이 슬라이싱은 복사가 아니라 같은 array를 가리키는 포인터이다.
- 따라서 거대한 array를 가리키는 슬라이스를 반환할 때는 대신 새 슬라이스를 생성하고 `copy` 등을 이용해 데이터를 복사해 반환하는 것이 좋다.  