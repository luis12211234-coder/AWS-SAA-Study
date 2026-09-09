# EC2 Placement Groups

## Overview

EC2 Placement Group은 EC2 Instance가 AWS의 물리적 Infrastructure에 어떻게 배치될지에 대한 전략을 정의하는 기능이다.

사용자가 특정 AWS Server나 Rack을 직접 선택하는 것은 아니지만,
EC2 Instance들을 서로 가깝게 배치할지, 서로 다른 Hardware에 분산할지 등의 배치 전략을 AWS에 지정할 수 있다.

현재 AWS에서는 다음 네 가지 Placement Strategy를 제공한다.

```text
Cluster
Spread
Partition
Precision Time
```

각 전략의 핵심 목적은 다음과 같다.

```text
Cluster
→ Instance를 가깝게 배치
→ Network Performance

Spread
→ Instance 하나하나를 분리
→ Instance Failure Isolation

Partition
→ Instance들을 Partition 단위로 분리
→ Large Distributed Systems

Precision Time
→ 정밀 시간 동기화를 지원하는 Infrastructure에 배치
→ Accurate Clock Synchronization
```

> Cluster, Spread, Partition은 강의 및 제공된 PDF의 내용이다.
> Precision Time은 강의 이후 AWS에 추가된 Placement Strategy이다.

---

# Availability Zone Review

Placement Group을 이해하려면 Availability Zone(AZ)을 먼저 이해해야 한다.

AWS Infrastructure의 기본 구조는 다음과 같다.

```text
Region
│
├─ Availability Zone A
├─ Availability Zone B
├─ Availability Zone C
└─ Availability Zone D
```

예를 들어 서울 Region은 다음과 같이 표현된다.

```text
Region
ap-northeast-2

├─ ap-northeast-2a
├─ ap-northeast-2b
├─ ap-northeast-2c
└─ ap-northeast-2d
```

Availability Zone은 Region 내부에서 서로 독립적으로 설계된 Infrastructure 영역이다.

Placement Group Strategy에 따라 하나의 AZ 안에서 Instance를 배치하거나,
여러 AZ에 걸쳐 Instance를 분산할 수 있다.

---

# Cluster Placement Group

## Concept

Cluster Placement Group은 EC2 Instance들을 **하나의 Availability Zone 안에서 서로 가까운 위치에 배치**한다.

목적은 Instance 간 Network Performance를 극대화하는 것이다.

```text
Availability Zone

┌─────────────────────────────┐
│                             │
│ EC2 ─ EC2 ─ EC2 ─ EC2      │
│                             │
│ Low Latency                 │
│ High Throughput             │
│                             │
└─────────────────────────────┘
```

---

## Advantages

Cluster Placement Group의 핵심 장점은 Network Performance이다.

```text
Instances Close Together
↓
Low Network Latency
+
High Network Throughput
```

따라서 EC2 Instance 간 매우 빠른 통신이 필요한 Workload에 적합하다.

강의에서는 Enhanced Networking을 사용하는 경우
Instance 간 약 10 Gbps Network 성능을 예시로 설명한다.

> 정확한 Network Performance는 사용하는 EC2 Instance Type과 Network Configuration에 따라 달라질 수 있다.

---

## Disadvantages

Cluster Placement Group은 하나의 Availability Zone에 집중된다.

따라서 해당 AZ에 문제가 발생하면 Placement Group의 여러 Instance가 동시에 영향을 받을 수 있다.

```text
Availability Zone
        │
        X Failure
        │
        ▼
┌─────────────────┐
│ EC2 EC2 EC2 EC2 │
└─────────────────┘
        │
        ▼
Multiple Instances
Affected
```

즉,

```text
Performance ↑
Failure Isolation ↓
```

이라는 Trade-off가 존재한다.

---

## Use Cases

Cluster Placement Group은 다음과 같은 Workload에 적합하다.

- High Performance Computing (HPC)
- Big Data Processing
- Low-Latency Applications
- High-Throughput Applications
- Instance 간 매우 빠른 Network Communication이 필요한 Application

---

# Spread Placement Group

## Concept

