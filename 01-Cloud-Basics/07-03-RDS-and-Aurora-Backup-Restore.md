# RDS & Aurora Backup, Restore and Cloning

Amazon RDS와 Aurora는 **Automated Backup, Manual Snapshot, Point-in-Time Recovery(PITR)** 기능을 제공한다.

또한 Aurora는 **Database Cloning**을 통해 기존 Cluster에서 빠르게 새로운 Cluster를 생성할 수 있다.

---

# 1. RDS Automated Backups

RDS는 자동으로 데이터베이스를 백업할 수 있다.

### 동작

- 매일 데이터베이스 전체 백업 수행
- Transaction Log를 약 5분마다 백업
- 보존 기간 내 특정 시점으로 복구 가능
- Backup Retention Period: **1~35일**
- `0일`로 설정하면 Automated Backup 비활성화 가능

```text
RDS
 │
 ├─ Daily Full Backup
 │
 └─ Transaction Log
       └─ 약 5분마다 Backup
```

## Point-in-Time Recovery (PITR)

Automated Backup을 이용하면 보존 기간 내의 **특정 시점**으로 데이터베이스를 복구할 수 있다.

```text
Automated Backup
        ↓
Point-in-Time Recovery
        ↓
특정 시점의 DB 상태로 복구
```

시험에서 다음과 같은 요구사항이 나오면 PITR을 떠올린다.

> "데이터베이스를 특정 시간의 상태로 복구해야 한다."

---

# 2. Manual DB Snapshots

Manual Snapshot은 사용자가 직접 생성하는 데이터베이스 백업이다.

```text
Automated Backup
→ 자동 생성
→ Retention Period 존재

Manual Snapshot
→ 사용자가 직접 생성
→ 원하는 기간 동안 보관 가능
```

장기간 보관해야 하는 백업에는 Manual Snapshot을 사용할 수 있다.

## 장기간 사용하지 않는 RDS

RDS Instance를 중지해도 **Storage 비용은 계속 발생**한다.

장기간 데이터베이스를 사용하지 않는 경우 다음 방법을 고려할 수 있다.

```text
RDS
 ↓
Manual Snapshot 생성
 ↓
RDS 삭제
 ↓
Snapshot만 보관

필요할 때
 ↓
Snapshot Restore
 ↓
새 RDS 생성
```

### 시험 핵심

`장기간 사용하지 않는 RDS + 비용 절감 → Snapshot 생성 → DB 삭제 → 필요 시 Restore`

---

# 3. Aurora Automated Backups

Aurora도 RDS와 유사하게 Automated Backup과 PITR을 지원한다.

- Backup Retention Period: **1~35일**
- Point-in-Time Recovery 지원
- Manual Snapshot 지원
- Manual Snapshot은 원하는 기간 동안 보관 가능

하지만 중요한 차이가 있다.

```text
RDS Automated Backup
→ 비활성화 가능

Aurora Automated Backup
→ 비활성화 불가능
```

### 시험 핵심

`Aurora Automated Backup → Cannot be disabled`

---

# 4. Backup & Snapshot Restore

RDS 또는 Aurora의 Automated Backup이나 Manual Snapshot을 Restore하면 **새로운 Database가 생성된다.**

기존 Database에 데이터를 그대로 덮어쓰는 방식이 아니다.

```text
Existing DB
    │
    └─ Backup / Snapshot
              │
              │ Restore
              ▼
          New Database
```

### 시험 핵심

`Restore Backup / Snapshot → New Database`

---

# 5. MySQL Backup from Amazon S3

On-Premises MySQL의 백업을 Amazon S3에 저장한 뒤 새로운 RDS MySQL로 Restore할 수 있다.

```text
On-Premises MySQL
        ↓
      Backup
        ↓
    Amazon S3
        ↓
      Restore
        ↓
   New RDS MySQL
```

---

# 6. Aurora MySQL Restore from Amazon S3

On-Premises MySQL을 Aurora MySQL로 복원할 때는 **Percona XtraBackup**을 사용할 수 있다.

```text
On-Premises MySQL
        ↓
Percona XtraBackup
        ↓
    Amazon S3
        ↓
      Restore
        ↓
New Aurora MySQL Cluster
```

시험에서는 다음 연결 정도를 기억한다.

`Percona XtraBackup + S3 → Aurora MySQL Restore`

---

# 7. Aurora Database Cloning

Aurora는 기존 Aurora Cluster에서 새로운 Aurora Cluster를 빠르게 생성할 수 있는 **Database Cloning**을 지원한다.

대표적인 사용 사례는 Production 데이터를 이용해 Test 또는 Staging 환경을 만드는 것이다.

```text
Production Aurora
        │
        │ Clone
        ▼
 Staging Aurora
```

Snapshot을 생성하고 Restore하는 방법보다 빠르고 비용 효율적이다.

---

# 8. Copy-on-Write

Aurora Database Cloning이 빠른 이유는 **Copy-on-Write** 방식을 사용하기 때문이다.

Clone 생성 시 전체 데이터를 즉시 복사하지 않는다.

처음에는 원본 Cluster와 Clone이 동일한 Data Volume을 사용한다.

