# 17. Serverless

AWS의 Serverless 서비스와 이를 활용한 애플리케이션 아키텍처를 정리합니다.

Serverless는 서버가 존재하지 않는다는 의미가 아니라, 사용자가 직접 서버를 프로비저닝하거나 운영체제 및 인프라를 관리할 필요가 없다는 의미입니다.

## 📚 Contents

| File | Topic |
|---|---|
| `17-01-Serverless-and-Lambda.md` | Serverless 개념과 AWS Lambda |
| `17-02-Lambda-Advanced.md` | Lambda Networking, RDS Proxy, Edge, SnapStart |
| `17-03-DynamoDB.md` | DynamoDB 기본 구조와 Capacity Mode |
| `17-04-DynamoDB-Advanced.md` | DAX, Streams, Global Tables, TTL, Backup |
| `17-05-API-Gateway.md` | API Gateway와 Serverless API |
| `17-06-Step-Functions.md` | Serverless Workflow Orchestration |
| `17-07-Amazon-Cognito.md` | User Pool과 Identity Pool |

## 🏗️ Serverless Architecture

```text
User
 │
 ├── Login ─────────────→ Cognito
 │
 ↓
API Gateway
 │
 ↓
Lambda
 │
 ↓
DynamoDB
```

대표적인 AWS Serverless 서비스:

- AWS Lambda
- Amazon DynamoDB
- Amazon API Gateway
- Amazon Cognito
- AWS Step Functions
- Amazon S3
- Amazon SQS / SNS
- Amazon Data Firehose
- AWS Fargate

## 🎯 Exam Notes

- Serverless ≠ 서버가 없음
- Serverless = 서버 프로비저닝 및 관리 부담을 AWS가 담당
- Lambda = Serverless Compute
- DynamoDB = Serverless NoSQL Database
- API Gateway = API 요청의 진입점
- Step Functions = Workflow Orchestration
- Cognito = 웹/모바일 사용자 인증 및 AWS 접근 지원

## 💡 Practical Example

```text
사용자 로그인
      ↓
Cognito
      ↓
API Gateway
      ↓
Lambda
      ↓
DynamoDB
```

각 서비스가 인증, API, 코드 실행, 데이터 저장을 나누어 담당하기 때문에 서버를 직접 구축하지 않고 애플리케이션을 구성할 수 있습니다.

## 🇯🇵 日本語 Summary

サーバーレスでは、開発者がサーバーを直接プロビジョニング・管理する必要がありません。AWS Lambda、DynamoDB、API Gateway、Cognito、Step Functionsなどを組み合わせることで、スケーラブルなサーバーレスアプリケーションを構築できます。

## 🇺🇸 English Summary

Serverless allows developers to build applications without directly provisioning or managing servers. AWS services such as Lambda, DynamoDB, API Gateway, Cognito, and Step Functions can be combined to build scalable serverless architectures.

## 📚 Vocabulary

| Term | Meaning |
|---|---|
| Serverless | 서버를 직접 관리하지 않는 실행 모델 |
| Compute | 컴퓨팅 자원 |
| Event | 작업 실행을 유발하는 사건 |
| Workflow | 여러 작업으로 구성된 처리 흐름 |
| Authentication | 사용자가 누구인지 확인하는 인증 |
| Authorization | 사용자가 무엇을 할 수 있는지 결정하는 권한 부여 |

## 📝 Review Questions

### Q1. Serverless는 서버가 존재하지 않는다는 의미인가?

<details>
<summary>정답 보기</summary>

아니다. 실제 서버는 존재하지만 사용자가 직접 서버를 프로비저닝하거나 관리하지 않는다는 의미이다.

</details>

### Q2. Serverless API의 대표적인 세 서비스를 말해보자.

<details>
<summary>정답 보기</summary>

API Gateway → Lambda → DynamoDB

</details>