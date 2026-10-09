# 18-03. Microservices Architecture

## 1. Overview

Microservices Architecture는 하나의 큰 애플리케이션을 여러 독립적인 서비스로 분리하는 설계 방식이다.

각 서비스는 특정 비즈니스 기능을 담당하며 독립적으로 개발, 배포, 확장할 수 있다.

예시:

```text
E-Commerce Application
         |
         +-- User Service
         |
         +-- Product Service
         |
         +-- Order Service
         |
         +-- Payment Service
```

Microservices는 Serverless와 같은 의미가 아니다.

각 서비스는 EC2, ECS, Lambda 등 다양한 컴퓨팅 환경에서 구현할 수 있다.

## 2. Requirements

- 여러 서비스의 독립적인 개발 및 배포
- 서비스별 기술 선택의 자유
- 서비스별 확장성
- 서비스 간 API 통신
- 동기식 및 비동기식 통신 지원

## 3. Different Architectures per Service

각 마이크로서비스는 서로 다른 AWS 서비스를 사용할 수 있다.

```text
                   Users
                     |
                 Route 53
                     |
       +-------------+-------------+
       |             |             |
       v             v             v
   Service 1     Service 2     Service 3
       |             |             |
       v             v             v
      ELB        API Gateway      ELB
       |             |             |
       v             v             v
      ECS          Lambda       EC2 ASG
       |             |             |
       v             v             v
   DynamoDB      ElastiCache      RDS
```

### Service 1: ECS + DynamoDB

```text
service1.example.com
        |
        v
       ELB
        |
        v
    ECS Tasks
        |
        v
    DynamoDB
```

컨테이너 기반 서비스를 운영한다.

### Service 2: API Gateway + Lambda

```text
service2.example.com
        |
        v
   API Gateway
        |
        v
      Lambda
        |
        v
   ElastiCache
```

서버리스 API를 구현한다.

ElastiCache는 반복적인 데이터 접근의 지연을 줄이는 캐시 계층으로 사용할 수 있다.

### Service 3: EC2 + RDS

```text
service3.example.com
        |
        v
       ELB
        |
        v
    EC2 Auto Scaling
        |
        v
       RDS
```

전통적인 서버 기반 웹 애플리케이션을 구성한다.

## 4. Synchronous Communication

동기식 통신은 호출자가 다른 서비스의 응답을 기다리는 방식이다.

```text
Order Service
      |
      | HTTP Request
      v
Payment Service
      |
      | HTTP Response
      v
Order Service
```

AWS에서 활용할 수 있는 서비스:

- API Gateway
- Elastic Load Balancing

### Advantages

- 요청과 응답 흐름이 직관적이다.
- 즉시 처리 결과가 필요한 작업에 적합하다.

### Trade-offs

- 호출 대상 서비스의 응답 지연이 전파될 수 있다.
- 서비스 장애가 연쇄적으로 영향을 줄 수 있다.
- Timeout과 Retry 설계가 중요하다.

## 5. Asynchronous Communication

비동기식 통신은 호출자가 후속 작업 완료를 기다리지 않는 방식이다.

```text
Order Service
      |
      v
   Amazon SQS
      |
      v
Shipping Service
```

AWS에서 활용할 수 있는 서비스:

- Amazon SQS
- Amazon SNS
- Amazon Kinesis
- S3 Event Notifications
- AWS Lambda Triggers

### Example

주문이 완료되면 배송 준비 작업을 수행한다.

```text
Order Created
      |
      v
Order Service
      |
      v
SQS Queue
      |
      v
Shipping Worker
      |
      v
Prepare Shipment
```

### Advantages

- 서비스 간 결합도를 낮출 수 있다.
- 요청량 급증을 Queue로 완충할 수 있다.
- 작업 처리 속도를 독립적으로 조절할 수 있다.

### Trade-offs

- 즉시 결과를 받지 못할 수 있다.
- 재시도와 중복 메시지 처리가 필요하다.
- Eventual Consistency를 고려해야 한다.
- 메시지 추적과 장애 분석이 복잡해질 수 있다.

## 6. Microservices Challenges

### Repeated Infrastructure Overhead

