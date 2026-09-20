# EC2 Instance Store

## Overview

**EC2 Instance Store**는 EC2가 실행되는 물리 서버에 직접 연결된 **Local Storage**이다.

```text
Physical Server
│
├─ EC2 Instance
└─ Instance Store
```

EBS가 Network Storage인 것과 달리,
Instance Store는 Local Hardware Disk를 사용하므로 매우 높은 I/O 성능을 제공한다.

```text
EBS
→ Network Storage

Instance Store
→ Local Storage
→ Very High I/O Performance
```

---

# 1. High Performance Local Storage

Instance Store는 물리 서버에 직접 연결된 Storage를 사용한다.

따라서 매우 높은 Disk I/O 성능이 필요한 Workload에 적합하다.

```text
Need very high disk performance
↓
EC2 Instance Store
```

강의의 구체적인 IOPS 수치는 Instance Type에 따라 달라질 수 있으므로
핵심은 **Instance Store = Very High I/O Performance**로 기억한다.

---

# 2. Ephemeral Storage

Instance Store의 가장 중요한 특징은 **Ephemeral Storage**라는 것이다.

강의에서는 Instance가 Stop 또는 Terminate되면
Instance Store의 데이터가 손실된다고 설명한다.

또한 기반 Physical Host에 장애가 발생하면
데이터를 잃을 위험이 있다.

따라서 장기적인 데이터 보관에는 적합하지 않다.

```text
Instance Store
→ Temporary Storage
→ Data Loss Risk
```

---

# 3. Use Cases

Instance Store는 데이터가 사라져도
다시 생성할 수 있는 임시 데이터에 적합하다.

대표적인 사용 사례:

```text
Buffer
Cache
Scratch Data
Temporary Content
```

반대로 장기적인 데이터 보관이 필요하다면:

```text
Persistent Data
↓
EBS
```

를 고려한다.

Instance Store의 데이터가 중요하다면
Backup 또는 Replication은 사용자가 구성해야 한다.

---

# 4. EBS vs Instance Store

| | EBS | Instance Store |
|---|---|---|
| 연결 방식 | Network | Local Hardware |
| 데이터 지속성 | Persistent | Ephemeral |
| I/O 성능 | High | Very High |
| EC2와 독립적 관리 | 가능 | 불가능 |
| 주요 용도 | 장기 데이터 | 임시 고성능 데이터 |

핵심:

```text
Persistence
→ EBS

Very High Local I/O
→ Instance Store
```

---

# Exam Notes

```text
High-performance local disk
Very high I/O
Temporary data
Cache / Buffer / Scratch
→ EC2 Instance Store
```

```text
Persistent Storage
Long-term Data
→ EBS
```

Instance Store는 기반 Hardware 장애 시에도
데이터 손실 위험이 있으므로 중요한 데이터는
Backup 또는 Replication이 필요하다.

---

# Summary

```text
EC2 Instance Store
= High-performance Local Storage
= Ephemeral Storage
```

한 줄 암기:

```text
EBS            = 느려도(?) 오래 보관하는 Network Storage
Instance Store = 빠르지만 임시인 Local Storage
```

※ EBS가 느리다는 의미가 아니라,
Instance Store와의 상대적인 개념 구분이다.

---

# Japanese Summary

**EC2 Instance Store**は、
EC2が動作する物理サーバーに接続されたLocal Storageです。

```text
EBS
→ Network Storage

Instance Store
→ Local Storage
→ Very High I/O Performance
```

ただし、Instance Storeは**一時的なストレージ（Ephemeral Storage）**です。

主な用途：

```text
Buffer
Cache
Scratch Data
Temporary Data
```

長期的なデータ保存にはEBSが適しています。

---

# English Summary

**EC2 Instance Store** provides high-performance local storage
physically attached to the host running the EC2 instance.

```text
EBS
→ Network Storage

Instance Store
→ Local Storage
→ Very High I/O Performance
```

Instance Store is ephemeral and is suitable for temporary data such as
buffers, caches, and scratch data.

For persistent data, EBS is more appropriate.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Instance Store | インスタンスストア | EC2 호스트에 연결된 로컬 스토리지 |
| Local Storage | ローカルストレージ | 물리 서버에 직접 연결된 저장소 |
| Ephemeral Storage | 一時ストレージ | 영구 보존을 보장하지 않는 임시 저장소 |
| I/O Performance | I/O性能 | 입출력 성능 |
| Buffer | バッファ | 데이터를 임시로 저장하는 공간 |
| Cache | キャッシュ | 빠른 접근을 위한 임시 데이터 저장 공간 |
| Scratch Data | スクラッチデータ | 작업 과정에서 사용하는 임시 데이터 |
| Replication | レプリケーション | 데이터를 다른 위치에 복제하는 것 |
| Hardware Failure | ハードウェア障害 | 물리 장비 장애 |

---

# Review Questions

### Q1. EC2 Instance Store란?

EC2가 실행되는 Physical Host에 연결된
고성능 Local Storage이다.

### Q2. Instance Store의 가장 큰 장점은?

매우 높은 I/O 성능이다.

### Q3. 장기적인 데이터 저장에 적합한가?

아니다. Ephemeral Storage이므로
장기 데이터에는 EBS가 더 적합하다.

### Q4. 대표적인 사용 사례는?

```text
Buffer
Cache
Scratch Data
Temporary Content
```

### Q5. Instance Store에서 중요한 데이터를 사용한다면?

데이터 손실에 대비해 Backup 또는 Replication을 구성해야 한다.