# MyBlog.com - Architecture

## Architecture Evolution

```mermaid
flowchart LR
    A["Static Website on S3"]
    B["CloudFront CDN"]
    C["OAC + Private S3"]
    D["API Gateway + Lambda"]
    E["DynamoDB + DAX"]
    F["DynamoDB Global Tables"]
    G["DynamoDB Streams + SES"]
    H["S3 Events + Thumbnail Lambda"]

    A --> B
    B --> C
    C --> D
    D --> E
    E --> F
    F --> G
    G --> H
```

---

## Final Architecture

```mermaid
flowchart TB
    USER["Global Users"]

    subgraph STATIC["Static Content Delivery"]
        CF["Amazon CloudFront"]
        S3STATIC["Private S3 Bucket<br/>HTML / CSS / JS / Images"]
    end

    subgraph BACKEND["Serverless API"]
        API["Amazon API Gateway"]
        LAMBDA["AWS Lambda"]
        DAX["Amazon DAX"]
    end

    subgraph DATABASE["Database Layer"]
        DDB1["DynamoDB<br/>Region A"]
        DDB2["DynamoDB<br/>Region B"]
    end

    subgraph EVENTS["Event-Driven Processing"]
        STREAM["DynamoDB Streams"]
        EMAIL["Email Lambda"]
        SES["Amazon SES"]
    end

    subgraph IMAGES["Image Processing"]
        UPLOAD["S3 Upload Bucket"]
        THUMB["Thumbnail Lambda"]
        OUTPUT["S3 Thumbnail Storage"]
    end

    USER --> CF
    CF -->|"OAC"| S3STATIC

    USER --> API
    API --> LAMBDA
    LAMBDA --> DAX
    DAX --> DDB1

    DDB1 <-->|"Global Tables Replication"| DDB2

    DDB1 --> STREAM
    STREAM --> EMAIL
    EMAIL --> SES

    USER -->|"Upload Image"| UPLOAD
    UPLOAD -->|"ObjectCreated Event"| THUMB
    THUMB --> OUTPUT
```

이 다이어그램은 여러 아키텍처 기능을 한눈에 보여주는 개념 설계다.

실제 Multi-Region 환경에서는 Region별 API, Lambda, DAX 구성과 사용자 라우팅을 별도로 설계해야 한다.

---

## Static Website Delivery

```mermaid
sequenceDiagram
    participant User
    participant CF as CloudFront
    participant S3 as Private S3

    User->>CF: Request Static Content

    alt Cache Hit
        CF-->>User: Cached Content
    else Cache Miss
        CF->>S3: Origin Request using OAC
        S3-->>CF: Static File
        CF-->>User: Content
    end
```

### Architecture

```text
Global User
     |
     v
CloudFront Edge Location
     |
     | Cache Miss
     v
Private S3 Origin
```

OAC와 S3 Bucket Policy를 통해 CloudFront Distribution만 Origin에 접근하도록 구성할 수 있다.

---

## Dynamic REST API

```text
Browser
   |
   | HTTPS Request
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

### Responsibilities

```text
API Gateway
→ API Entry Point

Lambda
→ Business Logic

DAX
→ DynamoDB Read Cache

DynamoDB
→ Persistent NoSQL Data
```

---

## Multi-Region Database

```mermaid
flowchart LR
    ASIA["Asia Users"]
    US["US Users"]

    API1["Tokyo API Gateway + Lambda"]
    API2["US API Gateway + Lambda"]

    DB1["DynamoDB Tokyo"]
    DB2["DynamoDB US"]

    ASIA --> API1
    API1 --> DB1

    US --> API2
    API2 --> DB2

    DB1 <-->|"Global Tables Replication"| DB2
```

DynamoDB Global Tables는 Multi-Region Active-Active 데이터베이스 구성을 지원한다.

글로벌 지연 시간 개선을 위해서는 데이터베이스뿐 아니라 API 처리 경로도 Region별로 설계해야 한다.

복제 지연 및 동시 쓰기 충돌을 고려한다.

---

## Welcome Email Workflow

```mermaid
sequenceDiagram
    participant User
    participant API as API Gateway
    participant Lambda as Registration Lambda
    participant DDB as DynamoDB
    participant Stream as DynamoDB Streams
    participant Worker as Email Lambda
    participant SES as Amazon SES

    User->>API: Subscribe
    API->>Lambda: Registration Request
    Lambda->>DDB: Insert Subscriber
    DDB-->>Lambda: Write Result
    Lambda-->>API: Registration Response
    API-->>User: Success

    DDB->>Stream: INSERT Event
    Stream->>Worker: Invoke via Event Source Mapping
    Worker->>SES: Send Welcome Email
    SES-->>User: Welcome Email
```

### Event Flow

```text
New Subscriber
      ↓
DynamoDB INSERT
      ↓
DynamoDB Streams
      ↓
Lambda
      ↓
Amazon SES
      ↓
Welcome Email
```

### Design Considerations

- 신규 구독자 이벤트 필터링
- 중복 이벤트 처리
- Lambda 재시도
- SES 발신자 검증
- IAM 최소 권한

---

## Automatic Thumbnail Generation

```mermaid
sequenceDiagram
    participant User
    participant S3 as Amazon S3
    participant Lambda as Thumbnail Lambda

    User->>S3: Upload Original Image
    S3->>Lambda: ObjectCreated Event
    Lambda->>S3: Read Original Image
    Lambda->>Lambda: Resize Image
    Lambda->>S3: Save Thumbnail
```

### S3 Prefix Design

```text
S3 Bucket
├── originals/
│   ├── photo-001.jpg
│   └── photo-002.jpg
│
└── thumbnails/
    ├── photo-001-small.jpg
    └── photo-002-small.jpg
```

S3 Event Notification을 `originals/` Prefix에만 적용해 재귀 호출을 방지한다.

---

## Cache Layers

```mermaid
flowchart TB
    USER["Global User"]
    CF["CloudFront Cache"]
    S3["S3 Static Origin"]

    USER --> CF
    CF --> S3

    USER2["API Client"]
    API["API Gateway"]
    LAMBDA["Lambda"]
    DAX["DAX"]
    DDB["DynamoDB"]

    USER2 --> API
    API --> LAMBDA
    LAMBDA --> DAX
    DAX --> DDB
```

| Cache | Purpose |
|---|---|
| CloudFront | Static Content Delivery |
| API Gateway Cache | Optional API Response Caching |
| DAX | DynamoDB Read Caching |

---

## Failure Handling

```text
S3 Origin Request Failure
→ CloudFront returns an error or eligible cached response

Lambda Failure
→ API error response
→ CloudWatch investigation

Email Lambda Failure
→ Retry and failure-handling configuration required

Thumbnail Lambda Failure
→ Original file remains
→ Thumbnail generation requires retry or recovery

Regional Database Failure
→ Multi-Region failover design required
```

---

## Architecture Principles

```text
Global CDN
    +
Serverless API
    +
Managed NoSQL Database
    +
Event-Driven Processing
    +
Optional Multi-Region Replication
    =
Scalable Global Website
```

## Design Status

Architecture Design Only.

No production deployment or multi-region failover test has been completed.