Spread Placement Group은 EC2 Instance들을 **서로 다른 물리 Hardware에 분산 배치**한다.

목적은 하나의 Hardware Failure가 여러 Instance에 동시에 영향을 주는 것을 방지하는 것이다.

```text
Availability Zone

EC2-A
  │
  ▼
Hardware 1


EC2-B
  │
  ▼
Hardware 2


EC2-C
  │
  ▼
Hardware 3
```

즉,

```text
One Instance
→ One Distinct Hardware

Another Instance
→ Another Distinct Hardware
```

와 같은 방식으로 Failure Risk를 분산한다.

---

## Spread Does NOT Mean One Instance per AZ

Spread Placement Group은 각 Instance를 반드시 서로 다른 Availability Zone에 하나씩 배치한다는 의미가 아니다.

같은 AZ 안에서도 Instance들을 서로 다른 Hardware에 분산할 수 있다.

```text
AZ-A

EC2-1 → Hardware A
EC2-2 → Hardware B
EC2-3 → Hardware C
```

또한 하나의 Spread Placement Group은 여러 Availability Zone에 걸쳐 구성할 수도 있다.

```text
Region

AZ-A
├─ EC2 → Hardware A
└─ EC2 → Hardware B

AZ-B
├─ EC2 → Hardware C
└─ EC2 → Hardware D
```

핵심은 AZ 자체가 아니라:

```text
Each EC2 Instance
↓
Distinct Underlying Hardware
```

이다.

---

## Advantages

하나의 Hardware에 장애가 발생해도 다른 Instance가 영향을 받을 가능성을 낮춘다.

```text
Hardware 1
    X
    │
    ▼
EC2-A Failure


Hardware 2
    │
    ▼
EC2-B Running


Hardware 3
    │
    ▼
EC2-C Running
```

따라서 중요한 Instance 간 Failure Isolation을 강화할 수 있다.

---

## Limit

Spread Placement Group에는 규모 제한이 있다.

강의 및 PDF 기준:

```text
Maximum
7 EC2 Instances
per Availability Zone
per Placement Group
```

따라서 수백 개 Instance를 사용하는 대규모 Distributed System보다는
소수의 중요한 Instance를 서로 격리하는 경우에 적합하다.

---

## Use Cases

- Critical Applications
- High Availability Workloads
- Instance Failure Isolation이 중요한 Workload
- 소수의 중요한 Server를 서로 다른 Hardware에 배치해야 하는 경우

---

# Partition Placement Group

## Concept

Partition Placement Group은 Instance 하나하나를 완전히 분리하는 Spread 방식과 달리,
여러 EC2 Instance를 **Partition이라는 그룹 단위로 나누어 배치**한다.

각 Partition은 서로 다른 Hardware Rack Set을 사용한다.

```text
Availability Zone

Partition 1
├─ EC2
├─ EC2
├─ EC2
└─ EC2

        │

Partition 2
├─ EC2
├─ EC2
├─ EC2
└─ EC2
```

물리적으로는 다음과 같은 개념이다.

```text
Partition 1
↓
Rack Set A


Partition 2
↓
Rack Set B


Partition 3
↓
Rack Set C
```

서로 다른 Partition은 동일한 Hardware Rack을 공유하지 않는다.

---

## Failure Isolation

Partition 하나에 Hardware Failure가 발생하더라도
다른 Partition은 영향을 받지 않도록 설계된다.

```text
Partition 1
Rack Set A
   │
   ▼
Running


Partition 2
Rack Set B
   X
   │
   ▼
Failure


Partition 3
Rack Set C
   │
   ▼
Running
```

따라서 Application이 데이터를 여러 Partition에 분산하여 저장하면
하나의 Rack Failure가 전체 System Failure로 이어질 위험을 줄일 수 있다.

---

## Multiple Availability Zones

Partition Placement Group 역시 하나의 AZ에만 제한되는 것은 아니다.

같은 Region의 여러 Availability Zone에 걸쳐 Partition을 구성할 수 있다.

```text
Region

AZ-A

Partition 1
├─ EC2
├─ EC2
└─ EC2

Partition 2
├─ EC2
├─ EC2
└─ EC2


AZ-B

Partition 3
├─ EC2
├─ EC2
└─ EC2
```

