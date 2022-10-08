# Memory Management
## Virtual Memory를 이해해야 하는 이유
- Virtual memory is central
    - Virtual memory는 컴퓨터 시스템에서 중점적인 역할을 하고 있어 시스템을 이해하기 위해 알아야 함
- Virtual memory is powerful
    - Virtual memory는 applications에게 메모리에 관한 강력한 권한을 주는 것이기 때문
- Virtual memory is dangerous
    - Virtual memory가 부적절하게 사용되면 메모리 관련 버그가 생길 수 있음
## Physical and Virtual Addressing
Physical addressing   
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Physical%20addressing.PNG" width="600" height="400"/>   

Virtual Adderessing
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Virtual%20addressing.PNG" width="600" height="400"/>   

## Dynamic Memory Allocation
Dynamic memory는 heap영역에 allocated됨   
Memory는 word addressed   
Allocator 제약
- Allocated blocks의 수나 사이즈를 조절할 수 없음
- malloc 요청에 즉시 처리해야함, reorder나 buffer을 사용할 수 없음
- Free memory에서 allocate 해야 함
- Alignment 규정을 만족해야 함
- Free memory만 조작할 수 있음
- 일단 malloc되면 allocated blocks를 움직일 수 없음   
### Peak Memory Utilization
Aggregate payload P<sub>k</sub>: malloc(p)를 하면 p bytes의 payload가 생기고 free()를 하면 allocated된 payload가 사라지는데 현재 request까지의 총 aclloated payloads를 의미    
Current heap size H<sub>k</sub>: 현재 heap에 allocated될 공간이 없으면 sbrk를 통해 heap size가 늘어나고 다시 줄어들지 않는다고 가정함   
Peak memory utilization: U<sub>k</sub> = ( max<sub>i≤k</sub>P<sub>i</sub> ) / H<sub>k</sub>
### Fragmentation
- Internal Fragmentation
    - payload가 block size보다 작은 경우에 발생함
    - 이전 request에 의존하기 때문에 측정하기 쉬움
- External Fragmentation
    - 총 heap memory는 충분하지만, 충분히 큰 single free block은 없을 때 발생함
    - 이전뿐만 아니라 미래의 request에도 의존하기 때문에 측정하기 어려움
### Implicit Free Lists
각 block마다 size와 allocation status를 알기 위해 header라 불리는 맨 앞 word에 size를 넣고 마지막 bit에 allocated flag를 사용   
Free block을 찾는 데에는 세 가지 방법이 있음
- First fit
    - 처음부터 시작해 맞는 free block을 찾음
- Next fit
    - 이전 탐색이 끝났던 위치부터 시작해 맞는 free block을 찾음
- Best fit
    - 가장 적절한 free block을 찾음(byte 수가 거의 남지 않는)
    - first fit에 비해 속도는 느림

Free 후에는 앞 혹은 뒤 블럭도 free block일 경우 coalescing을 해야함   
앞 블럭과 coalescing을 하기 위해 header뿐만 아니라 footer tag도 사용하고 이를 통틀어 boundary tag라고 명칭함
### Explicit Free Lists
Free block 내부에는 payload area를 사용할 수 있으므로 next free block과 previous free block 포인터를 넣음   
Root부터 시작해서 Explicit Free lists가 생김   
Insertion policy에는 두 가지가 있음
- LIFO (last in first out) policy
    - Freed block을 free list에 첫 위치에 넣음(Root)
    - 장점: 간단하고 상수 시간
    - 단점: Fragmentation이 address ordered policy보다 나쁨
- Address ordered policy
    - Freed block을 항상 address order에 맞게 free list에 넣음
    - 장점: Fragmentation이 LIFO보다 좋음
    - 단점: 탐색이 필요
### Segrated Free List
각 사이즈 클래스마다 free list가 있음  
ex) 1-2, 3, 4, 5-8, 9-inf   
장점:
- Higher throughput
    - 2의 거듭제곱 사이즈 클래스들에 대해 로그 시간에 처리
- First fit이 best fit 같은 효과를 가짐