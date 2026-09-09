# EC2 Elastic Network Interfaces (ENI)

## Overview

Elastic Network Interface(ENI)는 VPC 내부에서 사용하는 논리적 Network Component이며,
**Virtual Network Card**를 나타낸다.

```text
Physical Server
→ Network Interface Card (NIC)

AWS EC2
→ Elastic Network Interface (ENI)
```

EC2 Instance는 ENI를 통해 VPC Network에 연결된다.

---

# ENI Basic Structure

EC2 Instance에는 기본적으로 Primary ENI가 연결된다.

Linux 환경에서는 일반적으로 다음과 같이 볼 수 있다.

```text
EC2 Instance

eth0
 ↓
Primary ENI
 ↓
Private IPv4
 ↓
VPC Network
```

추가 ENI를 연결할 수도 있다.

```text
EC2 Instance

├─ eth0
│    ↓
│  ENI-1
│    ↓
│  10.0.1.10
│
└─ eth1
     ↓
   ENI-2
     ↓
   10.0.1.20
```

즉 하나의 EC2 Instance가 여러 Network Interface를 가질 수 있다.

---

# ENI Attributes

ENI는 다음과 같은 Network 속성을 가질 수 있다.

```text
Elastic Network Interface

├─ Primary Private IPv4
├─ Secondary Private IPv4 Address(es)
├─ Public IPv4
├─ Elastic IP
├─ Security Group(s)
└─ MAC Address
```

---

## Private IPv4

ENI에는 하나의 Primary Private IPv4 Address가 존재한다.

추가로 하나 이상의 Secondary Private IPv4 Address를 가질 수도 있다.

```text
ENI

Primary Private IPv4
10.0.1.10

Secondary Private IPv4
10.0.1.11
10.0.1.12
```

---

## Public IPv4 and Elastic IP

ENI는 Public IPv4 또는 Elastic IP와 연관될 수 있다.

강의에서는 Private IPv4 Address와 Public/Elastic IPv4 Address의 관계를 설명한다.

```text
EC2
 ↓
ENI
 ↓
Private IPv4
 ↓
Public IPv4 / Elastic IP
```

Elastic IP는 ENI의 Private IPv4 Address와 연결하여 사용할 수 있다.

---

# Security Groups and ENI

Security Group은 ENI에 연결된다.

```text
EC2
 │
 ▼
ENI
 │
 ├─ Private IP
 ├─ Public / Elastic IP
 └─ Security Group
```

하나의 ENI에는 하나 이상의 Security Group을 연결할 수 있다.

따라서 초반 강의에서:

```text
Security Group
→ EC2에 연결
```

이라고 단순하게 이해했다면,
보다 정확한 구조는 다음과 같다.

```text
Security Group
↓
ENI
↓
EC2
```

---

# Multiple ENIs

EC2 Instance에는 여러 ENI를 연결할 수 있다.

예:

```text
EC2

eth0
↓
ENI-A
↓
10.0.1.10


eth1
↓
ENI-B
↓
10.0.1.20
```

각 ENI는 서로 다른 Network Configuration을 가질 수 있다.

---

# ENI is an Independent Resource

ENI는 EC2 Instance와 독립적으로 생성할 수 있는 VPC Resource이다.

```text
Create ENI
↓
Attach to EC2
```

필요한 경우 ENI를 Instance에서 분리한 후
다른 EC2 Instance에 연결할 수도 있다.

```text
EC2-A
 │
 └─ ENI

      ↓ Detach

     ENI

      ↓ Attach

EC2-B
 │
 └─ ENI
```

이를 이용하여 Network Interface와 관련된 Network Configuration을
다른 Instance로 이동할 수 있다.

---

# ENI Failover

ENI를 다른 EC2 Instance로 이동시키는 기능은
Failover Architecture에 활용할 수 있다.

예를 들어 Application이 특정 Private IP를 사용한다고 가정한다.

```text
Clients
   │
   ▼
10.0.1.50
   │
   ▼
ENI
   │
   ▼
EC2-A
```

EC2-A에 장애가 발생한다.

```text
EC2-A
  X
```

ENI를 다른 Instance로 이동시킨다.

```text
Clients
   │
   ▼
10.0.1.50
   │
   ▼
ENI
   │
   ▼
EC2-B
```

