# Pods
### Pod 이란
Pod에는 storage와 network resource, run하는 명세가 공유되는 하나 이상의 container가 들어감   
보통 하나의 Pod에 하나의 container가 있는 경우가 더 많이 쓰임

### Pod 사용하는법
다음과 같이 yaml 파일을 만들어서 실행시킬 수 있음
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```
그러나 이렇게 직접적으로는 보통 생성 안하고 workload resource를 이용해서 생성함

#### Pods 관리를 위한 workload resources
다음과 같은 workload resource로 주로 생성됨
- Deployment
- Job
- StatefulSet (상태 track이 필요한 경우)

### Pods 작업하기
Pod는 수명이 짧고 일회용으로 디자인됨
#### Pods와 Controller
workload resource를 위한 controller가 복제, rollout, Pod failure 일 때 자동 복구를 해줌
#### Pod templates
workload resource를 위한 controller는 Pod template을 통해 Pod를 생성하고 관리함    
Pod template을 통해 container에 env 주입 가능  
다음과 같은 형식임(하나의 container 실행하는 Pod template)
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: hello
spec:
  template:
    # This is the pod template
    spec:
      containers:
      - name: hello
        image: busybox:1.28
        command: ['sh', '-c', 'echo "Hello, Kubernetes!" && sleep 3600']
      restartPolicy: OnFailure
    # The pod template ends here
```
Pod template을 수정해도 이미 존재하는 Pod에는 영향을 주지 않음   
새로운 Pod를 생성해서 교체하는 식으로 진행됨  

### Resource 공유와 communication
#### Storage in Pods
Pod는 shared storage volume(data가 있는 directory)을 지정할 수 있음   
Pod의 container들은 shared volume에 접근할 수 있고, 이 volume의 data는 persistent할 수도 있음  
#### Pod networking
각각의 Pod는 고유한 IP 주소를 배정받음  
Pod의 모든 container는 IP와 port 등 network namespace를 공유함  
Pod 안에서, container들은 localhost로 통신 가능하고, IPC로도 통신 가능  
다른 Pod 끼리는 IP를 사용해야만 함
### Pod 보안 설정
보안 설정을 위해서는 securityContext field를 pod 명세에 넣음

### Static Pods
Static Pods는 API 서버가 아니라 특정 노드에서 kubelet에 의해 직접적으로 관리되는 Pod임   
Static Pod가 생성되면 kubelet은 그에 대응하는 mirror Pod를 k8s API 서버에 생성함(읽기 전용으로, 모니터링에 사용) 

### Container probes
probe는 kubelet이 컨테이너에서 주기적으로 실행하는 진단으로, 다음과 같은 다양한 작업 등을 수행함  
- ExecAction
    - container 안에서 특정 cmd를 실행
    - exit code가 0이면 정상으로 판단
- TCPSocketAction
    - Pod의 IP의 특정 포트에 TCP 체크
    - 포트가 열려있으면 정상으로 판단
- HTTPGetAction
    - Pod의 IP의 특정 포트와 경로에 HTTP GET 을 날림
    - reply의 status code가 200 이상 400 미만이면 정상으로 판단