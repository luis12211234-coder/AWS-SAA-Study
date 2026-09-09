# EC2 Spot Instances and Spot Fleet

## Overview

EC2 Spot Instances는 AWS의 남는 EC2 Compute Capacity를 낮은 가격으로 사용할 수 있는 구매 옵션이다.

On-Demand Instances와 동일한 EC2 Instance이지만, AWS의 여유 Capacity를 사용하는 대신 비용이 매우 저렴하며 AWS가 해당 Capacity를 다시 필요로 할 경우 Instance가 중단될 수 있다.

```text
On-Demand
→ 일반 EC2 Capacity 사용
→ 안정적
→ 상대적으로 높은 비용

Spot
→ Spare EC2 Capacity 사용
→ 매우 저렴
→ AWS에 의해 중단될 수 있음
```

Spot Instances는 중단에 대응할 수 있는 **Fault-Tolerant Workload**에 적합하다.

---

## Spot Instances

Spot Instances는 On-Demand보다 큰 비용 절감이 가능하다.

강의에서는 최대 약 90%까지 할인될 수 있다고 설명한다.

> 실제 가격과 할인율은 Region, Availability Zone, Instance Type 및 시점에 따라 달라질 수 있다.

### Suitable Workloads

Spot Instances는 다음과 같이 Instance가 중단되더라도 다시 작업을 수행할 수 있는 Workload에 적합하다.

- Batch Processing
- Data Analysis
- Image Processing
- Distributed Workloads
- Flexible Start / End Time Workloads

### Not Suitable

- Critical Workloads
- Critical Databases
- Interruption을 허용할 수 없는 Workloads

```text
Can tolerate interruption?
        │
        ├─ YES → Spot 고려
        │
        └─ NO  → Spot 부적합
```

---

## Why Can Spot Instances Be Interrupted?

Spot Instances는 AWS의 Spare Capacity를 이용한다.

예를 들어 특정 Availability Zone에 다음과 같은 EC2 Capacity가 있다고 가정한다.

```text
Total EC2 Capacity
████████████████████

Used Capacity
████████████████

Spare Capacity
████
```

Spot Instances는 이 Spare Capacity를 사용할 수 있다.

하지만 AWS가 해당 Capacity를 다른 수요에 사용해야 하면 Spot Capacity가 부족해질 수 있다.

```text
Spare Capacity 감소
↓
AWS가 Capacity 회수
↓
Spot Instance Interruption
```

따라서 Spot을 사용할 때는 Instance 자체가 항상 유지된다고 가정하지 않는 것이 중요하다.

---

## Spot Pricing

강의에서는 Spot Instance의 Maximum Price와 현재 Spot Price를 비교하는 방식으로 설명한다.

```text
Maximum Price
>
Current Spot Price

→ Spot Instance 사용 가능
```

반대로:

```text
Maximum Price
<
Current Spot Price

→ Instance가 실행되지 않거나 중단될 수 있음
```

현재 AWS에서는 일반적으로 Maximum Price를 직접 지정하지 않는 것을 권장한다.

Maximum Price를 너무 낮게 설정하면 사용할 수 있는 Spot Capacity가 줄어들어 Interruption 가능성이 높아질 수 있다.

---

## Spot Instance Interruption

AWS가 Spot Capacity를 회수해야 하는 경우 Spot Instance가 중단될 수 있다.

Spot Instance가 중단될 예정이면 약 **2분의 Interruption Notice**를 받을 수 있다.

```text
AWS
↓
Spot Interruption Notice
↓
약 2분
↓
Save State / Checkpoint
↓
Instance Interruption
```

이 시간을 이용해 다음과 같은 작업을 수행할 수 있다.

- 작업 상태 저장
- Checkpoint 생성
- Queue에 작업 상태 기록
- Graceful Shutdown

Spot Instance의 중단 동작에는 다음과 같은 방식이 존재할 수 있다.

```text
Terminate
Stop
Hibernate
```

Spot Workload는 Instance가 사라져도 다시 작업을 수행할 수 있도록 설계하는 것이 중요하다.

---

## Spot Requests

Spot Request는 원하는 조건의 Spot Instance를 요청하는 방식이다.

강의에서는 다음과 같은 설정을 설명한다.

- AMI
- Instance Type
- Number of Instances
- Maximum Price
- Request Validity
- Interruption Behavior

### One-Time Request

One-Time Request는 Spot Instance를 한 번 요청한다.

```text
Spot Request
↓
조건 충족
↓
Spot Instance Launch
↓
Request 완료
```

Instance가 이후 중단되더라도 동일한 Request가 계속 새로운 Instance를 요청하지 않는다.