```text
Production ─┐
            ├── Shared Data
Staging ────┘
```

이후 Production 또는 Clone에서 데이터가 변경되면 **변경되는 데이터에 대해서만 새로운 Storage가 할당**된다.

```text
기존 데이터
→ Shared

변경된 데이터
→ Separate Storage
```

즉,

```text
처음부터 전체 데이터 복사 X
        ↓
기존 데이터 공유
        ↓
변경 발생
        ↓
변경된 부분만 분리
```

이것이 **Copy-on-Write**이다.

### 장점

- Snapshot & Restore보다 빠른 Clone 생성
- 전체 데이터를 처음부터 복사하지 않아 비용 효율적
- Production Database에 영향을 최소화하면서 Test/Staging 환경 생성 가능

### 시험 핵심

`Aurora Clone + Fast + Cost-efficient + Copy-on-Write → Aurora Database Cloning`

---

# 9. 시험 핵심 정리

```text
RDS Automated Backup
→ Daily Full Backup
→ Transaction Log 약 5분마다
→ PITR
→ Retention 1~35일
→ Disable 가능

Aurora Automated Backup
→ PITR
→ Retention 1~35일
→ Disable 불가능

Manual Snapshot
→ 사용자가 직접 생성
→ 원하는 기간 보관

Backup / Snapshot Restore
→ 기존 DB 덮어쓰기 X
→ New Database 생성

On-Premises MySQL → RDS
→ Backup → S3 → RDS MySQL

On-Premises MySQL → Aurora
→ Percona XtraBackup → S3 → Aurora MySQL

Aurora Database Cloning
→ Copy-on-Write
→ 빠르고 비용 효율적
→ Production → Test / Staging
```

---

# 日本語まとめ

## RDS / Aurora Backup

RDSとAuroraは、Automated Backup、Manual Snapshot、Point-in-Time Recoveryをサポートする。

### RDS Automated Backup

- 毎日Full Backupを実行
- Transaction Logを約5分ごとにバックアップ
- Retention Periodは1〜35日
- Point-in-Time Recoveryをサポート
- Automated Backupを無効化できる

### Aurora Automated Backup

Auroraも1〜35日のRetention PeriodとPITRをサポートする。

ただし、AuroraのAutomated Backupは**無効化できない**。

### Manual Snapshot

ユーザーが手動で作成し、必要な期間保存できる。

### Restore

BackupまたはSnapshotをRestoreすると、既存DBを上書きするのではなく**新しいDatabaseが作成される**。

## Aurora Database Cloning

Aurora Database Cloningは、既存のAurora Clusterから新しいClusterを高速に作成する機能である。

**Copy-on-Write**を使用するため、最初からすべてのデータをコピーする必要がない。

Production DatabaseからTest / Staging環境を作成する場合に有効である。

---

# English Summary

## RDS / Aurora Backup

RDS and Aurora support Automated Backups, Manual Snapshots, and Point-in-Time Recovery.

### RDS Automated Backups

- Daily full backup
- Transaction logs backed up approximately every 5 minutes
- Retention period: 1–35 days
- Supports Point-in-Time Recovery
- Automated backups can be disabled

### Aurora Automated Backups

Aurora also supports a 1–35 day retention period and Point-in-Time Recovery.

Unlike RDS, **Aurora automated backups cannot be disabled**.

### Manual Snapshots

Manual snapshots are triggered by the user and can be retained as long as needed.

### Restore

Restoring a backup or snapshot creates a **new database** rather than overwriting the existing database.

## Aurora Database Cloning

Aurora Database Cloning quickly creates a new Aurora Cluster from an existing cluster.

It uses **Copy-on-Write**, so the entire dataset does not need to be copied when the clone is initially created.

A common use case is creating Test or Staging environments from Production data.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Automated Backup | 自動バックアップ | 자동 백업 |
| Manual Snapshot | 手動スナップショット | 수동 스냅샷 |
| Retention Period | 保持期間 | 보존 기간 |
| Point-in-Time Recovery | ポイントインタイムリカバリ | 특정 시점 복구 |
| Transaction Log | トランザクションログ | 트랜잭션 로그 |
| Restore | 復元 | 복원 |
| Database Cloning | データベースクローン | 데이터베이스 복제 |
| Copy-on-Write | コピーオンライト | 쓰기 시 복사 |
| Production Environment | 本番環境 | 운영 환경 |
| Staging Environment | ステージング環境 | 스테이징 환경 |

---

# Review Questions

1. RDS Automated Backup의 Backup Retention Period는 얼마인가?
2. Point-in-Time Recovery는 어떤 상황에서 사용하는가?
3. RDS와 Aurora의 Automated Backup에서 중요한 차이점은 무엇인가?
4. Manual Snapshot과 Automated Backup의 보존 방식에는 어떤 차이가 있는가?
5. Backup 또는 Snapshot을 Restore하면 기존 Database를 덮어쓰는가?
6. On-Premises MySQL을 Aurora MySQL로 Restore할 때 어떤 Backup 도구를 사용할 수 있는가?
7. Aurora Database Cloning이 Snapshot & Restore보다 빠른 이유는 무엇인가?