따라서 ENI에 연결된 Private IP를 새로운 EC2 Instance에서 계속 사용할 수 있다.

```text
Same ENI
+
Same Private IP
↓
Different EC2 Instance
```

---

# ENI and Availability Zones

ENI는 **특정 Availability Zone에 종속된다.**

예를 들어 AZ-A에서 생성한 ENI는
AZ-A에 존재하는 EC2 Instance에 연결할 수 있다.

```text
AZ-A

EC2-A
  │
 ENI

  ↓ Move

EC2-B
  │
 ENI
```

하지만 다른 Availability Zone으로 ENI 자체를 이동시킬 수는 없다.

```text
AZ-A                    AZ-B

ENI  ───────────────X──→ EC2
```

즉:

```text
ENI
→ Bound to a specific AZ
```

이다.

---

# ENI vs Elastic IP

Elastic IP와 ENI는 서로 다른 개념이다.

```text
ENI
= Virtual Network Card

Elastic IP
= Static Public IPv4 Address
```

관계는 다음과 같이 이해할 수 있다.

```text
EC2 Instance
     │
     ▼
    ENI
     │
     ├─ Private IPv4
     ├─ Security Group
     │
     └─ Elastic IP
```

즉 Elastic IP는 Network Interface 자체가 아니라
ENI의 Private IPv4와 연결하여 사용하는 Public IPv4 Address이다.

---

# Simple Mental Model

```text
EC2
= Computer

ENI
= Network Card

Private IP
= Internal Network Address

Elastic IP
= Static Public Address

Security Group
= Firewall Rules attached to ENI
```

물리 Server에 비유하면:

```text
Server
 │
 ├─ NIC
 │   ├─ IP Address
 │   └─ Network Configuration
 │
 └─ Operating System
```

AWS에서는:

```text
EC2
 │
 ├─ ENI
 │   ├─ Private IP
 │   ├─ Public / Elastic IP
 │   └─ Security Group
 │
 └─ Operating System
```

으로 생각할 수 있다.

---

# Summary

- ENI는 Elastic Network Interface의 약자이다.
- ENI는 VPC 내부의 논리적 Virtual Network Card이다.
- EC2는 ENI를 통해 Network에 연결된다.
- Primary ENI는 일반적으로 eth0으로 나타난다.
- 추가 ENI는 eth1 등의 Interface로 연결될 수 있다.
- ENI는 Primary Private IPv4를 가진다.
- ENI는 Secondary Private IPv4 Address를 추가로 가질 수 있다.
- Public IPv4 및 Elastic IP와 연관될 수 있다.
- 하나 이상의 Security Group을 ENI에 연결할 수 있다.
- ENI는 EC2와 독립적으로 생성할 수 있다.
- ENI를 다른 EC2 Instance로 이동하여 Failover에 활용할 수 있다.
- ENI는 특정 Availability Zone에 종속된다.
- 따라서 다른 AZ의 EC2로 ENI를 직접 이동할 수 없다.

---

# Exam Notes

## ENI Definition

```text
ENI
=
Virtual Network Card
inside a VPC
```

---

## ENI Attributes

```text
ENI

Primary Private IPv4
Secondary Private IPv4(s)
Public / Elastic IPv4
Security Group(s)
MAC Address
```

---

## Security Group

```text
Security Group
↓
ENI
↓
EC2
```

---

## Failover

문제에서 다음과 같은 요구가 나온다면 ENI를 생각할 수 있다.

```text
Move Network Interface
+
Preserve Private IP
+
Failover between EC2 Instances
↓
ENI
```

---

## Availability Zone

```text
ENI
→ AZ-specific
```

따라서:

```text
Same AZ
EC2-A → EC2-B
→ ENI 이동 가능
```

하지만:

```text
Different AZ
EC2-A → EC2-B
→ ENI 직접 이동 불가
```

---

# Practical Example

두 EC2 Instance가 같은 Availability Zone에 존재한다.

```text
AZ-A

EC2-A
10.0.1.10

EC2-B
10.0.1.20
```

Application에서 고정된 Private IP `10.0.1.50`을 사용하고 싶다고 가정한다.

별도의 ENI를 생성한다.

```text
ENI
Private IP
10.0.1.50
```

이를 EC2-A에 연결한다.

```text
Client
↓
10.0.1.50
↓
ENI
↓
EC2-A
```

