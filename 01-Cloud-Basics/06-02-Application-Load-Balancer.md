# Application Load Balancer (ALB)

## Overview

**Application Load Balancer (ALB)**는
Layer 7에서 동작하는 HTTP 기반 Load Balancer이다.

```text
Client
↓
ALB
↓
Target Groups
↓
Applications
```

HTTP Request의 내용을 분석하여
여러 Target Group으로 지능적으로 Routing할 수 있다.

---

# 1. Layer 7 Load Balancer

ALB는 Layer 7에서 동작하며
HTTP Traffic을 기반으로 Routing한다.

지원 기능:

```text
HTTP
HTTPS
HTTP/2
WebSocket
```

HTTP에서 HTTPS로
Redirect하는 것도 가능하다.

```text
HTTP
↓
ALB
↓
HTTPS
```

---

# 2. Target Groups

Target Group은
ALB가 Traffic을 전달할 Backend Target의 그룹이다.

```text
Client
↓
ALB
↓
Target Group
├─ EC2
├─ EC2
└─ EC2
```

지원되는 Target:

```text
EC2 Instances
ECS Tasks
Lambda Functions
Private IP Addresses
```

ALB는 여러 Target Group으로 Routing할 수 있다.

Health Check는 Target Group Level에서 설정된다.

---

# 3. Path-Based Routing

URL Path에 따라
서로 다른 Target Group으로 Traffic을 보낼 수 있다.

```text
example.com/users
↓
Users Target Group
```

```text
example.com/search
↓
Search Target Group
```

하나의 ALB로
여러 Application과 Microservice를 처리할 수 있다.

---

# 4. Host-Based Routing

Hostname을 기준으로 Routing할 수 있다.

```text
one.example.com
↓
Target Group A
```

```text
other.example.com
↓
Target Group B
```

---

# 5. Query String and Header Routing

Query String을 기준으로
Traffic을 Routing할 수 있다.

```text
?Platform=Mobile
↓
Target Group A
```

```text
?Platform=Desktop
↓
Target Group B
```

HTTP Header를 기반으로 한 Routing도 지원한다.

---

# 6. ALB and Microservices

ALB는 Microservices와
Container 기반 Application에 적합하다.

대표적인 서비스:

```text
Docker
Amazon ECS
```

ALB는 ECS의 Dynamic Port Mapping을 지원한다.

```text
ALB
↓
ECS Task
↓
Dynamic Port
```

따라서 하나의 ALB를 이용해
여러 Container Application으로 Traffic을 전달할 수 있다.

---

# 7. ALB vs Classic Load Balancer

Classic Load Balancer를 여러 Application에 사용하면
Application별 Load Balancer가 필요할 수 있다.

```text
App A
→ CLB A

App B
→ CLB B
```

ALB에서는 하나의 Load Balancer가
여러 Target Group으로 Routing할 수 있다.

```text
       ALB
      /   \
     /     \
 App A     App B
```

---

# 8. Private IP Targets

Target Group에는
Private IP Address를 등록할 수 있다.

이를 이용해 AWS 외부의
On-Premises Server도 Target으로 사용할 수 있다.

```text
        ALB
       /   \
      /     \
AWS EC2    On-Premises
Private IP  Private IP
```

---

# 9. X-Forwarded Headers

Client가 ALB를 통해 Application에 접근하면
Backend Server의 직접 연결 상대는 ALB가 된다.

```text
Client
↓
ALB
↓
EC2
```

따라서 실제 Client 정보를
HTTP Header를 통해 전달한다.

```text
X-Forwarded-For
→ Original Client IP

X-Forwarded-Port
→ Original Client Port

X-Forwarded-Proto
→ Original Protocol
```

시험 핵심:

```text
Original Client IP behind ALB
→ X-Forwarded-For
```

---

# 10. Fixed Hostname

ALB에는 고정된 DNS Hostname이 제공된다.

```text
ALB
→ AWS-provided DNS Name
```

