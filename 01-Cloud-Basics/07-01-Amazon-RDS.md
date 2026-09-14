# Amazon RDS

## 1. 개요

**Amazon RDS (Relational Database Service)** 는 AWS에서 제공하는 **관리형 관계형 데이터베이스 서비스**이다.

관계형 데이터베이스는 SQL을 사용하며, RDS에서는 다음과 같은 데이터베이스 엔진을 사용할 수 있다.

- PostgreSQL
- MySQL
- MariaDB
- Oracle
- Microsoft SQL Server
- IBM Db2
- Amazon Aurora

> RDS 자체가 데이터베이스 엔진인 것은 아니다.  
> MySQL, PostgreSQL 같은 데이터베이스 엔진을 AWS가 관리형 형태로 제공하는 서비스이다.

---

## 2. EC2에 DB를 직접 설치하는 것과 RDS의 차이

EC2 인스턴스에 직접 MySQL 또는 PostgreSQL을 설치해서 데이터베이스 서버를 만들 수도 있다.

```text
EC2
 ↓
Operating System
 ↓
MySQL / PostgreSQL
 ↓
Database
```

이 경우 사용자가 직접 다음과 같은 작업을 관리해야 한다.

- 서버 구성
- 운영체제 관리
- 패치
- 데이터베이스 설치
- 백업
- 장애 대응
- 모니터링
- 확장

반면 RDS에서는 AWS가 많은 운영 작업을 대신 수행한다.

```text
Amazon RDS

├─ DB Provisioning
├─ OS Patching
├─ Automated Backup
├─ Point-in-Time Restore
├─ Monitoring
├─ Maintenance
├─ Read Replica
├─ Multi-AZ
└─ Scaling
```

즉,

```text
EC2 + DB
→ 사용자가 서버와 DB를 직접 관리

RDS
→ AWS가 DB 운영의 많은 부분을 관리
```

### 중요한 차이

일반 RDS에서는 기저 EC2 인스턴스에 SSH로 접속할 수 없다.

```text
Standard RDS
→ Underlying EC2 SSH Access ❌
```

RDS가 관리형 서비스이기 때문에 운영체제와 내부 인프라를 AWS가 관리한다.

---

# 3. RDS Compute와 Storage

RDS 데이터베이스도 결국 CPU와 Memory가 필요하다.

이를 위해 **DB Instance** 크기를 선택한다.

```text
RDS DB Instance

├─ CPU
├─ Memory
└─ Database Engine
```

더 많은 CPU 또는 Memory가 필요하면 더 큰 DB Instance로 변경할 수 있다.

```text
Small DB Instance
       ↓
Large DB Instance

= Vertical Scaling
```

RDS의 Storage는 EBS 기반이다.

```text
Application
     ↓
Database Engine
     ↓
EBS-backed Storage
```

즉,

```text
MySQL / PostgreSQL
→ 데이터를 구조적으로 관리

EBS
→ 실제 데이터를 저장하는 기반 Storage
```

라고 이해하면 된다.

---

# 4. RDS Storage Auto Scaling

데이터베이스의 데이터가 계속 증가하면 기존에 할당한 Storage가 부족할 수 있다.

이때 **RDS Storage Auto Scaling**을 사용하면 Storage가 부족해질 때 자동으로 용량을 증가시킬 수 있다.

```text
Database Data 증가
       ↓
Free Storage 감소
       ↓
Storage Auto Scaling
       ↓
Storage 용량 증가
```

Storage가 무한히 증가하지 않도록 **Maximum Storage Threshold**를 설정한다.

예:

```text
Allocated Storage
20 GB

Maximum Storage Threshold
1000 GB
```

이 경우 RDS가 필요에 따라 Storage를 늘릴 수 있지만 최대 1000 GB를 넘지는 않는다.

### 적합한 경우

```text
데이터 증가량 예측 어려움
        ↓
Storage Auto Scaling
```

### 시험 포인트

```text
RDS Storage 부족
+
Storage 크기를 자동으로 증가

→ Storage Auto Scaling
```

---

# 5. RDS Read Replica

**Read Replica**는 데이터베이스의 **읽기 성능을 확장하기 위한 기능**이다.

