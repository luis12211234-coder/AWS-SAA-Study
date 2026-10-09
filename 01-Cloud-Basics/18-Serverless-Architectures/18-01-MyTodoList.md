# 18-01. MyTodoList Mobile Application

## 1. Requirements

모바일 Todo 애플리케이션을 AWS에 구축한다.

### Functional Requirements

- 모바일 클라이언트에서 HTTPS REST API 호출
- 사용자 회원가입 및 로그인
- 사용자별 Todo 데이터 저장
- 사용자별 파일 저장 공간 제공
- 사용자가 자신의 파일에 직접 접근
- 읽기 요청이 많은 데이터베이스 처리

### Non-Functional Requirements

- Serverless Architecture
- Automatic Scaling
- Secure Authentication
- Fine-Grained Authorization
- Low Operational Overhead
- Read Performance Optimization

## 2. Initial Serverless Architecture

```text
Mobile Application
        |
        | HTTPS REST API
        v
Amazon API Gateway
        |
        v
     AWS Lambda
        |
        v
Amazon DynamoDB
```

### API Gateway

클라이언트의 HTTP 요청을 수신하고 백엔드 서비스로 전달한다.

주요 역할:

- REST API Endpoint 제공
- 요청 라우팅
- 인증 및 인가 연동
- Throttling
- 선택적 Response Caching

### AWS Lambda

실제 비즈니스 로직을 처리한다.

예시:

```text
GET    /todos
POST   /todos
PUT    /todos/{id}
DELETE /todos/{id}
```

Lambda는 요청에 따라 실행되므로 별도의 EC2 서버를 운영할 필요가 없다.

### Amazon DynamoDB

Todo 데이터를 저장하는 Serverless NoSQL Database다.

예시 데이터:

```json
{
  "userId": "user-123",
  "todoId": "todo-001",
  "title": "Study AWS SAA",
  "completed": false
}
```

사용자별 데이터 조회가 빈번하다면 `userId`를 Partition Key로, `todoId`를 Sort Key로 설계하는 방안을 고려할 수 있다.

단, 실제 Key Design은 Access Pattern에 따라 결정해야 한다.

## 3. User Authentication

사용자 인증을 위해 Amazon Cognito User Pool을 도입한다.

```text
Mobile App
    |
    | Sign Up / Sign In
    v
Cognito User Pool
    |
    | JWT Token
    v
Mobile App
    |
    | Authorization Header
    v
API Gateway
    |
    | Token Validation
    v
Lambda
```

### Cognito User Pool

주요 기능:

- 사용자 회원가입
- 로그인
- 사용자 관리
- JWT Token 발급
- API Gateway Authorizer 연동

핵심 질문:

> Who is the user?

User Pool은 사용자 신원을 확인한다.

## 4. Direct S3 Access

요구사항 중 하나는 사용자가 자신의 S3 저장 공간에 직접 접근하는 것이다.

모든 파일을 Lambda를 통해 전달할 수도 있지만, 사용자에게 제한된 AWS 권한을 제공하는 구조도 가능하다.

여기서는 Cognito Identity Pool을 사용한다.

```text
Mobile Application
        |
        v
Cognito User Pool
        |
        | Authentication Token
        v
Cognito Identity Pool
        |
        | Temporary AWS Credentials
        v
Amazon S3
        |
        v
User-Specific Prefix
```

### Cognito Identity Pool

인증된 사용자에게 제한된 IAM Role에 기반한 임시 AWS 자격 증명을 제공할 수 있다.

핵심 질문:

> What AWS resources can this identity access?

### Example Authorization Design

```text
S3 Bucket
└── users/
    ├── identity-A/
    ├── identity-B/
    └── identity-C/
```

사용자별 S3 Prefix를 구분하고 IAM 정책을 통해 자신의 경로에만 접근하도록 제한한다.

주의:

- User Pool은 사용자 인증 담당
- Identity Pool은 AWS 임시 자격 증명 제공
- 실제 S3 접근 제한은 IAM Policy로 강제
- 클라이언트가 전달한 userId만 믿어서는 안 됨

## 5. DynamoDB Read Optimization

Todo 데이터를 반복해서 읽는 요청이 많다면 DynamoDB Accelerator(DAX)를 고려할 수 있다.

```text
API Gateway
     |
     v
   Lambda
     |
     v
Amazon DAX
     |
     v
DynamoDB
```

### DAX Cache Hit

```text
Lambda
  ↓
DAX
  ↓
Cached Data
```

DynamoDB까지 읽기 요청이 전달되지 않는다.

### DAX Cache Miss

```text
Lambda
  ↓
DAX
  ↓
DynamoDB
  ↓
DAX Cache Update
  ↓
Lambda
```

### Important Considerations

