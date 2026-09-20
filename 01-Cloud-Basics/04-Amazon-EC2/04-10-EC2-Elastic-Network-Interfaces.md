# EC2 Elastic Network Interfaces (ENI)

## Overview

**ENI (Elastic Network Interface)** 는  
VPC 내부에서 사용하는 **논리적인 네트워크 인터페이스**이다.

쉽게 말하면:

```text
ENI
= AWS의 가상 랜카드
= Virtual Network Card
```

EC2 Instance가 네트워크와 통신하려면 ENI가 필요하다.

일반적으로 EC2 Instance를 생성하면  
기본 네트워크 인터페이스인 **Primary ENI**가 함께 생성된다.

```text
EC2 Instance
     │
     ▼
Primary ENI (eth0)
     │
     ├─ Private IPv4
     ├─ Public IPv4 / Elastic IP association
     ├─ Security Groups
     └─ MAC Address
```

---

# 1. ENI Attributes

ENI는 다음과 같은 네트워크 속성을 가질 수 있다.

```text
ENI

├─ Primary Private IPv4
├─ Secondary Private IPv4(s)
├─ Public IPv4
├─ Elastic IP association
├─ Security Group(s)
└─ MAC Address
```

즉, ENI는 단순한 IP 주소 하나가 아니라  
**EC2의 네트워크 정체성을 구성하는 하나의 객체**라고 생각하면 된다.

---

# 2. Primary Private IPv4

ENI에는 기본적으로 하나의 **Primary Private IPv4**가 존재한다.

예:

```text
EC2
 │
 ▼
eth0
 │
 ▼
Primary ENI
 │
 └─ 10.0.1.10
```

이 Private IP는 VPC 내부 통신에 사용된다.

---

# 3. Secondary Private IPv4

ENI에는 Primary Private IPv4 외에도  
추가적인 **Secondary Private IPv4**를 할당할 수 있다.

예:

```text
ENI

Primary Private IPv4
10.0.1.10

Secondary Private IPv4
10.0.1.20
10.0.1.30
```

하나의 Network Interface가 여러 Private IP를 사용할 수 있는 것이다.

---

# 4. ENI and Security Groups

Security Group은 EC2 자체에 직접 붙는다고 이해하기 쉽지만,  
기술적으로는 **ENI에 연결된다.**

```text
Security Group
      │
      ▼
     ENI
      │
      ▼
     EC2
```

따라서 하나의 ENI에는 하나 이상의 Security Group을 연결할 수 있다.

```text
ENI

├─ Web-SG
└─ SSH-SG
```

결과적으로 해당 ENI를 통해 들어오고 나가는 트래픽에  
Security Group 규칙이 적용된다.

---

# 5. ENI and Availability Zone

ENI는 **특정 Availability Zone에 종속된다.**

ENI를 생성할 때 Subnet을 선택하기 때문이다.

```text
VPC
 │
 ▼
Subnet
 │
 ▼
Availability Zone
 │
 ▼
ENI
```

Subnet은 하나의 AZ에 속하므로  
그 Subnet에 생성된 ENI 역시 해당 AZ에 속하게 된다.

예:

```text
Availability Zone A

EC2-A
EC2-B
DemoENI

→ ENI 이동 가능
```

반면:

```text
AZ-A                AZ-B

EC2-A               EC2-B
 │
DemoENI ───────X────▶

→ 다른 AZ로 직접 이동 불가
```

따라서 ENI를 EC2 Instance 사이에서 이동시키려면  
**같은 Availability Zone**이어야 한다.

---

# 6. Primary ENI vs Secondary ENI

EC2에는 기본 ENI와 추가 ENI가 존재할 수 있다.

## Primary ENI

EC2 Instance를 생성할 때 기본적으로 생성되는 Network Interface이다.

Linux에서는 일반적으로:

```text
eth0
```

형태로 나타난다.

```text
EC2
 │
 ▼
eth0
 │
 ▼
Primary ENI
 │
 └─ 10.0.1.10
```

Primary ENI는 Instance의 기본 Network Interface이므로  
Instance에서 일반적인 Secondary ENI처럼 Detach할 수 없다.

---

## Secondary ENI

사용자가 추가 ENI를 생성하여 EC2 Instance에 연결할 수도 있다.

예:

```text
EC2 Instance

├─ eth0
│   └─ Primary ENI
│       └─ 10.0.1.10
│
└─ eth1
    └─ Secondary ENI
        └─ 10.0.1.50
```

Secondary ENI는 필요에 따라:

```text
Create
Attach
Detach
Re-Attach
```

할 수 있다.

이 특성을 이용하면 ENI를 다른 EC2 Instance로 이동시킬 수 있다.

---

# 7. Creating an ENI

EC2 Instance와 별도로 ENI를 직접 생성할 수 있다.

