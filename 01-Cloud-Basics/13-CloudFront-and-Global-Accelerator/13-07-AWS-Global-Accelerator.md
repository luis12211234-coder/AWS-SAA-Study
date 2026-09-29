# 13-07. AWS Global Accelerator

## 🇰🇷 1. Global Accelerator란?

AWS Global Accelerator는 AWS Global Network를 이용하여 전 세계 사용자의 Traffic을 Application Endpoint로 빠르고 안정적으로 전달하는 서비스입니다.

```text
User
 ↓
Nearest AWS Edge
 ↓
AWS Global Network
 ↓
Application
```

핵심은 사용자의 Traffic을 가능한 빨리 AWS Network에 진입시키는 것입니다.

---

## 2. 해결하려는 문제

Application은 특정 AWS Region에 있지만 사용자는 전 세계에 있을 수 있습니다.

```text
USA User ───────┐
Europe User ────┼─→ Public Internet → Application
Australia User ─┘
```

Public Internet에서는 여러 Network Hop을 거치면서 Latency와 Network 변동성이 발생할 수 있습니다.

Global Accelerator를 사용하면:

```text
User
 ↓
Nearby AWS Edge
 ↓
AWS Global Network
 ↓
Application
```

구조를 사용할 수 있습니다.

---

## 3. Unicast vs Anycast

### Unicast

일반적인 IP 방식입니다.

```text
Server A
IP A

Server B
IP B
```

각 목적지마다 다른 IP를 사용합니다.

### Anycast

여러 위치에서 동일한 IP를 제공하고 사용자는 네트워크상 가까운 위치로 Routing됩니다.

```text
           Same IP
          /       \
      Edge A     Edge B
```

Global Accelerator는 Anycast를 사용합니다.

여기서 Edge는 우리가 만든 EC2가 아니라 AWS가 운영하는 글로벌 Edge Infrastructure입니다.

---

## 4. Static Anycast IP × 2

Global Accelerator는 두 개의 Static Anycast IPv4 주소를 제공합니다.

```text
Static Anycast IP #1 🚪
Static Anycast IP #2 🚪
          │
          ↓
Global Accelerator
```

두 IP가 각각 미국 서버와 인도 서버를 의미하는 것은 아닙니다.

둘 다 같은 Accelerator로 들어가는 고정된 글로벌 진입점입니다.

쉽게 보면:

> **같은 건물로 들어가는 고정된 문 두 개**

입니다.

---

## 5. 전체 구조

```text
User
 ↓
Static Anycast IP × 2
 ↓
Nearest AWS Edge
 ↓
AWS Global Network
 ↓
Listener
 ↓
Endpoint Group
 ↓
Endpoint
```

---

## 6. Listener

Listener는 Global Accelerator가 받을 Traffic의 Port와 Protocol을 정의합니다.

강의 실습:

```text
Protocol = TCP
Port = 80
```

즉:

```text
"TCP 80으로 들어오는 Traffic을 받겠다."
```

라는 설정입니다.

---

## 7. Endpoint Group

Endpoint Group은 특정 AWS Region에 있는 Endpoint들을 묶는 단위입니다.

```text
Global Accelerator
       │
     Listener
       │
 ┌─────┴──────────┐
 ↓                ↓
Endpoint Group   Endpoint Group
us-east-1        ap-south-1
 ↓                ↓
EC2              EC2
```

즉:

```text
Endpoint Group
= Region 단위
```

강의 실습에서는:

```text
us-east-1
└─ EC2 Web Server

ap-south-1
└─ EC2 Web Server
```

를 각각 하나의 Endpoint Group으로 구성했습니다.

---

## 8. Endpoint

Endpoint는 실제 Application Traffic이 최종적으로 전달되는 Resource입니다.

대표적으로:

```text
EC2
ALB
NLB
Elastic IP
```

를 사용할 수 있습니다.

예:

```text
Endpoint Group
Region = us-east-1
       ↓
EC2 Endpoint
```

---

## 9. Traffic Dial과 Endpoint Weight

Traffic Dial은 Endpoint Group, 즉 Region으로 보낼 Traffic의 비율을 조절합니다.

```text
Traffic Dial
→ Endpoint Group / Region 수준
```

Endpoint Weight는 같은 Endpoint Group 내부에서 Endpoint 사이의 Traffic 분배를 조절합니다.

```text
Weight
→ 개별 Endpoint 수준
```

구분:

```text
Traffic Dial
= Region 단위 조절

Endpoint Weight
= 같은 Region 내부 Resource 단위 조절
```

---

## 10. Health Check와 Failover

Global Accelerator는 Endpoint의 상태를 확인합니다.

```text
Endpoint A 🟢 Healthy
Endpoint B 🔴 Unhealthy
```