즉,

```text
Partition
≠ Same AZ Only
```

이다.

---

## Scale

강의 및 PDF 기준:

```text
Up to 7 Partitions
per Availability Zone
```

중요한 점은 **7개의 Instance가 아니라 7개의 Partition**이라는 것이다.

각 Partition 안에는 여러 EC2 Instance가 존재할 수 있다.

```text
Partition 1
├─ EC2
├─ EC2
├─ EC2
├─ EC2
└─ ...

Partition 2
├─ EC2
├─ EC2
├─ EC2
├─ EC2
└─ ...
```

따라서 Partition Placement Group은 수백 개의 EC2 Instance를 사용하는
대규모 Distributed System에 사용할 수 있다.

---

## Instance Metadata

EC2 Instance는 Instance Metadata를 이용하여
자신이 어떤 Partition에 배치되어 있는지 확인할 수 있다.

이를 통해 Partition-aware Application이
자신의 물리적 Failure Domain을 인식하고 데이터를 적절하게 분산할 수 있다.

---

## Use Cases

Partition Placement Group은 Partition-aware Distributed Application에 적합하다.

대표적인 예:

```text
HDFS
HBase
Cassandra
Apache Kafka
```

이러한 Application은 원래 여러 Node에 Data를 분산하여 저장하므로
물리적인 Infrastructure 역시 Partition 단위로 분산하여 Failure Risk를 줄일 수 있다.

---

# Spread vs Partition

Spread와 Partition 모두 Hardware Failure를 분산하는 것이 목적이지만,
**격리 단위와 Scale이 다르다.**

## Spread

```text
EC2-1
↓
Hardware A

EC2-2
↓
Hardware B

EC2-3
↓
Hardware C
```

Instance 하나하나를 서로 다른 Hardware에 배치한다.

```text
Isolation Unit
= Individual EC2 Instance
```

따라서 강력한 개별 Instance Failure Isolation을 제공하지만
AZ당 Instance 수가 제한된다.

---

## Partition

```text
Partition 1
├─ EC2
├─ EC2
└─ EC2
↓
Rack Set A


Partition 2
├─ EC2
├─ EC2
└─ EC2
↓
Rack Set B
```

여러 Instance를 하나의 Partition으로 묶고,
Partition끼리 서로 다른 Hardware Rack Set을 사용한다.

```text
Isolation Unit
= Partition
```

따라서 Spread보다 훨씬 많은 Instance를 사용할 수 있다.

---

## Simple Comparison

```text
Spread

EC2 하나
→ Hardware 하나

EC2 하나
→ 다른 Hardware

EC2 하나
→ 또 다른 Hardware
```

```text
Partition

EC2 여러 개
→ Partition A
→ Rack Set A

EC2 여러 개
→ Partition B
→ Rack Set B
```

---

# Precision Time Placement Group

## Concept

Precision Time Placement Group은
EC2 Instance에서 **매우 정밀한 시간 동기화**가 필요한 Workload를 위한 Placement Strategy이다.

```text
EC2 Instances
↓
Precision Time Placement Group
↓
Precision Time Infrastructure
↓
Highly Accurate Clock Synchronization
```

AWS의 Enhanced Amazon Time Sync Service와 함께 사용하여
지원되는 EC2 Instance에서 매우 정밀한 Clock Synchronization을 사용할 수 있도록 한다.

> Precision Time은 기존 강의 및 PDF에 포함되지 않은,
> 이후 AWS에 추가된 Placement Strategy이다.

---

## Why Precise Time Matters

일반적인 Application에서는 Server 간 시간이 약간 차이나도 큰 문제가 되지 않을 수 있다.

하지만 Distributed System에서는 여러 Server에서 동시에 Event가 발생하기 때문에
정확한 Event Ordering과 Timestamp가 중요할 수 있다.

예:

```text
EC2-A
10:00:00.000001
Transaction A

EC2-B
10:00:00.000003
Transaction B
```

Clock이 정확하게 동기화되어 있다면
어떤 Event가 먼저 발생했는지를 보다 정확하게 판단할 수 있다.

