# Memory Management
## Address Translation
Memory Management Unit (MMU)를 통해 virtual address를 physical address로 전환
- Base and Bound
    - Process마다 base와 bound를 정하는 방식
    - 한번 정해지면 address를 바꿀 수 없고 fragmentation 문제
    - Process간 data와 code를 공유할 수 없고 stack/heap을 늘릴 수 없음
- Segmentation
    - Segment란 virtual memory의 연속적인 영역
    - 각 process가 segment table을 가짐
    - Virtual address를 각각의 logical segment로 나눔 (stack, heap, data, code 등)
    - 각 segment마다 base와 bound를 가지고 process간 공유 가능
    - Segment의 길이를 정하는 것이 어렵고 large segment에 대한 fragmentation 문제
- Paging
    - Memory가 fixed-size의 page들로 할당됨
    - 각 process가 page table을 가짐
        - page table은 memory에 저장되고 PTBR (Page table base register)이 가리킴
        - page table에는 vpn (virtual page number)에 대한 ppn (physical page number)가 있음
        - Address bits = page number + page offset
    - Virtual address space가 매우 크면 실제로 사용되지 않아도 page가 매우 많아지는 문제
- Multilevel Paging
    - 여러 level의 page table이 있고 virtual address에는 각 level의 index와 offset이 들어있음
    - 실제로 사용되는 page table entries만 채움
    - Lookup이 2번 이상이라는 단점이 있음 (Extra memory accesses)
    - Linux에는 four-level paging이 사용됨
- Translation Lookaside Buffer (TLB)
    - Recent address translation을 가진 cache
    - cache hit인 경우 translation을 사용하고, cache miss인 경우 page table 사용
    - Video frame buffer같은 경우 access가 sequential하게 일어나서 TLB의 효율이 없음
    - Context switch를 위해 Process ID tag가 있음

전체적인 구조는 다음과 같음  
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Memory%20management.PNG" width="700" height="500"/>

## Caching and Virtual Memory
Cache란 original data보다 접근이 빠른 copy of data  
예시로는 TLB, virtually addressed caches, physically addressed caches  
Temporal locality
- 프로그램이 같은 memory location을 여러번 참조하는 경향
- ex) instructions in a loop

Spartial locality
- 프로그램이 인접한 memory location을 참조하는 경향
- ex) data in a loop

Working set: resonable cache hit rate를 위해 cache되는것이 필요한 memory location set으로, 프로그램 전체보다 훨씬 작음  
Working set을 작게 만드는게 cache hit에 도움이 됨  
다만 많은 시스템에서 working set model이 적용되지 않음
- ex) web cache
- 이런 경우 Zipf distribution을 따르고, cache size를 늘려도 cache hit rate가 크게 늘지 않음


Cache Lookup
- Fully associative
    - cache entries의 처음부터 끝까지 순환하며 원하는 address가 있는지 찾음
- Directed mapped
    - main memory의 블록이 hash를 통해 cache에 저장되고 원하는 address가 있는지 hash로 조회
- Set associative
    - 여러 개의 cache set을 가지며 hash를 통해 어떤 cache set인지 찾고, 그 set에서 처음부터 끝까지 순환하면 원하는 address가 있는지 찾음

Cache Replacement Policy
- Random
- FIFO
    - Cache보다 큰 memory를 순차적으로 반복 방문하면 최악의 경우
    - ex) Cache entry가 4까지 있는데 A B C D E 를 반복해서 찾는 경우
- LRU (Least Recently Used)
    - 가장 오랜 시간 사용되지 않은 cache entry를 교체
    - 일반적으로 최적임
    - 다만 마찬가지로 data set이 memory보다 큰 repeated scan 일 때 성능이 안좋음
- MRU (Most Recently Used)
    - 가장 최근에 사용된 cache entry를 교체
    - repeated scan에 효율적

Cache size를 키우면 항상 성능이 향상될까?
- 대부분의 replacement policy와 상황의 경우 그렇다
- 다만 몇몇 경우 오히려 cache hit rate를 감소시킴
- ex) Belady's Anomaly (FIFO: 3개보다 4개일 때 cache hit 감소)

### Memory-Mapped Files & Virtual Memory
Memory-mapped files
- File을 memory segment로 열음
- Segment memory에 load/store
- File의 일부가 아직 memory에 없으면 page fault
- Kernel이 missing blocks를 memory로 불러오고 restart process

Memory-mapped files 장점
- Copy하지 않고 바로 memory에 접근하므로 I/O 비용이 없음
- Process간 공유할 수 있어 IPC를 할 수 있게 해줌

Demand Paging  
memory page를 disk에 대한 cache처럼 사용해 필요한 부분만 메모리에 올려 사용: page fault를 통해 disk에서 memory로 swap in  

Clock (Second chance) Algorithm  
- LRU는 좋은 알고리즘이지만, 구현에 overhead가 있음
- Clock 알고리즘은 LRU의 근사 알고리즘
- 페이지마다 reference bit이 있고 memory reference가 있을 때마다 1로 설정
- 시계처럼 circular하게 page를 돌며 ref bit을 확인
- 1이면 0으로 바꾸고 다음 page를 이동
- 0이면 현재 page를 victim page로 정함
- Extension: dirty bit을 추가해 dirty page를 skip하게 사용  

Thrashing  
Degree of multiprogramming이 임계점을 넘으면 page fault와 그로 인한 replacement가 자주 일어나 swapping의 비용이 크기 때문에 CPU utilization이 급격하게 떨어지는 현상

