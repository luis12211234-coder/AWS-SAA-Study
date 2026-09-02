# EC2 Instance Types

## Overview

Amazon EC2는 다양한 작업 유형에 맞게 여러 종류의 Instance Type을 제공한다.

각 Instance Type은 CPU, Memory, Storage, Network 성능 등의 특성이 다르며, 특정 Workload에 맞게 최적화되어 있다.

이번 내용에서는 EC2 Instance Type의 Naming Convention과 대표적인 Instance Category를 정리한다.

---

## EC2 Instance Naming Convention

EC2 Instance Type에는 일정한 Naming Convention이 있다.

예시

```text
m5.2xlarge
```

각 부분의 의미는 다음과 같다.

```text
m
↓
Instance Class

5
↓
Generation

2xlarge
↓
Instance Size
```

### Instance Class

첫 번째 문자는 Instance의 계열 또는 목적을 나타낸다.

예시

```text
m → General Purpose
c → Compute Optimized
r → Memory Optimized
```

---

### Generation

숫자는 Instance Family의 Generation을 나타낸다.

예시

```text
m5
m6
```

AWS가 새로운 하드웨어와 기능을 제공하면서 세대가 증가한다.

---

### Instance Size

마지막 부분은 같은 Instance Family 안에서의 크기를 나타낸다.

예시

```text
small
large
xlarge
2xlarge
4xlarge
```

일반적으로 크기가 커질수록 다음 자원이 증가한다.

- vCPU
- Memory
- Network Performance

---

## General Purpose Instances

General Purpose Instance는 다양한 Workload에서 사용할 수 있도록 CPU, Memory, Network 성능의 균형을 제공한다.

대표적인 사용 사례

- Web Server
- Code Repository
- 일반적인 Application Server

특징

```text
Compute
+
Memory
+
Networking
↓
Balanced
```

강의에서는 General Purpose Instance인 `t2.micro`를 실습에 사용한다.

---

## Compute Optimized Instances

Compute Optimized Instance는 높은 CPU 성능이 필요한 Workload에 적합하다.

일반적으로 Instance Family 이름이 `C`로 시작한다.

예시

```text
C5
C6
```

대표적인 사용 사례

- Batch Processing
- Media Transcoding
- High Performance Web Server
- High Performance Computing (HPC)
- Scientific Computing
- Machine Learning
- Dedicated Gaming Server

핵심

```text
CPU Performance
↑
```

즉, 많은 계산을 빠르게 처리해야 하는 Workload에 적합하다.

---

## Memory Optimized Instances

Memory Optimized Instance는 대용량 데이터를 Memory에서 빠르게 처리해야 하는 Workload에 적합하다.

대표적으로 `R` 계열이 사용된다.

예시

```text
R5
R6
```

강의에서는 R이 RAM을 연상시키는 이름이라고 설명한다.

다른 Memory Optimized Family로는 다음과 같은 계열도 존재한다.

```text
X1
Z1
```

대표적인 사용 사례

- High Performance Relational Database
- High Performance NoSQL Database
- Distributed Cache
- In-Memory Database
- Business Intelligence
- Real-Time Processing of Large Unstructured Data

예시 서비스

```text
Amazon ElastiCache
```

핵심

```text
RAM
↑
```

---

## Storage Optimized Instances

Storage Optimized Instance는 Local Storage에서 대규모 Dataset을 빠르게 읽고 쓰는 Workload에 적합하다.

대표적으로 다음 계열이 존재한다.

```text
I
D
H
```

대표적인 사용 사례

- OLTP Systems
- Relational Database
- NoSQL Database
- Redis Cache
- Data Warehousing
- Distributed File Systems

핵심

```text
Local Storage
+
High Sequential Read / Write
```

Storage 처리 성능이 중요한 Workload에 적합하다.

---

## Instance Type Comparison

EC2 Instance Type에 따라 CPU, Memory, Storage, Network Performance 등이 크게 달라진다.

PDF의 예시 비교는 다음과 같다. fileciteturn1file0