```text
One-Time
→ 한 번 요청
→ 자동 Capacity 유지 X
```

---

## Persistent Spot Request

Persistent Request는 요청이 유효한 동안 원하는 Spot Capacity를 계속 유지하려고 한다.

예:

```text
Desired Instances
= 3

현재
EC2
EC2
EC2
```

Instance 하나가 사라지면:

```text
EC2
EC2
X

↓ Persistent Request

EC2
EC2
NEW EC2
```

즉:

```text
Persistent
→ Desired Capacity 유지
```

---

## Cancelling a Persistent Spot Request

Persistent Request가 활성화된 상태에서 Instance만 종료하면 Request가 새로운 Instance를 다시 생성할 수 있다.

따라서 Spot Capacity를 완전히 종료하려면 다음 순서가 중요하다.

```text
1. Cancel Spot Request
↓
2. Terminate Spot Instances
```

반대로:

```text
Instance 먼저 Terminate
↓
Persistent Request는 아직 Active
↓
새 Spot Instance 요청 가능
```

따라서 Persistent Spot Request에서는 **Request를 먼저 취소한 뒤 Instance를 종료**한다.

---

## Spot Fleet

Spot Fleet은 여러 Spot Instances를 하나의 Fleet으로 관리하여 원하는 Target Capacity를 확보하는 기능이다.

Spot Fleet에는 필요에 따라 On-Demand Instances도 포함할 수 있다.

```text
Spot Pool A
Spot Pool B
Spot Pool C
Spot Pool D
       ↓
   Spot Fleet
       ↓
Target Capacity
```

Spot Fleet은 사용자가 정의한 여러 Launch Pool 중에서 적절한 Pool을 선택한다.

---

## Spot Pool

Spot Pool은 특정 조건을 가진 Spot Capacity의 집합이다.

예를 들어 다음 조합들이 서로 다른 Spot Pool이 될 수 있다.

```text
m5.large + us-east-1a
m5.large + us-east-1b
m5.xlarge + us-east-1a
c5.large + us-east-1c
```

Spot Fleet에서 여러 Instance Type과 Availability Zone을 허용하면 사용할 수 있는 Spot Pool의 수가 증가한다.

```text
More Instance Types
+
More Availability Zones
↓
More Spot Pools
↓
Greater Capacity Flexibility
```

---

## Spot Fleet Configuration

Spot Fleet에서는 다양한 조건을 정의할 수 있다.

### Launch Configuration

Instance를 어떻게 생성할지 지정한다.

예:

- AMI
- Key Pair
- Launch Template
- Network Configuration

---

## Target Capacity

Spot Fleet은 필요한 전체 Capacity를 기준으로 Instance를 실행한다.

강의에서는 Target Capacity를 다음과 같은 방식으로 설정할 수 있음을 보여준다.

```text
Target Capacity
├─ Number of Instances
├─ vCPU
└─ Memory
```

예를 들어 단순히:

```text
10 Instances
```

를 요청할 수도 있지만,

```text
10 vCPU
```

와 같이 필요한 Compute Capacity를 기준으로 Fleet을 구성할 수도 있다.

즉 Spot Fleet의 핵심은 반드시 특정 Instance 개수를 고정하는 것이 아니라 **필요한 전체 Compute Capacity를 확보하는 것**이다.

---

## Instance Selection

Spot Fleet에서 사용할 Instance Type을 직접 선택할 수 있다.

예:

```text
c5.large
c5.xlarge
m5.large
```

또는 특정 Instance Type을 지정하지 않고 Hardware Requirement를 정의할 수도 있다.

```text
Minimum vCPU
Maximum vCPU

Minimum Memory
Maximum Memory
```

이 경우 AWS가 해당 조건을 만족하는 Instance Type들을 후보로 선택할 수 있다.

---

## Flexible Instance Selection

Spot에서는 Instance Type과 Availability Zone을 유연하게 지정하는 것이 중요하다.

조건을 너무 제한하면:

```text
Few Instance Types
+
One AZ
↓
Few Spot Pools
↓
Capacity 확보 어려움
```

반대로:

```text
Multiple Instance Types
+
Multiple AZs
↓
More Spot Pools
↓
Capacity 확보 가능성 증가
```

따라서 Spot Workload에서는 가능한 경우 여러 Instance Type과 Availability Zone을 허용하는 것이 유리하다.

---

## Spot Fleet Allocation Strategies

Spot Fleet에는 여러 Spot Pool 중 어느 Pool에서 Capacity를 가져올지 결정하는 Allocation Strategy가 있다.