---

## Use Cases

Precision Time Placement Group은 다음과 같이
정확한 Timestamp와 Clock Synchronization이 중요한 Workload에 사용할 수 있다.

- Distributed Databases
- Financial Applications
- Transaction Processing
- High-Precision Event Ordering
- Applications requiring precise timestamps

예:

```text
Financial Transaction
↓
Precise Timestamp Required
↓
Precision Time Placement Group
```

또는:

```text
Distributed Database
↓
Transaction Ordering
↓
Accurate Clock Synchronization
↓
Precision Time
```

---

# Placement Strategy Comparison

| Strategy | Placement | Main Goal | Failure Isolation | Typical Use Case |
|---|---|---|---|---|
| Cluster | 같은 AZ에서 Instance를 가깝게 배치 | Network Performance | 낮음 | HPC, Big Data |
| Spread | Instance별 서로 다른 Hardware | Individual Instance Isolation | 매우 높음 | Critical Applications |
| Partition | Partition별 서로 다른 Rack Set | Large-Scale Failure Isolation | Partition 단위 | Kafka, Cassandra, Hadoop |
| Precision Time | 정밀 시간 지원 Infrastructure | Clock Synchronization | 목적이 다름 | Distributed DB, Financial Systems |

---

# Simple Mental Model

Placement Group은 다음 네 문장으로 기억할 수 있다.

```text
Cluster
→ 붙인다
→ Performance
```

```text
Spread
→ 한 대씩 떨어뜨린다
→ Individual Failure Isolation
```

```text
Partition
→ 덩어리별로 떨어뜨린다
→ Large Distributed System
```

```text
Precision Time
→ 시계를 맞춘다
→ Precise Time Synchronization
```

---

# Placement Group Selection Flow

```text
EC2 Placement Requirement
          │
          ▼
Instance 간 Network Performance가 가장 중요한가?
          │
       YES
          │
          ▼
       Cluster
```

```text
개별 Critical Instance를
서로 다른 Hardware에 격리해야 하는가?
          │
       YES
          │
          ▼
        Spread
```

```text
수십~수백 개 Instance를 사용하는
Partition-aware Distributed System인가?
          │
       YES
          │
          ▼
      Partition
```

```text
매우 정밀한 Timestamp와
Clock Synchronization이 필요한가?
          │
       YES
          │
          ▼
   Precision Time
```

---

# Summary

- Placement Group은 EC2 Instance의 물리적 배치 전략을 정의한다.
- 현재 AWS에는 Cluster, Spread, Partition, Precision Time 전략이 있다.
- Cluster는 Instance를 하나의 AZ에서 가깝게 배치한다.
- Cluster는 Low Latency와 High Throughput이 중요한 Workload에 적합하다.
- Cluster는 AZ 장애에 여러 Instance가 동시에 영향을 받을 수 있다.
- Spread는 Instance 하나하나를 서로 다른 Hardware에 배치한다.
- Spread는 여러 AZ에 걸쳐 사용할 수 있다.
- Spread는 Critical Instance의 Failure Isolation에 적합하다.
- 강의 기준 Spread는 Placement Group당 AZ당 최대 7개의 Instance를 지원한다.
- Partition은 여러 Instance를 Partition 단위로 나눈다.
- 서로 다른 Partition은 동일한 Hardware Rack을 공유하지 않는다.
- Partition은 여러 AZ에 걸쳐 사용할 수 있다.
- 강의 기준 Partition은 AZ당 최대 7개의 Partition을 지원한다.
- Partition은 수백 개 Instance 규모의 Distributed System에 적합하다.
- Precision Time은 매우 정밀한 Clock Synchronization이 필요한 Workload를 위한 전략이다.

---

# Exam Notes

## Cluster

문제에서 다음 Keyword가 나오면 Cluster를 생각한다.

```text
Low Latency
High Throughput
High Network Performance
HPC
Big Data Processing
```

정답 후보:

```text
Cluster Placement Group
```

---

## Spread

다음 Keyword가 나오면 Spread를 생각한다.

```text
Critical EC2 Instances
Individual Failure Isolation
Distinct Hardware
Reduce Simultaneous Hardware Failure
```

