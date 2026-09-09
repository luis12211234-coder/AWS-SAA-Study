# Amazon EBS Volumes

## Overview

**EBS (Elastic Block Store)** 는  
EC2 Instance에 연결하여 사용하는 **Block Storage** 서비스이다.

쉽게 생각하면:

```text
EBS
= EC2에 연결하는 Network Drive
= Network USB Stick
```

EC2 Instance와 물리적으로 직접 연결된 Disk가 아니라  
**Network를 통해 EC2와 통신하는 Storage**이다.

```text
EC2 Instance
     │
     │ Network
     ▼
 EBS Volume
```

EBS의 가장 중요한 특징은:

```text
EC2와 Storage의 Lifecycle을 분리할 수 있다.
```

는 것이다.

즉, EC2 Instance가 없어지더라도  
설정에 따라 EBS Volume과 데이터를 유지할 수 있다.

---

# 1. Why Use EBS?

EC2 Instance 내부에서 중요한 데이터를 저장해야 한다고 하자.

```text
EC2
 │
 ▼
Application Data
Database Data
Files
```

이 데이터를 EBS에 저장하면:

```text
EC2-A
 │
 ▼
EBS
 │
 └─ Data
```

EC2-A가 더 이상 필요하지 않을 때  
EBS를 보존하도록 구성할 수 있다.

이후 다른 EC2에 EBS를 연결하면:

```text
EC2-A
  X

EBS
 │
 │ Attach
 ▼
EC2-B
```

기존 데이터를 다시 사용할 수 있다.

따라서 EBS는 **Persistent Storage**로 사용할 수 있다.

---

# 2. EBS Is a Network Drive

EBS는 EC2에 물리적으로 붙어 있는 Disk가 아니다.

```text
EC2
 │
 │ Network
 ▼
EBS
```

즉:

```text
EBS
= Network Drive
```

이다.

Network를 통해 EC2와 통신하기 때문에  
Local Disk와 비교하면 Network Latency가 존재할 수 있다.

하지만 Network Storage이기 때문에  
EC2 Instance와 Storage를 분리해서 관리할 수 있다는 장점이 있다.

---

# 3. Network USB Stick Analogy

EBS를 이해하는 가장 쉬운 비유는:

```text
EBS
= Network USB Stick
```

이다.

일반 USB:

```text
Computer A
   │
  USB
   │
Detach
   │
Attach
   ▼
Computer B
```

EBS:

```text
EC2-A
  │
 EBS
  │
Detach
  │
Attach
  ▼
EC2-B
```

차이점은 EBS가 물리적인 USB가 아니라  
**Network를 통해 연결되는 Virtual Block Storage**라는 것이다.

---

# 4. EBS Can Be Detached and Attached

EBS Volume은 EC2 Instance에서 분리할 수 있다.

```text
EC2-A
 │
 ▼
EBS
```

Detach:

```text
EC2-A

EBS
```

그리고 다른 EC2에 Attach할 수 있다.

```text
EC2-B
 │
 ▼
EBS
```

따라서 기존 Storage를 새로운 EC2 Instance에서  
빠르게 다시 사용할 수 있다.

---

# 5. One EC2 Can Have Multiple EBS Volumes

하나의 EC2 Instance에는  
여러 EBS Volume을 연결할 수 있다.

```text
        EC2
       / | \
      /  |  \
     ▼   ▼   ▼

   EBS  EBS  EBS
   10GB 50GB 100GB
```

즉:

```text
One EC2
→ Multiple EBS Volumes
```

가 가능하다.

필요에 따라 OS, Application Data, Database Data 등을  
서로 다른 Volume으로 구성할 수 있다.

---

# 6. Can One EBS Be Attached to Multiple EC2 Instances?

기본 개념에서는 하나의 EBS Volume을  
한 번에 하나의 EC2 Instance에 연결한다고 이해한다.

```text
EC2-A
 │
 ▼
EBS
```

다음처럼 일반적인 EBS Volume 하나를 동시에 여러 EC2에서  
사용한다고 생각하면 안 된다.

```text
EC2-A ─┐
       ├── EBS   ← 일반적인 경우 X
EC2-B ─┘
```

하지만 이후 배우게 될 **EBS Multi-Attach**라는 예외가 있다.

일부 EBS Volume Type에서는:

```text
io1 / io2
+
Multi-Attach
```

기능을 사용하여 하나의 EBS Volume을  
여러 EC2 Instance에 연결할 수 있다.

따라서 시험에서는 문맥을 확인해야 한다.

