# Running Containers on Amazon Elastic Kubernetes Service 실습 
## kubectl 명령어
- `kubectl version --output=yaml`
    - 버전 정보 확인
- `kubectl get namespaces`
    - 생성된 네임스페이스 확인
- `kubectl get deploy,svc,pod -n <namespace>`
    - 해당 네임스페이스에 배포된 리소스 확인
- `kubectl set image deployment/<container> <container>=<image> -n <namespace>`
    - Deployment에서 이미지 변경
- `kubectl apply -f ~/<manifest>.yaml`
    - 매니페스트를 클러스터에 적용
- `kubectl describe [TYPE] <name> -n <namespace>`
    - 배포된 리소스의 세부 정보 확인
- `kubectl exec -it <pod-name> -n <namespace> -- /bin/bash`
    - 컨테이너 내부에서 shell 실행
    - `/etc/resolv.conf`에 Pod의 DNS 구성이 있음
- `kubectl get event -n <namespace> --field-selector involvedObject.name=<name>`
    - 특정 이름의 리소스가 관련된 로그 확인
- `kubectl delete namespace <namespace>`
    - 네임스페이스와 그 안의 모든 것을 삭제
## Kubernetes Pod 배포
1. Deployment manifest 생성
    - kind: Deployment
    - metadata: 이름과 네임스페이스, 라벨, 어노테이션 등 설정
    - spec
        - ReplicaSet
        - selector: ReplicaSet이 수를 관리해야 할 Pod를 설명함
        - template: 새 Pod를 생성하는 데 필요한 정보
2. Service manifest 생성
    - kind: Service
    - metadata
    - spec
        - type: ClusterIP
        - ports
        - selector: 필터 역할
3. Deployment manifest에 livenessProbe와 readinessProbe 추가
    - template/spce/containers
        - livenessProbe: 애플리케이션이 살아있는지 확인. 교착 또는 경합 상태면 컨테이너를 다시 띄울 수 있음
        - readinessProbe: 서비스가 트래픽을 줘도 되는지 확인
        - kubernetes 프로브는 http, ext, TCP를 지원
4. 