정답 후보:

```text
Spread Placement Group
```

기억:

```text
Spread
→ EC2 한 대 한 대를 분리
→ AZ당 최대 7 Instances
```

---

## Partition

다음 Keyword가 나오면 Partition을 생각한다.

```text
Large Distributed System
Partition-aware
Rack Failure Isolation
Hundreds of EC2 Instances

HDFS
HBase
Cassandra
Kafka
```

정답 후보:

```text
Partition Placement Group
```

기억:

```text
Partition
→ Instance 여러 대를 Partition으로 묶음
→ Partition끼리 다른 Rack Set
→ AZ당 최대 7 Partitions
```

---

## Precision Time

다음 Keyword가 나오면 Precision Time을 생각한다.

```text
Precise Timestamp
Clock Synchronization
Transaction Ordering
Distributed Database
Financial Application
```

정답 후보:

```text
Precision Time Placement Group
```

---

# Practical Examples

## Example 1: HPC

여러 EC2 Instance가 동시에 복잡한 계산을 수행하고 있으며
Instance 간 매우 빠른 Network Communication이 필요하다.

```text
Low Latency
+
High Throughput
↓
Cluster Placement Group
```

---

## Example 2: Critical Servers

3개의 매우 중요한 EC2 Instance가 있고
하나의 Physical Hardware Failure로 여러 Instance가 동시에 중단되어서는 안 된다.

```text
Individual Hardware Failure Isolation
↓
Spread Placement Group
```

---

## Example 3: Kafka Cluster

수백 개의 EC2 Instance로 Apache Kafka Cluster를 운영한다.

Broker를 여러 Failure Domain에 분산하여
하나의 Rack Failure가 전체 Cluster에 영향을 주는 것을 방지해야 한다.

```text
Large Distributed System
+
Rack Failure Isolation
↓
Partition Placement Group
```

---

## Example 4: Financial Transactions

여러 EC2 Instance에서 금융 Transaction을 처리하고 있으며
각 Transaction의 Timestamp를 매우 정밀하게 기록해야 한다.

```text
Precise Timestamp
+
Clock Synchronization
↓
Precision Time Placement Group
```

---

# 🇯🇵 日本語

## EC2 Placement Groups

Placement Groupは、
EC2 InstanceをAWSの物理Infrastructure上にどのように配置するかを指定する機能である。

現在、主なPlacement Strategyには以下がある。

```text
Cluster
Spread
Partition
Precision Time
```

---

## Cluster Placement Group

Cluster Placement Groupは、
EC2 Instanceを同じAvailability Zone内で近くに配置する。

目的はNetwork Performanceの向上である。

```text
Low Latency
High Throughput
High Performance
```

HPCやBig Data Processingなどに適している。

一方、同じAZにInstanceが集中するため、
AZ Failureによって複数Instanceが同時に影響を受ける可能性がある。

---

## Spread Placement Group

Spread Placement Groupは、
EC2 Instanceをそれぞれ異なるPhysical Hardwareに配置する。

```text
EC2-A → Hardware A
EC2-B → Hardware B
EC2-C → Hardware C
```

Critical Applicationなど、
InstanceごとのFailure Isolationが重要な場合に適している。

Spreadは複数のAvailability Zoneにまたがって構成することもできる。

講義基準では、

```text
Maximum 7 Instances
per AZ
per Placement Group
```

という制限がある。

---

## Partition Placement Group

Partition Placement Groupは、
複数のEC2 InstanceをPartition単位に分割する。

各Partitionは異なるHardware Rack Setを使用する。

```text
Partition A
→ Rack Set A

Partition B
→ Rack Set B
```

そのため、一つのPartitionで障害が発生しても
他のPartitionへの影響を分離できる。

HDFS、HBase、Cassandra、Kafkaなどの
大規模Distributed Systemに適している。

---

## Precision Time Placement Group

Precision Time Placement Groupは、
非常に正確なClock Synchronizationが必要なWorkload向けのStrategyである。

```text
Precise Timestamp
Clock Synchronization
Transaction Ordering
```

