# Amazon RDS

## Overview

**Amazon RDS (Relational Database Service)** is a managed relational database service provided by AWS.

RDS is designed for relational databases using SQL and allows AWS to manage much of the underlying database infrastructure and operational work.

### Supported Database Engines

- PostgreSQL
- MySQL
- MariaDB
- Oracle
- Microsoft SQL Server
- IBM Db2
- Amazon Aurora

> RDS is not a database engine itself.  
> It is a managed service that runs supported database engines.

---

# 1. Why Use RDS?

A database engine such as MySQL can also be installed directly on an EC2 instance.

```text
EC2
 ↓
Operating System
 ↓
MySQL / PostgreSQL
 ↓
Database
```

However, in this model the user is responsible for operating the database infrastructure.

With RDS, AWS manages many operational tasks.

```text
Amazon RDS

├─ Database provisioning
├─ Operating system patching
├─ Automated backups
├─ Point-in-Time Restore
├─ Monitoring
├─ Maintenance
├─ Read Replicas
├─ Multi-AZ
└─ Storage management
```

Because RDS is a managed service, users normally cannot access the underlying EC2 instance using SSH.

```text
Standard RDS
→ Underlying EC2 SSH access ❌
```

---

# 2. RDS Compute and Storage

An RDS database uses a DB instance for compute resources.

```text
RDS DB Instance
├─ CPU
├─ Memory
└─ Database Engine
```

The instance size can be changed when additional compute resources are required.

```text
Smaller DB Instance
        ↓
Larger DB Instance

= Vertical Scaling
```

RDS storage is backed by EBS.

```text
Application
     ↓
Database Engine
     ↓
EBS-backed Storage
```

The database engine manages the data while the underlying storage physically stores it.

---

# 3. RDS Storage Auto Scaling

When an RDS database begins running out of allocated storage, **Storage Auto Scaling** can automatically increase the storage capacity.

```text
Database grows
      ↓
Available Storage decreases
      ↓
RDS Storage Auto Scaling
      ↓
Storage Capacity increases
```

A **Maximum Storage Threshold** is configured to prevent storage from increasing without a limit.

### Good Use Case

- Database growth is unpredictable
- Manual storage expansion should be avoided

### Exam Point

```text
Unpredictable RDS storage growth
→ RDS Storage Auto Scaling
```

---

# 4. RDS Read Replicas

Read Replicas are used to **scale read workloads**.

```text
                  WRITE
                    ↓
               Primary DB
                /       \
           ASYNC         ASYNC
             ↓             ↓
      Read Replica   Read Replica
           ↑               ↑
         READ            READ
```

The Primary DB handles reads and writes.

Read Replicas are used for read operations such as:

```sql
SELECT ...
```

They are not intended for:

```sql
INSERT ...
UPDATE ...
DELETE ...
```

## Asynchronous Replication

Replication between the Primary DB and Read Replicas is **asynchronous**.

```text
Primary Update
      ↓
Read Replica receives update later
```

Therefore, reads from a replica are **eventually consistent**.

A Read Replica may temporarily contain slightly older data before replication catches up.

## Replica Location

Read Replicas may be created:

- In the same AZ
- In another AZ
- In another Region

The course specifies support for up to **15 Read Replicas**.

## Common Use Case

A reporting or analytics workload should not overload the production database.

```text
Production Application
          ↓
      Primary DB
          │
          │ ASYNC
          ↓
     Read Replica
          ↑
 Reporting / Analytics
```

The reporting application sends its read workload to the replica instead of the Primary DB.

## Promotion

A Read Replica can be **promoted to an independent database**.

```text
Primary DB
    │
    │ ASYNC
    ↓
Read Replica
    │
    │ Promote
    ↓
Independent Database
```

After promotion, it no longer follows the original Primary DB through the previous replication relationship.

## Application Connection

Applications must be configured to use Read Replica connections when they want to send reads to the replicas.

```text
Application

WRITE
  ↓
Primary DB

READ
  ↓
Read Replica
```

## Replication Network Cost

Within the same Region, the course notes no replication network charge between RDS Read Replicas across AZs.

Cross-Region replication incurs network transfer cost.

```text
Same Region
→ No replication transfer charge

Cross Region
→ Network cost
```

### Exam Point

```text
High Read Load
Reporting / Analytics
Read Scaling

→ Read Replica
```

---

# 5. RDS Multi-AZ

Multi-AZ is designed for **High Availability and Disaster Recovery**, not read scaling.

```text
Application
     ↓
  DNS Name
     ↓
Primary DB
   AZ-A
     │
     │ SYNC
     ↓
Standby DB
   AZ-B
```

Changes from the Primary DB are synchronously replicated to the Standby DB.

## Standby Instance

The Standby exists for failover.

It is not used as a normal read-scaling database.

```text
Primary
→ Read / Write ⭕

Standby
→ Read Scaling ❌
→ Failover ⭕
```

If the Primary DB or its AZ fails:

```text
Primary DB 💥
     ↓
Automatic Failover
     ↓
Standby becomes active
```

The application uses a single RDS DNS name, so manual application endpoint changes are not normally required during failover.

