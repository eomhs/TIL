# Architecting on AWS 실습
## VPC 인프라 구축
1. VPC 생성
    - CIDR block 지정
    - dns 설정
        - Enable DNS hostnames
            - 이 VPC 안의 인스턴스들에 대해 DNS hostname을 만들어야 하는지
2. Subnet 생성
    - vpc, 가용영역 선택, CIDR block 지정
    - subnet 설정
        - Enable auto-assign public IPv4 address
3. Internet gateway 생성
    - name과 tag만 입력하면 됨
    - Attach to VPC로 연결
4. Route table 생성
    - name, VPC, tag 입력
    - Add route로 0.0.0.0/0 -> Internet gateway
    - Subnet association으로 해당 라우팅 테이블을 사용할 subnet 지정
5. Security group 생성
    - name 입력, VPC 선택
    - Inbound, Outbound rules 설정
        - type, source 설정
            - ex) type: http, source: 0.0.0.0/0
6. EC2 인스턴스 생성
    - 기본적으로 인스턴스는 VPC와 서브넷 안에서 정의된 private IP 주소만 인식함
    - name, AMI(Amazon Machine Image), Instance type 선택
    - 네트워크 설정(VPC, subnet, security group)
    - Instance Profile 설정
    - User Data(시작 시 실행되는 스크립트) 설정
    - 생성 후 Networking 탭에서 address 및 DNS 등을 볼 수 있음
7. NAT gateway
    - name 입력, subnet 선택
    - private subnet이 인터넷에 연결할 수 있게 함 (인터넷 -> private subnet은 안됨)
        - subnet은 public subnet을 선택하고 private subnet의 route table에서 0.0.0.0/0 -> NAT gateway로

## VPC에 DB 생성
1. RDS 생성
    - DB Engine, template, master username, instance class 등 설정
    - VPC, subnet, security group 설정
2. Application Load Balancer 생성
    - target group 생성
        - target type, target group name, vpc 선택
        - register target
    - alb 생성
        - name, scheme, vpc, az와 subnet, security group 설정
        - Listener와 router 설정

## VPC에서 고가용성 구성
1. EC2 launch template 생성
    - name, version, AMI, security group, profile 등 설정
2. Auto Scaling Group 생성
    - name, launch template 설정
    - vpc와 subnet 선택
    - lb integrate 설정
        - 선택한 lb의 target group에 새 인스턴스가 scale됨
    - group size와 scaling 설정
    - tag 설정 
3. DB 고가용성 구성
    - cluster에서 primary만 있을 때 cluster를 누르고 Add reader로 replica 생성
        - az를 다르게 설정
4. NAT 고가용성 구성
    - 각 가용 영역에 NAT가 하나씩 있는 것이 가용성이 높음
5. Aurora DB 장애 조치 적용
    - read replica가 있을 때
    - primary instance를 누르고 Actions -> Failover

## 서버리스 아키텍처 구축
1. SNS topic 생성
    - type 선택, name 입력
2. SQS 생성
    - type 선택, name 입력
    - 위에서 만든 SNS subscript
    - SNS Publish message 버튼으로 잘 구독했는지 확인 가능
3. S3 이벤트 알림 생성
    - SNS에서 S3 특정 버킷 허용하게 policy 수정
    - S3에서 버킷 선택 -> Properties -> Event Notification에서 이벤트 알림 생성
4. Lambda 함수 생성
    - name, runtime, role 선택
    - add trigger
    - runtime setting의 Handler를 통해 실행될 함수 선택 가능

## Amazon S3 오리진으로 Amazon CloudFront 배포 구성
1. Cloudfront
    - General: distributed domain name, arn 등이 있음
    - Origins: 오리진에 대한 세부 정보
    - Behaviors: 캐시 등 설정
    - Error page, Invalidations, Tags
2. S3 버킷 생성
    - name 입력
    - Permissions -> Bucket policy에서 생성한 버킷에 cloudfront만 s3:GetObject등 할 수 있게 설정
3. Cloudfront create origin
    - Origin domain 선택
    - name 입력
    - Origin access control settings
        - Create new OAC
4. Cloudfront create behavior
    - path pattern 설정
    - origin과 origin group 선택
5. 결과 확인
    - S3의 object url로 접근하면 에러로 막힘
    - cloudfront의 domain + 위에서 설정한 /path로 접근하면 접근 가능(캐싱도 적용)
    