- DAX는 DynamoDB용 In-Memory Cache다.
- 반복적인 읽기 요청의 지연을 줄일 수 있다.
- DAX는 별도의 클러스터 비용이 발생한다.
- DAX의 기본 Item Cache TTL은 5분이다.
- 강한 일관성이 필요한 읽기는 별도로 고려해야 한다.
- DAX를 도입하려면 애플리케이션에서 DAX Client를 사용해야 한다.

## 6. API Gateway Response Caching

API Gateway에서도 응답 캐싱을 적용할 수 있다.

```text
Client
  |
  v
API Gateway Cache
  |
  | Cache Miss
  v
Lambda
  |
  v
DAX
  |
  v
DynamoDB
```

### API Gateway Cache Hit

API Gateway가 응답을 반환하므로 Lambda 호출 자체가 생략된다.

### DAX Cache Hit

Lambda는 실행되지만 DynamoDB 읽기 요청이 줄어든다.

### Comparison

| Layer | Cached Data | Main Benefit |
|---|---|---|
| API Gateway Cache | API Response | Reduce Backend Invocations |
| DAX | DynamoDB Read Results | Reduce Database Read Latency |

사용자별 Todo API에 캐시를 적용한다면 사용자별 캐시 분리가 필수다.

잘못된 Cache Key 설계는 다른 사용자의 데이터 노출로 이어질 수 있다.

## 7. Final Architecture

```text
                Mobile Application
                   /         \
                  /           \
                 v             v
       Cognito User Pool    Cognito Identity Pool
           |                        |
           | JWT                    | Temporary Credentials
           v                        v
       API Gateway              Amazon S3
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

Identity Pool은 User Pool 인증 결과를 이용하도록 구성할 수 있다.

API Gateway Response Cache는 필요할 때 추가하는 선택적 계층이다.

## 🎯 Exam Notes

- Serverless REST API: API Gateway + Lambda
- Serverless NoSQL Database: DynamoDB
- User Authentication: Cognito User Pool
- Temporary AWS Credentials: Cognito Identity Pool
- Direct User Access to S3: Identity Pool + IAM
- DynamoDB Read Caching: DAX
- API Response Caching: API Gateway Cache
- User-Specific Data: Fine-Grained Authorization

## 💡 Practical Example

사용자가 Todo 목록을 조회한다.

1. 사용자가 Cognito User Pool에 로그인한다.
2. 클라이언트가 JWT를 포함하여 API Gateway에 요청한다.
3. API Gateway가 토큰을 검증한다.
4. Lambda가 사용자 정보를 확인한다.
5. Lambda가 DAX를 통해 Todo 데이터를 조회한다.
6. 조회 결과가 사용자에게 반환된다.

사용자가 첨부 파일을 업로드할 때는 Identity Pool을 통해 발급받은 임시 자격 증명으로 허용된 S3 경로에 직접 업로드할 수 있다.

## 🇯🇵 日本語 Summary

MyTodoList は、API Gateway、Lambda、DynamoDB を利用したサーバーレスモバイルアプリケーションの設計例である。Cognito User Pool はユーザー認証を担当し、Identity Pool は S3 などの AWS リソースにアクセスするための一時的な認証情報を提供する。読み取り負荷が高い場合は DAX によるキャッシュを検討できる。

## 🇺🇸 English Summary

MyTodoList demonstrates a serverless mobile backend built with API Gateway, Lambda, and DynamoDB. Cognito User Pools handle user authentication, while Identity Pools provide temporary AWS credentials for controlled access to resources such as S3. DAX can improve performance for read-heavy DynamoDB workloads.

## 📚 Vocabulary

| Term | Meaning |
|---|---|
| Authentication | 인증, 사용자 신원 확인 |
| Authorization | 인가, 접근 권한 확인 |
| Temporary Credentials | 임시 자격 증명 |
| Cache Hit | 캐시에 요청 데이터가 존재하는 상태 |
| Cache Miss | 캐시에 요청 데이터가 없는 상태 |
| Read-Heavy Workload | 읽기 요청이 많은 작업 |
| Fine-Grained Access | 세분화된 접근 제어 |
| Serverless Backend | 서버 운영 부담을 줄인 백엔드 |

## 📝 Review Questions

### Q1. User Pool과 Identity Pool의 차이는?

<details>
<summary>정답 보기</summary>

User Pool은 사용자 인증과 토큰 발급을 담당한다.

Identity Pool은 사용자에게 IAM Role에 기반한 임시 AWS 자격 증명을 제공한다.

</details>

### Q2. DAX와 API Gateway Cache의 차이는?

<details>
<summary>정답 보기</summary>

DAX는 DynamoDB 읽기 결과를 캐싱한다.

API Gateway Cache는 API 응답을 캐싱하여 백엔드 호출 자체를 줄일 수 있다.

</details>

### Q3. 사용자가 자신의 S3 폴더에만 접근하도록 하려면?

<details>
<summary>정답 보기</summary>

Cognito Identity Pool과 IAM 정책을 이용하여 사용자별 S3 Prefix 접근 권한을 제한한다.

</details>