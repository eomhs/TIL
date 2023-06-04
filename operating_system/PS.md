# Persistent Storage
## File System
File이란
- Named byte array (User view)
- Physical storage에 있는 blocks (OS view)

File system의 역할
- Naming
- Reliability
- Protection
- Disk management

File metadata
- Name: 인간이 읽을 수 있는 유일한 정보
- Identifier: file system에서 파일을 구분하는 고유한 tag (inode)
- 이외에 type, location, size, time 등이 있음
- Disk에 저장됨

Directories
- Tree-Structured directory
    - Directory가 file처럼 disk에 저장됨
    - Data가 <name, index>로 이루어짐
- Acyclic-Graph directory
    - Tree structure보다 더 일반적임
    - Tree에서 link를 통해 한 file이나 directory에서 다른 하나로 연결함
    - Link의 종류
        - Symbolic link: file 이름을 다른 file 이름으로 mapping
        - Hard link: file 이름이 바로 그 file로 mapping

Allocation Strategy  
- Contiguous allocation
    - Contiguous memory allocation처럼 file들을 allocate
- Extent-based allocation
    - File마다 여러 contiguous region (각 region을 extent라고 함)을 가짐
- Linked allocation
    - File의 block들을 linked list로 관리
    - 각 block은 다음 block에 대한 pointer를 가짐
- FAT (Fast-Allocation Table)
    - Linked list pointer를 따로 block 밖에 있는 FAT에 저장
- Indexed allocation
    - 한 개의 index block에 block pointer들을 연속적으로 저장
- Multi-level indexed files

File system: Software layers   
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Software%20layers.PNG" width="500" height="400"/>   

## Storage Devices
Magnetic disk
- 낮은 가격에 높은 용량
- Random access에는 느린 성능
- Disk scheduling
    - FIFO
    - Shortest seek time first
    - SCAN (elevator 스케쥴링)
    - CSCAN, R-CSCAN

Flash memory
- 중간 가격에 중간 용량
- Read 성능이 뛰어나고 random write가 가장 느림
- Write는 clean cell에만 가능, 따라서 write전에 erasure block에 erase 필요
- Flash Translation Layer (FTL)
    - Write가 일어날 때 erase하는게 느리기 때문에 도입
    - Logical page를 physical location으로 mapping
    - Logical page가 다시 write되면 미리 비어있는 physical page에 write하고 remapping

## FFS, EXT, LFS
Original Unix FS의 문제점
- Block의 크기가 매우 작음
- Freelist가 정렬되지 않음
- Data locality가 없음

FFS
- Block의 크기를 4KB 혹은 8KB로 만듦
- Free block의 bitmap을 도입
- Cylinder group을 도입

Ext2
- Ext3 전에 리눅스 표준 file system
- Superblock
    - inode 수, block 수, block size, free block 수 등의 정보들이 있음
- Group Descriptor
    - block/inode bitmap의 위치와 inode table의 위치가 있음
- Block bitmap
- inode bitmap
- inode table/inode
    - inode table: 정해진 수의 inode를 포함하는 연속적인 blocks
    - inode: i_mode, i_blocks 등으로 이루어짐
- File creation시 세 dirty block이 생김
    - inode bitmap (B), 새 file에 대한 inode (I), parent directory data block (D)

<img src = "https://github.com/eomhs/TIL/blob/main/figures/Software%20layers.PNG" width="600" height="200"/>

B, I, D에 crash가 생기면 문제가 발생할 수 있어 해결해야 함  
- fsck
    - 리부팅 시에 전체 disk를 스캔해 FS consistency를 보장하지만, 느림
- Journaling
    - FS를 업데이트 하기 전에 log에 intent를 write
    - Intent 작성 전에 crash: no-op
    - Intent 작성 후에 crash: redo-op
    - 분류
        - Physical journaling: log에 real block 내용을 write
        - Logical journaling: log에 logical record를 write
    - Journal writes -> FS writes -> Journal clear 순서

Log-Structured File system (LFS)
- 나오게 된 동기
    - CPU가 빨라지면서 I/O가 더 bottleneck이 됨
    - 메모리가 많아져서 file cache가 read에 효과적임
    - Write가 disk traffic의 대부분을 차지함
- 이전의 FS의 문제점
    - 크고 연속적이면 좋지만 많은 작은 write의 경우 성능이 떨어짐
    - Data loss를 막기 위해 동기화 필요
- 아이디어 
    - Disk를 테이프처럼 취급
    - Sequential log로 data를 write
- Overwrite를 하지 않음
- 어떻게 read할 data를 찾는가?
    - inode map과 fixed checkpoint를 도입
- Disk가 꽉 차면 어떻게 하는가?
    - Disk cleaning
        - FS는 많은 segment들로 이루어져 있고, 많이 비어 있는 segment를 cleaning
        - Disk가 free space가 없거나 사용되지 않을 때 실행됨
        - 오래된 file들은 같은 segment에 놓고 스킵함