Application은 이 Hostname을 통해
Load Balancer에 접근할 수 있다.

---

# Exam Notes

ALB의 핵심:

```text
Layer 7
HTTP / HTTPS
Target Groups
```

Routing:

```text
Path
Hostname
Query String
Headers
```

대표 사용 사례:

```text
Microservices
Containers
ECS
Multiple HTTP Applications
```

Target:

```text
EC2
ECS
Lambda
Private IP
```

Client 정보:

```text
Original Client IP
→ X-Forwarded-For
```

Health Check:

```text
Target Group Level
```

---

# Summary

```text
ALB
= Layer 7 HTTP Load Balancer
```

핵심:

```text
One ALB
↓
Multiple Target Groups
↓
Path / Host / Query / Header Routing
```

시험 암기:

```text
Microservices / ECS
→ ALB

Original Client IP
→ X-Forwarded-For
```

---

# Japanese Summary

**Application Load Balancer (ALB)**は、
Layer 7で動作するHTTP Load Balancerです。

HTTP Requestの内容を利用して
複数のTarget GroupへRoutingできます。

```text
Path
Hostname
Query String
Header
```

Targetとして以下を利用できます。

```text
EC2
ECS
Lambda
Private IP
```

実際のClient IPは
`X-Forwarded-For` HeaderでBackendへ渡されます。

---

# English Summary

**Application Load Balancer (ALB)** is
a Layer 7 HTTP load balancer.

It can route requests to multiple target groups based on:

```text
URL Path
Hostname
Query String
HTTP Headers
```

Supported targets include:

```text
EC2 instances
ECS tasks
Lambda functions
Private IP addresses
```

The original client IP is passed to the backend
using the `X-Forwarded-For` header.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Application Load Balancer | Application Load Balancer | 애플리케이션 계층 로드 밸런서 |
| Layer 7 | レイヤー7 | OSI 7계층 애플리케이션 계층 |
| Target Group | ターゲットグループ | ALB가 트래픽을 전달할 대상 그룹 |
| Path-Based Routing | パスベースルーティング | URL 경로 기반 라우팅 |
| Host-Based Routing | ホストベースルーティング | 호스트 이름 기반 라우팅 |
| Query String | クエリ文字列 | URL에 전달되는 매개변수 문자열 |
| Redirect | リダイレクト | 요청을 다른 URL 또는 프로토콜로 전환 |
| Health Check | ヘルスチェック | Target의 정상 동작 여부 확인 |
| Microservices | マイクロサービス | 기능별로 분리된 소규모 서비스 구조 |
| Dynamic Port Mapping | 動的ポートマッピング | 동적으로 할당된 포트로 요청을 전달하는 기능 |
| X-Forwarded-For | X-Forwarded-For | 원래 Client IP를 전달하는 HTTP Header |
| X-Forwarded-Port | X-Forwarded-Port | 원래 요청 Port를 전달하는 Header |
| X-Forwarded-Proto | X-Forwarded-Proto | 원래 HTTP Protocol을 전달하는 Header |

---

# Review Questions

### Q1. ALB는 OSI 몇 계층에서 동작하는가?

```text
Layer 7
```

### Q2. ALB가 Routing에 사용할 수 있는 정보는?

```text
Path
Hostname
Query String
Headers
```

### Q3. ALB의 Target Group에 등록할 수 있는 대표적인 Target은?

```text
EC2
ECS
Lambda
Private IP
```

### Q4. ALB가 Microservices와 ECS에 적합한 이유는?

여러 Target Group으로 Routing할 수 있고
Dynamic Port Mapping을 지원하기 때문이다.

### Q5. Backend Server에서 실제 Client IP를 확인하려면?

```text
X-Forwarded-For
```

### Q6. ALB의 Health Check는 어디에서 설정되는가?

```text
Target Group Level
```

### Q7. HTTP Traffic을 HTTPS로 Redirect할 수 있는가?

가능하다.

ALB Level에서 Redirect Rule을 구성할 수 있다.