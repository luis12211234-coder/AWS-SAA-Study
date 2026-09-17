# Amazon Route 53 - Health Checks

## Overview

Route 53 Health Check는 리소스의 상태를 확인하여  
**DNS Routing에 사용할 리소스가 정상적으로 동작하고 있는지 판단**하는 기능이다.

예를 들어 여러 Region에 애플리케이션이 배포되어 있다고 하자.

```text
                    Route 53
                       │
             Latency-based Routing
                       │
              ┌────────┴────────┐
              ▼                 ▼
        us-east-1 ALB      eu-west-1 ALB
          Healthy ⭕        Unhealthy ❌
```

Routing Policy가 적절한 리소스를 선택하더라도  
해당 리소스가 장애 상태라면 사용자를 보내고 싶지 않다.

Health Check를 Route 53 Record와 연결하면  
리소스의 상태를 DNS Routing 결정에 반영할 수 있다.

> **Routing Policy = 어디로 연결할 것인가?**  
> **Health Check = 그 리소스가 정상인가?**

Health Check는 자동으로 생성되는 것이 아니다.

필요한 Health Check를 별도로 생성한 뒤  
지원되는 Route 53 Record와 연결하여 사용한다.

---

# 1. Types of Route 53 Health Checks

Route 53 Health Check는 크게 세 가지 방식으로 사용할 수 있다.

```text
Route 53 Health Check
│
├── Endpoint Monitoring
│   └── Public Endpoint 직접 확인
│
├── Calculated Health Check
│   └── 여러 Health Check 결과 조합
│
└── CloudWatch Alarm Monitoring
    └── CloudWatch Alarm 상태 확인
```

---

# 2. Endpoint Health Check

Endpoint Health Check는 인터넷에서 접근 가능한  
Public Endpoint의 상태를 직접 확인한다.

대상은 다음과 같은 Public Resource가 될 수 있다.

- Application
- Server
- Load Balancer
- Public AWS Resource

예:

```text
Route 53 Health Checkers
        │
        │ HTTP / HTTPS / TCP
        ▼
   Public Endpoint
        │
        ▼
    Application
```

Route 53의 Health Checkers가 여러 위치에서 Endpoint로 요청을 보내고  
응답 결과를 바탕으로 Healthy / Unhealthy를 판단한다.

---

## Protocol and Endpoint

Health Check에서는 다음과 같은 프로토콜을 사용할 수 있다.

```text
HTTP
HTTPS
TCP
```

HTTP를 사용하는 경우 일반적으로 다음과 같은 Health Endpoint를 구성할 수 있다.

```text
/health
```

예:

```text
GET /health

→ 200 OK
```

강의에서는 실습용 웹 서버의 Root Path `/`를 사용했다.

```text
EC2 Public IP
Port: 80
Path: /
```

실제 애플리케이션에서는 `/health`와 같이  
상태 확인 전용 Endpoint를 사용하는 것이 일반적이다.

---

# 3. Health Check Settings

## Request Interval

Health Check 요청 간격을 설정할 수 있다.

```text
Standard
→ 30 seconds

Fast
→ 10 seconds
→ Higher Cost
```

실습에서는 기본값인 **30 seconds**를 사용했다.

---

## Failure Threshold

Endpoint를 Unhealthy로 판단하기 전에  
몇 번의 Health Check 실패를 허용할지 설정할 수 있다.

```text
Health Check
    │
    ├── Success
    ├── Failure
    ├── Failure
    └── Failure
           │
           ▼
       Unhealthy
```

---

## HTTP Response

HTTP/HTTPS Health Check는 정상적인 HTTP Response를 이용해  
Endpoint의 상태를 판단할 수 있다.

또한 응답의 처음 **5,120 bytes** 안에  
특정 문자열이 존재하는지도 검사할 수 있다.

```text
GET /health

Response:
200 OK

status=healthy
```

단순히 연결 여부뿐만 아니라  
애플리케이션이 예상한 내용을 반환하는지도 확인할 수 있다.

---

# 4. Network Access for Health Checks

