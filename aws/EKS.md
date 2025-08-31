# Running Containers on Amazon Elastic Kubernetes Service
## 모듈 1: Kubernetes 기본 개념
### 컨테이너 오케스트레이션
- 서비스
    - Pod는 언제든 떴다가 죽을 수 있기 때문에 사용되는 로드밸런서 같은 느낌
    - ClusterIP를 통해 Pod와 통신 가능
### Kubernetes 내부
- 제어 영역
    - API 서버, 컨트롤러, 스케줄러, etcd
- 데이터 영역
    - 노드(컨테이너 런타임, kubelet, kube-proxy, Pods)
### Pod 스케쥴링
- 조건자
    - 다음으로 필터링
    - 컨테이너별 resource requests(요청), limits(한도) 의 합
    - 토폴로지(Taint and Toleration)
        - taint는 노드의 속성으로, key=value:effect 형식을 가짐
        - toleration은 파드의 속성으로 다음과 같은 형식을 가짐  
        ```yaml
        tolerations:
        - key: "key1"
          operator: "Equal"
          value: "value1"
          effect: "NoSchedule"
        ```

    - 볼륨 요구 사항
            
- 우선순위
    - 조건자를 통과한 노드 중 가장 높은 점수의 노드에 Pod를 배치함
### Kubernetes 객체
- ConfigMap
    - 기밀이 아닌 데이터를 key value로 저장
- Secret
    - 기밀인 데이터를 key와 base64 인코딩된 value로 저장  
- Job 및 CronJob
- DaemonSet
    - 모든 노드가 요청된 Pod의 사본을 갖게 함
- StatefulSet
- Deployment 및 ReplicaSet
    - Pod의 수를 항상 일정하게 유지
- Custom Resource
- 기타
    - `~/.kube/config`

## 모듈 2: EKS 기본 사항
### EKS 제어 영역
- EKS는 제어 영역을 거의 자동으로 관리해줌
    - 3개의 AZ, 최소 2개의 제어 영역 노드, 3개의 etcd 노드
### EKS 데이터 영역
- 셀프 매니지드 노드
- 관리형 노드그룹
    - 프로비저닝, 관리, 업데이트, 스케일링 지원
- Fargate
    - 데이터 영역 완전 관리
    - fargate profile을 만들어서 selector와 일치하는 pod들은 fargate로 실행
- EC2 vs Fargate
    - 권한 있는 Pod 필요하면 EC2
    - 퍼블릭 서브넷 필요하면 EC2
    - 시작 시간도 EC2가 유리
    - 단순하고 인프라 부담 줄이고 가변 비용 절감은 Fargate  

