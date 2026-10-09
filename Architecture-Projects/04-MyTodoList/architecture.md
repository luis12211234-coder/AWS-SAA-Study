# MyTodoList - Architecture

## Architecture Evolution

```mermaid
flowchart LR
    A["Mobile Application"]
    B["API Gateway"]
    C["AWS Lambda"]
    D["Amazon DynamoDB"]
    E["Cognito User Pool"]
    F["Cognito Identity Pool"]
    G["Amazon S3"]
    H["Amazon DAX"]

    A --> B
    B --> C
    C --> D

    A --> E
    E -->|"JWT Authentication"| B

    E -->|"User Token"| F
    F -->|"Temporary AWS Credentials"| G

    C --> H
    H --> D
```

---

## Final Architecture

```mermaid
flowchart TB
    USER["Mobile User"]
    APP["Mobile Application"]

    USER --> APP

    subgraph AUTH["Authentication & Authorization"]
        USERPOOL["Cognito User Pool"]
        IDPOOL["Cognito Identity Pool"]
    end

    subgraph API["Serverless Backend"]
        APIGW["Amazon API Gateway"]
        LAMBDA["AWS Lambda"]
    end

    subgraph DATA["Data Layer"]
        DAX["Amazon DAX"]
        DDB["Amazon DynamoDB"]
    end

    S3["Amazon S3<br/>User-Specific Files"]

    APP -->|"Sign Up / Sign In"| USERPOOL
    USERPOOL -->|"JWT Token"| APP

    APP -->|"HTTPS + JWT"| APIGW
    APIGW -->|"Token Validation"| USERPOOL
    APIGW --> LAMBDA

    LAMBDA --> DAX
    DAX --> DDB

    APP -->|"User Pool Token"| IDPOOL
    IDPOOL -->|"Validate Token"| USERPOOL
    IDPOOL -->|"Temporary AWS Credentials"| APP

    APP -->|"Authorized S3 Access"| S3
```

---

## REST API Request Flow

```text
Mobile Application
        |
        | HTTPS Request + JWT
        v
Amazon API Gateway
        |
        | Validate Token
        v
AWS Lambda
        |
        | Read / Write Todo Data
        v
Amazon DAX
        |
        | Cache Miss / Write
        v
Amazon DynamoDB
        |
        v
API Response
        |
        v
Mobile Application
```

DAX를 사용하는 경우 애플리케이션은 DAX Client를 통해 DynamoDB 데이터에 접근한다.

DAX는 DynamoDB 읽기 성능 최적화를 위한 선택적 계층이다.

---

## Authentication Flow

```mermaid
sequenceDiagram
    participant User
    participant App as Mobile App
    participant Cognito as Cognito User Pool
    participant API as API Gateway
    participant Lambda

    User->>App: Sign In
    App->>Cognito: Authentication Request
    Cognito-->>App: JWT Tokens

    App->>API: HTTPS Request + JWT
    API->>Cognito: Validate JWT using Cognito configuration
    Note over API,Cognito: JWT validation normally uses published signing keys
    API->>Lambda: Authorized Request
    Lambda-->>API: Response
    API-->>App: API Response
```

Cognito User Pool은 사용자를 인증한다.

API Gateway의 Cognito Authorizer는 발급된 JWT의 유효성을 검증한다.

실제 JWT 검증은 매 요청마다 Cognito 서비스에 네트워크 요청을 보내는 방식이 아니라 공개 서명 키를 이용해 수행할 수 있다.

---

## Direct S3 Access Flow

```mermaid
sequenceDiagram
    participant App as Mobile App
    participant UP as Cognito User Pool
    participant IP as Cognito Identity Pool
    participant S3 as Amazon S3

    App->>UP: User Login
    UP-->>App: Authentication Token

    App->>IP: Exchange Token
    IP-->>App: Temporary AWS Credentials

    App->>S3: Upload / Download with AWS Credentials
    S3-->>App: Authorized Object Access
```

### User-Specific S3 Prefix

```text
S3 Bucket
└── users/
    ├── identity-A/
    │   ├── profile.jpg
    │   └── document.pdf
    │
    ├── identity-B/
    │   └── image.png
    │
    └── identity-C/
        └── attachment.zip
```

IAM 정책을 통해 사용자별 Prefix 접근을 제한한다.

---

## DynamoDB Caching

```mermaid
flowchart TB
    APP["AWS Lambda"]
    DAX["Amazon DAX"]
    DDB["Amazon DynamoDB"]

    APP -->|"Read Request"| DAX

    DAX -->|"Cache Hit"| HIT["Return Cached Data"]
    DAX -->|"Cache Miss"| DDB

    DDB -->|"Read Result"| DAX
    DAX -->|"Update Cache"| CACHE["Cached Item"]
    CACHE --> APP
    HIT --> APP
```

### Cache Hit

```text
Lambda
  ↓
DAX
  ↓
Cached Data
  ↓
Lambda
```

### Cache Miss

```text
Lambda
  ↓
DAX
  ↓
DynamoDB
  ↓
DAX
  ↓
Lambda
```

---

## Optional API Gateway Caching

```mermaid
flowchart TB
    USER["Mobile App"]
    CACHE["API Gateway Cache"]
    LAMBDA["AWS Lambda"]
    DAX["Amazon DAX"]
    DDB["DynamoDB"]

    USER --> CACHE
    CACHE -->|"Cache Hit"| USER
    CACHE -->|"Cache Miss"| LAMBDA
    LAMBDA --> DAX
    DAX --> DDB
```

API Gateway Cache와 DAX는 서로 다른 계층에서 작동한다.

```text
API Gateway Cache Hit
→ Lambda Invocation 생략 가능

DAX Cache Hit
→ DynamoDB Read 감소
```

사용자별 비공개 API 응답을 캐싱하는 경우 Cache Key와 인증 경계를 신중히 설계해야 한다.

---

## Security Boundary

```mermaid
flowchart LR
    USER["Authenticated User"]
    JWT["Cognito JWT"]
    API["API Gateway Authorizer"]
    LAMBDA["Lambda Execution Role"]
    DDB["DynamoDB"]

    USER --> JWT
    JWT --> API
    API --> LAMBDA
    LAMBDA -->|"Least Privilege"| DDB

    USER2["Authenticated User"]
    IDPOOL["Cognito Identity Pool"]
    IAM["IAM Role"]
    S3["User-Specific S3 Prefix"]

    USER2 --> IDPOOL
    IDPOOL --> IAM
    IAM -->|"Restricted Access"| S3
```

---

## Failure Handling

```text
Invalid JWT
→ API Gateway rejects request

Unauthorized S3 Prefix
→ IAM denies access

Lambda Error
→ API request fails
→ Logs and metrics used for investigation

DAX Unavailable
→ Requires application-level fallback strategy
→ Direct DynamoDB access can be designed if appropriate
```

---

## Architecture Principles

```text
Serverless Compute
        +
Managed Authentication
        +
Fine-Grained Authorization
        +
NoSQL Database
        +
Optional Read Cache
        =
Scalable Mobile Backend
```

## Design Status

Architecture Design Only.

No production deployment or performance benchmark has been completed.