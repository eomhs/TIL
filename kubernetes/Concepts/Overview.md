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
최대 253글자, 소문자 영숫자와 '-', '.'만 포함 가능, 첫 글자와 마지막 글자는 영숫자
### RFC 1123 Label Names
최대 63글자, 소문자 영숫자와 '-'만 포함 가능, 첫 글자와 마지막 글자는 영숫자
### RFC 1035 Label Names
RFC 1123 Label Names와 거의 같으나 첫 글자가 무조건 영문 
### Path Segment Names 
이름이 '.'나 '..'이면 안되고 '/'나 '%'를 포함하면 안됨
## UIDs
object를 구분하기 위해 kubernetes에서 생성한 UUID 문자열

# Labels and Selectors
Label은 object에 붙여진 key-value들로, attributes의 느낌으로 쓰임  
object 생성 시 혹은 언제든지 추가될 수 있음
## Syntax and character set
label key는 '/'로 구분되는 optional prefix 와 name으로 구성됨  
name은 최대 63글자, 영숫자와 '-', '_', '.', 로 이루어지고 첫 글자와 마지막 글자는 영숫자  
optional prefix는 DNS subdomain 제약을 따름  
만약 prefix가 없으면, 해당 label key는 개인용으로 간주됨  
end-user object에 label을 다는 컴포넌트들은 (system or third-party) 필수적으로 prefix를 달아야 함
label value는 label key의 name과 제약이 같음

## Label Selectors
label selector를 통해, client/user는 원하는 object 집합을 찾을 수 있음  
label selector는 comma-separated requirements로 만들어질 수 있고, logical AND로 작동함
### Equality-based requirement
'=', '==', '!=' 의 연산자가 있고 (앞에 두 개는 똑같이 작동함)
```
environment = production, tier != frontend
```
처럼 쓰면 environment라는 key가 있고 value가 production 이면서,  
tier라는 key에 대한 value가 frontend가 아닌(혹은 tier key가 없는) object들의 집합이 선택됨

### Set-based requirement 
'in', 'notin', 'exists'의 연산자가 있음  
```
environment in (production, qa)
tier notin (frontend, backend)
partition
!partition
```
각각  
- environment라는 key가 있으면서 value가 production이나 qa인 경우  
- tier라는 key에 대한 value가 frontend, backend가 아니거나 tier라는 key가 없는 경우  
- partition이라는 key가 있는 경우
- partition이라는 key가 없는 경우

똑같이 comma separator로 logical AND를 수행할 수 있고 이는 Equality-based requirement와 혼합해서 쓸 수 있음

# Namespaces
kubernetes에서 namespace는 하나의 cluster 안에서 resource들의 group을 분리할 수 있게 해줌  
resource의 name은 namespace 안에서만 고유하면 됨  
namespace 기반의 범위는 StorageClass, Nodes 등의 cluster 범위의 object들에 대해서는 적용 불가능
## When to Use Multiple Namespaces
namespace는 여러 팀이나 프로젝트에서 많은 유저들이 사용하는 환경에서 사용되는 것이 의도됨(많아야 수십명정도의 유저가 있는 경우에는 고려하지 않아도 됨)  
namespace는 중첩될 수 없고, resource는 하나의 namespace에만 속함  
## Initial namespaces
kubernetes는 네 개의 초기 namespace가 있음
- default
    - namespace를 생성하지 않고도 새로운 cluster를 만들 수 있게 포함됨
    - 다만 production 환경에서는 사용하지 않는 것이 권장됨
- kube-node-lease
    - 각 node에 연관된 lease object를 가지고 있음
    - Node lease는 kubelet이 heartbeat를 보내서 control plane이 node failure를 감지할 수 있게 해줌
- kube-public
    - 이 namespace는 인증되지 않았어도 모든 client가 읽을 수 있음
    - 전체 클러스터 중에서 공개적으로 드러나서 읽을 수 있는 resource들을 위해 예약되어 있음
    - 이 namespace가 public인 건 convention이지 필수는 아님
- kube-system
    - kubernetes에 의해 생성된 object들을 위한 namespace
