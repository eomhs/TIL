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