### Lowest Price

가장 가격이 낮은 Spot Pool에서 Instance를 실행한다.

```text
Pool A → $0.02
Pool B → $0.03
Pool C → $0.04

↓ Lowest Price

Pool A
```

장점:

```text
Cost Optimization
```

단점:

```text
Capacity를 충분히 고려하지 않음
↓
Interruption Risk 증가 가능
```

현재 AWS에서는 일반적으로 Lowest Price Strategy를 권장하지 않는다.

---

### Diversified

Spot Capacity를 여러 Spot Pool에 분산한다.

```text
Pool A → 25%
Pool B → 25%
Pool C → 25%
Pool D → 25%
```

하나의 Pool에 문제가 발생해도 다른 Pool의 Instance는 계속 실행될 수 있다.

```text
Multiple Pools
↓
Reduced Dependency
↓
Improved Availability
```

---

### Capacity Optimized

Spot Capacity가 가장 충분한 Pool을 우선적으로 선택한다.

```text
Pool A
Price: Low
Capacity: Low

Pool B
Price: Higher
Capacity: High

↓ Capacity Optimized

Pool B
```

목적:

```text
More Available Capacity
↓
Lower Interruption Risk
```

---

### Price-Capacity-Optimized

가격과 Capacity를 함께 고려한다.

```text
High Capacity Pools
↓
그중 낮은 가격 선택
```

즉:

```text
Capacity Availability
+
Price
↓
Price-Capacity-Optimized
```

현재 AWS에서는 대부분의 Spot Workload에 `price-capacity-optimized` 전략을 권장한다.

---

## Allocation Strategy Comparison

| Strategy | Main Priority | 특징 |
|---|---|---|
| Lowest Price | Price | 가장 싼 Pool 선택 |
| Diversified | Distribution | 여러 Pool에 분산 |
| Capacity Optimized | Capacity | 여유 Capacity가 많은 Pool 선택 |
| Price-Capacity-Optimized | Price + Capacity | 가격과 Capacity를 함께 고려 |

핵심:

```text
Lowest Price
→ 가격

Diversified
→ 분산

Capacity Optimized
→ Capacity

Price-Capacity-Optimized
→ 가격 + Capacity
```

---

## Spot Fleet Flow

Spot Fleet의 전체 흐름은 다음과 같다.

```text
Target Capacity 설정
↓
Instance Requirements 설정
↓
Multiple Instance Types / AZs 지정
↓
Spot Pools 생성
↓
Allocation Strategy 적용
↓
적절한 Spot Pool 선택
↓
Target Capacity 충족
```

Fleet은 Target Capacity를 충족하거나 설정된 비용 제한에 도달할 때까지 Instance를 실행한다.

---

## Spot Request vs Spot Fleet

### Spot Request

특정한 조건의 Spot Instance가 필요할 때 사용한다.

```text
Instance Type
+
AZ
+
Spot Request
↓
Spot Instance
```

### Spot Fleet

여러 Instance Type과 여러 Availability Zone을 후보로 제공하고 AWS가 적절한 Spot Capacity를 선택하도록 한다.

```text
Instance Type A
Instance Type B
Instance Type C

AZ A
AZ B
AZ C

↓
Spot Fleet
↓
Allocation Strategy
↓
Target Capacity
```

즉:

```text
Spot Request
→ 특정 Spot Instance 요청

Spot Fleet
→ 여러 Spot Pool에서 필요한 Capacity 확보
```

---

## Spot Block

과거에는 Spot Block이라는 기능이 존재했다.

Spot Instance를 약 1~6시간 동안 중단 없이 사용할 수 있도록 설계된 기능이었다.

하지만 Spot Block은 폐지된 기능이므로 현재 AWS 학습에서는 주요 기능으로 다루지 않는다.

```text
Spot Block
→ Legacy / Discontinued
→ Current Study: Ignore
```

---

## Summary

- Spot Instances는 AWS의 Spare EC2 Capacity를 저렴하게 사용한다.
- On-Demand와 달리 AWS가 Capacity를 회수하면 중단될 수 있다.
- Batch, Analytics, Image Processing, Distributed Workload에 적합하다.
- Critical Database 등 중단에 민감한 Workload에는 적합하지 않다.
- Spot Interruption 전에 약 2분의 Notice를 받을 수 있다.
- Maximum Price를 지정할 수 있지만 현재 AWS에서는 일반적으로 지정하지 않는 것을 권장한다.
- One-Time Request는 한 번 Spot Capacity를 요청한다.
- Persistent Request는 요청이 유효한 동안 Desired Capacity를 유지하려 한다.
- Persistent Request를 완전히 종료하려면 Request를 먼저 취소하고 Instance를 종료한다.
- Spot Fleet은 여러 Spot Pool에서 Target Capacity를 확보한다.
- Target Capacity는 Instance 개수뿐 아니라 vCPU 등의 Compute Capacity를 기준으로 정의할 수 있다.
- 여러 Instance Type과 AZ를 허용하면 Spot Capacity 확보 가능성을 높일 수 있다.
- `price-capacity-optimized`는 가격과 Capacity를 함께 고려한다.

