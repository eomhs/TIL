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
## Helm 및 Amazon S3를 사용하여 애플리케이션 배포
1. Helm, Helm-S3 플러그인 설치
    - `curl -sSL https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash`
    - `helm plugin install https://github.com/hypnoglow/helm-s3.git`
2. Helm S3 리포지토리 설정
    - `helm s3 init s3://<S3_BUCKET_NAME>` 
    - 차트 정보를 트래킹하기 위해 버킷에 index.yaml 파일이 생성됨
3. 버킷을 Helm 클라이언트용 차트 리포지토리로 추가
    - `helm repo add <name> s3://<S3_BUCKET_NAME>`
    - helm repo add는 index.yaml을 가져와 /home/ssm-user/.cache/helm/repository 디렉토리에 저장
4. Helm chart 디렉토리 생성
    - 디렉토리 이름은 차트 이름
    - Chart.yaml: 차트에 대한 정보가 들어있음
    - security/: Pod security group에 대한 manifest
    - templates/: values와 결합하면 유효한 manifest를 만들어내는 템플릿
    - values.yaml: 사용할 values가 정의되어 있는 파일
5. Helm chart 패키징 및 S3에 푸시
    - `helm package helm-chart/`
    - `helm s3 push <path-to-chart-tgz> <name>`
6. Helm chart 검색 및 설치
    - `helm search repo`
    - `helm install <name> s3://<S3_BUCKET_NAME>/<chart-tgz> --version <version> --dry-run --debug`
        - --dry-run: 차트 설치 테스트용
        - --debug: 설치 프로세스에 대한 자세한 디버깅 정보 제공
7. Helm chart 편집 및 재배포
    - values.yaml 편집
    - `helm upgrade <name> <path-to-chart-dir>`
8. 변경사항 롤백
    - `helm rollback <name> <rollback-version>`