## Typical Failure Scenarios

Multi-AZ helps protect against:

- AZ failure
- Network failure
- DB instance failure
- Storage failure

### Exam Point

```text
Database High Availability
Disaster Recovery
Automatic Failover

→ Multi-AZ
```

---

# 6. Read Replica vs Multi-AZ

| Feature | Read Replica | Multi-AZ |
|---|---|---|
| Main Purpose | Read Scalability | High Availability / DR |
| Replication | ASYNC | SYNC |
| Read Traffic | Yes | Standby is not for read scaling |
| Scaling | Yes | No |
| Failover Purpose | Not the primary purpose | Yes |
| Consistency | Eventually consistent replica | Synchronous standby |
| Main Exam Trigger | Heavy SELECT / Reporting | Failure / Availability |

### Core Memory

```text
Read Replica
→ Read Scaling
→ ASYNC

Multi-AZ
→ High Availability
→ SYNC
```

---

# 7. Multi-AZ Read Replica

A Read Replica itself may also be configured as Multi-AZ.

This means the Read Replica has its own Standby for High Availability.

```text
Original Primary DB
        │
        │ ASYNC
        ▼
Read Replica
        │
        │ SYNC
        ▼
Replica's Standby
```

The relationships are separate.

```text
Original Primary → Read Replica
= Read Scaling

Read Replica → its Standby
= High Availability
```

If the Read Replica fails, its own Standby can take over the Read Replica deployment.

The Standby of the Read Replica does not automatically replace the original Primary DB.

### Important

```text
Primary's Standby
→ protects the Primary

Replica's Standby
→ protects the Replica
```

---

# 8. Single-AZ to Multi-AZ

An existing Single-AZ RDS deployment can be modified into Multi-AZ without manually stopping the database.

Internally, RDS performs operations such as:

```text
Primary DB
    ↓
Snapshot
    ↓
Restore Standby in another AZ
    ↓
Establish synchronization
    ↓
Multi-AZ Deployment
```

### Exam Point

```text
Single-AZ → Multi-AZ
→ No need to manually stop the DB
```

---

# 9. Connecting to RDS

An application connects to an RDS database using:

```text
Endpoint
+
Port
+
Network Permission
+
Database Authentication
```

Example for MySQL:

```text
Application
     │
     │ Endpoint: mydb.xxxxx.rds.amazonaws.com
     │ Port: 3306
     ▼
Security Group
     ↓
MySQL RDS
```

## Endpoint

The **Endpoint** is the address used to reach the RDS database.

```text
mydb.xxxxx.rds.amazonaws.com
```

## Port

The port identifies the database service.

For MySQL:

```text
TCP 3306
```

## Security Group

The RDS Security Group controls whether network traffic is allowed to reach the database.

Recommended architecture:

```text
EC2
SG: Web-SG
   │
   │ TCP 3306
   ▼
RDS SG

Source = Web-SG
```

Avoid unnecessarily exposing a production database port to the entire Internet.

## Authentication

After network access is allowed, the client must authenticate to the database.

RDS supports options including:

- Username / Password
- IAM Database Authentication
- Kerberos where supported

### Connection Mental Model

```text
Endpoint
→ Where?

Port
→ Which service?

Security Group
→ Is network access allowed?

Authentication
→ Who are you?
```

---

# 10. Backup and Recovery Basics

RDS provides automated backups and manual snapshots.

## Automated Backup

- Automatically performed by RDS
- Supports Point-in-Time Restore
- Course retention range: 1–35 days
- Setting retention to 0 disables automated backups

## Manual DB Snapshot

- Manually triggered
- Can be retained until the user deletes it

```text
Automated Backup
→ Automatic
→ Point-in-Time Restore

Manual Snapshot
→ User-created
→ Long-term retention
```

---

# 11. Monitoring and Maintenance

RDS provides monitoring for database metrics such as:

- CPU utilization
- Database connections
- Other DB instance metrics

Logs may also be exported to CloudWatch Logs.

RDS provides scheduled maintenance windows for operations such as minor database version upgrades.

---

# 12. Deletion Protection

Deletion Protection helps prevent accidental deletion of an RDS database.

```text
Deletion Protection ON
→ Accidental Delete protection
```

It must be disabled before intentionally deleting a protected DB instance.

---

# 13. RDS Custom

Standard RDS manages the underlying operating system and database infrastructure.

Users normally cannot access the underlying EC2 instance.

**RDS Custom** is designed for workloads that require deeper OS and database customization.

The course covers RDS Custom for:

- Oracle
- Microsoft SQL Server

## RDS Custom Capabilities

Users can access and customize the underlying environment.

```text
RDS Custom
     ↓
Underlying EC2 / OS / Database
     ↑
SSH or SSM Session Manager
```

Possible customizations include:

- Configure operating system or database settings
- Install patches
- Enable native database features
- Access the underlying EC2 instance

## Automation Mode

Before performing customizations, the course recommends disabling **Automation Mode** so that automated RDS operations do not interfere with the customization.

```text
Disable Automation Mode
        ↓
Take DB Snapshot
        ↓
Perform Customization
```