---

## Exam Notes

### Spot Instance

```text
Spare EC2 Capacity
+
Low Price
+
Can Be Interrupted
```

```text
Batch
Analytics
Image Processing
Distributed Workload
↓
Spot
```

```text
Critical Database
↓
NOT Spot
```

### Interruption

```text
AWS needs Capacity
↓
Spot Interruption
↓
~2-minute Notice
```

Spot Application은 Interruption에 대응할 수 있도록 설계한다.

### One-Time vs Persistent

```text
One-Time
→ 한 번 요청
→ 자동 재요청 X
```

```text
Persistent
→ Desired Capacity 유지
→ Instance가 사라지면 다시 요청 가능
```

Persistent Request 종료:

```text
Cancel Spot Request
↓
Terminate Spot Instance
```

### Spot Fleet

```text
Multiple Instance Types
+
Multiple AZs
+
Target Capacity
↓
Spot Fleet
```

### Allocation Strategies

```text
Lowest Price
→ Cheapest Pool

Diversified
→ Multiple Pools

Capacity Optimized
→ Most Available Capacity

Price-Capacity-Optimized
→ Capacity + Price
→ Recommended for most workloads
```

---

## Practical Example

대규모 이미지 처리 시스템이 있다고 가정한다.

```text
Amazon S3
↓
Image Processing Queue
↓
Spot Fleet
│
├─ c5.large
├─ c5.xlarge
├─ m5.large
└─ Multiple AZs
↓
Image Processing Workers
↓
Amazon S3
```

특정 Spot Pool의 Capacity가 부족해 Instance가 중단되더라도 다른 Instance Type이나 AZ의 Spot Capacity를 이용할 수 있다.

작업 자체는 Queue 또는 Checkpoint를 사용하여 다시 처리할 수 있도록 설계한다.

이러한 Workload는 Spot Instance와 Spot Fleet을 활용하여 비용을 절감하기에 적합하다.

---

# 🇯🇵 日本語

## EC2 Spot Instances

EC2 Spot InstanceはAWSの余剰EC2 Capacityを低価格で利用できる購入オプションである。

On-Demand Instanceと異なり、AWSがCapacityを必要とする場合はInstanceが中断される可能性がある。

そのため、Spot Instanceは以下のような耐障害性のあるWorkloadに適している。

- Batch Processing
- Data Analysis
- Image Processing
- Distributed Workloads

Critical Databaseなど、中断できないWorkloadには適していない。

---

## Spot Interruption

Spot Instanceが中断される場合、約2分前にInterruption Noticeを受け取ることができる。

```text
Interruption Notice
↓
Checkpoint / Save State
↓
Instance Interruption
```

---

## Spot Requests

### One-Time

一度だけSpot Capacityを要求する。

```text
Request
↓
Instance Launch
↓
Complete
```

### Persistent

要求が有効な間、Desired Capacityを維持する。

```text
Desired Capacity
↓
Instance Interrupted
↓
New Instance Request
```

Persistent Requestを完全に終了する場合:

```text
Cancel Request
↓
Terminate Instance
```

---

## Spot Fleet

Spot Fleetは複数のInstance TypeやAvailability ZoneからSpot Capacityを確保する。

```text
Multiple Spot Pools
↓
Allocation Strategy
↓
Target Capacity
```

Target CapacityはInstance数だけでなく、vCPUなどのCompute Capacityを基準として設定することもできる。

---

## Allocation Strategies

```text
Lowest Price
→ 最安値

Diversified
→ 複数Poolへ分散

Capacity Optimized
→ Capacityを優先

Price-Capacity-Optimized
→ Price + Capacity
```

現在、Price-Capacity-Optimizedは多くのSpot Workloadに推奨されている。

---

## Summary

- Spotは余剰EC2 Capacityを低価格で利用する。
- Instanceが中断される可能性がある。
- Fault-Tolerant Workloadに適している。
- Spot Fleetは複数のSpot PoolからTarget Capacityを確保する。
- 複数のInstance TypeとAZを利用するとCapacityの柔軟性が高くなる。
- Price-Capacity-Optimizedは価格とCapacityの両方を考慮する。

