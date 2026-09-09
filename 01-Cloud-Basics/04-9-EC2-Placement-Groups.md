# EC2 Placement Groups

## Overview

EC2 Placement Group은 EC2 Instance가 AWS의 물리적 Infrastructure에 어떻게 배치될지에 대한 전략을 정의하는 기능이다.

AWS Hardware를 직접 제어하는 것은 아니지만,
EC2 Instance의 물리적 배치 방식을 AWS에 지정할 수 있다.

Placement Group에는 세 가지 주요 전략이 있다.

```text
Cluster
Spread
Partition
```

---

## Cluster Placement Group

Cluster Placement Group은 여러 EC2 Instance를 하나의 Availability Zone 안에서 서로 가까운 위치에 배치한다.

```text
Same Availability Zone

EC2  EC2  EC2  EC2
 ↕    ↕    ↕
Low Latency
High Throughput
```

### Advantages

- 매우 낮은 Network Latency
- 높은 Network Throughput
- 고성능 Compute Workload에 적합

강의와 PDF에서는 Enhanced Networking 사용 시
인스턴스 간 약 10 Gbps Network 성능 예시를 설명한다.

### Disadvantages

모든 Instance가 동일한 AZ에 위치하기 때문에
해당 AZ에 장애가 발생하면 여러 Instance가 동시에 영향을 받을 수 있다.

```text
AZ Failure
↓
Cluster Instances
↓
Multiple Failures
```

### Use Cases

- Big Data Jobs
- High Performance Computing
- Extremely Low-Latency Applications
- High Network Throughput Applications

---

## Spread Placement Group

Spread Placement Group은 EC2 Instance를 서로 다른 물리 Hardware에 분산 배치한다.

```text
EC2-A
↓
Hardware 1

EC2-B
↓
Hardware 2

EC2-C
↓
Hardware 3
```

### Advantages

- 여러 Availability Zone에 걸쳐 사용할 수 있음
- 인스턴스 간 동시 Hardware Failure 위험 감소
- 각 Instance의 장애를 서로 격리할 수 있음

```text
Hardware 1 Failure
↓
EC2-A Failure

EC2-B / EC2-C
→ Unaffected
```

### Limit

강의 및 PDF 기준:

```text
Maximum
7 EC2 Instances
per AZ
per Placement Group
```

### Use Cases

- Critical Applications
- High Availability Workloads
- Instances that must be isolated from each other's hardware failures

---

## Partition Placement Group

Partition Placement Group은 EC2 Instance들을 여러 Partition으로 나누어 배치한다.

각 Partition은 서로 다른 물리 Rack Set을 사용한다.

```text
Partition 1
├─ EC2
├─ EC2
└─ EC2

Partition 2
├─ EC2
├─ EC2
└─ EC2
```

서로 다른 Partition의 Instance들은 동일한 Hardware Rack을 공유하지 않는다.

```text
Partition 1
→ Rack Set A

Partition 2
→ Rack Set B

Partition 3
→ Rack Set C
```

따라서 하나의 Partition에 장애가 발생하더라도
다른 Partition은 영향을 받지 않도록 설계할 수 있다.

### Scale

강의 및 PDF 기준:

```text
Up to 7 Partitions per AZ
```

Partition Placement Group은 같은 Region의 여러 Availability Zone에 걸칠 수 있으며,
수백 개의 EC2 Instance까지 확장할 수 있다.

### Metadata

EC2 Instance는 Instance Metadata를 통해
자신이 어떤 Partition에 배치되어 있는지 확인할 수 있다.

### Use Cases

Partition-aware Distributed Applications에 적합하다.

대표적인 예:

- HDFS
- HBase
- Cassandra
- Apache Kafka

---

## Cluster vs Spread vs Partition

| Strategy | Placement | Main Goal | Scale | Typical Use Case |
|---|---|---|---|---|
| Cluster | 같은 AZ에 가깝게 배치 | Performance | 여러 Instance | HPC, Big Data |
| Spread | 각 Instance를 다른 Hardware에 배치 | Failure Isolation | AZ당 최대 7대 | Critical Applications |
| Partition | 여러 Rack Partition으로 분리 | Large-Scale Failure Isolation | 수백 대 | Hadoop, Cassandra, Kafka |

---

## Simple Mental Model

```text
Cluster
→ 붙인다
→ Performance

Spread
→ 흩뿌린다
→ Isolation

Partition
→ 그룹으로 나눈다
→ Large Distributed System
```

---

## Summary