문제가 발생하면 정상 Endpoint로 Traffic을 전달할 수 있습니다.

```text
User
 ↓
Global Accelerator
 ↓
Healthy Endpoint
```

강의 실습에서도 인도 EC2의 HTTP 접근을 차단하자 Health Check가 실패했고 이후 Traffic이 정상 상태인 미국 EC2로 전달되었습니다.

따라서 Disaster Recovery와 Multi-Region Failover에 활용할 수 있습니다.

---

## 11. Global Accelerator가 하지 않는 것

이 부분도 중요합니다.

Global Accelerator가 미국과 인도의 EC2를 자동으로 만들어주거나 동기화해 주는 것은 아닙니다.

```text
Global Accelerator

O Traffic Routing
O Network Acceleration
O Health Check
O Failover

X EC2 Synchronization
X Application Deployment
X Database Replication
X Data Synchronization
```

각 Region의 Application과 Data는 별도로 구축하고 관리해야 합니다.

Global Accelerator는 그 Application으로 가는 **Traffic 경로**를 담당합니다.

---

## 12. 대표 사용 사례

Cache가 아니라 Network Acceleration이 필요한 Application에 적합합니다.

```text
Gaming
IoT
VoIP
TCP / UDP Application
Global Static IP Requirement
Multi-Region Failover
```

---

## 🔑 핵심 정리

```text
Global Accelerator
= Network Acceleration

Static Anycast IP × 2
        ↓
Nearest AWS Edge
        ↓
AWS Global Network
        ↓
Endpoint
```

```text
Endpoint Group
= Region 단위

Endpoint
= EC2 / ALB / NLB / Elastic IP

Health Check
→ Failover

Cache
→ 없음
```

한 줄 정리:

> **고정 Anycast IP를 통해 사용자를 가까운 AWS Edge로 진입시키고 AWS Global Network로 Application까지 전달한다 = Global Accelerator**

---

## 🇯🇵 日本語 Summary

AWS Global Acceleratorは、Static Anycast IPとAWS Global Networkを利用して、世界中のユーザーTrafficをApplication Endpointへ高速かつ安定してRoutingするサービスです。

Endpoint GroupはRegion単位で構成され、その中にEC2、ALB、NLB、Elastic IPなどのEndpointを配置できます。

Health Checkによって異常なEndpointを検出し、正常なEndpointへFailoverできます。

Global AcceleratorはApplicationやDataを同期するサービスではありません。

---

## 🇺🇸 English Summary

AWS Global Accelerator improves global application network performance using static Anycast IP addresses and the AWS Global Network.

Traffic enters AWS through a nearby Edge Location and is routed to application endpoints.

Endpoint groups are Region-based and can contain endpoints such as EC2 instances, ALBs, NLBs, and Elastic IP addresses.

Health checks enable failover to healthy endpoints.

Global Accelerator does not synchronize applications or data across Regions.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Global Accelerator | グローバルアクセラレーター | 글로벌 액셀러레이터 |
| Anycast | エニーキャスト | 애니캐스트 |
| Unicast | ユニキャスト | 유니캐스트 |
| Static IP | 固定IP | 고정 IP |
| Listener | リスナー | 리스너 |
| Endpoint Group | エンドポイントグループ | 엔드포인트 그룹 |
| Endpoint | エンドポイント | 엔드포인트 |
| Traffic Dial | トラフィックダイヤル | 트래픽 다이얼 |
| Weight | 重み | 가중치 |
| Health Check | ヘルスチェック | 상태 확인 |
| Failover | フェイルオーバー | 장애 조치 |

---

## 📝 Review Questions

<details>
<summary>Q1. Global Accelerator가 사용하는 IP 방식은?</summary>

Static Anycast IP입니다.

</details>

<details>
<summary>Q2. 두 개의 Static Anycast IP가 각각 다른 Region을 의미하는가?</summary>

아닙니다. 둘 다 동일한 Global Accelerator로 들어가는 고정된 글로벌 진입점입니다.

</details>

<details>
<summary>Q3. Endpoint Group은 무엇을 기준으로 구성되는가?</summary>

AWS Region을 기준으로 구성됩니다.

</details>

<details>
<summary>Q4. Endpoint로 사용할 수 있는 대표 Resource는?</summary>

EC2, ALB, NLB, Elastic IP 등이 있습니다.

</details>

<details>
<summary>Q5. Endpoint가 비정상이 되면?</summary>

Health Check를 기반으로 정상 Endpoint로 Traffic을 Failover할 수 있습니다.

</details>

<details>
<summary>Q6. Global Accelerator가 Multi-Region EC2와 Data를 자동 동기화하는가?</summary>

아닙니다. Global Accelerator는 Network Routing과 Acceleration을 담당합니다.

</details>
