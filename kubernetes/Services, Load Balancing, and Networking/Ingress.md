## What is Ingress?
인그레스는 클러스터 외부의 HTTP/HTTPS 요청을 클러스터 안의 서비스로 라우팅함  
인그레스 컨트롤러와 인그레스 리소스가 모두 필요함  
## The Ingress Resource
예제는 다음과 같음  
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx-example
  rules:
  - http:
      paths:
      - path: /testpath
        pathType: Prefix
        backend:
          service:
            name: test
            port:
              number: 80
```
### Ingress rules
rules에서 host가 없으면 모든 인바운드 HTTP 트래픽에 적용됨  
### DefaultBackend
rules가 없는 인그레스는 모든 트래픽을 하나의 default backend(`.spec.defaultBackend`)로 전송함  
default backend는 보통 인그레스 컨트롤러의 옵션으로 인그레스 리소스에는 명세하지 않으나, rules가 없으면 인그레스 리소스에 있어야 함  
만약 없으면 인그레스 컨트롤러에 따라 처리 방식이 다름  
### Path types
- ImplementationSpecific
    - Ingress Class에 따라 달라짐
- Exact
    - 대소문자 구분하고 URL이 정확하게 동일해야 함
- Prefix
    - 대소문자 구분하고 `/`로 구분된 URL로 매치함

## Ingress Class
인그레스를 어떤 인그레스 컨트롤러가 처리할지 지정  
예제는 다음과 같음  
```yaml
apiVersion: networking.k8s.io/v1
kind: IngressClass
metadata:
  name: external-lb
spec:
  controller: example.com/ingress-controller
  parameters:
    apiGroup: k8s.example.com
    kind: IngressParameters
    name: external-lb
```
### IngressClass scope
`.spec.parameters`가 있는데 scope가 없으면 범위는 클러스터임  
scope가 있고 Namespace이면 범위는 네임스페이스  
### Default IngressClass
인그레스 리소스에 `.spec.ingressClassName` 가 없을 때, default ingress class가 있으면 해당 ingress class로 지정됨  
예제는 다음과 같음  
```yaml
apiVersion: networking.k8s.io/v1
kind: IngressClass
metadata:
  labels:
    app.kubernetes.io/component: controller
  name: nginx-example
  annotations:
    ingressclass.kubernetes.io/is-default-class: "true"
spec:
  controller: k8s.io/ingress-nginx
``` 