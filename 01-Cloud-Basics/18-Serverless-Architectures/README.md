# 18. Serverless Architectures

## Overview

이번 섹션에서는 AWS 서비스를 조합하여 실제 비즈니스 요구사항을 해결하는 Solution Architecture를 학습한다.

핵심은 개별 서비스의 기능을 암기하는 것이 아니라 다음 과정을 이해하는 것이다.

```text
Business Requirements
        ↓
Identify Architecture Challenges
        ↓
Choose AWS Services
        ↓
Design Data & Request Flows
        ↓
Evaluate Scalability, Security & Cost
```

## Lectures

| No. | Architecture | Main Concepts |
|---|---|---|
| 18-01 | MyTodoList | API Gateway, Lambda, DynamoDB, DAX, Cognito, S3 |
| 18-02 | MyBlog.com | CloudFront, OAC, DynamoDB Global Tables, Streams, SES, S3 Events |
| 18-03 | Microservices | ECS, EC2, Lambda, Synchronous / Asynchronous Communication |
| 18-04 | Software Updates Offloading | CloudFront, ELB, EC2 Auto Scaling, EFS |

## Core Architecture Patterns

### 1. Serverless Backend

```text
Client
  ↓
API Gateway
  ↓
Lambda
  ↓
DynamoDB
```

서버를 직접 관리하지 않고 API 요청과 데이터 처리를 수행한다.

### 2. Authentication & Authorization

```text
User
  ↓
Cognito User Pool
  ↓
JWT Token
  ↓
API Gateway
  ↓
Lambda
```

사용자 인증과 API 접근 권한을 분리한다.

AWS 리소스에 직접 접근해야 한다면 Cognito Identity Pool을 통해 임시 AWS 자격 증명을 제공할 수 있다.

### 3. Event-Driven Architecture

```text
Event Source
     ↓
Event / Stream / Queue
     ↓
Lambda
     ↓
Business Action
```

이벤트 발생 시 필요한 작업을 자동으로 실행한다.

### 4. Caching

```text
Client
  ↓
CloudFront / API Gateway Cache
  ↓
Application
  ↓
DAX
  ↓
DynamoDB
```

캐시 계층마다 목적과 캐싱 대상이 다르다.

- CloudFront: 웹 콘텐츠와 정적 파일 캐싱
- API Gateway Cache: API 응답 캐싱
- DAX: DynamoDB 읽기 결과 캐싱

모든 캐시를 무조건 적용하는 것이 아니라 실제 트래픽, 비용, 데이터 일관성 요구사항에 따라 선택한다.

### 5. Microservices

```text
Service A → REST API → Service B

Service A → SQS → Service B
```

독립적인 서비스 간 통신을 동기식 또는 비동기식으로 설계한다.

## Architecture Design Principles

1. 서비스 선택보다 요구사항 분석을 먼저 수행한다.
2. Stateless 구조와 Managed Service를 활용해 확장성을 확보한다.
3. 인증과 AWS 리소스 접근 권한을 구분한다.
4. 반복적인 읽기 요청은 적절한 캐싱으로 최적화한다.
5. 독립적인 작업은 비동기 이벤트로 분리할 수 있다.
6. 고가용성, 보안, 비용 사이의 Trade-off를 고려한다.

## Related Architecture Projects

- [04-MyTodoList](../../Architecture-Projects/04-MyTodoList/)
- [05-MyBlog](../../Architecture-Projects/05-MyBlog/)
- [06-Microservices-Architecture](../../Architecture-Projects/06-Microservices-Architecture/)
- [07-Software-Updates-Offloading](../../Architecture-Projects/07-Software-Updates-Offloading/)

## 🇯🇵 日本語 Summary

このセクションでは、AWS の各サービスを組み合わせて実際の要件を満たすアーキテクチャ設計を学習した。サーバーレス API、認証と認可、イベント駆動型処理、キャッシュ、マイクロサービス、CDN による負荷軽減が主なテーマである。

## 🇺🇸 English Summary

This section explores solution architecture patterns using AWS services. Key topics include serverless APIs, authentication and authorization, event-driven processing, caching, microservices, and content delivery optimization. The focus is on selecting appropriate services based on requirements and trade-offs.