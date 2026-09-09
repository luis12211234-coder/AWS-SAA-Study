# EC2 Hibernate

## Overview

EC2 Instance는 일반적으로 다음과 같은 상태 전환을 사용할 수 있다.

```text
Running
↓
Stop
↓
Stopped
↓
Start
↓
Running
```

또는:

```text
Running
↓
Terminate
↓
Deleted
```

EC2에는 이와 별도로 **Hibernate(절전 모드)** 기능이 있다.

```text
Hibernate
= RAM 상태까지 저장하는 EC2 절전 모드
```

쉽게 말하면:

```text
Stop
→ Disk 상태 보존

Hibernate
→ Disk + RAM 상태 보존
```

---

# 1. Normal Stop

EC2 Instance를 Stop하면:

```text
EC2 Running
↓
Stop
↓
RAM 내용 소실
↓
EBS 데이터 유지
↓
Stopped
```

다시 Start하면:

```text
Start
↓
OS Boot
↓
Application Start
↓
Cache Warm-up
↓
Service Ready
```

즉, EBS에 저장된 데이터는 유지되지만  
**RAM에 있던 실행 상태는 사라진다.**

따라서 애플리케이션 초기화나 Cache 구성에 시간이 오래 걸리는 경우  
서비스가 완전히 준비되기까지 시간이 걸릴 수 있다.

---

# 2. EC2 Hibernate

Hibernate를 사용하면  
EC2 Instance의 **RAM 상태를 보존**할 수 있다.

```text
Running EC2

RAM
├─ Running Processes
├─ Application State
├─ Cache
└─ In-memory Data

        ↓ Hibernate

Encrypted Root EBS
└─ RAM Contents Saved
```

Hibernate 과정:

```text
EC2 Running
↓
RAM contents
↓
Root EBS Volume에 저장
↓
Instance Stopped
```

다시 Instance를 Start하면:

```text
Root EBS
↓
Saved RAM contents
↓
RAM으로 복원
↓
Previous Processes Resume
↓
Running
```

즉, 처음부터 모든 것을 다시 초기화하는 것이 아니라  
**Hibernate 이전의 메모리 상태를 복원한다.**

---

# 3. Stop vs Hibernate

## Stop

```text
Running
↓
Stop
↓
RAM Lost
↓
EBS Preserved
```

다시 실행:

```text
Start
↓
OS Boot
↓
Application Start
↓
Cache Warm-up
```

---

## Hibernate

```text
Running
↓
Hibernate
↓
RAM → Root EBS
↓
Stopped
```

다시 실행:

```text
Start
↓
RAM ← Root EBS
↓
Previous Processes Resume
```

핵심 차이:

| Feature | Stop | Hibernate |
|---|---|---|
| EBS 데이터 유지 | O | O |
| RAM 상태 유지 | X | O |
| OS 상태 복원 | X | O |
| 실행 중 Process 복원 | X | O |
| Application 재초기화 | 필요 | 대부분 불필요 |
| 빠른 복귀 | 일반적 | 가능 |

---

# 4. How Hibernate Works

Hibernate의 핵심 구조는:

```text
EC2 Instance
     │
     │ Hibernate
     ▼

RAM
 │
 │ Save
 ▼

Encrypted Root EBS
```

EC2 Instance가 Hibernate되면  
RAM의 내용을 **Root EBS Volume**에 저장한다.

그리고 Instance를 다시 시작하면:

```text
Encrypted Root EBS
        │
        │ Restore
        ▼
       RAM
        │
        ▼
Previous Running State
```

저장했던 RAM 상태를 다시 Memory로 불러온다.

---

# 5. Why Root EBS Must Be Encrypted

RAM에는 다음과 같은 중요한 정보가 존재할 수 있다.

```text
Application State
Cache
Credentials
Session Data
Sensitive Data
```

Hibernate에서는 이러한 RAM 내용을  
Root EBS Volume에 저장한다.

따라서 Root EBS Volume은 반드시:

```text
Encrypted
```

상태여야 한다.

```text
RAM
 │
 ▼
Encrypted Root EBS
```

이를 통해 Disk에 저장된 Memory 내용을 보호한다.

