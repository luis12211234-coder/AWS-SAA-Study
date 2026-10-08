# 17-05. Amazon API Gateway

## 1. API Gateway

API Gateway는 애플리케이션의 **API 요청을 받는 진입점**이다.

대표적인 Serverless API:

```text
Client
  ↓
API Gateway
  ↓
Lambda
  ↓
DynamoDB
```

사용자가 버튼을 누르면 Frontend Code가 HTTP 요청을 전송한다.

```text
사용자 클릭
   ↓
GET /products/100
   ↓
API Gateway
   ↓
Lambda
```

사용자가 직접 Lambda를 의식해서 호출하는 것이 아니다.

---

# 2. API Gateway Features

API Gateway는 API에 필요한 여러 관리 기능을 제공한다.

- Authentication / Authorization
- API Keys
- Throttling
- API Versioning
- dev / test / prod Stage
- Request / Response Transformation
- Validation
- OpenAPI Integration
- API Response Cache
- WebSocket

---

# 3. Integrations

API Gateway Backend는 Lambda만 가능한 것이 아니다.

```text
API Gateway
 ├── Lambda
 ├── HTTP Backend
 └── AWS Service
```

예:

```text
Client
 ↓
API Gateway
 ↓
SQS / Kinesis / Step Functions
```

---

# 4. Lambda Proxy Integration

Lambda Proxy Integration에서는 API Gateway가 HTTP 요청 정보를 Lambda의 Event로 전달하고, Lambda가 HTTP Response 구조를 반환한다.

```text
Client
 │ GET /houses
 ↓
API Gateway
 │
 │ Request → Event
 ↓
Lambda
 │
 │ statusCode
 │ headers
 │ body
 ↓
API Gateway
 ↓
Client
```

---

# 5. Resource & Method

예:

```text
GET /houses
```

```text
/houses = Resource
GET     = Method
```

Method와 Backend Integration을 연결한다.

---

# 6. Stage

Stage는 **API의 배포 환경**이다.

```text
API
 ↓ Deploy
 ├── dev
 ├── test
 └── prod
```

`dev`라는 이름 자체가 AWS의 특별한 예약어인 것은 아니다.

예:

```text
https://example.execute-api.../dev/houses
                                ↑
                              Stage
```

배포는 반드시 실제 고객에게 공개한다는 의미가 아니라 해당 환경에서 API를 실행 가능한 상태로 만드는 것이다.

---

# 7. Endpoint Types

## Edge-Optimized

글로벌 Client에 적합하다.

```text
Global Users
     ↓
CloudFront Edge
     ↓
API Gateway
```

API Gateway 자체는 하나의 Region에 존재한다.

## Regional

특정 Region의 API Endpoint에 직접 접근한다.

```text
Client
 ↓
Regional API Gateway
```

필요하면 직접 CloudFront를 앞에 구성할 수도 있다.

## Private

VPC 내부에서만 접근할 API에 사용한다.

```text
VPC
 ↓
Interface VPC Endpoint
 ↓
Private API Gateway
```

---

# 8. Custom Domain & ACM

기본 API Gateway URL 대신:

```text
https://api.example.com
```

같은 Custom Domain을 사용할 수 있다.

HTTPS Certificate는 ACM을 이용한다.

```text
Route 53
→ DNS

ACM
→ TLS Certificate

API Gateway
→ API
```

Edge-Optimized Custom Domain에 사용하는 ACM Certificate는 `us-east-1`에 있어야 한다.

Regional Custom Domain의 Certificate는 API Gateway와 같은 Region에 있어야 한다.

---

# 9. API Gateway Cache

API Gateway 자체에서 Backend Response를 Cache할 수 있다.

```text
Client
 ↓
API Gateway Cache
 ↓ Cache Miss
Lambda
 ↓
DAX
 ↓ Cache Miss
DynamoDB
```

차이:

```text
API Gateway Cache
→ API Response Cache

DAX
→ DynamoDB Read Cache
```

API Gateway Cache Hit이면 Lambda까지 호출하지 않아도 된다.

---

## 🎯 Exam Notes

- Serverless REST API → API Gateway + Lambda
- API 관리 기능 필요 → API Gateway
- Global Client → Edge-Optimized
- Same Region / 직접 CloudFront 구성 → Regional
- VPC Only → Private
- Lambda Proxy → HTTP Request 정보를 Event로 Lambda에 전달
- Stage → API Deployment Environment
- API Response Cache → API Gateway Cache
- DynamoDB Read Cache → DAX

## 💡 Practical Example

```text
Mobile App
   ↓
POST /orders
   ↓
API Gateway
   ↓
Lambda
   ↓
DynamoDB
```

API Gateway는 요청을 받고 Lambda는 주문 처리 로직을 실행하며 DynamoDB는 주문 데이터를 저장한다.

## 🇯🇵 日本語 Summary

Amazon API Gatewayは、APIリクエストの入口として機能するマネージドサービスです。LambdaやHTTPバックエンド、AWSサービスと統合でき、認証、スロットリング、ステージ、キャッシュなどのAPI管理機能を提供します。

## 🇺🇸 English Summary

Amazon API Gateway provides a managed entry point for application APIs. It integrates with Lambda, HTTP backends, and AWS services while providing authentication, throttling, stages, caching, and other API management features.

## 📚 Vocabulary

| Term | Meaning |
|---|---|
| Resource | API 경로 |
| Method | GET, POST 등의 HTTP Method |
| Integration | API Gateway와 Backend의 연결 |
| Proxy Integration | HTTP 요청 정보를 Lambda에 전달하는 Integration 방식 |
| Stage | API의 배포 환경 |
| Endpoint | API에 접근하는 주소/접점 |
| Throttling | 요청 속도를 제한하는 기능 |

## 📝 Review Questions

### Q1. API Gateway에서 `GET /houses`의 `/houses`와 `GET`은 각각 무엇인가?

<details>
<summary>정답 보기</summary>

`/houses`는 Resource, `GET`은 Method이다.

</details>

### Q2. VPC 내부에서만 API Gateway에 접근해야 한다면?

<details>
<summary>정답 보기</summary>

Private Endpoint를 사용한다.

</details>

### Q3. Stage란?

<details>
<summary>정답 보기</summary>

API의 배포 환경이다. 예를 들어 dev, test, prod 등의 Stage를 구성할 수 있다.

</details>