| Instance | vCPU | Memory | Storage | Network Performance |
|---|---:|---:|---|---|
| t2.micro | 1 | 1 GiB | EBS Only | Low to Moderate |
| t2.xlarge | 4 | 16 GiB | EBS Only | Moderate |
| c5d.4xlarge | 16 | 32 GiB | 1 × 400 GB NVMe SSD | Up to 10 Gbps |
| r5.16xlarge | 64 | 512 GiB | EBS Only | 20 Gbps |
| m5.8xlarge | 32 | 128 GiB | EBS Only | 10 Gbps |

이를 통해 Instance Family마다 CPU, Memory, Storage, Network 특성이 다르다는 것을 알 수 있다.

---

## Choosing an Instance Type

EC2 Instance Type은 Application의 Workload 특성에 따라 선택한다.

```text
일반적인 Web / Application
→ General Purpose

CPU-intensive Workload
→ Compute Optimized

Large In-Memory Dataset
→ Memory Optimized

Storage-intensive Workload
→ Storage Optimized
```

---

## Instance Comparison Tools

AWS는 다양한 EC2 Instance Type을 제공하기 때문에 실제 환경에서는 Instance 정보를 비교할 필요가 있다.

강의에서는 다음 사이트를 예시로 소개한다.

```text
instances.vantage.sh
```

이 사이트에서는 다음과 같은 정보를 비교할 수 있다.

- Instance Name
- vCPU
- Memory
- Pricing
- Instance Family

AWS 공식 Instance Type 페이지에서도 최신 Instance 종류와 특성을 확인할 수 있다.

---

## Summary

- EC2에는 Workload별로 최적화된 다양한 Instance Type이 있다.
- Instance 이름은 Class, Generation, Size로 구성된다.
- General Purpose는 CPU, Memory, Network의 균형이 좋다.
- Compute Optimized는 CPU 집약적인 Workload에 적합하다.
- Memory Optimized는 대규모 In-Memory 데이터 처리에 적합하다.
- Storage Optimized는 Local Storage의 높은 Read / Write 성능이 필요한 작업에 적합하다.
- Instance Type마다 vCPU, Memory, Storage, Network Performance가 다르다.

---

## Exam Notes

- `m5.2xlarge`
  - `m` = Instance Class
  - `5` = Generation
  - `2xlarge` = Instance Size
- General Purpose = 균형 잡힌 Compute / Memory / Networking
- Compute Optimized = CPU-intensive Workloads
- Compute Optimized Family는 일반적으로 `C`로 시작
- Memory Optimized = Large In-Memory Datasets
- Memory Optimized Family의 대표적인 예는 `R`
- Storage Optimized = High Sequential Read / Write on Local Storage
- Workload의 특성에 따라 적절한 Instance Type을 선택한다.

---

## Practical Example

Application별 Instance 선택 예시

```text
Web Server
↓
General Purpose

Video Transcoding
↓
Compute Optimized

In-Memory Database
↓
Memory Optimized

Data Warehouse
↓
Storage Optimized
```

예를 들어 대규모 웹 서비스를 운영한다고 가정한다.

일반적인 Web Server는 General Purpose Instance를 사용할 수 있지만,

Video Transcoding처럼 CPU 사용량이 높은 작업은 Compute Optimized Instance가 더 적합하다.

반대로 대규모 데이터를 Memory에 올려 처리하는 Database는 Memory Optimized Instance가 적합하다.

---

# 🇯🇵 日本語

## EC2 Instance Types

Amazon EC2には、Workloadに応じて最適化されたさまざまなInstance Typeが存在する。

主な種類は以下のとおりである。

- General Purpose
- Compute Optimized
- Memory Optimized
- Storage Optimized

---

## Naming Convention

例

```text
m5.2xlarge
```

```text
m
→ Instance Class

5
→ Generation

2xlarge
→ Instance Size
```

---

## General Purpose

Compute、Memory、Networkingのバランスが良い。

主な用途

- Web Server
- Code Repository
- Application Server

---

## Compute Optimized

CPU性能が重要なWorkloadに適している。

主な用途

- Batch Processing
- Media Transcoding
- HPC
- Machine Learning
- Gaming Server

代表的なFamilyは `C` で始まる。

---

## Memory Optimized

