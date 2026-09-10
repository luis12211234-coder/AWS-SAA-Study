# EBS Volume Types

## Overview

Amazon EBS는 Workload의 성능과 비용 요구사항에 따라
여러 Volume Type을 제공한다.

```text
EBS
│
├─ gp2 / gp3 → General Purpose SSD
├─ io1 / io2 → Provisioned IOPS SSD
├─ st1       → Throughput Optimized HDD
└─ sc1       → Cold HDD
```

Volume Type을 선택할 때 주요 기준은:

```text
Size
IOPS
Throughput
Cost
```

이다.

---

# 1. gp2 / gp3 - General Purpose SSD

범용 Workload를 위한 비용 효율적인 SSD이다.

```text
General Purpose
Low Latency
Cost Effective
```

대표적인 용도:

```text
Boot Volume
Virtual Desktop
Development / Test
General Applications
```

### gp3

최신 세대 General Purpose SSD이다.

강의 기준 기본 성능:

```text
3,000 IOPS
125 MB/s Throughput
```

gp3의 중요한 특징은 **Size와 Performance를 독립적으로 설정할 수 있다는 것**이다.

```text
Volume Size
IOPS
Throughput

→ Independently configurable
```

### gp2

gp2에서는 Volume Size와 IOPS가 연결되어 있다.

```text
Volume Size ↑
↓
IOPS ↑
```

시험 핵심:

```text
gp2
→ Size and IOPS linked

gp3
→ Size / IOPS / Throughput independently configurable
```

---

# 2. io1 / io2 - Provisioned IOPS SSD

매우 높은 Storage Performance와
일관된 IOPS가 필요한 Workload를 위한 SSD이다.

대표적인 사용 사례:

```text
Mission-Critical Applications
Databases
Low-Latency Workloads
High IOPS Requirements
```

필요한 IOPS를 Storage Size와 별도로 Provision할 수 있다.

```text
Need consistent high IOPS
↓
io1 / io2
```

**io2 Block Express**는 특히 높은 성능을 제공하며,
강의에서는 최대 256,000 IOPS 수준까지 설명한다.

정확한 성능 한도는 Volume 및 EC2 구성에 따라 달라질 수 있으므로
시험에서는 **Provisioned IOPS = 고성능/중요 DB**라는 선택 기준을 우선 기억한다.

---

# 3. st1 - Throughput Optimized HDD

`st1`은 높은 **Throughput**이 필요한 대용량 Workload를 위한 HDD이다.

대표적인 사용 사례:

```text
Big Data
Data Warehouse
Log Processing
```

핵심:

```text
Large Sequential Data
+
High Throughput
+
Low Cost
↓
st1
```

Boot Volume으로 사용할 수 없다.

---

# 4. sc1 - Cold HDD

`sc1`은 접근 빈도가 낮은 데이터를 위한
가장 저렴한 HDD 계열이다.

```text
Infrequently Accessed Data
+
Lowest Cost
↓
sc1
```

Archive 성격의 데이터처럼
자주 접근하지 않는 Workload에 적합하다.

Boot Volume으로 사용할 수 없다.

---

# 5. Boot Volume Support

강의에서 설명한 Boot Volume 지원 여부:

```text
Boot Volume O
→ gp2
→ gp3
→ io1
→ io2
```

```text
Boot Volume X
→ st1
→ sc1
```

---

# 6. Quick Comparison

| Type | Storage | Main Purpose |
|---|---|---|
| gp2 | SSD | General Purpose |
| gp3 | SSD | General Purpose, flexible performance |
| io1 / io2 | SSD | High-performance / Database |
| st1 | HDD | High Throughput |
| sc1 | HDD | Lowest Cost / Cold Data |

선택 기준:

```text
General Purpose SSD
→ gp3

Consistent / Very High IOPS
→ io1 / io2

High Throughput HDD
→ st1

Lowest-Cost HDD
→ sc1
```

---

# Exam Notes

가장 중요한 구분:

```text
gp2
→ Size와 IOPS 연결

gp3
→ Size / IOPS / Throughput 독립 설정
```

```text
Critical Database
Consistent High IOPS
Low Latency
→ io1 / io2
```

```text
Big Data / Data Warehouse / Logs
High Throughput
→ st1
```

```text
Cold / Infrequently Accessed Data
Lowest Cost
→ sc1
```

그리고:

```text
Boot Volume
→ SSD Types O
→ HDD Types X
```

---

# Summary

```text
gp3
= General Purpose SSD

io1 / io2
= High Performance SSD

st1
= Throughput Optimized HDD

sc1
= Cold / Lowest-Cost HDD
```

한 줄 암기:

```text
범용 → gp3
고성능 DB → io
대용량 처리 → st1
싸게 보관 → sc1
```

---

# Japanese Summary

Amazon EBSにはWorkloadに応じて
複数のVolume Typeがあります。

```text
gp2 / gp3
→ 汎用SSD

io1 / io2
→ プロビジョンドIOPS SSD

st1
→ スループット最適化HDD

sc1
→ Cold HDD
```

重要な違い：

```text
gp2
→ Volume SizeとIOPSが関連

gp3
→ Size / IOPS / Throughputを独立して設定可能
```

高性能なDatabaseにはio1/io2、
高いThroughputにはst1、
低頻度アクセス・低コストにはsc1が適しています。

---

# English Summary

Amazon EBS provides multiple volume types for different workloads.

```text
gp2 / gp3
→ General Purpose SSD

io1 / io2
→ Provisioned IOPS SSD

st1
→ Throughput Optimized HDD

sc1
→ Cold HDD
```

The key difference between gp2 and gp3 is:

```text
gp2
→ IOPS depends on volume size

gp3
→ Size, IOPS, and throughput can be configured independently
```

Provisioned IOPS volumes are suitable for performance-sensitive databases,
while st1 focuses on throughput and sc1 focuses on low cost.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| General Purpose SSD | 汎用SSD | 범용 SSD |
| Provisioned IOPS SSD | プロビジョンドIOPS SSD | 프로비저닝된 IOPS SSD |
| Throughput Optimized HDD | スループット最適化HDD | 처리량 최적화 HDD |
| Cold HDD | Cold HDD / コールドHDD | 저빈도 접근용 저비용 HDD |
| IOPS | IOPS（1秒あたりのI/O操作数） | 초당 입출력 작업 수 |
| Throughput | スループット | 단위 시간당 데이터 처리량 |
| Low Latency | 低レイテンシー | 낮은 지연 시간 |
| Boot Volume | ブートボリューム | 운영체제를 부팅하는 볼륨 |
| Mission-Critical | ミッションクリティカル | 업무상 매우 중요한 시스템 |
| Data Warehouse | データウェアハウス | 분석용 대규모 데이터 저장 시스템 |

---

# Review Questions

### Q1. 일반적인 Workload에 적합한 EBS SSD는?

```text
gp2 / gp3
```

### Q2. gp2와 gp3의 중요한 차이는?

gp2는 Volume Size와 IOPS가 연결되어 있지만,
gp3는 Size, IOPS, Throughput을 독립적으로 설정할 수 있다.

### Q3. 매우 높은 IOPS가 필요한 중요한 Database에는?

```text
io1 / io2
```

### Q4. Big Data나 Log Processing처럼 높은 Throughput이 중요하다면?

```text
st1
```

### Q5. 접근 빈도가 낮고 가장 낮은 비용이 중요하다면?

```text
sc1
```

### Q6. st1과 sc1을 Boot Volume으로 사용할 수 있는가?

사용할 수 없다.