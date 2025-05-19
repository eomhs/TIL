# Architecting on AWS
## 모듈 1: 아키텍팅 기본 사항
### AWS 인프라
- 데이터 센터
- 가용 영역
    - 리전 내의 데이터 센터
- 리전
    - 거버넌스, 레이턴시 등을 고려해서 선택
- AWS Local Zones
    - 사용자와 더 가까운 위치에서 핵심 인프라 사용
- 엣지 로케이션
    - cdn, 데이터 캐싱 등에 사용
## 모듈 2: 계정 보안
### AWS Identity and Access Management(IAM)
- IAM User
    - 부여된 권한은 영구적
    - Access Key, Secret Access Key로 인증
    - 규모가 일정 수준 이상이면 User Group 사용
- IAM Role
    - 부여된 권한이 임시
    - AWS STS에서 AssumeRole이 가능한지 확인
    - switch role 하면 완전히 권한이 바뀜(기존의 권한에서 추가되는게 아님)
- IAM Policy
    - User, User Group, Role에 할당됨
### 보안 정책
- 권한 부여
    - 자격 증명 기반 정책
    - 리소스 기반 정책(인라인)
        - Principal
### AWS Organizations
- 계정이 많을 경우 사용
    - SCP(Service Control Policies)
## 모듈 3: 네트워킹 1
### VPC (Virtual Private Cloud)
- 하나의 리전 안에 VPC가 있고, 그 안에 가용 영역(들), 가용 영역 안에 퍼블릭 서브넷과 프라이빗 서브넷이 있는 구조
- CIDR 표기법 사용
- 서브넷
    - VPC 내의 IP 주소 범위 
    - 퍼블릭 서브넷
        - 다음의 3가지 요소가 필요함
            - 라우팅 테이블: VPC가 네트워크 트래픽을 라우팅하는데 사용하는 규칙 집합
            - 인터넷 게이트웨이: VPC의 리소스와 인터넷 간의 통신 허용, NAT 수행
            - 퍼블릭 IP 주소: 인터넷에서 연결할 수 있는 IP 주소
        - NAT 게이트웨이가 여기 위치함
    - 프라이빗 서브넷
        - 인터넷에 간접적으로만 액세스 허용
        - IP 주소 변경 안됨
        - 아웃바운드 시 NAT 게이트웨이가 프라이빗 IP를 탄력적 IP (EIP) 로 바꿔서 인터넷에 액세스하게 해줌
### 보안 그룹 (Security Group)
- AWS 리소스로 전송되는 인바운드 및 아웃바운드 트래픽을 제어하는 가상 방화벽
- 기본은 모든 인바운드 차단, 모든 아웃바운드 허용
- chaining이 가능함. 웹 -> 앱 -> DB 순으로 규칙을 적용하는 등
## 모듈 4: 컴퓨팅
### EC2 (Elastic Computing Cloud)
- 이름 및 태그
- AMI (Amazone Machine Image)
    - 제공 옵션
        - AWS 사전 구축
        - AWS Marketplace 이용
        - 고유한 AMI 생성
- 인스턴스 유형 이름
    - ex) c7gn.2xlarge
    - c: 시리즈, 7: 세대, gn: 옵션, 2xlarge: 인스턴스 크기
    - 시리즈 + 세대 + (옵션) 을 인스턴스 패밀리라고 함
- 테넌시(Tenancy)
    - EC2 인스턴스는 기본적으로 shared tenancy
        - 즉 여러 AWS 계정이 하드웨어를 공유
    - 전용 인스턴스와 전용 호스트도 가능
- 배치 그룹 설정 가능(Cluster, Partition, Spread 등)
- 사용자 데이터로 인스턴스 생성 시 shellscript 실행 가능
### EBS (Elastic Block Store)
- 블록 수준 스토리지
- 볼륨을 EC2 인스턴스에 연결함
