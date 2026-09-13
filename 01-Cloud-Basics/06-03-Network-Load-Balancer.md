# Network Load Balancer (NLB)

## Overview

**Network Load Balancer (NLB)**는
OSI **Layer 4**에서 동작하는 고성능 Load Balancer이다.

```text
NLB
→ Layer 4
→ TCP / UDP
→ Very High Performance
→ Ultra-low Latency
```

ALB와의 핵심 차이:

```text
ALB → Layer 7 → HTTP / HTTPS
NLB → Layer 4 → TCP / UDP
```

---

# 1. NLB Characteristics

NLB는 대량의 Traffic을 매우 낮은 Latency로 처리하는 데 적합하다.

또한 활성화된 **AZ마다 하나의 Static IP**를 가지며,
각 AZ에 **Elastic IP**를 할당할 수도 있다.

```text
AZ-A → Static IP A
AZ-B → Static IP B
AZ-C → Static IP C
```

따라서 다음 요구사항에서 NLB를 고려한다.

```text
TCP / UDP
Extreme Performance
Ultra-low Latency
Static IP / Elastic IP
IP Whitelisting
```

---

# 2. Target Groups

기본 구조는 ALB와 비슷하다.

```text
Client
↓
NLB
↓
Listener
↓
Target Group
↓
Target
```

NLB Target Group의 대표 Target:

```text
EC2 Instances
Private IP Addresses
Application Load Balancer
```

Private IP를 이용하면
AWS 내부 Resource뿐 아니라 연결 가능한
On-Premises Server도 Target으로 사용할 수 있다.

ALB를 Target으로 사용할 수도 있다.

```text
Client
↓
NLB
↓
ALB
↓
EC2
```

이 구조에서는:

```text
NLB → Static IP / Layer 4
ALB → HTTP Layer 7 Routing
```

두 Load Balancer의 특징을 함께 사용할 수 있다.

---

# 3. Health Checks

NLB Target Group은 다음 Health Check Protocol을 지원한다.

```text
TCP
HTTP
HTTPS
```

실제 Traffic이 TCP여도
Backend가 HTTP Application이라면
HTTP Health Check를 사용할 수 있다.

```text
Healthy Target
→ Traffic 전달

Unhealthy Target
→ Traffic 제외
```

---

# 4. Security Groups

NLB에도 Security Group을 연결하여
Traffic을 제어할 수 있다.

Backend EC2에서는 NLB로부터 오는 Traffic을
허용하도록 Security Group을 구성할 수 있다.

```text
Internet
↓
NLB SG
↓
NLB
↓
EC2 SG
↓
EC2
```

EC2 Security Group:

```text
TCP/HTTP :80
Source = NLB Security Group
```

---

# 5. Hands-On Summary

실습에서는 다음 구조를 생성했다.

```text
Client
↓
DemoNLB
↓
TCP :80 Listener
↓
demo-tg-nlb
├─ EC2 A
└─ EC2 B
```

NLB를 여러 AZ에 활성화하고
각 AZ에 Static IPv4가 할당되는 것을 확인했다.

처음에는 EC2 Security Group이
NLB Traffic을 허용하지 않아 Health Check가 실패했다.

```text
NLB
↓
EC2 SG ❌
↓
Unhealthy
```

EC2 Security Group에
NLB Security Group을 허용한 후:

```text
NLB
↓
EC2 SG ✅
↓
Healthy
```

두 EC2가 Healthy 상태가 되었고,
NLB를 통해 두 Instance로 Traffic이 분산되는 것을 확인했다.

---

# Exam Notes

```text
NLB
→ Layer 4
→ TCP / UDP
```

시험 키워드:

```text
Extreme Performance
Ultra-low Latency
Static IP
Elastic IP
IP Whitelisting
→ NLB
```

Target:

```text
EC2
Private IP
ALB
```

Health Check:

```text
TCP
HTTP
HTTPS
```

핵심 비교:

```text
ALB
→ Layer 7
→ HTTP Routing

NLB
→ Layer 4
→ TCP / UDP
→ Static IP
→ High Performance
```

---

# Japanese Summary

**Network Load Balancer (NLB)**は、
Layer 4で動作する高性能なLoad Balancerです。

```text
TCP / UDP
High Performance
Ultra-low Latency
Static IP / Elastic IP
```

Targetとして
EC2、Private IP、ALBを使用できます。

Health Checkは
TCP、HTTP、HTTPSをサポートします。

---

# English Summary

**Network Load Balancer (NLB)** is
a high-performance Layer 4 load balancer.

```text
TCP / UDP
High Performance
Ultra-low Latency
Static IP / Elastic IP
```

Targets can include EC2 instances,
private IP addresses, and an ALB.

Health checks support TCP, HTTP, and HTTPS.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Network Load Balancer | ネットワークロードバランサー | Layer 4 기반 로드 밸런서 |
| Layer 4 | レイヤー4 | OSI 전송 계층 |
| TCP | TCP | 연결 지향 전송 프로토콜 |
| UDP | UDP | 비연결형 전송 프로토콜 |
| Static IP | 固定IP | 변경되지 않는 고정 IP |
| Elastic IP | Elastic IP | AWS에서 할당 가능한 고정 공인 IPv4 |
| Target | ターゲット | Traffic을 전달받는 실제 대상 |
| Target Group | ターゲットグループ | Target들의 논리적 그룹 |
| Health Check | ヘルスチェック | Target의 정상 상태 확인 |
| Latency | レイテンシー | 통신 지연 시간 |
| IP Whitelisting | IPホワイトリスト | 특정 IP만 접근하도록 허용 |

---

# Review Questions

### Q1. NLB는 OSI 몇 계층에서 동작하는가?

```text
Layer 4
```

### Q2. NLB의 대표 Protocol은?

```text
TCP / UDP
```

### Q3. 고정 IP가 필요한 Load Balancer는?

```text
NLB
```

NLB는 AZ별 Static IP를 가지며
Elastic IP도 할당할 수 있다.

### Q4. NLB의 대표 Target은?

```text
EC2
Private IP
ALB
```

### Q5. NLB Health Check가 지원하는 Protocol은?

```text
TCP
HTTP
HTTPS
```

### Q6. NLB Target이 Unhealthy가 되는 원인 중 하나는?

Backend EC2의 Security Group이
NLB의 Traffic 또는 Health Check를 허용하지 않는 경우이다.