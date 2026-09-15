# Amazon RDS Proxy

Amazon RDS Proxy는 RDS/Aurora 앞에 배치되는 **Fully Managed Database Proxy**이다.

핵심 목적은 **Database Connection을 Pooling하고 공유하여 DB의 Connection 부담을 줄이는 것**이다.

---

# 1. Connection Pooling

Application이 Database에 직접 연결하면 많은 요청이 발생할 때 DB Connection 수도 크게 증가할 수 있다.

```text
Applications
 │ │ │ │ │
 ▼ ▼ ▼ ▼ ▼
     RDS

→ 많은 DB Connections
→ CPU / RAM 부담 증가
→ Connection / Timeout 문제
```

RDS Proxy를 사용하면 Application은 Database 대신 Proxy에 연결한다.

```text
Applications
 │ │ │ │ │
 ▼ ▼ ▼ ▼ ▼
┌───────────┐
│ RDS Proxy │
└───────────┘
      │
      │ Connection Pool
      ▼
  RDS / Aurora
```

RDS Proxy가 Database Connection을 **Pooling하고 공유**하여 실제 Database로 향하는 Connection 부담을 줄인다.

### 장점

- Database Connection 수 및 부담 감소
- CPU / RAM 등 Database Resource 부담 감소
- Connection과 Timeout 문제 최소화
- Database 효율성과 Scalability 향상

### 시험 핵심

```text
Too many DB Connections
+ Connection Pooling
+ Reduce DB Resource Usage

→ Amazon RDS Proxy
```

---

# 2. Failover

RDS Proxy는 RDS/Aurora의 Failover를 처리하여 Application에 미치는 영향을 줄일 수 있다.

```text
Application
     │
     ▼
 RDS Proxy
     │
     ▼
Primary DB 💥
     ↓
Standby DB
```

Application은 Database Instance에 직접 연결하는 대신 RDS Proxy에 연결한다.

강의 기준 RDS Proxy는 RDS/Aurora의 **Failover Time을 최대 66% 감소**시킬 수 있다.

### 시험 핵심

`RDS / Aurora + Faster Failover → RDS Proxy`

---

# 3. Managed Architecture

RDS Proxy는 다음 특징을 가진다.

```text
Fully Managed
Serverless
Auto Scaling
Highly Available
Multi-AZ
```

Proxy Infrastructure의 Capacity를 직접 관리할 필요가 없다.

---

# 4. IAM Authentication & Secrets Manager

RDS Proxy를 사용하여 Database에 **IAM Authentication**을 강제할 수 있다.

Database Credentials는 **AWS Secrets Manager**에 안전하게 저장할 수 있다.

```text
Application
     │
     │ IAM Authentication
     ▼
 RDS Proxy
     │
     ▼
RDS / Aurora

Credentials
     ↓
AWS Secrets Manager
```

### 시험 핵심

```text
RDS Proxy
+ IAM Authentication
+ Database Credentials

→ AWS Secrets Manager
```

---

# 5. VPC Access

RDS Proxy는 Publicly Accessible하지 않으며 **VPC 내부에서 접근**해야 한다.

```text
Internet
   │
   X
RDS Proxy

VPC
└─ RDS Proxy
      ↓
   RDS / Aurora
```

---

# 6. Lambda + RDS Proxy

Lambda 함수는 짧은 시간에 많은 실행 환경이 생성될 수 있다.

각 Lambda가 Database에 직접 연결하면 많은 DB Connection이 동시에 생성될 수 있다.

```text
Lambda
Lambda
Lambda ───→ RDS Proxy ───→ RDS
Lambda
Lambda
```

RDS Proxy가 Connection을 Pooling하여 Database Connection 부담을 줄인다.

### 시험 핵심

`Lambda + Too many DB Connections → RDS Proxy`

Lambda에 대한 자세한 내용은 이후 Lambda Section에서 학습한다.

---

# 7. Supported Databases

강의/PDF 기준 RDS Proxy는 다음 Database Engine을 지원한다.

- RDS for MySQL
- RDS for PostgreSQL
- RDS for MariaDB
- RDS for Microsoft SQL Server
- Aurora MySQL
- Aurora PostgreSQL

대부분의 Application에서는 Database 대신 RDS Proxy Endpoint에 연결하는 방식으로 사용할 수 있다.

---

# 8. 시험 핵심 정리

```text
RDS Proxy
→ Fully Managed Database Proxy

Connection Pooling
→ DB Connections 감소
→ CPU / RAM 부담 감소

Failover
→ Failover Time 최대 66% 감소

Security
→ IAM Authentication
→ Credentials in Secrets Manager

Architecture
→ Serverless
→ Auto Scaling
→ Multi-AZ

Network
→ Public Access ❌
→ VPC 내부

Lambda + RDS
→ Connection 폭증
→ RDS Proxy
```

---

# 日本語まとめ

## Amazon RDS Proxy

Amazon RDS Proxyは、RDS/AuroraへのDatabase ConnectionをPoolingして共有するFully Managed Database Proxyである。

### 主な特徴

- Database Connectionを削減
- DatabaseのCPU / RAM負荷を軽減
- Failover時間を最大66%短縮
- Serverless / Auto Scaling / Multi-AZ
- IAM Authenticationを強制可能
- CredentialsはAWS Secrets Managerに保存
- Public Accessはできず、VPC内からアクセスする

Lambdaから大量のDatabase Connectionが発生する場合にもRDS Proxyが有効である。

---

# English Summary

## Amazon RDS Proxy

Amazon RDS Proxy is a fully managed database proxy for RDS and Aurora.

It pools and shares database connections, reducing the number of connections and the load on database resources.

### Key Features

- Connection pooling
- Reduced CPU / RAM pressure
- Up to 66% faster failover
- Serverless and Auto Scaling
- Highly Available and Multi-AZ
- IAM Authentication
- Credentials stored in AWS Secrets Manager
- Not publicly accessible

RDS Proxy is especially useful when Lambda functions could create a large number of database connections.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Database Proxy | データベースプロキシ | 데이터베이스 프록시 |
| Connection Pooling | コネクションプーリング | 연결 풀링 |
| Database Connection | データベース接続 | 데이터베이스 연결 |
| Failover | フェイルオーバー | 장애 조치 |
| Serverless | サーバーレス | 서버리스 |
| Auto Scaling | オートスケーリング | 자동 확장 |
| IAM Authentication | IAM認証 | IAM 인증 |
| Credentials | 認証情報 | 자격 증명 |
| Secrets Manager | Secrets Manager | 시크릿 관리 서비스 |
| Timeout | タイムアウト | 시간 초과 |

---

# Review Questions

1. Amazon RDS Proxy의 가장 중요한 목적은 무엇인가?
2. Connection Pooling이 Database의 CPU/RAM 부담을 줄이는 이유는 무엇인가?
3. RDS Proxy는 RDS/Aurora의 Failover에 어떤 도움을 주는가?
4. RDS Proxy에서 IAM Authentication과 AWS Secrets Manager는 각각 어떤 역할을 하는가?
5. RDS Proxy에 Internet에서 직접 접근할 수 있는가?
6. 많은 Lambda 함수가 RDS에 연결하는 환경에서 RDS Proxy가 유용한 이유는 무엇인가?