예를 들어 하나의 Primary DB에 읽기 요청이 너무 많이 몰린다고 하자.

```text
Application
    ↓
Primary DB

SELECT
SELECT
SELECT
SELECT
SELECT
```

이러면 Primary DB에 읽기 부하가 커진다.

Read Replica를 추가하면:

```text
                 Primary DB
                 Read / Write
                    │
          ┌─────────┴─────────┐
          │ ASYNC             │ ASYNC
          ▼                   ▼
   Read Replica 1       Read Replica 2
        READ                 READ
```

읽기 요청을 Replica로 분산할 수 있다.

---

## 5.1 Read Replica의 목적

```text
Read Replica
→ Read Scalability
```

대표적인 사용 사례는 다음과 같다.

- Reporting
- Analytics
- 읽기 요청이 많은 Application
- SELECT 부하 분산

예:

```text
Production Application
        ↓
    Primary DB
        │
        │ ASYNC
        ▼
   Read Replica
        ↑
Reporting System
```

Reporting System이 Primary DB를 계속 읽는 대신 Read Replica를 사용하면 Primary의 부하를 줄일 수 있다.

---

## 5.2 Read Replica Replication

Primary DB와 Read Replica 사이의 복제는 **비동기 방식(ASYNC)** 이다.

```text
Primary DB
    │
    │ ASYNC
    ▼
Read Replica
```

즉 Primary에 데이터가 저장된 직후 Replica에 아직 반영되지 않은 짧은 시간이 존재할 수 있다.

그래서 Read Replica는 **Eventually Consistent**하다.

```text
Primary
Data = 최신

Replica
Data = 잠시 이전 상태일 수 있음
        ↓
조금 후 동기화
```

### 핵심

```text
Read Replica
→ ASYNC
→ Eventually Consistent
```

---

## 5.3 Read Replica 위치

Read Replica는 다음 위치에 생성할 수 있다.

- Same AZ
- Cross AZ
- Cross Region

강의 기준 최대 **15개의 Read Replica**를 생성할 수 있다.

---

## 5.4 Read Replica Promotion

Read Replica를 독립적인 데이터베이스로 **Promote**할 수도 있다.

```text
Primary DB
    │
    │ ASYNC
    ▼
Read Replica
    │
    │ Promote
    ▼
Independent DB
```

Promote된 이후에는 기존 Primary의 Read Replica가 아니라 별도의 독립적인 DB가 된다.

즉 기존 복제 관계는 끊어진다.

---

## 5.5 Application에서 Read Replica 사용

Read Replica를 만들었다고 해서 AWS가 자동으로 모든 읽기 요청을 Replica로 보내주는 것은 아니다.

Application이 Read Replica의 Endpoint를 사용하도록 구성해야 한다.

```text
Application

WRITE
  ↓
Primary Endpoint

READ
  ↓
Read Replica Endpoint
```

---

# 6. RDS Multi-AZ

**Multi-AZ**는 읽기 확장이 아니라 **High Availability와 Disaster Recovery**를 위한 기능이다.

```text
Application
     ↓
RDS Endpoint
     ↓
Primary DB
   AZ-A
     │
     │ SYNC
     ▼
Standby DB
   AZ-B
```

Primary DB의 데이터를 다른 AZ에 있는 Standby DB로 동기식으로 복제한다.

---

## 6.1 Multi-AZ의 목적

```text
Multi-AZ
→ High Availability
→ Disaster Recovery
```

Primary에 장애가 발생하면 AWS가 Standby로 자동 Failover한다.

```text
Primary DB 💥
      ↓
Automatic Failover
      ↓
Standby DB 활성화
```

Application은 기존 RDS Endpoint를 계속 사용한다.

즉 Application에서 DB 주소를 직접 변경할 필요가 없다.

---

## 6.2 Standby는 Read Replica가 아니다

Multi-AZ의 Standby는 평소 읽기 요청을 처리하기 위한 DB가 아니다.

```text
Primary DB
→ Read / Write ⭕

Standby DB
→ 일반 Read Traffic ❌
→ Failover 용도 ⭕
```

