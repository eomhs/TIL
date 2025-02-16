# Kubernetes Components
<img src = "https://github.com/eomhs/TIL/blob/main/figures/k8s_overview.png" width="800" height="500"/>   

## Core Components
쿠버네티스 클러스터는 control plane과 하나 이상의 node로 이루어짐
### Control Plane Components
- kube-apiserver
- etcd
    - key value 저장소
- kube-scheduler
- kube-controller-manager
- cloud-controller-manager (optional)
### Node Components
- kubelet
- kube-proxy (optional)
- Container runtime

# Kubernetes Object Management
Object란 영속성을 가지는 객체
## Imperative commands
명령형 커맨드로, 클러스터의 살아있는 object에 대해 동작함
## Imperative object configuration
yaml이나 json으로 설정 파일을 만들어서 그 파일로 object를 생성하거나 삭제하거나 변경함    
`kubectl create`, `kubectl delete`, `kubectl replace`등의 형태
## Declarative object configuration 
원하는 상태를 yaml로 만들고 적용      
`kubectl apply`의 형태