- Placement Group은 EC2 Instance의 물리적 배치 전략을 정의한다.
- Cluster는 같은 AZ에서 Instance를 가깝게 배치하여 낮은 Latency와 높은 Throughput을 제공한다.
- Cluster는 AZ 장애 시 여러 Instance가 동시에 영향을 받을 수 있다.
- Spread는 Instance를 서로 다른 Hardware에 배치하여 장애를 격리한다.
- Spread는 AZ당 Placement Group당 최대 7개의 Instance로 제한된다.
- Partition은 여러 Rack Set 기반 Partition으로 Instance를 분산한다.
- Partition은 대규모 Distributed Workload에 적합하다.
- Partition은 AZ당 최대 7개의 Partition을 가질 수 있다.
- Partition은 수백 개의 EC2 Instance까지 확장할 수 있다.

---

## Exam Notes

### Cluster

```text
Low Latency
High Throughput
Same AZ
↓
Cluster
```

```text
HPC
Big Data Job
↓
Cluster
```

### Spread

```text
Critical Application
+
Instance Failure Isolation
↓
Spread
```

```text
Spread
→ Different Hardware
→ Max 7 Instances per AZ per Placement Group
```

### Partition

```text
Large Distributed Application
+
Rack-Level Isolation
↓
Partition
```

```text
Hadoop
HDFS
HBase
Cassandra
Kafka
↓
Partition
```

---

## Practical Example

### Cluster Example

대규모 병렬 연산을 수행하는 EC2 Application이
Instance 간 매우 빠른 Network Communication을 필요로 한다.

```text
High Throughput
+
Low Latency
↓
Cluster Placement Group
```

### Spread Example

3개의 중요 EC2 Server가 있으며
하나의 물리 Hardware 장애로 여러 Server가 동시에 중단되어서는 안 된다.

```text
Hardware Failure Isolation
↓
Spread Placement Group
```

### Partition Example

대규모 Kafka Cluster를 운영하며
Broker들을 서로 다른 Rack Failure Domain에 분산하고 싶다.

```text
Kafka
+
Partition-Aware Architecture
+
Rack Isolation
↓
Partition Placement Group
```

---

# 🇯🇵 日本語

## EC2 Placement Groups

Placement GroupはEC2 InstanceをAWSの物理Infrastructure上に
どのように配置するかを指定する機能である。

主なStrategyは以下の3つ。

```text
Cluster
Spread
Partition
```

### Cluster

同じAvailability Zone内でInstanceを近くに配置する。

```text
Low Latency
High Throughput
```

HPCやBig Data Workloadに適している。

### Spread

Instanceを異なるPhysical Hardwareに分散する。

Hardware Failureを互いに分離したいCritical Applicationに適している。

```text
Max 7 Instances per AZ per Placement Group
```

### Partition

Instanceを複数のPartitionに分割し、
それぞれ異なるRack Setに配置する。

HDFS、HBase、Cassandra、Kafkaなどの
大規模Distributed Applicationに適している。

---

# 🇺🇸 English

## EC2 Placement Groups

EC2 Placement Groups control how EC2 instances are placed on AWS physical infrastructure.

There are three main placement strategies:

```text
Cluster
Spread
Partition
```

### Cluster

Places instances close together within a single Availability Zone.

Best for workloads requiring:

- Low latency
- High network throughput
- High performance computing

### Spread

Places instances on distinct underlying hardware.

Best for critical workloads requiring strong failure isolation.

```text
Maximum 7 instances per AZ per placement group
```

### Partition

Distributes instances across separate partitions backed by different rack sets.

Best for large distributed systems such as:

- HDFS
- HBase
- Cassandra
- Kafka

---

## Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Placement Group | 배치 그룹 | プレイスメントグループ |
| Cluster | 클러스터 | クラスター |
| Spread | 분산 | スプレッド |
| Partition | 파티션 | パーティション |
| Physical Hardware | 물리 하드웨어 | 物理ハードウェア |
| Rack | 랙 | ラック |
| Failure Isolation | 장애 격리 | 障害分離 |
| Low Latency | 낮은 지연 시간 | 低レイテンシー |
| High Throughput | 높은 처리량 | 高スループット |
| Distributed System | 분산 시스템 | 分散システム |

---

## Review Questions

1. EC2 Placement Group의 목적은 무엇인가?
2. Cluster Placement Group은 Instance를 어떻게 배치하는가?
3. Cluster가 Low Latency Workload에 적합한 이유는 무엇인가?
4. Cluster Placement Group의 가장 큰 위험은 무엇인가?
5. Spread Placement Group은 Hardware Failure를 어떻게 격리하는가?
6. Spread Placement Group의 Instance 수 제한은 무엇인가?
7. Partition Placement Group은 Spread와 어떤 차이가 있는가?
8. Partition Placement Group에서 Partition은 무엇을 의미하는가?
9. Partition Placement Group은 왜 대규모 Distributed Application에 적합한가?
10. Hadoop, Cassandra, Kafka에 적합한 Placement Strategy는 무엇인가?