EC2-A에 장애가 발생하면 ENI를 EC2-B로 이동한다.

```text
Client
↓
10.0.1.50
↓
ENI
↓
EC2-B
```

Client는 동일한 Private IP를 계속 사용할 수 있다.

---

# 🇯🇵 日本語

## Elastic Network Interface

Elastic Network Interface (ENI)は、
VPC内のVirtual Network Cardを表す論理的なNetwork Componentである。

```text
EC2
↓
ENI
↓
VPC Network
```

ENIは以下の属性を持つことができる。

- Primary Private IPv4
- Secondary Private IPv4
- Public IPv4
- Elastic IP
- Security Group
- MAC Address

---

## Multiple ENIs

一つのEC2 Instanceに複数のENIを接続できる。

```text
EC2

eth0
→ ENI-A

eth1
→ ENI-B
```

---

## ENI Failover

ENIはEC2 Instanceとは独立して作成できる。

同じAvailability Zone内で
ENIを別のEC2 Instanceに移動することができる。

```text
EC2-A
↓
ENI

     ↓ Move

EC2-B
↓
ENI
```

これによりPrivate IPを別のInstanceへ移動し、
Failoverに利用できる。

---

## Availability Zone

ENIは特定のAvailability Zoneに紐付けられる。

```text
ENI
→ AZ Specific
```

そのため、異なるAvailability ZoneのEC2 Instanceへ
ENIを直接移動することはできない。

---

# 🇺🇸 English

## Elastic Network Interface

An Elastic Network Interface (ENI) is a logical networking component in a VPC that represents a virtual network card.

```text
EC2
↓
ENI
↓
VPC Network
```

An ENI can have:

- A primary private IPv4 address
- One or more secondary private IPv4 addresses
- A public IPv4 address
- Elastic IP associations
- One or more Security Groups
- A MAC address

---

## Multiple ENIs

An EC2 instance can have multiple network interfaces.

```text
EC2

eth0
→ ENI-A

eth1
→ ENI-B
```

---

## ENI Failover

An ENI can be created independently from an EC2 instance.

It can be detached and attached to another EC2 instance within the same Availability Zone.

```text
EC2-A
↓
ENI

     ↓ Move

EC2-B
↓
ENI
```

This can be used to move network configuration and private IP addresses during failover.

---

## Availability Zone

An ENI is bound to a specific Availability Zone.

```text
ENI
→ AZ Specific
```

It cannot be directly moved to an EC2 instance in another Availability Zone.

---

# Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Elastic Network Interface | 탄력적 네트워크 인터페이스 | Elastic Network Interface |
| ENI | 탄력적 네트워크 인터페이스 | ENI |
| Network Interface Card | 네트워크 인터페이스 카드 | ネットワークインターフェースカード |
| Primary Private IPv4 | 기본 사설 IPv4 | プライマリプライベートIPv4 |
| Secondary Private IPv4 | 보조 사설 IPv4 | セカンダリプライベートIPv4 |
| MAC Address | MAC 주소 | MACアドレス |
| Security Group | 보안 그룹 | セキュリティグループ |
| Attach | 연결 | アタッチ |
| Detach | 분리 | デタッチ |
| Failover | 장애 조치 | フェイルオーバー |
| Availability Zone | 가용 영역 | アベイラビリティゾーン |

---

# Review Questions

1. ENI란 무엇인가?
2. 실제 Network Hardware에 비유하면 ENI는 무엇에 해당하는가?
3. EC2의 Primary ENI는 Linux에서 일반적으로 어떤 Interface로 나타나는가?
4. 하나의 EC2 Instance에 여러 ENI를 연결할 수 있는가?
5. ENI가 가질 수 있는 주요 Network 속성은 무엇인가?
6. Security Group은 실제로 어떤 Network Resource에 연결되는가?
7. ENI를 EC2 Instance와 독립적으로 생성할 수 있는가?
8. ENI를 다른 EC2 Instance로 이동하는 것이 Failover에 유용한 이유는 무엇인가?
9. ENI를 이동하면 기존 Private IP를 유지할 수 있는 이유는 무엇인가?
10. ENI는 Availability Zone과 어떤 관계가 있는가?
11. AZ-A에서 생성한 ENI를 AZ-B의 EC2 Instance에 직접 연결할 수 있는가?
12. ENI와 Elastic IP의 차이는 무엇인가?