따라서:

```text
Read Scalability
→ Read Replica

High Availability
→ Multi-AZ
```

로 구분해야 한다.

---

# 7. Read Replica vs Multi-AZ

| 항목 | Read Replica | Multi-AZ |
|---|---|---|
| 목적 | Read Scaling | High Availability / DR |
| Replication | ASYNC | SYNC |
| 읽기 요청 처리 | 가능 | Standby는 사용하지 않음 |
| 장애 자동 Failover | 주 목적 아님 | 가능 |
| Consistency | Eventually Consistent | Synchronous |
| 대표 키워드 | Reporting, Analytics, SELECT | Failure, HA, Disaster Recovery |

### 시험용 핵심

```text
Read Replica
= 읽기 확장
= ASYNC

Multi-AZ
= 장애 대비
= SYNC
```

---

# 8. Read Replica도 Multi-AZ로 구성할 수 있다

Read Replica와 Multi-AZ는 서로 배타적인 기능이 아니다.

Read Replica 자체에도 Standby를 붙여 Multi-AZ로 구성할 수 있다.

```text
Original Primary DB
        │
        │ ASYNC
        ▼
Read Replica
        │
        │ SYNC
        ▼
Read Replica의 Standby
```

여기에는 두 개의 서로 다른 관계가 존재한다.

```text
Original Primary
      ↓ ASYNC
Read Replica

= Read Scaling
```

그리고:

```text
Read Replica
      ↓ SYNC
Replica Standby

= High Availability
```

### 장애 시 역할

```text
Original Primary의 Standby
→ Original Primary를 보호

Read Replica의 Standby
→ Read Replica를 보호
```

즉 Read Replica의 Standby는 Original Primary가 장애 났다고 해서 Original Primary를 대신하는 것이 아니다.

```text
Original Primary 💥

Replica Standby
→ Original Primary 자동 대체 ❌
```

Replica Standby는 자신이 붙어 있는 Read Replica의 장애를 대비한다.

### 기억하기

```text
Multi-AZ Standby
→ 자기가 붙어 있는 DB의 장애 대비
```

---

# 9. Single-AZ → Multi-AZ

기존 Single-AZ RDS를 Multi-AZ로 변경할 수 있다.

강의에서는 내부적으로 다음 흐름으로 설명한다.

```text
Primary DB
    ↓
Snapshot 생성
    ↓
다른 AZ에 Standby 생성
    ↓
SYNC Replication 설정
    ↓
Multi-AZ
```

사용자가 DB를 직접 중지하고 새로 만들 필요는 없다.

---

# 10. RDS 생성 시 주요 설정

RDS 데이터베이스를 만들 때 여러 설정을 선택한다.

대표적인 항목은 다음과 같다.

```text
RDS 생성

├─ Database Engine
├─ DB Version
├─ DB Instance Class
├─ Storage
├─ VPC
├─ Subnet Group
├─ Public Access
├─ Security Group
├─ Authentication
├─ Backup
├─ Monitoring
└─ Deletion Protection
```

실습에서 사용한 예시는 MySQL이다.

```text
Engine
→ MySQL

Instance
→ db.t3.micro

Storage
→ gp2

Port
→ 3306
```

시험에서 콘솔 클릭 순서를 외울 필요는 없다.

중요한 것은 각 설정이 무슨 역할을 하는지 이해하는 것이다.

---

# 11. RDS Endpoint + Port + Security Group

Application이 RDS에 접속하려면 크게 다음 흐름을 거친다.

```text
Endpoint
→ 어디로 갈 것인가?

Port
→ 어떤 서비스로 들어갈 것인가?

Security Group
→ 네트워크 접근이 허용되는가?

Authentication
→ 사용자가 누구인가?
```

---

## 11.1 Endpoint

RDS를 생성하면 AWS가 접속 주소를 제공한다.

예:

```text
database-1.xxxxx.ap-northeast-2.rds.amazonaws.com
```

이 주소가 **RDS Endpoint**이다.

Application 입장에서는:

```text
"Database 어디 있어?"

→ Endpoint
```

---

## 11.2 Port

Database Engine마다 사용하는 Port가 있다.

