# Amazon EBS Snapshots

## Overview

**EBS Snapshot**은 EBS Volume의 특정 시점 상태를 저장하는 백업이다.

```text
EBS Snapshot
= Point-in-Time Backup of an EBS Volume
```

기본 흐름:

```text
EBS Volume
↓
Snapshot
↓
New EBS Volume
```

EBS Volume을 Snapshot으로 백업하고,
필요할 때 새로운 Volume으로 복원할 수 있다.

---

# 1. Creating a Snapshot

EBS Volume에서 Snapshot을 생성할 수 있다.

```text
EBS Volume
↓
Create Snapshot
↓
Snapshot
```

Volume을 반드시 Detach할 필요는 없지만,
강의에서는 데이터 일관성을 위해 Detach를 권장한다.

---

# 2. Moving EBS Across Availability Zones

EBS Volume은 특정 Availability Zone에 종속된다.

```text
EBS in AZ-A
→ EC2 in AZ-A : O

EBS in AZ-A
→ EC2 in AZ-B : X
```

다른 AZ로 이동하려면 Snapshot을 사용한다.

```text
EBS in AZ-A
↓
Create Snapshot
↓
Create Volume from Snapshot
↓
New EBS in AZ-B
```

즉:

```text
EBS
→ AZ Scoped

Snapshot
→ 다른 AZ에서 새 EBS 생성 가능
```

---

# 3. Copying Snapshots Across Regions

Snapshot은 다른 AWS Region으로 복사할 수 있다.

```text
Region A
Snapshot
   │
   ▼
Copy Snapshot
   │
   ▼
Region B
Snapshot
```

대표적인 사용 사례:

```text
Cross-Region Backup
Disaster Recovery
```

---

# 4. Snapshot Archive

오랫동안 사용하지 않을 Snapshot은
**Archive Tier**로 이동할 수 있다.

강의 기준:

```text
Storage Cost
→ 최대 약 75% 절감

Restore Time
→ 24 ~ 72 Hours
```

따라서:

```text
장기 보관
+
즉시 복원이 필요하지 않음
↓
Snapshot Archive
```

---

# 5. Snapshot Recycle Bin

Snapshot을 실수로 삭제했을 때
Recycle Bin을 사용하면 복원할 수 있다.

```text
Snapshot
↓
Delete
↓
Recycle Bin
↓
Recover
```

강의 기준 보존 기간:

```text
1 Day ~ 1 Year
```

핵심:

```text
Recycle Bin
= Accidental Deletion Protection
```

---

# 6. Fast Snapshot Restore (FSR)

Snapshot에서 생성한 EBS Volume은
초기 Block 접근 시 지연이 발생할 수 있다.

**Fast Snapshot Restore (FSR)** 는
첫 사용 시 지연을 제거하여
Volume이 바로 높은 성능을 낼 수 있도록 한다.

```text
Large Snapshot
+
Need immediate performance
↓
Fast Snapshot Restore
```

단점:

```text
Higher Cost
```

---

# 7. Hands-On Summary

이번 실습에서는 다음을 확인했다.

```text
2 GiB EBS
↓
Create Snapshot
↓
DemoSnapshot
↓
Create Volume from Snapshot
↓
Different AZ에 새 EBS 생성
```

또한:

```text
Snapshot
→ Copy to another Region
```

이 가능함을 확인했다.

Recycle Bin 실습:

```text
Create Retention Rule
↓
Delete Snapshot
↓
Snapshot moves to Recycle Bin
↓
Recover Snapshot
```

---

# 8. Exam Notes

```text
EBS Backup
→ Snapshot
```

```text
Move EBS to another AZ
→ Snapshot → Create new EBS
```

```text
Backup to another Region
→ Copy Snapshot
```

```text
Long-term cheap storage
→ Snapshot Archive
```

```text
Recover accidentally deleted Snapshot
→ Recycle Bin
```

```text
Need immediate full performance
→ Fast Snapshot Restore
```

---

# Summary

```text
EBS Snapshot
= Point-in-Time Backup
```

핵심:

- EBS Volume 백업
- 다른 AZ에서 새 EBS 생성 가능
- 다른 Region으로 Snapshot 복사 가능
- Archive로 장기 저비용 보관 가능
- Recycle Bin으로 삭제 복구 가능
- FSR로 첫 사용 지연 제거 가능

한 줄 암기:

```text
EBS는 AZ에 묶임
→ 다른 AZ로 옮기려면 Snapshot
```

---

# Japanese Summary

EBS Snapshotは、
EBS Volumeの**Point-in-Time Backup**です。

```text
EBS in AZ-A
↓
Snapshot
↓
New EBS in AZ-B
```

主な機能：

```text
Snapshot Archive
→ 長期・低コスト保存

Recycle Bin
→ 削除したSnapshotを復元

Fast Snapshot Restore
→ 初回アクセスのLatencyを削減
```

Snapshotは別のRegionへコピーでき、
Disaster Recoveryにも利用できます。

---

# English Summary

An **EBS Snapshot** is a point-in-time backup of an EBS volume.

```text
EBS in AZ-A
↓
Snapshot
↓
New EBS in AZ-B
```

Key features:

```text
Snapshot Archive
→ Lower-cost long-term storage

Recycle Bin
→ Recover deleted snapshots

Fast Snapshot Restore
→ Remove first-use latency
```

Snapshots can also be copied across Regions for backup and disaster recovery.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| EBS Snapshot | EBSスナップショット | EBS 볼륨의 특정 시점 백업 |
| Point-in-Time Backup | ポイントインタイムバックアップ | 특정 시점 백업 |
| Snapshot Archive | スナップショットアーカイブ | 스냅샷 장기 저비용 보관 |
| Archive Tier | アーカイブ階層 | 장기 보관용 저비용 스토리지 계층 |
| Recycle Bin | ごみ箱 | 삭제한 스냅샷을 보관·복구하는 기능 |
| Retention Rule | 保持ルール | 삭제된 리소스의 보존 기간을 정하는 규칙 |
| Fast Snapshot Restore | 高速スナップショット復元 | 스냅샷 기반 EBS의 초기 접근 지연을 줄이는 기능 |
| FSR | 高速スナップショット復元（FSR） | Fast Snapshot Restore의 약어 |
| Disaster Recovery | 災害復旧 | 장애·재해 발생 시 시스템을 복구하는 전략 |
| Cross-Region Copy | リージョン間コピー | 다른 AWS 리전으로 복사 |
| Restore | 復元 | 백업된 데이터나 리소스를 복원 |

---

# Review Questions

### Q1. EBS Snapshot이란?

EBS Volume의 특정 시점 상태를 저장하는 Backup이다.

### Q2. 다른 AZ로 EBS 데이터를 옮기려면?

```text
Create Snapshot
↓
Create Volume in target AZ
```

### Q3. Snapshot을 다른 Region으로 복사할 수 있는가?

가능하다.

### Q4. Snapshot Archive는 언제 사용하는가?

장기간 저렴하게 보관하고,
빠른 복원이 필요하지 않을 때 사용한다.

### Q5. 삭제한 Snapshot을 복원하려면?

Recycle Bin을 사용한다.

### Q6. Snapshot 기반 EBS가 즉시 높은 성능을 내야 한다면?

Fast Snapshot Restore를 사용한다.