AWS Console:

```text
EC2
↓
Network & Security
↓
Network Interfaces
↓
Create Network Interface
```

ENI를 생성할 때 주요 설정은 다음과 같다.

```text
ENI

├─ Description
├─ Subnet
├─ Private IPv4
└─ Security Group
```

예를 들어:

```text
Description
DemoENI

Subnet
Subnet in AZ-A

Private IPv4
Auto-assign

Security Group
launch-wizard-1
```

형태로 생성할 수 있다.

생성이 완료되면:

```text
State
Available
```

상태의 독립적인 Network Interface가 만들어진다.

---

# 8. Attaching an ENI

생성한 ENI는 같은 Availability Zone의 EC2 Instance에 연결할 수 있다.

```text
DemoENI
   │
   │ Attach
   ▼
EC2-A
```

연결 후 EC2의 Networking 정보를 확인하면:

```text
EC2-A

eth0
└─ Primary ENI
   └─ 10.0.1.10

eth1
└─ DemoENI
   └─ 10.0.1.50
```

처럼 여러 Network Interface를 가진 EC2가 된다.

하나의 EC2에 연결할 수 있는 ENI 수는  
**Instance Type에 따라 달라진다.**

---

# 9. Moving an ENI

Secondary ENI는 한 EC2에서 Detach한 후  
같은 AZ의 다른 EC2에 Attach할 수 있다.

처음 상태:

```text
EC2-A
 │
 └─ DemoENI
     └─ 10.0.1.50


EC2-B
 │
 └─ Primary ENI
```

DemoENI를 EC2-A에서 분리한다.

```text
EC2-A

      DemoENI
      10.0.1.50
          │
          │ Detach
          ▼
       Available
```

그리고 EC2-B에 연결한다.

```text
DemoENI
10.0.1.50
    │
    │ Attach
    ▼
EC2-B
```

결과:

```text
EC2-B

├─ Primary ENI
│
└─ DemoENI
    └─ 10.0.1.50
```

즉, 단순히 설정을 다시 만드는 것이 아니라  
**Network Interface 자체를 이동시킬 수 있다.**

---

# 10. ENI Failover

ENI의 중요한 활용 사례 중 하나가 **Failover**이다.

예를 들어 동일한 애플리케이션을 실행하는 EC2가 두 개 있다고 하자.

```text
Client
  │
  ▼
10.0.1.50
  │
  ▼
DemoENI
  │
  ▼
EC2-A
```

EC2-A에 장애가 발생하면  
Secondary ENI를 EC2-A에서 분리하고 EC2-B에 연결할 수 있다.

```text
EC2-A
  X

DemoENI
10.0.1.50
    │
    ▼
EC2-B
```

이렇게 하면 DemoENI가 가지고 있던  
Private IP와 Network Interface 구성을 새로운 EC2에서 사용할 수 있다.

핵심:

```text
EC2-A Failure
↓
Detach Secondary ENI
↓
Attach ENI to EC2-B
↓
Private IP / Network Identity 이동
↓
Failover
```

---

# 11. ENI Lifecycle

ENI는 EC2와 별도로 생성할 수 있는  
**독립적인 VPC Resource**이다.

따라서 EC2 Instance와 ENI의 Lifecycle이  
항상 완전히 동일한 것은 아니다.

강의 실습에서는:

```text
EC2 생성
↓
Primary ENI 자동 생성
↓
EC2 Terminate
↓
Primary ENI 삭제
```

되는 모습을 확인했다.

반면 사용자가 별도로 생성한 DemoENI는:

```text
Create DemoENI
↓
Attach to EC2
↓
EC2 Terminate
↓
DemoENI Remains
```

형태로 유지될 수 있다.

중요한 설정:

```text
Delete on termination
```

이 설정은 EC2 Instance가 종료될 때  
연결된 ENI를 함께 삭제할 것인지 결정한다.

```text
Delete on termination = Enabled

EC2 Terminate
↓
ENI Delete
```

```text
Delete on termination = Disabled

EC2 Terminate
↓
ENI Remains
```

따라서:

```text
직접 만든 ENI = 무조건 유지
```

라고 외우기보다는

```text
ENI는 EC2와 독립적인 Lifecycle을 가질 수 있다.
삭제 여부는 Delete on termination 설정과 관련된다.
```

라고 이해하는 것이 정확하다.

---

# 12. Force Detach

Secondary ENI가 정상적으로 Detach되지 않는 경우  
Force Detach 기능을 사용할 수 있다.

```text
Normal Detach
→ Preferred

Force Detach
→ Last Resort
```

Force Detach는 정상적인 분리가 실패했을 때 사용하는  
예외적인 방법으로 이해하면 된다.

일반적인 상황에서는 정상적인 Detach를 우선 사용한다.

---