MySQL의 기본 Port는:

```text
3306
```

따라서 개념적으로:

```text
database-1.xxxxx.rds.amazonaws.com:3306
```

으로 접속한다.

```text
Endpoint
→ DB 주소

Port
→ DB 서비스의 문
```

---

## 11.3 Security Group

Endpoint와 Port를 알고 있어도 RDS Security Group이 접근을 허용하지 않으면 접속할 수 없다.

예:

```text
EC2
SG = Web-SG
    │
    │ TCP 3306
    ▼
RDS SG

Inbound:
MySQL / TCP 3306
Source = Web-SG
```

이 경우 Web-SG를 가진 EC2에서 RDS MySQL에 접근할 수 있다.

### 연결 실패 예

```text
Endpoint 맞음 ⭕
Port 맞음 ⭕
Password 맞음 ⭕

Security Group 차단 ❌

→ Connection 실패
```

---

## 11.4 Authentication

Security Group을 통과한 다음 실제 Database 인증이 이루어진다.

강의에서는 다음 방식을 소개한다.

- Username / Password
- IAM Database Authentication
- Kerberos

실습에서는 Username / Password 방식을 사용했다.

### 전체 흐름

```text
Application
     ↓
Endpoint
     ↓
Port
     ↓
Security Group
     ↓
Database Authentication
     ↓
RDS 접속 성공
```

---

# 12. Public Access

RDS 데이터베이스를 Publicly Accessible로 설정하면 외부 네트워크에서 접근 가능한 형태로 구성할 수 있다.

실습에서는 자신의 PC에서 SQL Client로 RDS에 직접 연결하기 위해 Public Access를 사용했다.

```text
Local PC
   ↓
Internet
   ↓
RDS
```

하지만 실제 환경에서는 데이터베이스를 인터넷 전체에 열어두는 방식보다 Application Server에서만 접근하도록 제한하는 것이 일반적이다.

```text
Internet
   ↓
Application / EC2
   ↓
RDS
```

Security Group에서는 필요한 Source만 허용한다.

```text
RDS SG

TCP 3306
Source = Application SG
```

---

# 13. RDS Backup

RDS에서는 자동 백업과 Snapshot을 사용할 수 있다.

## Automated Backup

RDS가 자동으로 Backup을 수행한다.

강의 기준 Backup Retention Period는:

```text
1 ~ 35 days
```

0으로 설정하면 Automated Backup이 비활성화된다.

Automated Backup은 **Point-in-Time Restore**를 지원한다.

```text
Automated Backup
      ↓
Point-in-Time Restore
```

특정 시점의 데이터베이스 상태로 복원할 수 있다.

---

## Manual Snapshot

사용자가 직접 DB Snapshot을 생성할 수 있다.

```text
RDS
 ↓
Take Snapshot
 ↓
DB Snapshot
```

Snapshot은 필요할 때 새로운 DB로 복원할 수 있다.

### 기본 구분

```text
Automated Backup
→ 자동
→ Point-in-Time Restore

Manual Snapshot
→ 사용자 직접 생성
→ 장기 보관 가능
```

---

# 14. Monitoring

RDS에서 다양한 DB Metric을 모니터링할 수 있다.

예:

- CPU Utilization
- Database Connections
- 기타 Database Metrics

```text
RDS
 ↓
Monitoring
 ↓
CPU / Connections / Metrics
```

예를 들어 DB 연결 수가 지속적으로 증가하면 현재 데이터베이스의 부하 상태를 확인할 수 있다.

일부 로그는 CloudWatch Logs로 내보낼 수도 있다.

---

# 15. Maintenance

RDS에서는 Database Maintenance Window를 지정할 수 있다.

예:

```text
Minor Version Upgrade
      ↓
Maintenance Window
```

AWS가 관리형 서비스로서 유지보수 작업을 수행할 시간을 지정하는 개념이다.

---

# 16. Deletion Protection

Deletion Protection은 실수로 RDS Database를 삭제하는 것을 방지한다.

```text
Deletion Protection ON
        ↓
Delete Database ❌
```

DB를 의도적으로 삭제하려면 먼저 Deletion Protection을 비활성화해야 한다.