---

# 6. Root Volume Requirements

Hibernate를 사용하려면 Root Volume은:

```text
EBS Volume
+
Encrypted
+
Enough Storage Capacity
```

이어야 한다.

Root Volume에는:

```text
Operating System
Applications
Data
RAM Dump
```

가 함께 저장될 수 있으므로  
RAM 내용을 저장할 수 있을 만큼 충분한 공간이 필요하다.

예:

```text
EC2 RAM
16 GiB

↓

Root EBS

OS
+
Applications
+
16 GiB RAM Dump를 저장할 충분한 공간
```

---

# 7. Hibernate Use Cases

Hibernate는 특히 다음과 같은 상황에서 유용하다.

## Long-running Processes

오랫동안 실행되는 Process의 상태를 유지하고 싶은 경우:

```text
Long-running Process
↓
Hibernate
↓
RAM State Saved
↓
Resume Later
```

---

## Saving RAM State

Memory에 많은 상태를 유지하는 Application:

```text
Application
↓
Large In-memory State
↓
Hibernate
↓
State Preserved
```

---

## Long Application Initialization

Application 초기화에 시간이 오래 걸리는 경우:

```text
Normal Start

OS Boot
↓
Application Start
↓
Load Data
↓
Build Cache
↓
Initialize Services
↓
Ready
```

Hibernate를 사용하면:

```text
Resume
↓
RAM Restore
↓
Previous State
```

기존 상태를 복원할 수 있다.

---

## Pre-warmed Applications

Application을 미리 실행하고 Cache나 Memory 상태를 준비한 뒤  
Hibernate해 둘 수도 있다.

```text
Launch EC2
↓
Start Application
↓
Warm Cache
↓
Initialize Services
↓
Hibernate
```

필요할 때:

```text
Start
↓
Restore RAM
↓
Service Ready Faster
```

---

# 8. Hibernate Requirements

EC2 Hibernate를 사용하려면 여러 조건을 만족해야 한다.

## Supported Instance Families

Hibernate를 지원하는 Instance Family를 사용해야 한다.

지원되는 Instance Family는 AWS에 따라 변경될 수 있으므로  
정확한 Family 목록을 암기할 필요는 없다.

시험에서는 주로:

```text
Hibernate requires supported EC2 instance types
```

정도로 이해하면 충분하다.

---

## Bare Metal

Bare Metal Instance는 Hibernate를 지원하지 않는다.

```text
Bare Metal
→ Hibernate X
```

---

## RAM Limit

강의와 AWS 문서 기준:

```text
Linux
RAM < 150 GiB
```

Windows에는 별도의 제한이 있다.

정확한 용량 제한은 변경될 수 있으므로  
시험에서는 Hibernate의 구조와 요구 조건을 우선적으로 이해한다.

---

# 9. Supported Operating Systems

Hibernate는 지원되는 AMI / OS에서 사용할 수 있다.

예:

```text
Amazon Linux
Linux
Ubuntu
RHEL
Windows
```

모든 AMI에서 무조건 지원되는 것은 아니며  
Hibernate를 지원하는 AMI를 사용해야 한다.

---

# 10. Purchasing Options

Hibernate는 지원 조건을 만족하는 경우  
다양한 EC2 구매 옵션에서 사용할 수 있다.

강의/PDF에서는:

```text
On-Demand
Reserved
Spot
```

에서 사용할 수 있다고 설명한다.

Spot Instance의 경우 Hibernate 동작에는  
Spot Capacity 및 Spot interruption 조건이 추가로 영향을 줄 수 있다.

---

# 11. Maximum Hibernation Duration

EC2 Instance를 Hibernate 상태로  
무기한 유지할 수 있는 것은 아니다.

현재 강의 및 AWS 문서 기준:

```text
Maximum Hibernation Period
= 60 days
```

60일보다 오래 유지해야 한다면  
Instance를 다시 Start한 뒤 적절한 상태 전환이 필요하다.

---

# 12. Hibernate vs Terminate

Terminate는 Hibernate와 완전히 다른 개념이다.

## Hibernate

```text
EC2
↓
RAM saved to EBS
↓
Stopped
↓
Can Start Again
```