서비스가 늘어날수록 배포, 보안, 모니터링, 네트워크 설정 등의 관리 작업이 증가한다.

### Server Density & Utilization

각 서비스를 독립적인 서버로 실행하면 리소스 활용률이 낮아질 수 있다.

### Multiple Service Versions

여러 서비스가 서로 다른 버전으로 실행되면 API 호환성 관리가 어려워진다.

### Client Integration Complexity

클라이언트가 여러 서비스와 직접 통신하면 연동 코드가 복잡해질 수 있다.

## 7. How Serverless Helps

### API Gateway + Lambda

- 요청량에 따른 자동 확장
- 서버 프로비저닝 부담 감소
- 사용량 기반 과금
- API 관리 기능 제공

### API Definition and SDK

OpenAPI Specification을 사용해 API 계약을 문서화하고 클라이언트 SDK 생성에 활용할 수 있다.

강의에서 언급한 Swagger는 OpenAPI 생태계와 관련된 도구 및 명칭이다.

서버리스가 모든 Microservices 문제를 해결하는 것은 아니다.

서비스 간 의존성, API 버전 관리, 데이터 일관성, 분산 시스템의 복잡성은 여전히 존재한다.

## 🎯 Exam Notes

- Microservices: Independently Deployable Services
- Container-Based Service: Amazon ECS
- Kubernetes-Based Service: Amazon EKS
- Serverless API: API Gateway + Lambda
- Synchronous Communication: API Gateway / ELB
- Asynchronous Communication: SQS / SNS / Kinesis / Lambda Triggers
- Loose Coupling: Message Queue / Event-Driven Design
- Independent Scaling: Service-Specific Scaling

## 💡 Practical Example

전자상거래 서비스에서 주문과 배송 기능을 분리한다.

사용자가 주문을 생성하면 Order Service는 주문 정보를 저장하고 SQS에 메시지를 전송한다.

Shipping Service는 메시지를 소비하여 배송 작업을 수행한다.

배송 서비스가 일시적으로 느려지더라도 주문 서비스가 배송 완료를 기다릴 필요는 없다.

## 🇯🇵 日本語 Summary

マイクロサービスアーキテクチャは、アプリケーションを独立した小さなサービスに分割する設計手法である。各サービスは ECS、EC2、Lambda など異なる技術で構築できる。サービス間通信には API Gateway や Load Balancer を利用した同期通信と、SQS、SNS、Kinesis などを利用した非同期通信がある。

## 🇺🇸 English Summary

Microservices architecture divides an application into independently deployable services. Each service can use different technologies, including ECS, EC2, and Lambda. Services communicate synchronously through APIs or asynchronously through queues and event streams. Serverless technologies can reduce infrastructure overhead, but distributed-system complexity remains.

## 📚 Vocabulary

| Term | Meaning |
|---|---|
| Microservices | 마이크로서비스 |
| Monolith | 단일 애플리케이션 구조 |
| Synchronous | 동기식 |
| Asynchronous | 비동기식 |
| Loose Coupling | 느슨한 결합 |
| Service Boundary | 서비스 경계 |
| Independent Deployment | 독립 배포 |
| API Contract | API 인터페이스 계약 |
| Server Utilization | 서버 자원 활용률 |
| Eventual Consistency | 최종적 일관성 |

## 📝 Review Questions

### Q1. Microservices와 Serverless는 같은 개념인가?

<details>
<summary>정답 보기</summary>

아니다.

Microservices는 애플리케이션을 독립적인 서비스로 분리하는 설계 방식이다.

Serverless는 서버 인프라 관리 부담을 줄이는 실행 및 운영 모델이다.

</details>

### Q2. 즉시 응답이 필요한 서비스 간 통신에는 무엇을 사용할 수 있는가?

<details>
<summary>정답 보기</summary>

API Gateway 또는 Load Balancer를 통한 동기식 HTTP 통신을 사용할 수 있다.

</details>

### Q3. 서비스 간 결합도를 낮추고 작업을 비동기적으로 처리하려면?

<details>
<summary>정답 보기</summary>

SQS, SNS, Kinesis 등의 메시징 및 이벤트 서비스를 고려한다.

</details>