Distributed DatabaseやFinancial Applicationなどで利用できる。

---

## Summary

```text
Cluster
→ 近くに配置
→ Performance

Spread
→ Instanceごとに分離
→ Failure Isolation

Partition
→ Partition単位で分離
→ Large Distributed System

Precision Time
→ 正確な時刻同期
→ Precise Clock
```

---

# 🇺🇸 English

## EC2 Placement Groups

EC2 Placement Groups control how EC2 instances are placed on the underlying AWS physical infrastructure.

The current placement strategies include:

```text
Cluster
Spread
Partition
Precision Time
```

---

## Cluster Placement Group

Cluster places EC2 instances close together within a single Availability Zone.

It is designed for workloads requiring:

- Low latency
- High network throughput
- High-performance communication

Typical use cases include HPC and big data processing.

---

## Spread Placement Group

Spread places individual EC2 instances on distinct underlying hardware.

It is designed for critical workloads that require strong failure isolation between individual instances.

A Spread Placement Group can span multiple Availability Zones.

According to the course material:

```text
Maximum 7 instances
per AZ
per Placement Group
```

---

## Partition Placement Group

Partition divides EC2 instances into logical partitions.

Different partitions do not share the same underlying hardware rack sets.

```text
Partition A
→ Rack Set A

Partition B
→ Rack Set B
```

It is designed for large distributed applications such as:

- HDFS
- HBase
- Cassandra
- Apache Kafka

---

## Precision Time Placement Group

Precision Time is designed for workloads that require highly accurate clock synchronization.

Typical requirements include:

- Precise timestamps
- Transaction ordering
- Distributed database synchronization
- Financial transaction processing

---

# Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Placement Group | 배치 그룹 | プレイスメントグループ |
| Placement Strategy | 배치 전략 | 配置戦略 |
| Availability Zone | 가용 영역 | アベイラビリティゾーン |
| Cluster | 클러스터 | クラスター |
| Spread | 분산 | スプレッド |
| Partition | 파티션 | パーティション |
| Precision Time | 정밀 시간 | Precision Time |
| Physical Hardware | 물리 하드웨어 | 物理ハードウェア |
| Rack | 랙 | ラック |
| Failure Isolation | 장애 격리 | 障害分離 |
| Low Latency | 낮은 지연 시간 | 低レイテンシー |
| High Throughput | 높은 처리량 | 高スループット |
| Clock Synchronization | 시간 동기화 | 時刻同期 |
| Timestamp | 타임스탬프 | タイムスタンプ |
| Distributed System | 분산 시스템 | 分散システム |

---

# Review Questions

1. EC2 Placement Group의 목적은 무엇인가?
2. Availability Zone은 Region과 어떤 관계인가?
3. Cluster Placement Group은 Instance를 어떻게 배치하는가?
4. Cluster Placement Group이 Low Latency Workload에 적합한 이유는 무엇인가?
5. Cluster Placement Group의 주요 Failure Risk는 무엇인가?
6. Spread Placement Group은 Instance를 어떻게 배치하는가?
7. Spread Placement Group이 반드시 EC2 Instance 하나당 하나의 AZ를 의미하는가?
8. Spread Placement Group의 강의 기준 Instance 수 제한은 무엇인가?
9. Spread와 Partition의 가장 중요한 차이는 무엇인가?
10. Partition Placement Group에서 Partition은 무엇을 의미하는가?
11. 서로 다른 Partition은 물리 Infrastructure 측면에서 어떻게 격리되는가?
12. Partition Placement Group이 수백 개 EC2 Instance를 지원할 수 있는 이유는 무엇인가?
13. HDFS, HBase, Cassandra, Kafka에 적합한 Placement Strategy는 무엇인가?
14. Precision Time Placement Group의 목적은 무엇인가?
15. 정확한 Transaction Timestamp가 중요한 Application에는 어떤 Placement Strategy가 적합한가?
16. HPC Application에서 Instance 간 Low Latency가 가장 중요하다면 어떤 Strategy를 선택해야 하는가?
17. 소수의 Critical EC2 Instance를 서로 다른 Hardware에 배치하려면 어떤 Strategy를 선택해야 하는가?