# EC2 Spot Instances and Spot Fleet

## Overview

EC2 Spot Instances는 AWS의 여유 EC2 Capacity를 낮은 가격으로 사용할 수 있는 구매 옵션이다.

On-Demand보다 큰 비용 절감이 가능하지만 AWS의 Capacity 상황 등에 따라 Instance가 중단될 수 있다.

따라서 Spot Instances는 **중단에 대응할 수 있는 Fault-Tolerant Workload**에 적합하다.

---

## Spot Instances

```text
Spare EC2 Capacity
↓
Spot Instance
↓
Low Cost
+
Possible Interruption
```

### Suitable Workloads

- Batch Processing
- Data Analysis
- Image Processing
- Distributed Workloads
- Flexible Workloads

### Not Suitable

- Critical Workloads
- Databases
- Workloads that cannot tolerate interruption

---

## Spot Instance Interruption

AWS가 Spot Capacity를 회수해야 하는 경우 Instance가 중단될 수 있다.

Spot Instance가 중단될 예정인 경우 약 2분의 Interruption Notice를 받을 수 있다.

```text
Interruption Notice
↓
Save State / Checkpoint
↓
Stop / Hibernate / Terminate
```

따라서 Spot 기반 Application은 Instance가 사라지더라도 작업을 다시 시작할 수 있도록 설계하는 것이 중요하다.

---

## Spot Requests

Spot Request를 통해 Spot Instance의 실행 조건을 정의할 수 있다.

강의에서는 다음 두 Request Type을 설명한다.

```text
One-Time
→ Instance를 한 번 요청

Persistent
→ 원하는 Capacity를 계속 유지
```

Persistent Request에서 Instance만 먼저 종료하면 새로운 Instance가 다시 실행될 수 있다.

따라서 Persistent Spot Request를 완전히 종료하려면:

```text
Cancel Spot Request
↓
Terminate Spot Instances
```

순서로 처리한다.

---

## Spot Fleet

Spot Fleet은 여러 Instance Type과 Availability Zone의 Spot Capacity Pool을 이용하여 목표 Capacity를 확보한다.

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

필요한 Compute Capacity를 충족하기 위해 여러 Spot Pool 중 적절한 Capacity를 선택할 수 있다.

Spot Fleet에는 On-Demand Capacity를 함께 사용할 수도 있다.

---

## Spot Fleet Configuration

Spot Fleet은 여러 Instance Type과 Availability Zone을 후보로 사용하여
필요한 Target Capacity를 확보할 수 있다.

### Target Capacity

Target Capacity는 단순한 Instance 개수뿐 아니라
필요한 Compute Resource를 기준으로 구성할 수도 있다.

```text
Target Capacity
├─ Number of Instances
├─ vCPU
└─ Memory
```

---

## Spot Fleet Allocation Strategies

### Lowest Price

가장 저렴한 Spot Pool에서 Capacity를 확보한다.

```text
Lowest Price
→ Maximum Cost Saving
→ Higher Interruption Risk
```

현재 AWS에서는 일반적으로 권장되지 않는다.

### Diversified

Capacity를 여러 Spot Pool에 분산한다.

```text
Multiple Pools
→ Reduced Dependency on One Pool
```

### Capacity Optimized

가용 Capacity가 많은 Spot Pool을 우선한다.

```text
More Available Capacity
→ Lower Interruption Risk
```

### Price-Capacity-Optimized

가용 Capacity와 가격을 함께 고려한다.

```text
Capacity Availability
+
Price
↓
Price-Capacity-Optimized
```

현재 AWS에서 대부분의 Spot Workload에 권장하는 전략이다.

---

## Summary

- Spot Instances는 AWS의 Spare EC2 Capacity를 낮은 가격으로 사용한다.
- Spot Instances는 중단될 수 있다.
- Batch, Analytics, Distributed Processing과 같은 Fault-Tolerant Workload에 적합하다.
- Critical Workload와 Database에는 일반적으로 적합하지 않다.
- Spot interruption 전 약 2분의 Notice를 받을 수 있다.
- Persistent Spot Request는 목표 Capacity를 유지한다.
- Spot Fleet은 여러 Spot Capacity Pool을 이용할 수 있다.
- Price-Capacity-Optimized는 가격과 Capacity를 함께 고려한다.

---

## Exam Notes

```text
Lowest Cost + Can Tolerate Interruption
→ Spot Instance
```

```text
Batch / Analytics / Image Processing
→ Spot
```

```text
Critical Database
→ NOT Spot
```

```text
Multiple Instance Types / AZs
+
Target Spot Capacity
→ Spot Fleet
```

```text
Cost + Capacity Availability
→ Price-Capacity-Optimized
```

Persistent Spot Request 종료:

```text
Cancel Request
↓
Terminate Instance
```

---

## Practical Example

대규모 이미지 변환 작업이 있다고 가정한다.

```text
S3 Images
↓
Spot EC2 Workers
↓
Image Processing
↓
S3 Results
```

Spot Instance 하나가 중단되더라도 다른 Instance가 작업을 다시 처리할 수 있도록 구성하면 비용을 크게 절감할 수 있다.

---

# 🇯🇵 日本語

## EC2 Spot Instances

Spot InstanceはAWSの余剰EC2 Capacityを低価格で利用できる購入オプションである。

ただし、Capacityの状況によってInstanceが中断される可能性がある。

そのため、Batch Processing、Data Analysis、Image Processingなどの耐障害性のあるWorkloadに適している。

Spot Fleetを利用すると、複数のInstance TypeやAvailability ZoneからSpot Capacityを確保できる。

現在、Price-Capacity-Optimizedは価格とCapacityの両方を考慮する推奨戦略である。

---

# 🇺🇸 English

## EC2 Spot Instances

Spot Instances use spare EC2 capacity at a significantly reduced cost.

They may be interrupted when AWS needs the capacity back, so they are best suited for fault-tolerant workloads.

Common use cases include batch processing, data analytics, image processing, and distributed workloads.

Spot Fleet can provision capacity across multiple instance types and Availability Zones.

The price-capacity-optimized strategy considers both available capacity and price.

---

## Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Spot Instance | 스팟 인스턴스 | スポットインスタンス |
| Spot Fleet | 스팟 플릿 | スポットフリート |
| Spot Pool | 스팟 용량 풀 | スポットプール |
| Interruption | 중단 | 中断 |
| Spare Capacity | 여유 용량 | 余剰キャパシティ |
| Fault-Tolerant | 장애 허용 | 耐障害性 |
| Persistent Request | 영구 요청 | 永続リクエスト |
| Target Capacity | 목표 용량 | ターゲットキャパシティ |
| Allocation Strategy | 할당 전략 | 配分戦略 |
| Capacity Optimized | 용량 최적화 | キャパシティ最適化 |

---

## Review Questions

1. Spot Instance가 On-Demand보다 저렴한 이유는 무엇인가?
2. Spot Instance의 가장 중요한 단점은 무엇인가?
3. Spot Instance에 적합한 Workload의 특징은 무엇인가?
4. Critical Database에 Spot을 사용하는 것이 적합하지 않은 이유는 무엇인가?
5. Spot Instance interruption 전에 받을 수 있는 Notice는 약 몇 분인가?
6. Persistent Spot Request의 역할은 무엇인가?
7. Persistent Request를 완전히 종료할 때 어떤 순서로 처리해야 하는가?
8. Spot Fleet은 일반 Spot Request와 어떤 차이가 있는가?
9. Diversified Strategy의 목적은 무엇인가?
10. Price-Capacity-Optimized Strategy는 무엇을 함께 고려하는가?