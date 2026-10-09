# 04. MyTodoList - Serverless Mobile Application

## Project Status

**Architecture Design / Not Yet Deployed**

AWS SAA 학습 과정에서 다룬 요구사항을 기반으로 작성한 설계 프로젝트다.

실제 AWS 배포 및 성능 검증은 아직 수행하지 않았다.

## 1. Project Overview

### Theme

**Serverless Mobile Backend with Authentication and Fine-Grained AWS Access**

모바일 Todo 애플리케이션의 백엔드를 서버리스 방식으로 설계한다.

사용자 인증, 데이터 저장, 사용자별 파일 접근, 읽기 성능 최적화를 단계적으로 해결한다.

## 2. Requirements

### Functional

- HTTPS REST API 제공
- 사용자 회원가입 및 로그인
- Todo 생성, 조회, 수정, 삭제
- 사용자별 Todo 데이터 분리
- 사용자별 S3 파일 저장 공간 제공

### Non-Functional

- Serverless
- Automatic Scaling
- Secure Authentication
- Fine-Grained Authorization
- Read Performance Optimization
- Low Operational Overhead

## 3. Architecture Evolution

### Stage 1. Serverless Backend

```text
Mobile App
    |
    v
API Gateway
    |
    v
Lambda
    |
    v
DynamoDB
```

### Problem

API는 구현할 수 있지만 사용자 인증과 접근 제어가 아직 없다.

### Design Decision

- API Gateway: HTTPS API Entry Point
- Lambda: Business Logic
- DynamoDB: Serverless Data Storage

### Stage 2. Add Authentication

```text
Mobile App
    |
    +----> Cognito User Pool
    |             |
    |          JWT Token
    |             |
    v             v
API Gateway <-----+
    |
    v
Lambda
    |
    v
DynamoDB
```

### Problem

사용자 로그인은 해결했지만 사용자에게 자신의 S3 파일에 직접 접근할 권한을 제공해야 한다.

### Stage 3. Add AWS Resource Authorization

```text
Mobile App
    |
    +----> Cognito User Pool
    |              |
    |              v
    +----> Cognito Identity Pool
    |              |
    |              v
    |        Temporary AWS Credentials
    |              |
    |              v
    |          Amazon S3
    |
    v
API Gateway
    |
    v
Lambda
    |
    v
DynamoDB
```

### Design Decision

Cognito User Pool로 사용자를 인증하고, Identity Pool을 통해 제한된 IAM Role에 기반한 임시 자격 증명을 제공한다.

S3 접근은 사용자별 Prefix로 제한한다.

### Stage 4. Optimize Read Performance

```text
Mobile App
    |
    v
API Gateway
    |
    v
Lambda
    |
    v
DAX
    |
    v
DynamoDB
```

읽기 요청이 많은 환경에서는 DAX를 고려한다.

API Gateway Response Cache는 별도의 선택적 최적화다.

## 4. Final Architecture

```text
                      Mobile App
                     /          \
                    /            \
                   v              v
          Cognito User Pool   Cognito Identity Pool
                   |              |
                   | JWT          | IAM Temporary Credentials
                   v              v
              API Gateway      Amazon S3
                   |
                   v
                 Lambda
                   |
                   v
                  DAX
                   |
                   v
                DynamoDB
```

## 5. Architecture Decisions

| Requirement | Selected Service | Reason |
|---|---|---|
| HTTPS API | API Gateway | Managed API Endpoint |
| Business Logic | Lambda | Serverless Execution |
| Todo Storage | DynamoDB | Managed NoSQL Database |
| User Login | Cognito User Pool | Authentication and JWT |
| Direct S3 Access | Cognito Identity Pool | Temporary AWS Credentials |
| File Storage | Amazon S3 | Durable Object Storage |
| Read Optimization | DAX | DynamoDB Read Cache |

## 6. Security Design

### Authentication

API Gateway는 Cognito User Pool에서 발급한 토큰을 검증하도록 구성한다.

### Authorization

Lambda는 토큰에서 확인한 사용자 정보를 바탕으로 Todo 데이터 접근을 제한한다.

### S3 Access

Identity Pool과 IAM 정책으로 사용자별 S3 Prefix에 대한 접근을 제한한다.

### Least Privilege

Lambda Execution Role에는 필요한 DynamoDB 및 AWS API 권한만 부여한다.

## 7. Trade-offs

### Advantages

- 서버 운영 부담 감소
- 자동 확장
- 사용자 인증 관리 단순화
- AWS 리소스 접근 권한 분리
- 읽기 캐시 적용 가능

### Disadvantages

- Cognito 구성 복잡성
- DAX 클러스터 추가 비용
- 캐시 데이터 일관성 관리
- DynamoDB Access Pattern 기반 설계 필요
- IAM 정책 오류 시 사용자 데이터 노출 위험

## 8. Alternatives

| Selected | Alternative | Consideration |
|---|---|---|
| DynamoDB | Amazon RDS | Relational Query Requirements |
| Lambda | ECS Fargate | Runtime and Workload Characteristics |
| Direct S3 Access | Presigned URL | Simpler Temporary Object Access |
| DAX | No Cache | Lower Cost for Small Workloads |
| API Gateway | ALB | Different API Management Requirements |

## 9. Future Implementation Plan

- [ ] AWS Budget 및 비용 알림 설정
- [ ] Cognito User Pool 생성
- [ ] DynamoDB 테이블 설계
- [ ] Lambda CRUD API 구현
- [ ] API Gateway 연동
- [ ] Cognito JWT 인증 테스트
- [ ] S3 사용자별 접근 정책 구현
- [ ] 사용자 간 데이터 접근 차단 테스트
- [ ] CloudWatch 로그 확인
- [ ] DAX 도입 필요성 및 비용 분석
- [ ] 테스트 리소스 정리

## 10. Validation Criteria

- 인증되지 않은 API 요청이 거부되는가?
- 사용자 A가 사용자 B의 Todo를 조회할 수 없는가?
- 사용자 A가 사용자 B의 S3 Prefix에 접근할 수 없는가?
- CRUD API가 정상 작동하는가?
- 캐시 도입 전후 응답 시간에 차이가 있는가?

## 🇯🇵 日本語 Summary

本プロジェクトは、Cognito、API Gateway、Lambda、DynamoDB、S3 を組み合わせたサーバーレスモバイルアプリケーションの設計である。ユーザー認証と AWS リソースへのアクセス権限を分離し、セキュリティと拡張性を考慮した構成を検討した。

## 🇺🇸 English Summary

This architecture design project explores a serverless mobile application using Cognito, API Gateway, Lambda, DynamoDB, and S3. It focuses on separating user authentication from AWS resource authorization while considering scalability, security, caching, and operational trade-offs.

## References

- [Amazon API Gateway](https://docs.aws.amazon.com/apigateway/)
- [AWS Lambda](https://docs.aws.amazon.com/lambda/)
- [Amazon Cognito](https://docs.aws.amazon.com/cognito/)
- [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/)
- [Amazon S3](https://docs.aws.amazon.com/s3/)