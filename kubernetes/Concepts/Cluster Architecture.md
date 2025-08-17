# Nodes
kubernetes는 container를 pod 안에 배치하고 node에서 실행해서 workload를 실행함  
node는 cluster에 따라 물리적 혹은 가상 머신임  
각 node는 control plane에 의해 관리되고 pod를 실행하기 위해 필수적인 service들을 포함  
보통 한 클러스터에는 여러 개의 node가 있음  
node는 kubelet, container runtime, kube-proxy를 포함
## Management
API server에 node를 등록하는 방법은 다음의 두 가지가 있음
- node에 있는 kubelet이 control plane에 자기 자신을 등록
- 수동으로 node object를 등록  

node object를 생성하거나 kubelet이 등록하면 control plane이 새 node object가 valid한지 검사함  
- `metadata.name`에 해당하는 node가 API server에 등록됐는지 확인
- name은 DNS subdomain name 규칙을 따름
- healthy (필요한 service들이 잘 실행되는중) 인지 확인  

### Node name uniqueness
같은 name은 같은 state와 label 등을 가지고 있다고 가정되기 때문에 name은 unique 해야 함
### Self-registration of Nodes 
`--register-node=true`이면(default도 true) kubelet은 자기 자신을 API server에 등록하려고 함
### Manual Node administration
`--register-node=false`로 하고 kubectl을 통해서 수동으로 node object를 생성할 수 있고, flag에 상관없이 수정도 가능  
`node-role.kubernetes.io/<role>: <role>` 형식의 label을 달아서 optional인 node role을 설정할 수도 있음 (value는 무시해서 보통 role name을 넣음)  
node selector와 label을 통해 특정 node에서만 돌게 하는 등 pods의 scheduling을 조절할 수 있음   
node를 다음의 명령어로 unschedulable로 마킹하면 기존에 돌고 있던 pods는 유지하면서 새 pods가 추가되지 않도록 할 수 있음   
`kubectl cordon $NODENAME`
## Node status
node의 status는 다음의 정보를 포함
- Addresses
    - 내
- Conditions
    - 용
- Capacity and Alloctable
    - 추
- Info
    - 가
## Node heartbeats 
node는 다음의 두 가지 방식으로 heartbeat를 보내 cluster가 각 node의 가용성을 확인할 수 있게 함  
- node의 .status를 업데이트
- `kube-node-lease` 네임스페이스 내의 Lease object
## Node controller
node controller는 control plane component로 node에 대해 다음의 다양한 부분을 관리함
- node가 등록되면 CIDR block을 할당함
- 클라우드 환경일 경우 내부 node list를 cloud provider의 가용 machine list와 동기화함
- node의 health를 모니터링
    - node가 unreachable이면 .status의 `Ready`를 `Unknown`으로 변경
    - `Unknown` 상태에서 5분이 지나면 해당 node의 모든 pods에 대해 API를 이용한 eviction을 트리거함

기본적으로 node controller는 각 node의 state를 5초마다 체크함(주기는 flag로 변경 가능)
### Rate limits on eviction
대부분의 경우, node controller는 `--node-eviction-rate` (default 0.1)을 이용해 초당 eviction의 상한을 정함  
default 0.1의 경우 10초에 최대 1개의 node에 있는 pods에 대해서만 eviction  
node eviction의 작동은 주어진 가용 영역의 node들이 unhealthy한 비율에 따라 달라짐
- unhealthy node의 비율이 `--unhealthy-zone-threshold` 이상이면 eviction rate가 줄어듬  
- 노드의 수가 `--large-cluster-size-threshold` 이하이면 eviction이 멈춤  
- 그렇지 않으면 초당 `--secondary-node-eviction-rate` 정도로 eviction rate가 줄어듬  
## Resource capacity tracking
node objects는 사용가능한 메모리나 CPU 수 같은 resource capacity 정보를 추적함  
kubelet이 self register한 경우 그 때 capacity를 보고하고, 수동 등록한 경우 그 때 capacity 정보를 추가해야함  
kubernetes scheduler는 node에 있는 모든 pods를 위한 충분한 resource가 있는 것을 보장함(노드에 대한 container request의 총 합이 node capacity보다 크지 않은지 확인함)  
request의 총 합은 kubelet에서 관리하는 모든 컨테이너를 포함하고, 컨테이너 런타임에서 시작된 컨테이너나 kubelet의 관리 범위 밖은 제외함

