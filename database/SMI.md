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
