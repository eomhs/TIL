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

# Object Names and IDs
cluster의 각 object에는 resource별 고유한 이름과 UID가 있음  
예를 들어 myapp-1234 인 Pod는 하나만 존재 가능하지만, Pod와 Deployment의 이름이 같을 수는 있음
## Names
`/api/v1/pods/some-name`처럼 resource URL을 지칭하는 client가 제공한 문자열  
API resource는 API group, resource type, namespace, name으로 구분되기 때문에, API version에 상관없이 name은 고유해야 함    
다음은 주로 사용되는 name에 대한 네 가지 제약임
### DNS Subdomain Names
최대 253글자, 영숫자와 '-', '.'만 포함 가능, 첫 글자와 마지막 글자는 영숫자
### RFC 1123 Label Names
최대 63글자, 영숫자와 '-'만 포함 가능, 첫 글자와 마지막 글자는 영숫자
### RFC 1035 Label Names
RFC 1123 Label Names와 거의 같으나 첫 글자가 무조건 영문 
### Path Segment Names 
이름이 '.'나 '..'이면 안되고 '/'나 '%'를 포함하면 안됨
## UIDs
object를 구분하기 위해 kubernetes에서 생성한 UUID 문자열