```text
일반 EBS
→ 한 EC2에 연결

EBS Multi-Attach
→ 일부 Volume Type에서 여러 EC2 연결 가능
```

---

# 7. EBS Is Bound to an Availability Zone

EBS Volume은 **특정 Availability Zone에 종속된다.**

예:

```text
Region: us-east-1

├─ us-east-1a
│    ├─ EC2-A
│    └─ EBS-A
│
└─ us-east-1b
     └─ EC2-B
```

`us-east-1a`에 생성한 EBS-A는:

```text
EBS-A
↓
EC2-A

O
```

하지만:

```text
us-east-1a             us-east-1b

EBS-A ──────────X────→ EC2-B
```

직접 연결할 수 없다.

핵심:

```text
EBS
→ AZ Scoped Resource
```

---

# 8. Moving EBS Across Availability Zones

EBS Volume 자체를 다른 AZ의 EC2에  
바로 Attach할 수는 없다.

예:

```text
us-east-1a

EBS
 │
 └────X────→ us-east-1b
```

다른 AZ로 데이터를 이동하려면  
**EBS Snapshot**을 사용할 수 있다.

```text
AZ-A

EBS
 │
 ▼
Snapshot
 │
 ▼
Create / Restore Volume
 │
 ▼
EBS

AZ-B
```

즉:

```text
EBS
→ Snapshot
→ New EBS in another AZ
```

이 과정은 이후 EBS Snapshot 강의에서 자세히 다룬다.

---

# 9. EBS and ENI Similarity

앞에서 배운 ENI와 비슷한 부분이 있다.

```text
ENI
→ 특정 AZ에 종속

EBS
→ 특정 AZ에 종속
```

둘 다 같은 AZ 안에서는  
다른 EC2 Instance로 연결 대상을 변경할 수 있다.

하지만 역할은 완전히 다르다.

```text
ENI
= Network Interface
= 가상 랜카드

EBS
= Block Storage
= 네트워크 저장장치
```

시험에서는 둘을 구분한다.

---

# 10. EBS Provisioned Capacity

EBS Volume을 생성할 때  
필요한 Storage 성능과 용량을 설정한다.

주요 요소:

```text
Size
Throughput
IOPS
```

---

## Size

필요한 Storage 크기를 지정한다.

예:

```text
10 GB
50 GB
100 GB
```

---

## IOPS

**IOPS (Input/Output Operations Per Second)** 는  
초당 처리할 수 있는 I/O 작업 수를 의미한다.

쉽게 말하면:

```text
IOPS
= 1초 동안 처리 가능한 Disk I/O 작업 수
```

높은 IOPS가 필요한 대표적인 Workload:

```text
Database
High-performance Application
I/O intensive workload
```

---

## Throughput

Throughput은 일정 시간 동안  
얼마나 많은 데이터를 전송할 수 있는지를 나타낸다.

```text
IOPS
→ 초당 I/O 작업 횟수

Throughput
→ 초당 전송 가능한 데이터 양
```

EBS Volume Type에 따라  
Size, IOPS, Throughput의 특성이 달라진다.

---

# 11. EBS Capacity Can Be Increased

EBS Volume은 처음 생성할 때 용량과 성능을 설정하지만  
필요하면 이후 확장할 수 있다.

```text
Initial

EBS
50 GB

↓

Need More Storage

↓

EBS
100 GB
```

즉:

```text
EBS Capacity
→ Can be increased over time
```

EBS는 Provision한 Storage 및 성능 특성에 따라  
비용이 발생할 수 있다.

---

# 12. EBS Volume Does Not Need to Be Attached

EBS Volume을 생성했다고 해서  
반드시 EC2 Instance에 연결해야 하는 것은 아니다.

```text
EBS
State: Available
```

처럼 EC2에 연결하지 않은 상태로 존재할 수 있다.

필요할 때:

```text
EBS
 │
 │ Attach
 ▼
EC2
```

하여 사용할 수 있다.

즉:

```text
EBS
≠ EC2에 항상 종속된 Storage
```

EBS는 EC2와 별도로 관리할 수 있는 Resource이다.

---

# 13. Delete on Termination

시험에서 중요한 EBS 설정 중 하나가:

```text
Delete on Termination
```

이다.

이 설정은:

```text
EC2 Instance가 Terminate될 때
연결된 EBS Volume도 같이 삭제할 것인가?
```

를 결정한다.

---

# 14. Root EBS Volume

EC2 Instance를 생성하면  
운영체제가 저장된 Root Volume이 존재한다.

예:

```text
EC2
 │
 ├─ Root EBS
 │   └─ OS
 │
 └─ Additional EBS
     └─ Data
```