---

# 17. RDS Custom

일반 RDS에서는 기저 Operating System과 Database 내부 환경에 직접 접근할 수 없다.

```text
Standard RDS

User
 ↓
Database

Underlying OS
Underlying EC2

→ 직접 접근 ❌
```

하지만 일부 Oracle 또는 Microsoft SQL Server 환경에서는 OS와 Database 내부를 직접 수정해야 할 수 있다.

이때 사용하는 것이 **RDS Custom**이다.

---

## 17.1 지원 Database

강의에서 설명하는 RDS Custom 대상은 다음 두 가지이다.

- Oracle
- Microsoft SQL Server

```text
RDS Custom
→ Oracle
→ Microsoft SQL Server
```

---

## 17.2 RDS Custom에서 가능한 것

RDS Custom에서는 기저 Operating System과 Database에 접근할 수 있다.

```text
User
 ↓
RDS Custom
 ↓
Underlying EC2
 ↓
Operating System
 ↓
Database
```

SSH 또는 SSM Session Manager를 이용해 기저 EC2 인스턴스에 접근할 수 있다.

가능한 작업 예:

- OS 설정 변경
- Database 설정 변경
- Patch 적용
- Native Feature 활성화
- 사용자 정의 구성 적용

즉 일반 RDS보다 훨씬 높은 수준의 제어가 가능하다.

---

## 17.3 RDS와 RDS Custom의 차이

```text
RDS
→ AWS가 OS와 DB 환경을 관리
→ 사용자가 내부 OS 접근 불가

RDS Custom
→ 사용자가 OS / DB 내부 접근 가능
→ Customization 가능
```

### 핵심 구조

```text
RDS
= 관리 편의성 높음
= 내부 제어 제한

RDS Custom
= RDS의 관리 기능
+
OS / DB Customization
```

---

## 17.4 Automation과 Snapshot

RDS Custom에서 직접 OS나 DB를 변경하는 동안 RDS의 자동 관리 작업이 동시에 실행되면 문제가 발생할 수 있다.

강의에서는 Customization을 수행할 때 RDS Automation을 중지하는 것을 권장한다.

```text
Pause / Disable Automation
        ↓
Customization
```

또한 기저 시스템에 직접 변경을 수행하기 때문에 문제가 발생할 경우를 대비해 DB Snapshot을 생성하는 것이 권장된다.

```text
DB Snapshot
     ↓
Customization
     ↓
문제 발생 시 복구
```

### 시험 포인트

문제에서 다음과 같은 키워드가 보이면 RDS Custom을 생각한다.

```text
Oracle / Microsoft SQL Server
+
Need OS Access
Need SSH / SSM
Need Custom Patch
Need Native Features

→ RDS Custom
```

---

# 18. RDS 전체 구조 정리

```text
Application
     │
     │ Endpoint + Port
     ▼
Security Group
     │
     ▼
Amazon RDS
     │
     ├─ DB Instance
     │   ├─ CPU
     │   └─ Memory
     │
     ├─ Database Engine
     │
     └─ EBS-backed Storage
```

확장과 고가용성 기능을 추가하면:

```text
                    Read Replica
                       ↑
                       │ ASYNC
Application → Primary RDS
                       │
                       │ SYNC
                       ↓
                    Standby
```

각 기능의 목적은 다르다.

```text
Read Replica
→ Read Scalability

Multi-AZ Standby
→ High Availability

Storage Auto Scaling
→ Storage Capacity Scaling
```

---

# 19. 시험 핵심 정리

```text
RDS
→ Managed Relational Database Service
→ SQL
→ AWS가 OS / Backup / Maintenance 등을 관리
```

```text
Read Replica
→ Read Scaling
→ ASYNC
→ Eventually Consistent
```

```text
Multi-AZ
→ High Availability / DR
→ SYNC
→ Automatic Failover
```

```text
Read Replica + Multi-AZ
→ Replica 자체에도 Standby를 둘 수 있음
→ Replica Standby는 Replica를 보호
```

```text
Storage Auto Scaling
→ Storage 부족 시 자동 확장
```