# Communication between Nodes and the Control Plane
아래 내용은 API server와 kubernetes cluster 사이의 통신 경로를 설명함  
## Node to Control Plane
kubernetes는 "hub and spoke" API 패턴임  
node나 pods에서 API는 모두 API server로만 통함  
API server는 443 포트를 사용하고 하나 이상의 client authentification을 사용  
node는 API server에 연결하기 위해 cluster를 위한 public root certificate로 프로비전 되어야함  
pod는 API server에 연결하기 위해 service account를 활용해서 kubernetes가 public root certificate와 bearer token을 pod가 인스턴스화될 때 자동으로 주입함
## Control plane to node
API server에서 node로 통신하는 법은 node의 kubelet과 통신하거나, API server의 proxy 기능을 통해 어떤 node, pod, service든 통신할 수 있음  
### API server to kubelet 
API server에서 kubelet으로 통신을 하는 이유는 
- pod의 log fetching
- 실행 중인 pod에 연결(보통 kubectl 사용해서)
- kubelet의 포트포워딩 기능 제공  
### API server to nodes, pods, and services
기본적으로 HTTP로 연결되며, https: 접두사를 붙여도 인증서의 유효성을 검사하지 않아 안전하지 않음

# Controllers
control loop란 시스템 상태를 조절하는 끝나지 않는 루프임  
controller는 cluster의 state를 보고, 필요한 변화를 요청하는 control loop임  
## Controller pattern
controller는 1개 이상의 resource type을 추적함  
이러한 object는 desired state를 나타내는 특정한 필드가 있음 
controller는 상태 조절을 직접 할 수도 있지만, 일반적으로 유용한 부가 효과가 있는 방식인 API server에게 메세지를 보냄  
### Control via API server
job controller는 kubernetes built-in controller의 예시임  
job이란 단일 pod나 여러 pods를 실행시켜 task를 수행하고 멈추는 kubernetes resource임  
job controller가 새로운 task를 발견하면,클러스터 어딘가에서 nodes의 kubelets이 그 일을 완수하기 위한 적절한 수의 pods를 실행시키게 함  
job controller가 직접 실행시키는 것이 아니라, API server에게 pods를 생성하거나 삭제하라고 요청함   
control plane의 다른 component는 새로운 정보에 대응하고 결국 그 일을 완료함  
새로운 job을 생성하면 desired state는 그 job이 완료되는 것이고, 따라서 job controller는 job을 완료하기 위해 pods를 생성하고 실행시킴    
끝나면 job object에 `Finished`로 마킹하는데, 이처럼 controller는 object의 설정을 업데이트함
### Direct control
job의 경우와 다르게, 일부 controller는 클러스터 외부의 것을 바꿔야 할 때가 있음  
예를 들어 클러스터에서 충분한 수의 node가 있는걸 보장하려면 필요할 때 새 node를 만들 수 있도록 현재 클러스터 밖의 무언가가 필요함  
외부 상태와 상호작용하는 controller는 API server로부터 desired state를 찾고 외부 시스템과 직접적으로 통신함  
중요한 점은 controller는 desired state로 바꾸기 위한 작업들을 한 뒤 API server에 현재 state를 보고하고, 다른 control loop는 이를 보고 행동할 수 있음  
## Desired versus current state 
kubernetes는 cloud-native 관점에서 시스템을 관찰하고 지속적인 변화에 대응함  
cluster는 어느 시점에서든지 변할 수 있고 control loop가 자동으로 실패를 바로잡음(이것은 잠재적으로 cluster가 stable state에 도달할 수 없음을 의미함)  
controller가 실행중이고 작업중이라면 전체 상태가 stable인지 아닌지는 중요하지 않음  
## Design  
디자인 원리에 의해, kubernetes는 cluster state의 특정 면을 관리하는 많은 controllers를 사용함  
일반적으로, 특정 controller는 desired state를 만들어야 하는 한 종류의 resource를 사용하고 그렇게 하기 위해 다른 종류의 resources를 관리함  
예를 들어 job controller는 job objects와 pod objects를 추적하는데, pod는 job controller가 생성하지만 job은 다른 controller가 생성함    
하나의 monolithic controller보다 간단한 여러 개의 controller를 사용하는 게 좋음(controller는 실패할 수 있음)
## Ways of running controllers
kubernetes는 kube-controller-manager 안에서 실행되는 deployment controller, job controller 등의 built-in controllers 의 집합이 있음    
control plane의 외부에서 실행되는 controller를 찾아서 kubernetes를 확장할 수도 있고, 직접 controller를 만들 수도 있음