Route 53 Health Checkers는 Public Network에서 Endpoint에 접근한다.

따라서 Firewall이나 Security Group이  
Health Checker의 요청을 차단하면 Health Check가 실패할 수 있다.

```text
Route 53 Health Checker
          │
          │ HTTP :80
          ▼
   Security Group
          │
          ▼
         EC2
```

Health Check가 정상적으로 작동하려면  
Route 53 Health Checker가 Endpoint에 접근할 수 있어야 한다.

---

# 5. Hands-on - Public Endpoint Health Checks

실습에서는 서로 다른 세 Region의 EC2에 대해  
각각 Endpoint Health Check를 생성했다.

```text
Route 53 Health Checks

├── us-east-1
│   └── EC2 Public IP :80
│
├── ap-southeast-1
│   └── EC2 Public IP :80
│
└── eu-central-1
    └── EC2 Public IP :80
```

각 Health Check는 다음과 같이 구성했다.

```text
Endpoint: EC2 Public IP
Protocol: HTTP
Port: 80
Path: /
Interval: 30 seconds
```

정상 상태에서는 세 EC2 모두 Health Check가 성공한다.

---

## Simulating a Failure

Health Check의 동작을 확인하기 위해  
Singapore EC2의 Security Group에서 HTTP Inbound Rule을 제거했다.

```text
Before

Route 53 Health Checker
        │
        │ TCP 80
        ▼
Security Group
        │ Allow
        ▼
Singapore EC2

→ Healthy ⭕
```

HTTP Rule 제거 후:

```text
Route 53 Health Checker
        │
        │ TCP 80
        ▼
Security Group
        │
        X Block
        │
Singapore EC2

→ Connection Timeout
→ Unhealthy ❌
```

결과:

```text
us-east-1       → Healthy ⭕
ap-southeast-1  → Unhealthy ❌
eu-central-1    → Healthy ⭕
```

Health Check 상세 정보에서도  
실패 원인으로 Connection Timeout을 확인할 수 있었다.

이 실습을 통해 Health Check 역시 실제 Network Request이므로  
Security Group / Firewall 설정의 영향을 받는다는 것을 확인했다.

---

# 6. Calculated Health Checks

Calculated Health Check는  
**여러 Health Check의 결과를 하나의 Health Check로 조합**한다.

기존 Health Checks는 Child Health Checks가 되고  
Calculated Health Check가 Parent 역할을 한다.

```text
Health Check A ─┐
Health Check B ─┼──→ Calculated Health Check
Health Check C ─┘
```

조건에는 다음과 같은 논리를 사용할 수 있다.

```text
AND
OR
NOT
```

예를 들어:

```text
A = Healthy
B = Healthy
C = Unhealthy
```

모든 Child Health Check가 정상이어야 한다고 설정했다면:

```text
A AND B AND C
       │
       ▼
Calculated Health Check
→ Unhealthy ❌
```

---

## Hands-on - Calculated Health Check

실습에서는 앞에서 생성한 세 Health Check를  
하나의 Calculated Health Check로 묶었다.

```text
Calculated Health Check
          │
    ┌─────┼─────┐
    ▼     ▼     ▼
   US     EU     AP
   ⭕     ⭕     ❌
```

그리고 **모든 Health Check가 Healthy여야 Parent도 Healthy**하도록 설정했다.

Singapore Health Check가 이미 Unhealthy였기 때문에:

```text
Calculated Health Check
→ Unhealthy ❌
```

가 되는 것을 확인했다.

Calculated Health Check는 여러 Child Health Checks를 조합하여  
하나의 최종 상태를 만들고 싶을 때 사용한다.

---

# 7. Health Checks for Private Resources

Route 53 Health Checkers는 VPC 외부에 있기 때문에  
Private Endpoint에 직접 접근할 수 없다.

```text
Route 53 Health Checker
          │
          X
          │
VPC
└── Private Subnet
      └── Private EC2
```

따라서 Private Resource를 모니터링할 때는  
CloudWatch Alarm을 활용할 수 있다.