---

## Terminate

```text
EC2
↓
Terminate
↓
Instance Deleted
```

Root EBS Volume의:

```text
Delete on Termination
```

설정이 활성화되어 있다면  
Instance 종료 시 Root Volume도 삭제된다.

따라서:

```text
Hibernate
≠ Terminate
```

이다.

---

# 13. Stop vs Hibernate vs Terminate

| State | EBS | RAM | 다시 시작 | Instance 유지 |
|---|---|---|---|---|
| Stop | 유지 | 삭제 | O | O |
| Hibernate | 유지 | EBS에 저장 | O | O |
| Terminate | 설정에 따라 삭제 | 삭제 | X | X |

핵심:

```text
Stop
= Disk만 보존

Hibernate
= Disk + RAM 보존

Terminate
= Instance 제거
```

---

# 14. Important Detail: User Data

EC2 Instance를 처음 생성할 때는:

```text
Launch
↓
OS Boot
↓
EC2 User Data
↓
Application Start
```

과정이 진행된다.

일반적인 이후 Start에서는  
OS가 다시 Boot되지만 최초 Launch와 동일한 User Data 실행이  
항상 반복되는 것은 아니다.

Hibernate에서는 OS와 Process의 Memory 상태를 복원하므로  
일반적인 Stop → Start와 다른 방식으로 빠르게 복귀할 수 있다.

---

# 15. Practical Example

초기화에 시간이 오래 걸리는 Application이 있다고 가정한다.

```text
EC2
↓
OS Boot
↓
Application Start
↓
Load Dataset
↓
Create Cache
↓
Initialize Services
↓
Ready
```

전체 준비에 10분이 걸린다고 하자.

Normal Stop 후 Start:

```text
Stop
↓
RAM Lost
↓
Start
↓
Initialization Again
↓
10 minutes
```

Hibernate 사용:

```text
Application Ready
↓
Hibernate
↓
RAM saved to EBS
↓
Start
↓
RAM restored
↓
Previous Application State Resumed
```

따라서 Application이  
처음부터 모든 Memory 상태를 다시 구축하는 시간을 줄일 수 있다.

---

# 16. Exam Notes

문제에서 다음과 같은 표현이 나오면  
**EC2 Hibernate**를 의심한다.

```text
Preserve RAM
Preserve in-memory state
Resume running processes
Long application initialization
Long-running process
Faster resume
Save memory state
```

정답:

```text
EC2 Hibernate
```

---

## Root Volume

Hibernate 문제에서 매우 중요한 조건:

```text
Root Volume
→ EBS
→ Encrypted
→ Large enough to store RAM
```

---

## Quick Comparison

```text
Need to preserve disk only
↓
Stop
```

```text
Need to preserve RAM / in-memory state
↓
Hibernate
```

```text
No longer need the EC2 instance
↓
Terminate
```

---

# 17. Summary

```text
EC2 Hibernate
= RAM 상태를 Root EBS에 저장하는 절전 모드
```

Hibernate 과정:

```text
Running
↓
RAM → Encrypted Root EBS
↓
Stopped
↓
Start
↓
RAM ← Root EBS
↓
Previous State Resumed
```

주요 특징:

- RAM의 In-memory State 보존
- 실행 중 Process 상태 복원 가능
- Application 초기화 시간을 줄일 수 있음
- Root Volume은 EBS여야 함
- Root EBS는 암호화 필수
- RAM Dump를 저장할 충분한 EBS 공간 필요
- Bare Metal Instance는 지원하지 않음
- 지원되는 Instance Type / AMI 필요
- On-Demand / Reserved / Spot에서 사용 가능
- 최대 Hibernate 기간 제한 존재

가장 중요한 암기:

```text
Stop
= EBS 보존

Hibernate
= EBS + RAM 보존
```

그리고:

```text
Hibernate
→ RAM saved to
→ Encrypted Root EBS
```

---

# Japanese Summary

## EC2 Hibernate

EC2 Hibernateは、  
EC2 Instanceの**RAM状態を保存する休止機能**です。

通常のStopでは：

```text
EBS
→ 保存

RAM
→ 消失
```