```text
Endpoint
→ DB 주소

Port
→ DB 서비스 Port

Security Group
→ 네트워크 접근 제어

Authentication
→ DB 사용자 인증
```

```text
Automated Backup
→ Point-in-Time Restore

Manual Snapshot
→ 사용자가 직접 생성
```

```text
RDS Custom
→ Oracle / SQL Server
→ OS / DB 접근
→ SSH / SSM
→ Customization
```

---

# 日本語まとめ

## Amazon RDS

Amazon RDS は AWS が提供する **マネージド型リレーショナルデータベースサービス**である。

AWS が以下のような運用作業を管理する。

- DB のプロビジョニング
- OS パッチ
- バックアップ
- モニタリング
- メンテナンス
- Read Replica
- Multi-AZ
- Storage Scaling

通常の RDS では、基盤となる EC2 インスタンスへ SSH 接続することはできない。

---

## Read Replica

Read Replica は **読み取り性能をスケールするための機能**である。

```text
Primary DB
    │
    │ ASYNC
    ▼
Read Replica
```

Replication は非同期であるため、Replica のデータは一時的に古い場合がある。

```text
Read Replica
→ Read Scalability
→ ASYNC
→ Eventually Consistent
```

レポートや分析など、読み取り負荷が大きい処理に適している。

---

## Multi-AZ

Multi-AZ は **高可用性と災害復旧**を目的とする。

```text
Primary DB
    │
    │ SYNC
    ▼
Standby DB
```

Primary に障害が発生した場合、AWS が Standby へ自動的に Failover する。

```text
Multi-AZ
→ High Availability
→ Disaster Recovery
→ SYNC
→ Automatic Failover
```

Standby は通常の読み取りスケーリングには使用しない。

---

## Read Replica + Multi-AZ

Read Replica 自体を Multi-AZ 構成にすることもできる。

```text
Original Primary
      │
      │ ASYNC
      ▼
Read Replica
      │
      │ SYNC
      ▼
Replica Standby
```

Replica Standby は Original Primary ではなく、Read Replica を保護する。

---

## RDS Connection

RDS への接続では以下を理解する。

```text
Endpoint
→ データベースの接続先

Port
→ DB サービスのポート

Security Group
→ ネットワークアクセス制御

Authentication
→ DB ユーザー認証
```

---

## Backup

```text
Automated Backup
→ Point-in-Time Restore

Manual Snapshot
→ 手動で作成するバックアップ
```

---

## RDS Custom

RDS Custom は Oracle と Microsoft SQL Server を対象に、基盤 OS と Database をカスタマイズできる。

```text
RDS Custom
→ OS / DB Access
→ SSH / SSM
→ Custom Patch
→ Native Features
```

通常の RDS より高いレベルの管理権限が必要な場合に使用する。

---

# English Summary

## Amazon RDS

Amazon RDS is a **managed relational database service** provided by AWS.

AWS manages many operational tasks such as:

- Database provisioning
- OS patching
- Automated backups
- Monitoring
- Maintenance
- Read Replicas
- Multi-AZ
- Storage scaling

Standard RDS does not provide SSH access to the underlying EC2 instance.

---

## Read Replica

Read Replicas are used for **read scalability**.

```text
Primary DB
    │
    │ ASYNC
    ▼
Read Replica
```

Replication is asynchronous, so replica data can temporarily lag behind the Primary.

```text
Read Replica
→ Read Scalability
→ ASYNC
→ Eventually Consistent
```

Typical use cases include reporting, analytics, and read-heavy workloads.

---

## Multi-AZ

Multi-AZ is used for **high availability and disaster recovery**.

```text
Primary DB
    │
    │ SYNC
    ▼
Standby DB
```

If the Primary database fails, RDS can automatically fail over to the Standby.

```text
Multi-AZ
→ High Availability
→ Disaster Recovery
→ SYNC
→ Automatic Failover
```

The Standby is not used for normal read scaling.

---

## Read Replica with Multi-AZ

A Read Replica can itself be configured as Multi-AZ.

```text
Original Primary
      │
      │ ASYNC
      ▼
Read Replica
      │
      │ SYNC
      ▼
Replica Standby
```