大量のデータをMemory上で処理するWorkloadに適している。

主な用途

- Relational Database
- NoSQL Database
- In-Memory Database
- Distributed Cache
- Real-Time Processing

代表的なFamilyは `R` である。

---

## Storage Optimized

Local Storageへの高速なRead / Writeが必要なWorkloadに適している。

主な用途

- OLTP
- Database
- Data Warehouse
- Distributed File System

---

## Summary

- Instance TypeはWorkloadに応じて選択する。
- General Purposeはバランス型。
- Compute OptimizedはCPU重視。
- Memory OptimizedはRAM重視。
- Storage OptimizedはStorage I/O重視。

---

# 🇺🇸 English

## EC2 Instance Types

Amazon EC2 provides multiple instance types optimized for different workloads.

The main categories covered here are:

- General Purpose
- Compute Optimized
- Memory Optimized
- Storage Optimized

---

## Naming Convention

Example:

```text
m5.2xlarge
```

```text
m
→ Instance Class

5
→ Generation

2xlarge
→ Instance Size
```

---

## General Purpose

General Purpose instances provide a balance of compute, memory, and networking.

Typical use cases:

- Web servers
- Code repositories
- Application servers

---

## Compute Optimized

Compute Optimized instances are designed for CPU-intensive workloads.

Typical use cases:

- Batch processing
- Media transcoding
- HPC
- Machine learning
- Dedicated gaming servers

These instance families commonly start with `C`.

---

## Memory Optimized

Memory Optimized instances are designed for workloads that process large datasets in memory.

Typical use cases:

- Relational databases
- NoSQL databases
- Distributed caches
- In-memory databases
- Real-time processing

A common family is `R`.

---

## Storage Optimized

Storage Optimized instances are designed for workloads requiring high sequential read and write access to local storage.

Typical use cases:

- OLTP
- Databases
- Data warehouses
- Distributed file systems

---

## Summary

- Choose an EC2 Instance Type based on the workload.
- General Purpose balances compute, memory, and networking.
- Compute Optimized focuses on CPU performance.
- Memory Optimized focuses on RAM.
- Storage Optimized focuses on local storage performance.

---

## Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Instance Type | 인스턴스 유형 | インスタンスタイプ |
| Instance Class | 인스턴스 클래스 | インスタンスクラス |
| Generation | 세대 | 世代 |
| Instance Size | 인스턴스 크기 | インスタンスサイズ |
| General Purpose | 범용 | 汎用 |
| Compute Optimized | 컴퓨팅 최적화 | コンピューティング最適化 |
| Memory Optimized | 메모리 최적화 | メモリ最適化 |
| Storage Optimized | 스토리지 최적화 | ストレージ最適化 |
| Workload | 워크로드 | ワークロード |
| Batch Processing | 일괄 처리 | バッチ処理 |
| Media Transcoding | 미디어 트랜스코딩 | メディアトランスコーディング |
| High Performance Computing | 고성능 컴퓨팅 | 高性能コンピューティング |
| In-Memory Database | 인메모리 데이터베이스 | インメモリデータベース |
| Data Warehouse | 데이터 웨어하우스 | データウェアハウス |
| OLTP | 온라인 트랜잭션 처리 | オンライントランザクション処理 |
| vCPU | 가상 CPU | 仮想CPU |

---

## Review Questions

1. `m5.2xlarge`에서 `m`, `5`, `2xlarge`는 각각 무엇을 의미하는가?

2. General Purpose Instance의 특징은 무엇인가?

3. Compute Optimized Instance는 어떤 Workload에 적합한가?

4. Compute Optimized Instance Family는 일반적으로 어떤 문자로 시작하는가?

5. Memory Optimized Instance는 어떤 작업에 적합한가?

6. `R` 계열 Instance가 특히 강점을 가지는 자원은 무엇인가?

7. Storage Optimized Instance는 어떤 종류의 작업에 사용되는가?

8. Video Transcoding에는 어떤 Instance Category가 적합한가?

9. In-Memory Database에는 어떤 Instance Category가 적합한가?

10. EC2 Instance Type을 선택할 때 확인해야 하는 주요 자원은 무엇인가?
