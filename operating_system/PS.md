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