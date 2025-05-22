# Architecting on AWS
## 모듈 1: 아키텍팅 기본 사항
### AWS 인프라
- 데이터 센터
- 가용 영역(AZ)
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
    - 위 두개가 or 으로 적용돼서 권한을 확인하게 됨
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
- 스팟 인스턴스: 가격을 정할 수 있으나 언제든 인스턴스 종료 가능
### EBS (Elastic Block Store)
- 블록 수준 스토리지
- 볼륨을 EC2 인스턴스에 연결함
## 모듈 5: 스토리지
- 블록 스토리지, 파일 스토리지, 객체 스토리지
### S3 (객체 스토리지)
- 데이터를 객체 단위로 버킷 내에 저장
- 객체에는 파일과 메타데이터가 포함
- 객체 키에는 접두사가 있음
- 기본적으로 소유자만 액세스할 수 있는 private
### EFS (파일 스토리지)
- 여러 인스턴스가 동일한 스토리지를 사용해야 하는 경우에 적합함
- FSx: NetApp ONTAP, OpenZFS, Windows File Server, Lustre 를 사용 가능
## 모듈 6: 데이터베이스 서비스
|RDB|NoSQL DB|
|---|--------|
|엄격한 스키마|기존 스키마에 적합하지 않음|
|과도한 RW 용량 필요 없음|데이터베이스 크기 수평 조절이 필요할 때|
|최상의 성능을 필요로 하지 않는 관계형 데이터 집합|RW 속도가 빨라야 할 때| 
### Amazon RDS
- Aurora, PostgreSQL, MySQL, MariaDB, Oracle, SQL Server, IBM Db2 엔진 사용 가능
- Primary DB Instance
- 다중 AZ 배포로 Failover용 대기DB 인스턴스 사용 가능
- Read Replica로도 DR, 부하 분산
- KMS로 저장 데이터 암호화 가능
- 서브넷 그룹(두 개 이상의 AZ에 있는 private subnet) 필요
### Amazon Aurora
- 클라우드를 위해 구축된 MySQL 및 PostgreSQL 호환 RDB
- 인스턴스와, 여러 AZ에 걸쳐있는 가상 스토리지인 클러스터 볼륨으로 구성됨
### Amazon DynamoDB
- 키-값 기반의 NoSQL DB
- 읽기, 쓰기별 처리 용량을 측정하기 위한 용량 유닛(RCU, WCU) 사용
- 글로벌 테이블로도 할 수 있음
- PK (파티션 키) 로 hash를 돌려서 같은 스토리지에 저장
- On-Demand or Provisioned
- 일반적으로 1초 내에 한 리전 내 3개의 AZ에 걸쳐 테이블 데이터를 복제함
- 일관성 옵션: 최종적 읽기(어떤 AZ에서는 동기화 안됐을 수도 있음) or 강력한 읽기(성공한 모든 이전 작업 업데이트 반영, 대신 비용 2배)
### Amazon ElastiCache (DB 캐싱)
- Lazy loading
- Write through
- 쓰기에 TTL 
## 모듈 7: 모니터링 및 규모 조정
### Amazon CloudWatch
- 지표 수집 및 추적
- 임계값, 경보 데이터 포인트 등 설정해서 경보 받기 가능
### AWS CloudTrail
- 계정 내 대부분의 AWS 서비스에 대한 API 호출 및 응답을 S3에 로깅
### VPC flow log
- VPC 네트워크 인터페이스에서 송수신되는 IP 트래픽 정보
- 커스텀 가능
### Amazon EventBridge
- 이벤트 받아서 라우팅
### Elastic Load Balancing
- 여러 컴퓨팅 리소스 대상으로 자동으로 트래픽 분산 및 상태 확인 수행
- 가용 영역 수준에서의 고가용성 및 보안 기능 제공
- Listener, Target Group으로 구성
- 종류
    - ALB
        - http 헤더를 보고 로드밸런싱
        - health check: health check path로 GET을 보내 상태체크
        - 보통 헬스체크용 테스트 페이지를 따로 만들어서 헬스체크 
    - NLB
        - 패킷을 보고 프로토콜과 포트 번호로 로드밸런싱
    - GWLB
        - 보안 검사에 사용
### EC2 Auto Scaling
- 다음의 세 가지 요소가 필요
    - 시작 템플릿(launch template)
    - Auto Scaling 그룹 (최소, 최대 수량, 처음에 원하는 수량)
    - Auto Scaling 정책
## 모듈 8: 자동화
- 코드형 인프라(IaC)를 사용하면 다음의 장점이 있음
    - 빠른 속도와 높은 안정성
    - 재사용성
    - 문서 및 버전 제어
    - 유효성 검사
