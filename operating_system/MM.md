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