# 13. ENI vs Elastic IP

ENI와 Elastic IP는 서로 다른 개념이다.

## ENI

```text
ENI
= Virtual Network Card
= 가상 랜카드
```

ENI는 다음과 같은 네트워크 정보를 가진다.

```text
Private IP
Security Groups
MAC Address
Public IPv4 / Elastic IP association
```

---

## Elastic IP

```text
Elastic IP
= Static Public IPv4
= 고정 공용 IPv4 주소
```

즉:

```text
ENI
→ 네트워크 인터페이스

Elastic IP
→ IP 주소
```

둘의 차이를 단순하게 기억하면:

```text
ENI
= 랜카드

Elastic IP
= 고정 공용 IP 주소
```

시험에서:

```text
Static Public IPv4
↓
Elastic IP
```

```text
Virtual Network Card
Private IP
Security Group
Network Interface Failover
↓
ENI
```

---

# 14. ENI Hands-On Flow

이번 실습의 전체 흐름:

```text
Launch EC2-A
Launch EC2-B
↓
각 EC2에 Primary ENI 생성
↓
Network Interfaces 확인
↓
Create DemoENI
↓
Select Subnet
↓
Private IPv4 Auto-assign
↓
Select Security Group
↓
Create ENI
↓
DemoENI = Available
↓
Attach DemoENI to EC2-A
↓
EC2-A now has two ENIs
↓
Detach DemoENI
↓
Attach DemoENI to EC2-B
↓
Same ENI / Private IP moves to EC2-B
↓
EC2 Termination
↓
ENI Lifecycle 확인
```

이 실습의 목적은 단순히 콘솔 사용법을 익히는 것이 아니라:

```text
ENI는 EC2에 종속된 단순 설정값이 아니라
독립적으로 생성하고 이동시킬 수 있는
Network Resource이다.
```

라는 점을 이해하는 것이다.

---

# 15. Practical Example

두 개의 EC2가 같은 애플리케이션을 실행한다고 가정한다.

```text
EC2-A
Application Server

EC2-B
Application Server
```

서비스 내부에서 특정 Private IP를 사용해야 한다.

```text
10.0.1.50
```

이 Private IP를 Secondary ENI에 할당한다.

정상 상태:

```text
10.0.1.50
    │
    ▼
DemoENI
    │
    ▼
EC2-A
```

EC2-A 장애:

```text
EC2-A
  X
```

ENI 이동:

```text
DemoENI
10.0.1.50
    │
    ▼
EC2-B
```

이렇게 하면 새로운 EC2에서도  
동일한 ENI와 Private IP를 사용할 수 있다.

---

# 16. Exam Notes

## ENI Definition

```text
ENI
= Elastic Network Interface
= Logical component in a VPC
= Virtual Network Card
```

---

## ENI Attributes

```text
Primary Private IPv4
Secondary Private IPv4(s)
Public IPv4
Elastic IP association
Security Group(s)
MAC Address
```

---

## Availability Zone

```text
ENI
→ Bound to a specific AZ
```

```text
Same AZ
→ ENI 이동 가능

Different AZ
→ ENI 직접 이동 불가
```

---

## Primary vs Secondary

```text
Primary ENI
→ EC2 기본 Network Interface
→ 일반적으로 eth0
→ Detach 불가
```

```text
Secondary ENI
→ 추가 연결 가능
→ Detach 가능
→ 같은 AZ의 다른 EC2에 Attach 가능
```

---

## Failover

문제에서 다음 표현이 나오면 ENI를 떠올린다.

```text
Move a network interface
Preserve a private IP
Network interface failover
Move network configuration between EC2 instances
Same Availability Zone
```

정답 후보:

```text
Elastic Network Interface (ENI)
```

---

## ENI vs Elastic IP

```text
고정 Public IPv4
→ Elastic IP

가상 Network Interface
→ ENI
```

```text
Elastic IP
= 주소

ENI
= 랜카드
```

---

# 17. Summary

```text
ENI
= AWS Virtual Network Card
```

ENI는:

- VPC 내부의 Logical Network Interface
- Primary / Secondary Private IPv4를 가질 수 있음
- Public IPv4 / Elastic IP와 연결될 수 있음
- Security Group을 가질 수 있음
- MAC Address를 가짐
- EC2와 독립적으로 생성 가능
- Secondary ENI는 Detach / Attach 가능
- 같은 AZ의 다른 EC2로 이동 가능
- Failover에 활용 가능
- 특정 Availability Zone에 종속됨
- Delete on termination에 따라 EC2 종료 시 삭제 여부가 달라질 수 있음

가장 중요한 그림:

```text
EC2-A
 │
 └─ ENI
     └─ Private IP

       ↓ Move

EC2-B
 │
 └─ ENI
     └─ Same Private IP
```

