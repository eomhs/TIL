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
    - `Unknown` 상태에서 5분이 지나면 해당 node의 모든 pods에 대해 API를 이용한 제거를 트리거함

기본적으로 node controller는 각 node의 state를 5초마다 체크함(주기는 flag로 변경 가능)
### Rate limits on eviction
대부분의 경우, node controller는 `--node-eviction-rate` (default 0.1)을 이용해 초당 eviction의 상한을 정함  
default 0.1의 경우 10초에 최대 1개의 node에 있는 pods에 대해서만 eviction

