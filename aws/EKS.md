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