강의에서 설명하는 기본 설정은:

```text
Root EBS
Delete on Termination = Enabled
```

따라서:

```text
EC2 Terminate
↓
Root EBS Delete
```

된다.

---

# 15. Additional EBS Volumes

추가로 연결한 EBS Volume은 강의의 기본 설정에서:

```text
Delete on Termination = Disabled
```

이다.

따라서:

```text
EC2 Terminate
↓
Additional EBS remains
```

형태가 된다.

정리하면:

```text
Default

Root EBS
→ Delete on Termination = Enabled

Additional EBS
→ Delete on Termination = Disabled
```

---

# 16. Preserving the Root Volume

EC2 Instance를 Terminate하더라도  
Root EBS의 데이터를 보존하고 싶을 수 있다.

예:

```text
EC2
 │
 ▼
Root EBS
 │
 └─ Important Data
```

이때:

```text
Delete on Termination
= Disabled
```

로 설정하면 된다.

그러면:

```text
EC2 Terminate
↓
EC2 Deleted

Root EBS
↓
Preserved
```

된다.

즉:

```text
Need to preserve Root EBS after EC2 termination
↓
Disable Delete on Termination
```

시험에서 나올 수 있는 중요한 시나리오이다.

---

# 17. EBS vs Instance Store

앞의 Hibernate에서 잠깐 등장했던  
Instance Store와 EBS를 구분해두면 좋다.

```text
EBS
= Network Storage

Instance Store
= Local Storage
```

EBS:

```text
EC2
 │
 │ Network
 ▼
EBS
```

Instance Store:

```text
Physical Host
 │
 ├─ EC2
 └─ Instance Store
```

EBS는 Persistent Storage 용도로 사용할 수 있고  
EC2와 별도의 Lifecycle을 가질 수 있다.

Instance Store는 이후 강의에서 자세히 다룬다.

---

# 18. Practical Example

EC2에서 중요한 Application Data를 저장한다고 하자.

```text
EC2-A
 │
 ├─ Root EBS
 │   └─ OS
 │
 └─ Data EBS
     └─ Application Data
```

EC2-A를 더 이상 사용할 필요가 없다.

```text
EC2-A
↓
Terminate
```

설정:

```text
Root EBS
Delete on Termination = Enabled

Data EBS
Delete on Termination = Disabled
```

결과:

```text
EC2-A
→ Deleted

Root EBS
→ Deleted

Data EBS
→ Preserved
```

이후:

```text
EC2-B
 │
 │ Attach
 ▼
Data EBS
```

새로운 EC2에서 기존 데이터를 사용할 수 있다.

---

# 19. Exam Notes

## EBS Definition

```text
EBS
= Elastic Block Store
= Network Block Storage for EC2
```

---

## Persistence

문제에서:

```text
Persist EC2 data
Preserve data after instance termination
Detach storage
Attach storage to another EC2
```

등이 나오면 EBS를 생각한다.

---

## Availability Zone

```text
EBS
→ Specific AZ
```

```text
EBS in AZ-A
+
EC2 in AZ-A
→ Attach O
```

```text
EBS in AZ-A
+
EC2 in AZ-B
→ Attach X
```

다른 AZ로 이동:

```text
Snapshot
→ Restore/Create EBS in target AZ
```

---

## Delete on Termination

매우 중요한 시험 포인트:

```text
Root EBS
→ Delete on Termination
→ Enabled by default
```

```text
Additional EBS
→ Delete on Termination
→ Disabled by default
```

Root Volume을 보존하고 싶다면:

```text
Disable
Delete on Termination
```

---

## EBS Attachment

기본 개념:

```text
One EBS
→ One EC2
```

예외:

```text
EBS Multi-Attach
→ Supported volume types
→ Multiple EC2 instances
```

---

# 20. Summary

EBS는:

```text
Elastic Block Store
```

이며 EC2에서 사용하는 **Network Block Storage**이다.

핵심 특징:

- EC2와 Network를 통해 연결
- Persistent Storage
- EC2에서 Detach 가능
- 다른 EC2에 Attach 가능
- 하나의 EC2에 여러 EBS 연결 가능
- 일반적으로 하나의 EBS는 한 EC2에 연결
- 일부 EBS는 Multi-Attach 지원
- 특정 Availability Zone에 종속
- 다른 AZ로 이동하려면 Snapshot 사용
- Size / IOPS / Throughput 등의 성능 특성을 가짐
- 용량을 이후 증가시킬 수 있음
- EC2에 연결하지 않은 상태로 존재 가능
- Delete on Termination으로 EC2 종료 시 삭제 여부 제어