## Working with Namespaces
### Viewing namespaces 
다음 명령어로 현재 namespace들 조회  
`kubectl get namespace`
### Setting the namespace for a request
--namespace flag로 request에 대한 namespace를 지정할 수 있음
```
kubectl run nginx --image=nginx --namespace=<insert-namespace-name-here>
kubectl get pods --namespace=<insert-namespace-name-here>
```
### Setting the namespace preference 
이후의 kubectl command에 대한 영구적인 namespace를 설정할 수도 있음  
```
kubectl config set-context --current --namespace=<insert-namespace-name-here>
# Validate it
kubectl config view --minify | grep namespace:
```
## Namespace and DNS
Service를 생성하면, 이에 대응되는 DNS Entry가 생성됨  
이 entry는 `<service-name>.<namespace-name>.svc.cluster.local`와 같은 형식이고, 이는 service-name만 사용하는 컨테이너에서 namespace의 local인 service로 resolve 됨  
이는 dev, stage, prod 등의 여러 namespace가 같은 설정을 쓰는 경우 유용함
## Not all objects are in a namespace
대부분의 resource는 namespace에 속하지만, nodes 등 그렇지 않은 경우도 있음  
## Automatic labelling 
kubernetes control plane은 모든 namespace에 대해 `kubernetes.io/metadata.name` 라는 label key를 달고, value는 namespace name임

# Annotations
label과 비슷한 방식으로, object에 metadata를 붙일 수 있음
## Attaching metadata to objects
label과 다른 점은 annotation은 object를 식별하거나 선택하는 데 사용되지 않음  
## Syntax and character set 
label과 같음(key name + optional prefix)

# Field Selectors
field selector는 다음처럼 resource의 하나 이상의 field의 value로 kubernetes object를 선택할 수 있게 함  
`metadata.namespace!=default`   
`kubectl get pods --field-selector status.phase=Running`  
## Supported fields
[링크](https://kubernetes.io/docs/concepts/overview/working-with-objects/field-selectors/#list-of-supported-fields) 참조
## Custom resources fields 
모든 custom resource type은 `metadata.name` and `metadata.namespace` 필드를 가짐
## Supported operators
label selector와 같음, comma-separated로 여러 개 가능, 여러 resource type 한번에 select도 가능

# Finalizers
finalizer는 특정 조건을 만족하기 전까지 resources를 지우지 않게 하는, namespace에 속한 key임   
controller에게 deleted object가 가졌던 resources들을 정리하라고 알림  
finalizer를 가진 특정 object를 삭제하라고 API를 쏘면 일단 `.metadata.deletionTimestamp`가 object에 생기고, terminating state로 바뀜. 이후에 finalizer에 명세된 행동을 끝낸 뒤 해당 finalizer를 지우고, 이렇게 모든 finalizer를 지워 `metadata.finalizers`가 없어지면 이제 완전히 지움  
## How finalizers work
manifest file을 이용해 resource를 생성할 때 `metadata.finalizers`에 명세할 수 있음  
finalizer를 가진 특정 object를 삭제하라고 API를 쏘면 object에 `.metadata.deletionTimestamp`가 생기고 terminating state로 바뀜  
이후에 controller가 finalizer에 명세된 일들을 하기 시작하고, 하나 끝낼때마다 해당 finalizer을 지움  
모든 finalizer가 지워지면 deletionTimestamp가 있는 object는 자동으로 지워짐
## Owner references, labels, and finalizers
Job이 하나 이상의 Pods를 만들 때, Job controller는 tracking 하기 쉽게 같이 만들어진 Pods에 label을 추가함  
또한 Job controller는 Pods에, 그 Pods를 생성한 Job을 owner reference로 추가함  
만약 이 Job을 Pods가 실행 중일 때 지우면, 이 owner reference를 이용해 어떤 Pods를 cleanup 해야할지 추적함  
이 때 finalizer도 같이 처리함  
따라서 finalizer가 자신이 처리되기 전까지 owner reference cleanup을 막을 수도 있음

# Owners and Dependents
kubernetes에서, 어떤 object는 다른 object의 owner임  
예를 들어, ReplicaSet은 Pods set들의 owner임  
## Owner references in object specifications 
dependent object에는 `metadata.ownerReferences` 필드에 owner object reference가 있음  
reference는 같은 namespace에 있는 object의 이름과 UID로 구성됨  
kubernetes는 ReplicaSets, DaemonSets, Deployments, Jobs and CronJobs, and ReplicationControllers 같은 objects에 의해 생성된 objects들에 있는 이 필드의 value를 자동으로 채움  
dependent object에는 또한 `ownerReferences.blockOwnerDeletion` boolean 필드가 있는데, 이게 true이면 이 객체가 먼저 지워지기 전에 owner가 지워지는 것을 막음  
## Ownership and finalizers 
foreground deletion에서, kubernetes는 owner object에 `foreground` finalizer를 달아서 controller가 `ownerReferences.blockOwnerDeletion=true`인 모든 dependent object를 먼저 지워야 지울 수 있게 함  
orphan cascading deletion에서, kubernetes는 `orphan` finalizer를 달아서 owner object가 지워지면 dependent objects에서 `ownerReferences`를 지움

# Recommended Labels
[링크](https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/) 참조