```text
Private Resource
       │
       │ Metric
       ▼
   CloudWatch
       │
       ▼
CloudWatch Alarm
       │
       ▼
Route 53 Health Check
```

CloudWatch Metric이 설정된 조건을 위반하여  
Alarm이 `ALARM` 상태가 되면 이를 Route 53 Health Check에 반영할 수 있다.

즉:

> **Public Resource → Endpoint Health Check로 직접 확인**  
> **Private Resource → CloudWatch Alarm을 이용해 간접적으로 확인**

실습에서는 사용할 CloudWatch Alarm이 없었기 때문에  
실제로 생성하지 않고 설정 방법만 확인했다.

---

# 8. Health Check + Routing Policy

Health Check의 중요한 목적 중 하나는  
Routing Policy와 결합하여 장애가 발생한 리소스를 DNS Routing에서 제외하는 것이다.

```text
                       Route 53
                          │
                    Routing Policy
                          │
               ┌──────────┴──────────┐
               ▼                     ▼
          Record A               Record B
        Health Check           Health Check
         Healthy ⭕            Unhealthy ❌
               │
               ▼
          DNS Response
```

예를 들어 Latency-based Routing에서는  
가장 낮은 Latency의 Region을 선택하면서 Health 상태도 함께 고려할 수 있다.

```text
Lowest Latency
     +
Healthy Resource
     ↓
DNS Response
```

이를 통해 DNS 수준의 Failover를 구성할 수 있다.

---

# 9. Exam Notes

```text
Public Endpoint 상태 확인
→ Endpoint Health Check

여러 Health Check 결과 조합
→ Calculated Health Check

Private Resource 상태 확인
→ CloudWatch Metric / Alarm 활용

Health Checker 요청이 Timeout
→ Security Group / Firewall 확인

Routing 중 장애 리소스 제외
→ Routing Policy + Health Check
```

### 핵심 Mental Model

```text
Routing Policy
→ "어디로 보낼까?"

Health Check
→ "거기 살아있나?"
```

---

# 日本語まとめ

## Route 53 Health Checks

Route 53 Health Check は、リソースが正常に動作しているかを確認し、  
DNS Routing にその状態を反映するための機能である。

Health Check は自動的に作成されるものではなく、  
必要に応じて作成し、対応する Route 53 Record と関連付ける。

### 主な種類

1. **Endpoint Health Check**
   - Public Endpoint を直接監視
   - HTTP / HTTPS / TCP を利用

2. **Calculated Health Check**
   - 複数の Health Check の結果を組み合わせる
   - AND / OR / NOT を利用可能

3. **CloudWatch Alarm Monitoring**
   - CloudWatch Alarm の状態を監視
   - Private Resource の監視に有効

### Network Access

Route 53 Health Checkers から Endpoint への通信が  
Security Group や Firewall によって遮断されると Health Check は失敗する。

### Routing Policy Integration

```text
Routing Policy
→ どこへ接続するか

Health Check
→ そのリソースが正常か
```

Health Check を Routing Policy と組み合わせることで  
DNS Failover を構成できる。

---

# English Summary

## Route 53 Health Checks

Route 53 Health Checks determine whether resources are healthy and can use  
that status when Route 53 makes DNS routing decisions.

Health Checks are configured separately and can be associated with supported Route 53 records.

### Main Types

1. **Endpoint Health Check**
   - Directly monitors public endpoints
   - Supports HTTP, HTTPS, and TCP

2. **Calculated Health Check**
   - Combines multiple child health checks
   - Supports logical conditions such as AND, OR, and NOT

3. **CloudWatch Alarm Monitoring**
   - Monitors the state of a CloudWatch Alarm
   - Useful for monitoring private resources indirectly

Route 53 Health Checkers must have network access to public endpoints.  
Security Groups or firewalls can therefore cause Health Checks to fail.

