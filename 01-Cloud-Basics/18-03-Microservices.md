# Microservices Architecture

> Section 18 | AWS Solutions Architect Associate | Lecture study note

## 1. 개념과 요구사항
마이크로서비스는 하나의 거대한 애플리케이션을 독립적으로 개발·배포·확장 가능한 여러 서비스로 분리하는 아키텍처 방식이다. 마이크로서비스 자체가 서버리스를 뜻하지는 않는다. 각 서비스는 서로 다른 컴퓨팅·데이터베이스 기술을 선택할 수 있다.

## 2. 서로 다른 서비스의 조합
```text
Users / Clients
    ├── service1.example.com ── Route 53 ── ELB ── ECS ── DynamoDB
    ├── service2.example.com ── API Gateway ── Lambda ── ElastiCache
    └── service3.example.com ── Route 53 ── ELB ── EC2 ASG ── RDS
```

**ECS**는 컨테이너 기반 서비스에, **Lambda**는 이벤트 중심의 서버리스 서비스에, **EC2 ASG**는 서버 인프라를 직접 제어할 필요가 있는 서비스에 적합할 수 있다. 하나의 시스템에 이들을 혼합해도 된다.

## 3. 서비스 간 통신
**동기식(Synchronous)** 통신은 호출자가 응답을 기다린다. HTTP/REST를 사용하며 API Gateway 또는 Load Balancer가 진입점이 될 수 있다. 예: 주문 서비스가 결제 서비스에 승인 요청을 보낸 뒤 결과를 기다린다.

**비동기식(Asynchronous)** 통신은 호출자가 즉시 응답을 기다리지 않고 메시지·이벤트를 전달한다. SQS, SNS, Kinesis, S3 이벤트를 사용할 수 있다. 예: 주문 생성 후 SQS에 배송 작업 메시지를 넣고 배송 서비스가 나중에 처리한다.

## 4. 복잡성과 트레이드오프
서비스를 분리하면 개별 팀의 배포·확장이 쉬워질 수 있지만 서비스별 운영 비용, 네트워크 통신, 여러 API 버전 관리, 장애 전파, 클라이언트 통합 복잡성이 증가한다. API Gateway와 Lambda의 자동 확장·사용량 기반 과금, API 정의 기반 SDK 생성(OpenAPI/Swagger)은 일부 운영 부담을 줄일 수 있지만 분산 시스템의 복잡성까지 제거하지는 않는다.

## 🎯 Exam Notes

- Microservices ≠ Serverless.
- 서비스마다 ECS / Lambda / EC2 등 다른 구현 가능.
- 동기식: REST API, API Gateway, ELB.
- 비동기식: SQS, SNS, Kinesis, S3 이벤트.
- 이점: 독립 개발·배포·확장. 비용: 운영·통합·버전 복잡성.

## 💡 Practical Example

쇼핑몰에서 주문 API는 ECS, 알림 서비스는 Lambda, 정산 시스템은 EC2로 운영할 수 있다. 주문 완료 이벤트는 SQS를 통해 알림 서비스에 비동기 전달한다.

## 🇯🇵 日本語 Summary

マイクロサービスはアプリケーションを独立したサービスに分割する設計手法であり、サーバーレスと同義ではない。各サービスはECS、Lambda、EC2など異なる技術を使える。同期通信にはREST API、非同期通信にはSQSやSNSなどを利用する。

## 🇺🇸 English Summary

Microservices split an application into independently deployable services. Each service may use ECS, Lambda, or EC2. Synchronous REST calls and asynchronous messaging serve different communication needs, with trade-offs in operational complexity.

## 📚 Vocabulary

Microservice=독립 서비스, Synchronous=동기식, Asynchronous=비동기식, Coupling=결합도, OpenAPI=API 명세

## 📝 Review Questions

**Q. 마이크로서비스를 구현하려면 Lambda가 필수인가?**

<details>
<summary>정답 보기</summary>

아니다. ECS, EC2, Lambda 등 요구사항에 맞는 기술을 각 서비스별로 선택한다.

</details>