## 모듈 9: 컨테이너
### Amazon ECR
- 도커 컨테이너 레지스트리
### Amazon ECS
- 클러스터 안에 서비스 안에 태스크가 있는 구조
- 컨테이너는 태스크에 대응됨
- EKS보다 쉬움(k8s나 오픈소스를 잘 모르면)
### Amazon EKS
- K8s를 클라우드에서 관리하는 서비스
### AWS Fargate
- 서버리스로 컨테이너 실행
## 모듈 10: 네트워킹 2
### VPC 엔드포인트
- VPC 리소스와 AWS 서비스가 프라이빗하게 통신하는 데 사용됨
- 게이트웨이 엔드포인트
    - S3, DynamoDB 대상으로 추가 비용 없이 사용할 수 있는 가상 라우터
- 인터페이스 엔드포인트
    - AWS 서비스 대상으로 생성되는 ENI와 프라이빗 링크
### VPC 피어링
- VPC간 1:1로 연결해서 트래픽을 프라이빗으로 라우팅
- VPC 피어링을 고려한다면 다른 VPC의 주소와 겹치지 않게 주소 지정 고려
- 라우팅 테이블 업데이트 필요
- 전이적 피어링 연결은 안됨(a↔b, b↔c여도 a↔c는 아님)
### AWS Site-to-Site VPN
- VPC의 가상 프라이빗 게이트웨이(VGW)와 온프레미스 네트워크 디바이스 간 VPN 연결
- VGW당 2개의 엔드포인트 생성, Active-Stanby 형태, 터널당 1.25Gbps 대역폭
### AWS Direct Connect
- 광케이블로 사용자 라우터와 Direct Connect 라우터를 연결
### AWS Transit Gateway
- 온프레미스 환경과 여러 VPC를 연결하는 허브 역할
## 모듈 11: 서버리스
- 서버리스란 인프라의 프로비저닝, 용량 조절, 보안, 고가용성을 직접 관리하지 않아도 되는 서비스
### API Gateway
- 백엔드 서비스의 API 생성 및 배포 관리를 수행하는 관리형 서비스
- 여러 마이크로 서비스의 통합 Frontend로 구성 가능
### Amazon SQS (Simple Queue Service)
- 대기열 서비스로 애플리케이션 구성 요소 사이에 배치, 메시지 처리한 소비자가 메시지 삭제
- 메시지 생산자와 소비자간 일대일 관계
- 비동기식 느슨한 결합을 통하여, 실패한 메시지 처리에 대한 내결함성 제공
- 최소 1회의 Standard 대기열과, 정확한 순서로 1회 처리하는 FIFO 대기열 구성 가능
- 짧은 폴링: 요청을 수신하는 즉시 응답 반환
- 긴 폴링: 메시지가 하나 이상 도착하거나 폴링 시간이 초과될 때까지 응답 반환 안함
### Amazon SNS (Simple Notification Service)
- 하나 주제의 여러 구독자 대상으로 메시지를 전송하는 서비스
- 주제의 메시지 게시자와 구독자간 일대다 관계
- 주제에 등록된 메시지는 바로 구독자에게 전송
- 경보 알림, 푸시 알림, 이메일 전송의 용도로 사용
### Amazon Kinesis
- 실시간 애플리케이션 구동을 위한 스트리밍 데이터 수집에서 처리, 분석
- Data Streams, Firehose, Service for Apache Flink, Video Streams 가 있음
### AWS Step Functions
- 개별 함수와 서비스로 분리된 애플리케이션의 로직을 워크플로 형태로 조정할 수 있는 서비스
- 각 스텝마다 애플리케이션 기능 사용 가능
- state machine
    - 출력을 결정하기 위해 이전 상태에 의존하는 조건을 가진 객체
## 모듈 12: 엣지 서비스
- 엣지 서비스는 AWS 엣지 로케이션 등에서 실행됨
### Amazon Route 53
- DNS 서비스로, 도메인 이름 등록 및 상태 확인 기능 제공
- 퍼블릭 호스팅 영역: 인터넷에서 트래픽을 라우팅
- 프라이빗 호스팅 영역: VPC에서 트래픽을 라우팅
- 라우팅
    - 단순
    - 장애 조치(health check)
    - 지리적 위치
    - 지리 근접 라우팅
    - 지연 시간 기반(빠른 쪽으로)
    - 다중값 응답
    - 가중치 기반
### Amazon CloudFront
- CDN 서비스
- 엣지 로케이션에서 캐싱해서 빠름
    - 캐싱 전략은 사용자가 정함
- 배포를 만들고 오리진 리소스와 연결해서 사용
### AWS Shield
- 관리형 DDoS 보호 서비스
### AWS WAF
- 일반적인 웹 공격 및 봇으로부터 웹 애플리케이션과 API를 보호하는 데 도움이 되는 웹 방화벽
- 웹 ACL과 연결할 AWS 리소스를 정의
### AWS Outposts
- 사무실에 랙을 보내 온프레미스에 AWS 서비스를 호스트
## 모듈 13: 백업 및 복구
- RPO: 목표 복구 시점
- RTO: 목표 복구 시간
- DR: 재해 복구
### 복구 전략
- 백업 및 복원
- 파일럿 라이트
- 웜 스탠바이
- 다중 사이트 액티브/액티브