Health Checks can be combined with Routing Policies to support DNS failover.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Health Check | ヘルスチェック | 상태 확인 |
| Healthy | 正常 | 정상 |
| Unhealthy | 異常 | 비정상 |
| Endpoint | エンドポイント | 엔드포인트 |
| Public Endpoint | パブリックエンドポイント | 퍼블릭 엔드포인트 |
| Private Resource | プライベートリソース | 프라이빗 리소스 |
| Health Checker | ヘルスチェッカー | 상태 확인기 |
| Calculated Health Check | 計算済みヘルスチェック | 계산된 상태 확인 |
| Child Health Check | 子ヘルスチェック | 하위 상태 확인 |
| CloudWatch Alarm | CloudWatch アラーム | CloudWatch 알람 |
| Failure Threshold | 障害しきい値 | 장애 임계값 |
| Request Interval | リクエスト間隔 | 요청 간격 |
| Connection Timeout | 接続タイムアウト | 연결 시간 초과 |
| Failover | フェイルオーバー | 장애 조치 |
| Firewall | ファイアウォール | 방화벽 |

---

# Review Questions

### 1. Route 53 Health Check의 가장 기본적인 역할은 무엇인가?

<details>
<summary>정답 보기</summary>

**리소스가 정상적으로 동작하고 있는지 확인하는 것이다.**

Routing Policy와 연결하면 장애가 발생한 리소스를 DNS Routing 결정에서 제외하는 데 사용할 수 있다.

```text
Routing Policy = 어디로 보낼까?
Health Check   = 거기 살아있나?
```

</details>

### 2. Route 53 Record를 생성하면 Health Check도 자동으로 생성되는가?

<details>
<summary>정답 보기</summary>

**아니다.**

Health Check는 별도로 생성하며, 필요한 경우 지원되는 Route 53 Record와 연결한다.

</details>

### 3. Route 53 Health Check의 세 가지 주요 방식은 무엇인가?

<details>
<summary>정답 보기</summary>

1. Public Endpoint를 직접 확인하는 **Endpoint Health Check**
2. 여러 Health Check 결과를 조합하는 **Calculated Health Check**
3. **CloudWatch Alarm** 상태를 이용하는 Health Check

</details>

### 4. 정상적으로 동작하던 EC2의 HTTP Inbound Rule을 Security Group에서 제거하면 Endpoint Health Check에는 어떤 일이 발생할 수 있는가?

<details>
<summary>정답 보기</summary>

Route 53 Health Checker가 EC2의 HTTP Endpoint에 접근할 수 없게 되어  
**Connection Timeout이 발생하고 Health Check가 Unhealthy가 될 수 있다.**

Health Check 역시 실제 Network Request이므로 Security Group과 Firewall의 영향을 받는다.

</details>

### 5. Calculated Health Check는 무엇인가?

<details>
<summary>정답 보기</summary>

**여러 Child Health Check의 결과를 조합하여 하나의 최종 Health 상태를 만드는 Health Check이다.**

AND, OR, NOT 등의 조건을 이용할 수 있다.

예:

```text
A = Healthy
B = Healthy
C = Unhealthy

A AND B AND C
→ Unhealthy
```

</details>

### 6. Route 53 Health Checker가 Private Subnet의 EC2에 직접 접근할 수 없는 경우 어떻게 상태를 확인할 수 있는가?

<details>
<summary>정답 보기</summary>

**CloudWatch Metric과 CloudWatch Alarm을 이용할 수 있다.**

```text
Private Resource
      ↓
CloudWatch Metric
      ↓
CloudWatch Alarm
      ↓
Route 53 Health Check
```

Route 53 Health Checker가 Private Resource에 직접 접근하는 것이 아니라  
CloudWatch Alarm의 상태를 통해 간접적으로 Health 상태를 판단한다.

</details>

### 7. Latency-based Routing과 Health Check를 함께 사용하면 각각 무엇을 판단하는가?

<details>
<summary>정답 보기</summary>

**Latency-based Routing**은 사용자에게 낮은 Network Latency를 제공하는 Region을 선택하고,  
**Health Check**는 해당 리소스가 정상인지 판단한다.

따라서 개념적으로:

```text
Low Latency
    +
Healthy
    ↓
DNS Response
```

가 된다.

</details>