## 모듈 3: EKS 클러스터 구축 및 유지 관리
- eksctl
- terraform
## 모듈 4: EKs 클러스터에 애플리케이션 배포
- 컨테이너 이미지 레포지토리: ECR
- 패키지 관리자: helm
    - Chart.yaml, values.yaml, templates/*.yaml
    - 차트는 ECR 혹은 S3에 저장 가능
- CI/CD: ArgoCD
## 모듈 5: EKS를 사용하여 대규모로 애플리케이션 관리
### EKS에서 수요에 맞게 크기 조정
- Horizontal Pod Autoscaler(HPA)
    - Pod 수를 조정
- Vertical Pod Autoscaler(VPA)
    - Pod의 사용가능한 리소스를 조정
- Cluster Autoscaler
    - Auto Scaling 그룹을 사용해서 노드 수를 조정
    - 머신 스펙이 거의 고정
    - 최대, 최소, 필요 노드 수로 조정
- Karpenter
    - Auto Scaling 그룹과 상관 없이 노드 수를 조정
    - 머신 스펙을 동적으로 정해줌
    - Provisioner & Constraint를 사용해 Scaling
    - topologyConstraint로 한 node 아래 최대 pod 수, zone 등 제약 가능
## 모듈 6: EKS에서 네트워킹 관리
### EKS의 통신
- 컨테이너 간 통신
    - localhost를 사용해 통신
- Pod 간 통신
    - CNI(ENI)를 통해 통신
- 클러스터 외부에서의 수신
### 서비스를 사용한 로드 밸런싱
- 서비스
    - ClusterIP
        - 클러스터 내부에서만 사용되는 IP
    - NodePort
        - \<NodeIP>:\<NodePort>를 요청해서 클러스터 외부에서 액세스 할 수 있음
        - NodePort를 사용해서 자동으로 생성되는 ClusterIP로 연결
    - LoadBalancer
        - 자동으로 생성되는 NodePort와 ClusterIP에 연결
    - ExternalName
        - CNAME 레코드를 해당 외부 DNS 이름 값과 함께 반환해서 내부 클러스터 DNS 이름에 매핑됨
- Ingress
    - 클러스터 외부에 있는 HTTP 및 HTTPS 경로를 서비스에 노출하고 트래픽 규칙 정의
    - 어떤 수신 경로가 어떤 NodePort 서비스로 이동하는지 지정하는 path 필드가 있음
    - NodePort를 만들어야 함
- CoreDNS
    - 클러스터의 모든 Pod에 대한 DNS 이름 확인을 제공해줌
## 모듈 7: EKS에서 관찰 기능 구성
### 지표 수집
- Prometheus
    - 오픈소스 db
    - Prometheus 지표
        - metric_name{”tag”=”value”[,…]}value
### 로그 관리
- 로그 수집, 로그 집계 및 저장, 데이터 시각화의 3단계
## 모듈 8: EKS에서 스토리지 관리
### 스토리지 설계 패턴
- 애플리케이션 유형
    - stateless
        - 복제본이 동시에 실행, 데이터가 일시적, 임시 스토리지가 적합
        - k8s의 대부분의 기능은 stateless
    - stateful
        - 각 인스턴스의 고유한 자격 증명, 데이터 내구성 필요, 영구 스토리지가 적합
        - k8s StatefulSet
- AWS 스토리지 옵션: EBS, EFS, FSx
### Kubernetes의 영구 스토리지
- 임시 볼륨
- Persistent Volume(PV) 수명 주기
    - 프로비저닝(PV 생성)
        - 정적(수동 생성)과 동적(PVC 요청 혹은 초기 PVC 소비로 자동 생성) 방식이 있음
    - 바인딩
        - Persistent Volume Claim(PVC) 기준과 일치하는 PV를 찾아 바인딩
    - 사용
    - 회수
- 스토리지 클래스
    - PV를 미리 만들지 않고 Storage Pool이 있어서 가져다 씀
### AWS 스토리지 서비스를 사용하는 영구 스토리지
- Container Storage Interface(CSI) 드라이버
    - 컨테이너화된 워크로드에 임의 블록 및 파일 스토리지 시스템을 노출하는 표준
    - aws의 경우 EKS에서 EBS,EFS 등을 가져오는 데 쓰이고, iam policy, iam role, csi 드라이버 service account 필요
- EBS
    - 애플리케이션을 위한 일대일 스토리지 옵션
- EFS
    - 애플리케이션을 위한 일대다 스토리지 옵션
    - 데이터 공유에 적합
## 모듈 9: EKS에서 보안 관리
### 인증 및 권한 부여
- AWS: 인증과 권한 다 IAM
- kubernetes: 인증 IAM 권한 RBAC (Role Based Access Control)
    - (Cluster)RoleBinding, (Cluster)Role
        - Cluster Role과 Role의 차이는 범위가 Cluster 전체인지, 한 Namespace인지 차이
    - IAM Role을 그룹에 매핑
- Kubernetes Service Account
    - Pod 등 쿠버네티스 리소스가 대상인, RBAC 주체 유형 중 하나임
- IAM Roles for Service Accounts(IRSA)
    - IAM Role을 RBAC Service Account와 간접적으로 연결
    - RBAC에서 만든 토큰을 디지털로 서명하는 OIDC 공급자를 사용해 three-way handshake