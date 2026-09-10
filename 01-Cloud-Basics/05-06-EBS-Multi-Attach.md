# EBS Multi-Attach

## Overview

**EBS Multi-Attach**는 하나의 EBS Volume을
같은 Availability Zone에 있는 여러 EC2 Instance에
동시에 연결할 수 있는 기능이다.

```text
        EBS Volume
        io1 / io2
       /    |    \
      /     |     \
    EC2    EC2    EC2
```

각 EC2 Instance는 동일한 EBS Volume에 대해
Read / Write 권한을 가질 수 있다.

---

# 1. Supported Volume Types

Multi-Attach는 모든 EBS Volume에서 사용할 수 있는 기능이 아니다.

```text
Supported
→ io1
→ io2
```

```text
Not Supported
→ gp2
→ gp3
→ st1
→ sc1
```

핵심:

```text
EBS Multi-Attach
→ io1 / io2 family
```

---

# 2. Same Availability Zone Only

EBS Volume은 Availability Zone에 종속된다.

따라서 Multi-Attach 역시
**같은 AZ 안에서만 사용할 수 있다.**

```text
Same AZ
EC2-A ─┐
EC2-B ─┼─ EBS
EC2-C ─┘
```

```text
Different AZ
→ Not Supported
```

중요:

```text
Multi-Attach
≠ Multi-AZ
```

---

# 3. Read and Write Access

Multi-Attach된 모든 EC2 Instance는
같은 EBS Volume에 Read / Write 작업을 수행할 수 있다.

```text
EC2-A ─┐
EC2-B ─┼─ Read / Write
EC2-C ─┘
        ↓
      EBS
```

따라서 애플리케이션은
**Concurrent Write**를 안전하게 관리할 수 있어야 한다.

---

# 4. Cluster-Aware File System

여러 EC2가 같은 Volume에 동시에 접근하기 때문에
Cluster 환경을 인식할 수 있는 File System이 필요하다.

```text
Multi-Attach
↓
Cluster-Aware File System Required
```

강의에서는 일반적인 File System인:

```text
XFS
EXT4
```

등은 Multi-Attach용 Cluster File System이 아니라고 설명한다.

---

# 5. Use Cases

대표적인 사용 사례:

```text
Clustered Linux Applications
High Application Availability
Concurrent Access to Shared Storage
```

예:

```text
Teradata
```

특히 여러 EC2가 하나의 고성능 Storage를 공유해야 하는
Clustered Application에서 사용할 수 있다.

---

# 6. Limits

강의 기준:

```text
Maximum
→ 16 EC2 Instances
```

하나의 Multi-Attach EBS Volume을
동시에 최대 16개의 EC2 Instance에 연결할 수 있다.

---

# Exam Notes

가장 중요한 조건:

```text
EBS Multi-Attach
→ io1 / io2
→ Same AZ
→ Multiple EC2
→ Read / Write
→ Max 16 Instances
→ Cluster-Aware File System
```

시험 함정:

```text
Multi-Attach across multiple AZs
→ X
```

```text
gp3 Multi-Attach
→ X
```

```text
Normal XFS / EXT4 for shared concurrent access
→ X
```

---

# Summary

```text
EBS Multi-Attach
= One EBS Volume
+ Multiple EC2 Instances
+ Same AZ
```

핵심 암기:

```text
io1 / io2
Same AZ
16 Instances
Cluster-Aware File System
```

---

# Japanese Summary

**EBS Multi-Attach**は、
1つのEBS Volumeを同じAvailability Zone内の
複数のEC2 Instanceに接続する機能です。

```text
io1 / io2
↓
Multi-Attach
↓
Multiple EC2 Instances
```

主な条件：

```text
Same AZ
Read / Write Access
Maximum 16 Instances
Cluster-Aware File System
```

複数のInstanceが同時に書き込むため、
Application側でもConcurrent Writeを管理する必要があります。

---

# English Summary

**EBS Multi-Attach** allows a single EBS volume
to be attached to multiple EC2 instances in the same Availability Zone.

```text
io1 / io2
↓
Multi-Attach
↓
Multiple EC2 Instances
```

Key requirements:

```text
Same AZ
Read / Write access
Up to 16 EC2 instances
Cluster-aware file system
```

Applications must also manage concurrent write operations.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Multi-Attach | マルチアタッチ | 하나의 EBS를 여러 EC2에 연결하는 기능 |
| Cluster | クラスター | 여러 서버가 하나의 시스템처럼 동작하는 구성 |
| Cluster-Aware File System | クラスター対応ファイルシステム | 여러 노드의 동시 접근을 처리할 수 있는 파일 시스템 |
| Concurrent Write | 同時書き込み | 여러 시스템이 동시에 데이터를 쓰는 작업 |
| Shared Storage | 共有ストレージ | 여러 시스템이 함께 사용하는 저장소 |
| Availability | 可用性 | 시스템을 지속적으로 사용할 수 있는 정도 |
| Availability Zone | アベイラビリティゾーン | AWS Region 내부의 독립된 데이터센터 영역 |
| Read Permission | 読み取り権限 | 데이터를 읽을 수 있는 권한 |
| Write Permission | 書き込み権限 | 데이터를 쓸 수 있는 권한 |

---

# Review Questions

### Q1. EBS Multi-Attach란?

하나의 EBS Volume을
여러 EC2 Instance에 동시에 연결하는 기능이다.

### Q2. 어떤 EBS Volume Type에서 사용할 수 있는가?

```text
io1 / io2
```

### Q3. 서로 다른 Availability Zone의 EC2에도 연결할 수 있는가?

아니다.

같은 Availability Zone의 EC2에만 연결할 수 있다.

### Q4. 강의 기준 최대 몇 개의 EC2에 연결할 수 있는가?

```text
16 Instances
```

### Q5. 왜 Cluster-Aware File System이 필요한가?

여러 EC2 Instance가 동일한 Volume에
동시에 접근하고 Write할 수 있기 때문이다.

### Q6. Multi-Attach를 사용하면 애플리케이션이 고려해야 하는 것은?

Concurrent Write 작업을 안전하게 관리해야 한다.