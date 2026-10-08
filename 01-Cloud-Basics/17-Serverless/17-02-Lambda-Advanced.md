# 17-02. AWS Lambda Advanced

## 1. Lambda SnapStart

Lambda의 초기화 과정에서 발생하는 Cold Start 시간을 줄이기 위한 기능이다.

```text
Function Version 게시
        ↓
   Function 초기화
        ↓
초기화 상태 Snapshot
        ↓
    Snapshot 저장


Invocation
    ↓
Snapshot 복원
    ↓
Handler 실행
```

핵심은 **매번 처음부터 초기화하는 대신 미리 초기화된 상태를 복원**하는 것이다.

---

# 2. Lambda Networking

기본 Lambda는 사용자의 VPC 내부에 직접 연결되어 있지 않다.

```text
Default Lambda
     ↓
Public AWS Services / Internet
```

Private RDS와 같은 VPC 내부 리소스에 접근하려면 Lambda에 VPC 연결을 구성해야 한다.

```text
Lambda
  ↓
VPC Connectivity
  ↓
Private RDS
```

중요:

```text
VPC-connected Lambda
Private Resource 접근 ✅
Internet 자동 제공 ❌
```

IPv4 인터넷 접근이 필요한 일반적인 구조:

```text
Lambda
 ↓
Private Subnet
 ↓
NAT Gateway
 ↓
Internet Gateway
 ↓
Internet
```

---

# 3. Lambda + RDS Proxy

Lambda가 RDS에 직접 대량으로 연결하면 많은 DB Connection이 생성될 수 있다.

```text
Lambda × 1000
     ↓
   RDS
```

이를 완화하기 위해 RDS Proxy를 사용할 수 있다.

```text
Lambda Functions
       ↓
    RDS Proxy
       ↓
      RDS
```

RDS Proxy는 DB Connection을 Pooling하고 재사용한다.

장점:

- DB Connection 관리
- Scalability 향상
- Failover 대응 개선
- IAM Authentication 지원 가능

---

# 4. Invoking Lambda from RDS / Aurora

일부 RDS/Aurora 엔진에서는 DB 내부 작업을 통해 Lambda를 호출할 수 있다.

```text
Database 작업
     ↓
Lambda 호출
     ↓
추가 처리
```

예:

```text
사용자 등록
   ↓
DB INSERT
   ↓
Lambda
   ↓
SES
   ↓
Welcome Email
```

단순히 모든 INSERT가 자동으로 Lambda를 호출하는 것은 아니며 DB 측에서 Lambda 호출 로직을 구성해야 한다.

---

# 5. RDS Event Notification과의 차이

```text
Database Data Event
→ 데이터 자체의 변화

RDS Event Notification
→ RDS Resource 자체의 상태 변화
```

예:

```text
DB 내부 INSERT
→ Lambda Integration

RDS Instance 시작/중지
Snapshot 생성
→ RDS Event Notification
```

---

# 6. Edge Functions

CloudFront에서는 사용자 가까운 Edge Location에서 코드를 실행할 수 있다.

두 가지 주요 방식:

```text
CloudFront Functions
Lambda@Edge
```

### CloudFront Functions

- 매우 가벼운 작업
- Viewer Request / Viewer Response
- 매우 짧은 실행
- JavaScript

### Lambda@Edge

보다 복잡한 Edge Logic에 사용한다.

```text
Viewer
 ↓
Viewer Request
 ↓
CloudFront
 ↓
Origin Request
 ↓
Origin
 ↓
Origin Response
 ↓
CloudFront
 ↓
Viewer Response
 ↓
Viewer
```

Lambda@Edge는 위 네 Event 지점에서 실행할 수 있다.

---

## 🎯 Exam Notes

- Lambda → Private RDS 접근 시 VPC Connectivity 필요
- VPC 연결만으로 인터넷 접근이 자동 제공되는 것은 아님
- Lambda의 대량 DB Connection → RDS Proxy
- RDS Proxy → Connection Pooling
- CloudFront Functions → 가벼운 Edge Logic
- Lambda@Edge → 더 복잡한 Edge Logic
- SnapStart → 초기화 상태 Snapshot으로 시작 지연 감소

## 💡 Practical Example

```text
API Gateway
    ↓
Lambda × Many
    ↓
RDS Proxy
    ↓
RDS
```

Lambda가 급격하게 Scaling되더라도 RDS Proxy가 DB Connection을 관리해 RDS에 직접 연결이 몰리는 것을 줄일 수 있다.

## 🇯🇵 日本語 Summary

LambdaはVPCに接続することでプライベートリソースへアクセスできます。多数のLambdaからRDSへ接続する場合は、RDS Proxyによってデータベース接続をプールできます。また、SnapStartやLambda@Edgeなど、性能やエッジ処理を支援する機能もあります。

## 🇺🇸 English Summary

Lambda can connect to a VPC to access private resources. RDS Proxy pools database connections for highly scalable Lambda workloads. SnapStart can reduce initialization latency, while Lambda@Edge and CloudFront Functions enable logic to run closer to users.

## 📚 Vocabulary

| Term | Meaning |
|---|---|
| Cold Start | 새로운 Lambda 실행 환경 초기화로 발생하는 지연 |
| SnapStart | 초기화된 상태의 Snapshot을 활용하는 기능 |
| RDS Proxy | DB Connection을 중계하고 Pooling하는 서비스 |
| Connection Pooling | DB 연결을 재사용하는 방식 |
| Edge Function | 사용자와 가까운 Edge에서 실행되는 코드 |
| Failover | 장애 발생 시 다른 리소스로 전환하는 과정 |

## 📝 Review Questions

### Q1. Lambda가 대량으로 RDS에 연결해서 Connection 문제가 발생한다면?

<details>
<summary>정답 보기</summary>

RDS Proxy를 사용한다.

</details>

### Q2. VPC에 연결한 Lambda는 자동으로 인터넷에 접근할 수 있는가?

<details>
<summary>정답 보기</summary>

아니다. 일반적인 IPv4 인터넷 접근 구조에서는 Private Subnet에서 NAT Gateway 등을 통한 인터넷 경로를 구성해야 한다.

</details>