The Replica Standby protects the Read Replica, not the Original Primary.

---

## RDS Connection

Applications connect to RDS using:

```text
Endpoint
→ Database address

Port
→ Database service port

Security Group
→ Network access control

Authentication
→ Database user authentication
```

---

## Backup

```text
Automated Backup
→ Point-in-Time Restore

Manual Snapshot
→ User-created database backup
```

---

## RDS Custom

RDS Custom provides deeper operating system and database access for Oracle and Microsoft SQL Server.

```text
RDS Custom
→ OS / Database Access
→ SSH / SSM
→ Custom Patches
→ Native Features
```

It is used when applications require customization that is not possible with standard RDS.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Relational Database | リレーショナルデータベース | 관계형 데이터베이스 |
| Managed Service | マネージドサービス | 관리형 서비스 |
| Database Engine | データベースエンジン | 데이터베이스 엔진 |
| DB Instance | DB インスタンス | DB 인스턴스 |
| Read Replica | リードレプリカ | 읽기 복제본 |
| Primary Database | プライマリデータベース | 기본 데이터베이스 |
| Standby Database | スタンバイデータベース | 대기 데이터베이스 |
| Multi-AZ | マルチAZ | 다중 AZ |
| Read Scalability | 読み取りスケーラビリティ | 읽기 확장성 |
| High Availability | 高可用性 | 고가용성 |
| Disaster Recovery | 災害復旧 | 재해 복구 |
| Asynchronous Replication | 非同期レプリケーション | 비동기 복제 |
| Synchronous Replication | 同期レプリケーション | 동기 복제 |
| Eventually Consistent | 結果整合性 | 최종적 일관성 |
| Failover | フェイルオーバー | 장애 조치 |
| Promotion | 昇格 | 승격 |
| Endpoint | エンドポイント | 엔드포인트 |
| Port | ポート | 포트 |
| Security Group | セキュリティグループ | 보안 그룹 |
| Authentication | 認証 | 인증 |
| Storage Auto Scaling | ストレージオートスケーリング | 스토리지 자동 확장 |
| Maximum Storage Threshold | 最大ストレージしきい値 | 최대 스토리지 임계값 |
| Automated Backup | 自動バックアップ | 자동 백업 |
| Point-in-Time Restore | ポイントインタイムリストア | 특정 시점 복원 |
| DB Snapshot | DB スナップショット | DB 스냅샷 |
| Monitoring | モニタリング | 모니터링 |
| Maintenance Window | メンテナンスウィンドウ | 유지보수 기간 |
| Deletion Protection | 削除保護 | 삭제 방지 |
| RDS Custom | RDS Custom | RDS 커스텀 |
| Operating System | オペレーティングシステム | 운영체제 |
| Native Feature | ネイティブ機能 | 네이티브 기능 |
| SSM Session Manager | SSM セッションマネージャー | SSM 세션 관리자 |

---

# Review Questions

1. Amazon RDS와 EC2에 직접 Database Engine을 설치하는 방식의 가장 큰 차이는 무엇인가?

2. Primary DB의 SELECT 요청이 너무 많아 성능 문제가 발생하고 있다. 어떤 RDS 기능이 적합한가?

3. Read Replica가 Eventually Consistent한 이유는 무엇인가?

4. Read Replica와 Multi-AZ의 가장 중요한 목적 차이는 무엇인가?

5. Multi-AZ Standby가 일반적인 읽기 요청을 처리하지 않는 이유는 무엇인가?

6. Read Replica 자체를 Multi-AZ로 구성했을 때 Replica의 Standby는 어떤 DB를 보호하는가?

7. Application이 RDS에 접속할 때 Endpoint, Port, Security Group, Authentication은 각각 어떤 역할을 하는가?

8. 데이터베이스 Storage 사용량을 예측하기 어려운 경우 어떤 기능을 사용할 수 있는가?

9. Automated Backup과 Manual Snapshot의 차이는 무엇인가?

10. Oracle 또는 Microsoft SQL Server에서 기저 OS에 접근해 Custom Patch를 적용해야 한다면 어떤 서비스를 사용해야 하는가?