---

# 🇺🇸 English

## EC2 Spot Instances

EC2 Spot Instances provide access to spare EC2 compute capacity at a significantly reduced cost.

Unlike On-Demand Instances, Spot Instances can be interrupted when AWS needs the capacity back.

They are suitable for fault-tolerant workloads such as:

- Batch processing
- Data analytics
- Image processing
- Distributed workloads

They are generally not suitable for critical databases or workloads that cannot tolerate interruptions.

---

## Spot Interruption

Spot Instances can receive approximately two minutes of interruption notice before AWS reclaims the capacity.

Applications should be designed to save state, checkpoint work, or restart tasks after an interruption.

---

## Spot Requests

### One-Time

Requests Spot capacity once.

```text
Request
↓
Instance Launch
↓
Complete
```

### Persistent

Attempts to maintain the desired Spot capacity while the request remains valid.

```text
Desired Capacity
↓
Instance Interrupted
↓
Request Replacement Capacity
```

To completely stop a Persistent Spot Request:

```text
Cancel Request
↓
Terminate Instances
```

---

## Spot Fleet

Spot Fleet can provision capacity across multiple Spot pools using different instance types and Availability Zones.

Target capacity may represent a number of instances or a required amount of compute capacity.

```text
Multiple Instance Types
+
Multiple Availability Zones
↓
Spot Fleet
↓
Target Capacity
```

---

## Allocation Strategies

```text
Lowest Price
→ Lowest-priced pools

Diversified
→ Distribute capacity across pools

Capacity Optimized
→ Pools with greater available capacity

Price-Capacity-Optimized
→ Balance capacity availability and price
```

Price-Capacity-Optimized is currently recommended for most Spot workloads.

---

## Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Spot Instance | 스팟 인스턴스 | スポットインスタンス |
| Spot Fleet | 스팟 플릿 | スポットフリート |
| Spot Pool | 스팟 용량 풀 | スポットプール |
| Spare Capacity | 여유 용량 | 余剰キャパシティ |
| Target Capacity | 목표 용량 | ターゲットキャパシティ |
| Interruption | 중단 | 中断 |
| Interruption Notice | 중단 알림 | 中断通知 |
| Fault-Tolerant | 장애 허용 | 耐障害性 |
| One-Time Request | 일회성 요청 | ワンタイムリクエスト |
| Persistent Request | 영구 요청 | 永続リクエスト |
| Allocation Strategy | 할당 전략 | 配分戦略 |
| Diversified | 분산 | 分散 |
| Capacity Optimized | 용량 최적화 | キャパシティ最適化 |
| Price-Capacity-Optimized | 가격-용량 최적화 | 価格・キャパシティ最適化 |
| Checkpoint | 체크포인트 | チェックポイント |
| Hibernate | 최대 절전 | ハイバネート |

---

## Review Questions

1. Spot Instance가 On-Demand Instance보다 저렴한 이유는 무엇인가?
2. Spot Instance에서 말하는 Spare Capacity란 무엇인가?
3. AWS가 Spot Instance를 중단할 수 있는 이유는 무엇인가?
4. Spot Instance에 적합한 Workload의 특징은 무엇인가?
5. Critical Database에 Spot Instance가 일반적으로 적합하지 않은 이유는 무엇인가?
6. Spot Interruption Notice는 약 몇 분 전에 제공되는가?
7. Maximum Price를 너무 낮게 설정하면 어떤 문제가 발생할 수 있는가?
8. One-Time Spot Request와 Persistent Spot Request의 차이는 무엇인가?
9. Persistent Spot Request에서 Instance만 먼저 종료하면 어떤 일이 발생할 수 있는가?
10. Persistent Spot Request를 완전히 종료하려면 어떤 순서로 처리해야 하는가?
11. Spot Pool이란 무엇인가?
12. Spot Fleet은 일반 Spot Request와 어떤 차이가 있는가?
13. Spot Fleet의 Target Capacity는 어떤 기준으로 설정할 수 있는가?
14. 여러 Instance Type과 Availability Zone을 허용하는 것이 Spot에서 유리한 이유는 무엇인가?
15. Lowest Price Strategy는 무엇을 기준으로 Pool을 선택하는가?
16. Diversified Strategy를 사용하는 목적은 무엇인가?
17. Capacity Optimized Strategy는 무엇을 우선하는가?
18. Price-Capacity-Optimized Strategy는 무엇을 함께 고려하는가?
19. 현재 AWS에서 대부분의 Spot Workload에 권장되는 Allocation Strategy는 무엇인가?