한 줄 암기:

```text
ENI = 이동 가능한 AWS 가상 랜카드
```

---

# Japanese Summary

## Elastic Network Interface (ENI)

ENI（Elastic Network Interface）は、  
VPC内で使用される**仮想ネットワークインターフェース**です。

簡単に言えば：

```text
ENI
= AWSの仮想NIC
```

ENIは以下の情報を持つことができます。

- Primary Private IPv4
- Secondary Private IPv4
- Public IPv4
- Elastic IP
- Security Groups
- MAC Address

ENIはEC2とは別に作成することができます。

Secondary ENIはEC2からDetachし、  
同じAvailability Zoneにある別のEC2へAttachできます。

```text
EC2-A
 │
 ENI
 │
Private IP

↓

EC2-B
 │
Same ENI
 │
Same Private IP
```

そのため、ENIはNetwork Failoverにも利用できます。

重要：

```text
ENI
= 仮想ネットワークカード

Elastic IP
= 固定Public IPv4
```

---

# English Summary

## Elastic Network Interface (ENI)

An **Elastic Network Interface (ENI)** is a logical networking component in a VPC.

It can be considered a:

```text
Virtual Network Card
```

An ENI can have:

- A primary private IPv4 address
- One or more secondary private IPv4 addresses
- A public IPv4 address
- Elastic IP associations
- One or more Security Groups
- A MAC address

ENIs can be created independently from EC2 instances.

A secondary ENI can be detached from one EC2 instance and attached to another instance in the **same Availability Zone**.

This makes ENIs useful for network failover.

```text
EC2-A
 │
 ENI
 │
Private IP

↓

EC2-B
 │
Same ENI
 │
Same Private IP
```

Key difference:

```text
ENI
= Virtual Network Interface

Elastic IP
= Static Public IPv4 Address
```

---

# Vocabulary

| Term | Meaning |
|---|---|
| ENI | Elastic Network Interface |
| Network Interface | 네트워크 인터페이스 |
| Virtual Network Card | 가상 랜카드 |
| Primary ENI | EC2의 기본 네트워크 인터페이스 |
| Secondary ENI | EC2에 추가로 연결하는 네트워크 인터페이스 |
| Primary Private IPv4 | ENI의 기본 Private IPv4 |
| Secondary Private IPv4 | ENI에 추가로 할당된 Private IPv4 |
| Attach | ENI를 EC2에 연결 |
| Detach | ENI를 EC2에서 분리 |
| Force Detach | ENI 강제 분리 |
| MAC Address | 네트워크 인터페이스의 MAC 주소 |
| Failover | 장애 발생 시 다른 시스템으로 전환 |
| Delete on termination | EC2 종료 시 연결된 ENI 삭제 여부 설정 |
| Availability Zone | Region 내부의 독립적인 인프라 영역 |

---

# Review Questions

### Q1. ENI란 무엇인가?

VPC 내부에서 사용하는 논리적인 Network Interface이며,  
쉽게 말하면 AWS의 Virtual Network Card이다.

### Q2. ENI가 가질 수 있는 주요 속성은?

Primary / Secondary Private IPv4, Public IPv4, Elastic IP association,  
Security Groups, MAC Address 등이 있다.

### Q3. ENI는 EC2와 별도로 생성할 수 있는가?

가능하다.

### Q4. Secondary ENI를 다른 EC2로 이동할 수 있는가?

가능하다.  
단, 대상 EC2가 **같은 Availability Zone**에 있어야 한다.

### Q5. Primary ENI도 다른 EC2로 이동할 수 있는가?

Primary ENI는 일반적인 Secondary ENI처럼 Detach할 수 없다.

### Q6. ENI를 다른 EC2로 이동하면 Private IP도 같이 이동하는가?

ENI에 할당된 Private IP가 ENI와 함께 사용되므로  
같은 Private IP 기반의 Network Identity를 다른 EC2에서 사용할 수 있다.

### Q7. ENI를 Failover에 사용할 수 있는 이유는?

Secondary ENI를 장애가 발생한 EC2에서 분리하고  
같은 AZ의 다른 EC2에 연결할 수 있기 때문이다.

### Q8. ENI와 Elastic IP의 차이는?

```text
ENI
= Virtual Network Card

Elastic IP
= Static Public IPv4 Address
```

### Q9. ENI는 Availability Zone과 어떤 관계가 있는가?

ENI는 특정 Subnet에 생성되고,  
Subnet은 특정 AZ에 속하기 때문에 ENI 역시 특정 AZ에 종속된다.

### Q10. EC2가 종료되면 ENI도 반드시 삭제되는가?

항상 그런 것은 아니다.

ENI의 연결 및 `Delete on termination` 설정에 따라  
EC2 종료 후 ENI를 유지하도록 구성할 수 있다.
