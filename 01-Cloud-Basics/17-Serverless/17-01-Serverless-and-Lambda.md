# 17-01. Serverless & AWS Lambda

## 1. Serverless

Serverless는 서버가 없는 것이 아니라 **개발자가 서버를 직접 프로비저닝하고 관리하지 않는 구조**이다.

```text
Traditional

Server 생성
   ↓
OS 관리
   ↓
Application 실행


Serverless

Code / Function 배포
   ↓
AWS가 Infrastructure 관리
```

---

# 2. AWS Lambda

AWS Lambda는 서버를 직접 관리하지 않고 **필요할 때 코드를 실행하는 Serverless Compute 서비스**이다.

```text
Event
  ↓
Lambda Function
  ↓
Code 실행
  ↓
Result
```

### EC2 vs Lambda

| EC2 | Lambda |
|---|---|
| Virtual Server | Function 실행 |
| 서버 관리 필요 | 서버 관리 불필요 |
| 지속적으로 실행 가능 | 필요할 때 실행 |
| 직접 Scaling 구성 | 자동 Scaling |
| CPU/RAM 중심 | 실행 시간 제한 존재 |

Lambda의 최대 일반 실행 시간은 **15분**이다.

---

# 3. Event-Driven Architecture

Lambda는 다양한 Event에 의해 실행될 수 있다.

예:

```text
S3에 이미지 업로드
        ↓
      Event
        ↓
      Lambda
        ↓
  Thumbnail 생성
        ↓
       S3
```

대표적인 Lambda Integration:

- API Gateway
- S3
- DynamoDB
- SQS
- SNS
- EventBridge
- Kinesis

---

# 4. Lambda Function

Lambda의 기본 구조:

```text
Event
  ↓
Lambda Function
  ↓
Handler
  ↓
Result
```

예:

```python
def lambda_handler(event, context):
    print(event)
    return event["key1"]
```

`event`에는 Lambda를 호출한 서비스가 전달한 데이터가 들어간다.

---

# 5. Execution Role

Lambda가 다른 AWS 서비스에 접근하려면 IAM 권한이 필요하다.

```text
Lambda
  │
  │ Execution Role
  ↓
S3 / DynamoDB / CloudWatch ...
```

예를 들어 Lambda가 CloudWatch Logs에 로그를 기록하려면 해당 작업을 허용하는 IAM 권한이 필요하다.

---

# 6. Lambda Limits

대표적인 제한:

| Resource | Limit |
|---|---:|
| Memory | 128 MB ~ 10,240 MB |
| Timeout | 최대 15분 |
| Environment Variables | 총 4 KB |
| `/tmp` | 512 MB ~ 10,240 MB |
| ZIP Uncompressed | 250 MB |
| Container Image | 최대 10 GB |

Lambda의 동시 실행 수는 **Concurrency**로 관리된다.

---

## 🎯 Exam Notes

```text
Lambda
= Serverless Compute
= Event Driven
= On-Demand
= Automatic Scaling
```

- 서버 및 OS 직접 관리 불필요
- Event가 Lambda 실행을 Trigger할 수 있음
- 최대 일반 실행 시간 15분
- AWS 서비스 접근 권한은 Execution Role로 부여
- 장시간 실행되는 일반 서버 애플리케이션에는 EC2/ECS 등이 더 적합

## 💡 Practical Example

매시간 작업을 실행해야 한다면:

```text
EventBridge Schedule
        ↓
      Lambda
        ↓
     Task 실행
```

별도의 EC2 서버에서 Cron을 계속 실행할 필요 없이 Serverless 방식으로 구성할 수 있다.

## 🇯🇵 日本語 Summary

AWS Lambdaは、サーバーを直接管理せずにコードを実行できるサーバーレスコンピューティングサービスです。イベント駆動で実行され、自動的にスケーリングします。S3、API Gateway、SQS、EventBridgeなど多くのAWSサービスと連携できます。

## 🇺🇸 English Summary

AWS Lambda is a serverless compute service that executes code in response to events. It automatically scales and integrates with services such as S3, API Gateway, SQS, DynamoDB, and EventBridge.

## 📚 Vocabulary

| Term | Meaning |
|---|---|
| Function | Lambda에서 실행되는 코드 단위 |
| Handler | Lambda 코드의 진입점 |
| Event | Lambda에 전달되는 호출 데이터 |
| Trigger | Lambda 실행을 유발하는 대상 |
| Execution Role | Lambda가 AWS 서비스에 접근할 때 사용하는 IAM Role |
| Concurrency | 동시에 실행되는 Lambda 실행 수 |

## 📝 Review Questions

### Q1. Lambda와 EC2의 가장 큰 차이는?

<details>
<summary>정답 보기</summary>

Lambda는 서버를 직접 관리하지 않고 Event에 따라 코드를 실행하는 Serverless Compute 서비스이다.

</details>

### Q2. Lambda가 S3에 접근하려면 무엇이 필요한가?

<details>
<summary>정답 보기</summary>

필요한 S3 권한이 포함된 Lambda Execution Role이 필요하다.

</details>