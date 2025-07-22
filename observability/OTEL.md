## Opentelemetry란?
- Observability framework
    - observability란 시스템 밖에서 시스템 안을 몰라도 질문할 수 있게 해 시스템을 이해할 수 있게 되는 것
    - 시스템이 instrumented(signal을 찍음) 필요
## distributed tracing
- logs
    - timestamped message
    - span에 포함됐을 때 더 유용함
- spans
    - span이란 동작의 단위
    - 이름, 시간 관련 데이터, log message, attribute 등을 포함
- traces
    - 요청에 의해 진행된 경로를 기록
    - 하나 이상의 span으로 구성됨
    - 첫 span을 root span이라 함
## context propagation
- 생성된 위치가 달라도 signal을 추적하는 traces를 생성
- context
    - 연관된 signal끼리 알아볼 수 있게 하는 정보를 담은 object
- propagation
    - 서비스들 사이에서 context를 직렬화/역직렬화를 통해 이동시키는 메커니즘
## Instrumentation
- code-based
    - OpenTelemetry API랑 SDK를 import 하고
    - 잘 설정하고 수집하고 전송(in-process or through OTEL Collector)
        - 후자의 경우 OTLP(OpenTelemetry Protocol) 사용
- zero-code
    - 소스코드에 추가 안하는 경우
    - agent가 로직을 injection 함
    - request/response, db call, mqueue call 등이 instrumented 됨
- libraries
    - native instrumentation
        - 개발자가 OpenTelemetry API를 사용
        - 추가적인 dependency 필요 없음
    - instrumentation libraries
        - OpenTelemetry에서 공식 제공하는 라이브러리
        - hook이나 monkey-patching 방식
## Components
### Specification
- API: tracing, metrics, log 를 생성하고 연관시키기 위해 데이터 타입과 operation을 정의 
- SDK: API 구현을 위해 language-specific한 설정, 데이터 처리, exporting concept 등 요구사항들을 정의 
- Data: OLTP와 vendor에 상관 없는 semantic convention을 정의 
### Collector
- OTEL Collector는 vendor-agnostic한 프록시로 telemetry data를 수집, 처리, 내보냄
### Language-specific API & SDK implementations
- Instrumentation libraries
- Exporters
    - telemetry data를 Collector에게 전달
    - telemetry를 시각화하기 위해서는 backend(Jaeger, Zipkin, Prometheus 등) 에 export
- Zero-Code instrumentation
- Resource detectors
    - OTEL_RESOURCE_ATTRIBUTES 라는 환경변수를 통해 제공함
- Cross-service propagators
    - propagation이란 data를 서비스나 프로세스간 전파하는 메카니즘
    - 대부분 instrumental libraries에 의해 일어나지만 propagator를 커스텀하게 사용할 수도 있음
- Samplers
    - Sampling이란 시스템에 의해 생성되는 traces의 양을 제한하는 것