Hibernateでは：

```text
RAM
↓
Encrypted Root EBS
↓
保存
```

Instanceを再開すると：

```text
Root EBS
↓
RAM Restore
↓
Previous Processes Resume
```

以前のMemory状態を復元できます。

主な利用例：

- 長時間実行するProcess
- RAM状態の保存
- 初期化に時間がかかるApplication
- CacheやServiceを素早く復元したい場合

重要な条件：

```text
Root Volume
= EBS
= Encrypted
= RAMを保存できる十分な容量
```

覚え方：

```text
Stop
= Diskだけ保存

Hibernate
= Disk + RAMを保存
```

---

# English Summary

## EC2 Hibernate

EC2 Hibernate preserves the **in-memory state (RAM)** of an EC2 instance.

With a normal Stop:

```text
EBS
→ Preserved

RAM
→ Lost
```

With Hibernate:

```text
RAM
↓
Encrypted Root EBS
↓
Stored
```

When the instance starts again:

```text
Root EBS
↓
RAM Restored
↓
Previous Processes Resume
```

Hibernate is useful for:

- Long-running processes
- Preserving in-memory state
- Applications that take a long time to initialize
- Pre-warmed applications and caches

Important requirements:

```text
Root Volume
= EBS
= Encrypted
= Large enough to store RAM
```

Key distinction:

```text
Stop
= Preserve disk

Hibernate
= Preserve disk + RAM
```

---

# Vocabulary

| Term | Meaning |
|---|---|
| Hibernate | 최대 절전 모드 |
| In-memory State | RAM에 존재하는 실행 상태 |
| RAM | Random Access Memory |
| Root EBS Volume | EC2 운영체제가 저장된 기본 EBS Volume |
| RAM Dump | RAM 내용을 Disk에 저장한 데이터 |
| Resume | 이전 실행 상태를 복원하여 다시 시작 |
| Boot | 운영체제를 시작하는 과정 |
| Cache Warm-up | Cache를 다시 구성하여 준비하는 과정 |
| Long-running Process | 장시간 실행되는 Process |
| Encryption | 암호화 |
| Bare Metal | Virtualization 없이 물리 서버에 직접 접근하는 Instance |
| Pre-warmed | 미리 초기화되어 바로 사용할 수 있는 상태 |

---

# Review Questions

### Q1. EC2 Hibernate란 무엇인가?

EC2의 RAM 상태를 Root EBS Volume에 저장하여  
다시 시작할 때 이전 Memory 상태를 복원하는 기능이다.

### Q2. Stop과 Hibernate의 가장 큰 차이는?

```text
Stop
→ RAM 소실

Hibernate
→ RAM 보존
```

### Q3. Hibernate 시 RAM은 어디에 저장되는가?

Encrypted Root EBS Volume에 저장된다.

### Q4. 왜 Root EBS Volume을 암호화해야 하는가?

RAM에는 Credential, Session, Application State 등  
민감한 정보가 존재할 수 있기 때문이다.

### Q5. Root EBS에는 어떤 용량 조건이 필요한가?

OS와 Application 데이터뿐 아니라  
RAM Dump도 저장할 수 있을 정도로 충분히 커야 한다.

### Q6. Hibernate의 주요 사용 사례는?

장시간 실행되는 Process,  
RAM 상태 보존,  
초기화 시간이 긴 Application,  
Cache가 큰 서비스 등에 사용할 수 있다.

### Q7. Bare Metal Instance에서 Hibernate를 사용할 수 있는가?

사용할 수 없다.

### Q8. Hibernate 후 EC2를 다시 Start하면 어떻게 되는가?

Root EBS에 저장했던 RAM 내용을 Memory로 복원하고  
이전에 실행 중이던 Process 상태를 이어서 사용할 수 있다.

### Q9. Stop, Hibernate, Terminate를 간단히 비교하면?

```text
Stop
= EBS 보존

Hibernate
= EBS + RAM 보존

Terminate
= Instance 제거
```

### Q10. 시험에서 어떤 키워드가 나오면 Hibernate를 떠올려야 하는가?

```text
Preserve RAM
In-memory state
Resume process
Long initialization
Faster resume
```
