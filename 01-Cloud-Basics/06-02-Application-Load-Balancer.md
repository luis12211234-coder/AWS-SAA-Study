# Application Load Balancer (ALB)

## Overview

**Load Balancer**는 들어오는 Traffic을 여러 Backend Server에 분산한다.

**Application Load Balancer (ALB)**는
OSI **Layer 7**에서 동작하는 HTTP/HTTPS Load Balancer이다.

```text
Client
↓
ALB
↓
Listener
↓
Target Group
↓
EC2
```

- **Listener**: 어떤 Protocol / Port에서 요청을 받을지 정의
- **Target Group**: 요청을 전달할 Backend Target들의 그룹
- **Target**: 실제 요청을 처리하는 EC2 등의 Resource

---

# 1. ALB Routing

ALB는 HTTP Request의 내용을 확인하여
서로 다른 Target Group으로 Routing할 수 있다.

```text
/users
→ Users Target Group

/search
→ Search Target Group
```

Routing 조건:

```text
Path
Hostname
Query String
HTTP Header
```

따라서 하나의 ALB로
여러 Web Application이나 Microservice를 처리할 수 있다.

ALB는 HTTP/2, WebSocket도 지원하며
HTTP → HTTPS Redirect도 가능하다.

---

# 2. Listener & Rules

Listener는 ALB가 Traffic을 받는 Protocol과 Port를 정의한다.

```text
HTTP  :80
HTTPS :443
```

Listener Rule:

```text
IF Condition
→ Action
```

대표 Action:

```text
Forward
→ Target Group으로 전달

Redirect
→ 다른 URL / Protocol로 이동

Fixed Response
→ ALB가 직접 응답
```

실습 예:

```text
IF Path = /error
→ Fixed Response
→ HTTP 404
```

어떤 Rule에도 일치하지 않으면
**Default Rule**이 적용된다.

---

# 3. Target Group & Health Check

Target Group은 ALB가 요청을 전달할 Target들의 그룹이다.

```text
Target Group
├─ EC2 A
└─ EC2 B
```

ALB Target으로 사용할 수 있는 대표 Resource:

```text
EC2
ECS
Lambda
Private IP
```

Health Check는 Target Group Level에서 수행된다.

```text
EC2 A → Healthy
EC2 B → Unhealthy
```

Unhealthy Target에는 Traffic을 전달하지 않는다.

```text
ALB
├─ EC2 A ✅ → Traffic
└─ EC2 B ❌ → 제외
```

---

# 4. Security Groups

Backend EC2는 Internet에서 직접 접근시키기보다
ALB를 통해서만 접근하도록 구성할 수 있다.

```text
Internet
↓
ALB
↓
EC2
```

ALB Security Group:

```text
HTTP :80
Source = Internet
```

EC2 Security Group:

```text
HTTP :80
Source = ALB Security Group
```

따라서:

```text
Internet → EC2
❌

Internet → ALB → EC2
✅
```

---

# 5. Hands-On Summary

실습에서는 Web Server가 실행되는
EC2 Instance 두 개를 생성했다.

```text
EC2 A
EC2 B
```

두 Instance를 하나의 Target Group에 등록하고
ALB의 HTTP :80 Listener와 연결했다.

```text
Client
↓
ALB DNS
↓
HTTP :80 Listener
↓
Target Group
├─ EC2 A
└─ EC2 B
```

ALB DNS로 반복 요청하여
두 EC2에 Traffic이 분산되는 것을 확인했다.

한 Instance를 중지하면 해당 Target이
Unhealthy / Unused 상태가 되고,
ALB는 정상 Target으로만 요청을 전달했다.

또한 EC2 Security Group을 수정하여
ALB에서 오는 HTTP Traffic만 허용했다.

마지막으로:

```text
/error
→ 404 Fixed Response
```

Listener Rule을 생성하여
Path 기반 Rule 동작을 확인했다.

---

# 6. Important Extras

ALB는 Microservices와 Container 환경에 적합하며
ECS의 Dynamic Port Mapping을 지원한다.

Backend에서 실제 Client IP가 필요한 경우:

```text
X-Forwarded-For
→ Original Client IP
```

추가 Header:

```text
X-Forwarded-Port
→ Original Port

X-Forwarded-Proto
→ Original Protocol
```

---

# Exam Notes

```text
ALB
→ Layer 7
→ HTTP / HTTPS
```

```text
Listener
→ Protocol + Port

Target Group
→ Backend Targets

Health Check
→ Unhealthy Target 제외
```

ALB Routing:

```text
Path
Host
Query String
Header
```

Listener Action:

```text
Forward
Redirect
Fixed Response
```

보안:

```text
EC2 Security Group
Source = ALB Security Group
```

Client IP:

```text
X-Forwarded-For
```

---

# Japanese Summary

**Application Load Balancer (ALB)**は、
Layer 7で動作するHTTP/HTTPS Load Balancerです。

```text
Client
↓
ALB
↓
Listener
↓
Target Group
↓
EC2
```

HTTP RequestのPath、Host、Query String、Headerを利用して
複数のTarget GroupへRoutingできます。

Health Checkにより、
UnhealthyなTargetにはTrafficを送信しません。

---

# English Summary

**Application Load Balancer (ALB)** is
a Layer 7 HTTP/HTTPS load balancer.

```text
Client
↓
ALB
↓
Listener
↓
Target Group
↓
EC2
```

ALB can route requests based on
Path, Host, Query String, and HTTP Headers.

Health checks prevent traffic
from being sent to unhealthy targets.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Load Balancer | ロードバランサー | 여러 서버에 부하를 분산하는 서비스 |
| Listener | リスナー | 특정 프로토콜과 포트에서 요청을 받는 설정 |
| Target | ターゲット | 실제 요청을 처리하는 대상 |
| Target Group | ターゲットグループ | Backend Target의 그룹 |
| Health Check | ヘルスチェック | Target의 정상 상태 확인 |
| Routing | ルーティング | 요청을 적절한 대상으로 전달 |
| Listener Rule | リスナールール | 요청 조건에 따라 동작하는 규칙 |
| Forward | 転送 | Target Group으로 요청 전달 |
| Redirect | リダイレクト | 다른 URL 또는 프로토콜로 전환 |
| Fixed Response | 固定レスポンス | ALB가 직접 반환하는 응답 |
| Backend | バックエンド | 실제 요청을 처리하는 후단 시스템 |

---

# Review Questions

### Q1. ALB는 OSI 몇 계층에서 동작하는가?

```text
Layer 7
```

### Q2. Listener란?

ALB가 요청을 받을
Protocol과 Port를 정의하는 설정이다.

### Q3. Target Group이란?

ALB가 Traffic을 전달할
Backend Target들의 그룹이다.

### Q4. Target이 Unhealthy하면?

ALB가 해당 Target으로 Traffic을 전달하지 않는다.

### Q5. ALB가 Routing에 사용할 수 있는 정보는?

```text
Path
Host
Query String
HTTP Header
```

### Q6. EC2를 ALB를 통해서만 접근하게 하려면?

```text
EC2 Security Group
Source = ALB Security Group
```

### Q7. 실제 Client IP를 확인할 때 사용하는 Header는?

```text
X-Forwarded-For
```