# Storage Management And Indexing
## Physical Storage Systems
기능적인 storage hierarchy는 다음과 같음
- Primary storage
    - 가장 빠르지만 volatile
    - cache, main memory
- Secondoary storage
    - 중간 정도로 빠르고 non-volatile
    - Primary storage와 더불어 on-line storage라고도 불림
    - flash memory, magnetic disks
- Tertiary storage
    - 느리고 non-volatile
    - Off-line storage라고도 불림
    - magnetic tape, optical storage

Magnetic disks는 다음으로 구성됨
- Disk arm
- Read-write head
- 원형 tracks
- 각 track은 여러 sectors로 구성됨
- Cylinder는 여러 platter의 한 track

Disk의 performance 측정에는 다음의 기준들이 있음
- Access time: read나 write가 요청된 후 data transfer가 시작되기까지 걸리는 시간
    - Seek time: 올바른 track으로 arm을 옮기는데 걸리는 시간
    - Rotational latency: Disk가 돌아서 원하는 sector가 head로 오기까지 걸리는 시간
- Data-transfer rate: Disk로 read하거나 write하는 초당 data
- Mean time to failure (MTTF): Disk가 잘 작동할 수 있는 남은 시간

Disk-Block Access 최적화
- Block: 한 트랙에서 연속된 sector의 단위로 read or write
- Elevator algorithm: Disk arm을 최대한 한 방향으로 움직임
- File organization: 관련된 data들을 disk의 비슷한 위치에 저장함
- Nonvolatile write buffers: Main memory에서 disk로 block을 write하는 overhead가 크므로 우선 non-volatile RAM buffer에 write함
- Log disk: Log는 보통 write만 하면 되므로 따로 log disk에 write하면 seek time이 필요 없어 매우 빠름

RAID (Redundant Arrays of Independent Disks)
- 여러개의 disk를 이용해 high capacity, high speed, high reliability를 얻는 방법
- Redundancy를 이용해 두 개의 disk에 동일한 정보를 저장하는 mirroring과, 한 disk의 parity bit를 다른 disk에 저장하는 방법으로 reliability를 올림
- Parallelism을 이용해 Block-level striping을 통해 여러 disk에 block들을 나누어 저장하여 performance를 올림

RAID Levels
- Level 0: Block striping, non-redundant
    - Reliability가 낮아서 data 손실이 있어도 괜찮은 경우에 쓰임
- Level 1: Mirrored disks (with block striping)
    - DBMS에서 log file을 저장하는데 주로 쓰임
- Level 5: Block-interleaved distributed parity
    - Parity를 한 disk에 몰아서 저장하는 것이 아닌 그림과 같이 저장함
<img src = "https://github.com/eomhs/TIL/blob/main/figures/RAID5.PNG" width="500" height="300"/>  

## Data Storage Structures
### File organization
Database는 file들로 저장됨  
각 file은 sequence of records  
각 record는 sequence of fields  
Record type에는 fixed-length와 variable-length가 있음  
Fixed length record
- Record size가 정해져 있다고 가정함
- 각 file은 하나의 type의 record들만 가지고 있음

Variable length record
- Attributes가 순서대로 저장됨
- Attributes의 길이가 다양함
    - record의 첫 부분에 (offset, length)로 각 attribute를 나타냄
    - 고정된 길이의 attribute는 대신 그냥 값이 나타남


<img src = "https://github.com/eomhs/TIL/blob/main/figures/Variable%20Length%20%20Record.png" width="600" height="200"/>  

Variable length record는 주로 **Slotted page structure**을 통해 저장됨  
- Page header가 다음을 포함
    - Record entity의 수
    - Block의 free space의 끝을 가리키는 포인터
    - 각 record의 위치와 크기
- Header와 record들 사이에 free space가 있음(record가 끝부분부터 앞부분으로 추가됨)

### Storage Access
CPU가 disk에 있는 data 처리를 하기 위해서는 main memory로 옮겨야 함  
Buffer: Disk block의 copy를 저장하기 위한 main memory의 일부  
Buffer manager: main memory에 buffer을 allocating하는 subsystem  
OS는 보통 buffer replacement에 LRU를 사용하지만 DBMS는 mixed strategy 사용(data block의 access pattern 등을 이용)

## Indexing
기본 개념
- Search key: file에서 record를 찾기 위해 사용되는 attribute
- Index file: search key와 해당하는 pointer들로 이루어진 file
- Ordered indices: search key들이 sorted order로 저장됨
- Hash indices: seach key들이 hash로 저장됨

Index 성능 평가 기준
- 지원되는 access type
    - point queries, range queries
- Time
    - access, insertion, deletion

Ordered Indices
- **Primary index**
    - Search key가 file을 sequential order로 가리킴
    - 주로 primary key로 이용되지만 필수는 아님
- **Secondary index**
    - Search key가 file을 not sequential order로 가리킴
- **Index-sequential file**
    - Primary index가 있는 ordered sequential file

Dense index: file의 모든 search key-value에 대한 index가 있음  
Sparse index: file의 몇몇 search key-value에 대해서만 index가 있음 (Index sequential인 경우에만 가능)    
Multilevel Index
- Outer index: primary index를 가리키는 sparse index
- Inner index: primary index

**B+ Tree**
- 정의
    - root에서 leaf node로 가는 모든 path의 길이가 같음
    - Tree는 at least half full
- Node 구조
    - Root
        - 최소 2명의 children
    - Typical node
        - P (pointer)와 K (search key value)로 이루어짐
        - Search key value와 K의 크기를 비교해 P를 구하고 이를 통해 다음 level로 감
    - Leaf node
        - 마지막 P가 다음 leaf node를 가리킴
        - Search key value와 일치하는 Ki가 있으면 Pi가 그 record를 가리킴
- Level
    - File에 K개의 search key가 있으면 높이는 $log_{n/2}K$를 넘지 않음

<img src = "https://github.com/eomhs/TIL/blob/main/figures/B+Tree.png" width="700" height="500"/>

- Insertion
    - Search key를 통해 leaf node를 search함
    - 이미 search key value가 leaf node에 있으면 그 파일에 record를 추가함 (필요하면 bucket으로 가는 pointer 추가)
    - Search key value가 없으면
        - Main file에 record 추가
        - Leaf node에 공간이 있으면 추가하고 없으면 **split**함
        - Split이란 원래 node의 크기를 n/2 만큼 남기고 나머지와 새 record로 new node를 만듦
- Deletion
    - Search key를 통해 leaf node를 search함
    - 그 record를 main file에서 제거함
    - Leaf node가 n/2보다 entry가 적어지면 **merge** 하거나 **redistribute**함
    - Merge는 근처 node로 남은 entry가 모두 들어갈 수 있는 경우
    - Redistribute는 근처 node가 많이 차있어 그럴 수 없는 경우