Taking a DB snapshot before customization is recommended because direct changes to the underlying environment can cause problems.

## RDS vs RDS Custom

| Feature | RDS | RDS Custom |
|---|---|---|
| Managed DB | Yes | Yes |
| AWS manages underlying OS | Yes | Reduced control by AWS during customization |
| OS / DB admin access | No | Yes |
| SSH / SSM to underlying instance | No | Yes |
| Oracle | Yes | Yes |
| Microsoft SQL Server | Yes | Yes |
| Main Use Case | Fully managed DB | Special OS / DB customization |

### Exam Point

```text
Oracle / SQL Server
+
Need OS access
Need custom patches
Need native DB features
Need SSH / SSM access

→ RDS Custom
```

---

# Summary

```text
Amazon RDS
→ Managed Relational Database Service

Read Replica
→ Read Scalability
→ ASYNC
→ Eventually Consistent

Multi-AZ
→ High Availability / DR
→ SYNC
→ Automatic Failover

Storage Auto Scaling
→ Automatically increases storage when required

Endpoint
→ RDS address

Port
→ Database service port

Security Group
→ Controls network access

Automated Backup
→ Point-in-Time Restore

Manual Snapshot
→ Long-term manually controlled backup

RDS Custom
→ Oracle / SQL Server
→ OS + Database customization
→ SSH / SSM access
```

---

# 日本語まとめ

## Amazon RDS

Amazon RDS は AWS が提供するマネージド型リレーショナルデータベースサービスです。

AWS がデータベースのプロビジョニング、OS パッチ、バックアップ、モニタリング、メンテナンスなどを管理します。

### Read Replica

```text
目的
→ 読み取り性能のスケーリング

Replication
→ ASYNC

Consistency
→ Eventually Consistent
```

レポートや分析などの読み取り負荷を Primary DB から分離できます。

### Multi-AZ

```text
目的
→ 高可用性 / Disaster Recovery

Replication
→ SYNC

障害時
→ Automatic Failover
```

Standby DB は通常の読み取りスケーリングには使用しません。

### RDS Custom

Oracle と Microsoft SQL Server で、OS とデータベースをカスタマイズできます。

SSH または SSM Session Manager を使用して基盤 EC2 インスタンスへアクセスできます。

---

# English Summary

Amazon RDS is a managed relational database service.

AWS manages much of the database infrastructure including provisioning, operating system patching, backups, monitoring, and maintenance.

### Read Replica

- Used for read scalability
- Uses asynchronous replication
- Reads are eventually consistent
- Useful for reporting and analytics workloads

### Multi-AZ

- Used for high availability and disaster recovery
- Uses synchronous replication
- Provides automatic failover
- Standby instances are not used for read scaling

### RDS Custom

- Supports Oracle and Microsoft SQL Server in this course
- Provides access to the underlying OS and database
- Supports SSH and SSM Session Manager access
- Used when custom patches, settings, or native database features are required

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Relational Database | リレーショナルデータベース | 관계형 데이터베이스 |
| Managed Service | マネージドサービス | 관리형 서비스 |
| Database Engine | データベースエンジン | 데이터베이스 엔진 |
| Read Replica | リードレプリカ | 읽기 전용 복제본 |
| Multi-AZ | マルチAZ | 다중 AZ |
| Primary Database | プライマリデータベース | 기본 / 주 데이터베이스 |
| Standby Database | スタンバイデータベース | 대기 데이터베이스 |
| Asynchronous Replication | 非同期レプリケーション | 비동기 복제 |
| Synchronous Replication | 同期レプリケーション | 동기 복제 |
| Eventually Consistent | 結果整合性 | 최종적 일관성 |
| Failover | フェイルオーバー | 장애 조치 |
| High Availability | 高可用性 | 고가용성 |
| Disaster Recovery | 災害復旧 | 재해 복구 |
| Endpoint | エンドポイント | 엔드포인트 / 접속 주소 |
| Port | ポート | 포트 |
| Security Group | セキュリティグループ | 보안 그룹 |
| Point-in-Time Restore | ポイントインタイムリストア | 특정 시점 복원 |
| DB Snapshot | DB スナップショット | DB 스냅샷 |
| Storage Auto Scaling | ストレージオートスケーリング | 스토리지 자동 확장 |
| Deletion Protection | 削除保護 | 삭제 방지 |
| RDS Custom | RDS Custom | RDS 커스텀 |
| Automation Mode | オートメーションモード | 자동화 모드 |

---

# Review Questions

1. What is the main difference between running a database on EC2 and using Amazon RDS?

2. A production database is overloaded by SELECT queries from a reporting application. Which RDS feature should be used?

3. What is the difference between asynchronous Read Replica replication and synchronous Multi-AZ replication?

4. Why is a Multi-AZ Standby not considered a read scaling solution?

5. If a Read Replica itself is configured as Multi-AZ, which database does its Standby protect?

6. What roles do an RDS Endpoint, Port, Security Group, and Database Authentication play during a database connection?

7. When should RDS Custom be considered instead of standard RDS?