가장 중요한 암기:

```text
EBS
= Network USB Stick
= Persistent Block Storage
= AZ Scoped
```

그리고:

```text
Root EBS
→ Delete on Termination = ON by default

Additional EBS
→ Delete on Termination = OFF by default
```

---

# Japanese Summary

## Amazon EBS

**EBS (Elastic Block Store)** は、  
EC2 Instanceで使用するNetwork Block Storageです。

簡単に言えば：

```text
EBS
= Network Drive
= Network USB Stick
```

EBSはEC2とは独立して管理でき、  
EC2からDetachして別のEC2へAttachできます。

重要な特徴：

```text
EBS
→ Persistent Storage
→ Availability Zoneに固定
```

例えば：

```text
EBS in us-east-1a
→ EC2 in us-east-1a : O

EBS in us-east-1a
→ EC2 in us-east-1b : X
```

別のAZへ移動する場合は：

```text
EBS
↓
Snapshot
↓
New EBS in another AZ
```

を使用します。

Delete on Terminationの基本設定：

```text
Root EBS
→ Enabled

Additional EBS
→ Disabled
```

---

# English Summary

## Amazon EBS

**Amazon EBS (Elastic Block Store)** provides network block storage for EC2 instances.

Think of an EBS volume as a:

```text
Network USB Stick
```

An EBS volume can be detached from one EC2 instance and attached to another compatible instance.

EBS volumes provide persistent storage and are bound to a specific Availability Zone.

```text
EBS in AZ-A
→ EC2 in AZ-A : Supported

EBS in AZ-A
→ EC2 in AZ-B : Not supported directly
```

To move EBS data across Availability Zones:

```text
EBS
↓
Snapshot
↓
Create / Restore EBS in another AZ
```

Delete on Termination:

```text
Root EBS
→ Enabled by default

Additional EBS
→ Disabled by default
```

---

# Vocabulary

| Term | Meaning |
|---|---|
| EBS | Elastic Block Store |
| Block Storage | 데이터를 Block 단위로 저장하는 Storage |
| Network Drive | Network를 통해 연결되는 Storage |
| Persistent Storage | 시스템 종료 후에도 데이터를 유지하는 Storage |
| Volume | Storage 단위 |
| Attach | Volume을 EC2에 연결 |
| Detach | Volume을 EC2에서 분리 |
| Provisioned Capacity | 미리 설정한 Storage 용량/성능 |
| IOPS | Input/Output Operations Per Second |
| Throughput | 일정 시간 동안 전송할 수 있는 데이터 양 |
| Root Volume | 운영체제가 저장되는 기본 Volume |
| Delete on Termination | EC2 종료 시 EBS 삭제 여부 |
| Snapshot | EBS Volume의 Point-in-Time Backup |
| Multi-Attach | 하나의 EBS Volume을 여러 EC2에 연결하는 기능 |

---

# Review Questions

### Q1. EBS란 무엇인가?

EC2 Instance에 Network를 통해 연결하는  
Persistent Block Storage이다.

### Q2. EBS를 쉽게 비유하면?

```text
Network USB Stick
```

이라고 생각할 수 있다.

### Q3. EC2가 Terminate되면 EBS 데이터도 반드시 삭제되는가?

아니다.

`Delete on Termination` 설정에 따라 달라진다.

### Q4. 하나의 EC2에 여러 EBS Volume을 연결할 수 있는가?

가능하다.

### Q5. 하나의 EBS를 여러 EC2에 동시에 연결할 수 있는가?

일반적인 EBS 사용에서는 하나의 EC2에 연결한다.

일부 지원되는 Volume Type에서는  
EBS Multi-Attach를 사용할 수 있다.

### Q6. EBS는 Availability Zone에 종속되는가?

그렇다.

### Q7. AZ-A의 EBS를 AZ-B의 EC2에 바로 Attach할 수 있는가?

불가능하다.

### Q8. EBS 데이터를 다른 AZ로 이동하려면?

EBS Snapshot을 생성한 후  
대상 AZ에서 새로운 EBS Volume을 생성/복원한다.

### Q9. Root EBS의 Delete on Termination 기본값은?

```text
Enabled
```

이다.

### Q10. 추가 EBS Volume의 Delete on Termination 기본값은?

강의에서 설명한 기본 설정에서는:

```text
Disabled
```

이다.

### Q11. EC2 Termination 후 Root EBS를 보존하려면?

```text
Delete on Termination
→ Disable
```

하면 된다.

### Q12. EBS의 성능을 나타내는 주요 요소는?

```text
Size
IOPS
Throughput
```

이다.