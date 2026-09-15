# Amazon Aurora

## 1. 개요

**Amazon Aurora**는 AWS가 자체 개발한 클라우드 네이티브 관계형 데이터베이스이다.

Aurora는 오픈 소스 데이터베이스 자체는 아니지만 다음과 호환된다.

- MySQL
- PostgreSQL

즉 기존 MySQL 또는 PostgreSQL용 드라이버와 애플리케이션을 Aurora와 함께 사용할 수 있다.

```text
RDS MySQL
→ AWS가 MySQL을 관리

Aurora MySQL-Compatible
→ AWS가 자체 개발한 DB
→ MySQL API와 호환
```

Aurora는 일반적인 RDS보다 성능, 가용성, 확장성 측면에서 클라우드 환경에 최적화되어 있다.

---

# 2. Aurora Architecture

Aurora의 가장 중요한 특징 중 하나는 **Compute와 Storage가 분리되어 있다는 것**이다.

```text
          Aurora Compute

       Writer Instance
             │
      ┌──────┴──────┐
      ▼             ▼
 Reader          Reader
 Instance        Instance

             │
             ▼

      Shared Storage Volume
```

여러 DB Instance가 하나의 분산 Shared Storage를 사용한다.

```text
Compute
→ Writer / Reader Instances

Storage
→ Shared Distributed Storage
```

---

# 3. Aurora Storage

Aurora Storage는 자동으로 확장된다.

강의/PDF 기준으로 Storage는 10 GB 단위로 증가하며 최대 256 TB까지 확장된다.

```text
10 GB
  ↓
Automatic Growth
  ↓
Up to 256 TB
```

사용자가 Storage 크기를 지속적으로 수동 관리할 필요가 없다.

---

## 3.1 Six Copies Across Three AZs

Aurora는 데이터를 **3개의 Availability Zone에 걸쳐 6개의 복사본**으로 저장한다.

```text
AZ-A        AZ-B        AZ-C

Copy        Copy        Copy
Copy        Copy        Copy

Total = 6 Copies
```

### Write

Write 작업에는 6개 중 4개의 복사본이 필요하다.

```text
Write
→ 4 / 6 Copies
```

### Read

Read 작업에는 6개 중 3개의 복사본이 필요하다.

```text
Read
→ 3 / 6 Copies
```

이 구조를 통해 하나의 AZ에 장애가 발생해도 높은 가용성을 유지할 수 있다.

---

## 3.2 Self-Healing Storage

Aurora Storage는 손상된 데이터 블록을 다른 정상 복사본을 이용해 자동 복구할 수 있다.

```text
Damaged Block
      ↓
Healthy Copy
      ↓
Self Healing
```

Storage는 여러 볼륨에 분산되어 관리된다.

### 시험 핵심

```text
Aurora Storage
→ 3 AZ
→ 6 Copies
→ Write 4/6
→ Read 3/6
→ Self Healing
→ Auto Scaling
```

---

# 4. Writer and Reader Instances

Aurora Cluster에는 하나의 Writer Instance와 여러 Reader Instance가 존재할 수 있다.

```text
             Writer
          Read / Write
               │
       ┌───────┼───────┐
       ▼       ▼       ▼
    Reader  Reader   Reader
     Read    Read     Read
```

### Writer

```text
Writer
→ Read
→ Write
```

Storage에 Write를 수행하는 Instance이다.

### Reader

```text
Reader
→ Read workloads
```

강의/PDF 기준 최대 15개의 Aurora Read Replica를 사용할 수 있다.

---

# 5. Automatic Failover

Writer Instance에 장애가 발생하면 Reader 중 하나가 새로운 Writer로 승격될 수 있다.

```text
Writer 💥

Reader 1
Reader 2
Reader 3

    ↓ Failover

Reader 1
    ↓
New Writer
```

강의에서는 Failover가 일반적으로 30초 이내에 이루어진다고 설명한다.

### 핵심

```text
Writer Failure
→ Reader Promotion
→ Automatic Failover
```

---

# 6. Aurora Endpoints

Aurora Cluster에서는 Instance 주소를 직접 관리하는 대신 Endpoint를 사용할 수 있다.

대표적으로:

- Writer Endpoint
- Reader Endpoint
- Custom Endpoint

---

## 6.1 Writer Endpoint

Writer Endpoint는 항상 현재 Writer Instance를 가리킨다.

```text
Application
     │
     │ Writer Endpoint
     ▼
Current Writer
```

Writer가 Failover로 변경되어도 Application은 동일한 Writer Endpoint를 사용할 수 있다.

```text
Writer Endpoint
→ Current Writer
→ Read / Write
```

---

## 6.2 Reader Endpoint

Reader Endpoint는 Aurora Reader Instances에 대한 연결을 분산한다.

```text
Application
      │
      │ Reader Endpoint
      ▼
┌─────────────────────┐
│ Reader 1            │
│ Reader 2            │
│ Reader 3            │
└─────────────────────┘
```

Reader Endpoint는 **Connection Level Load Balancing**을 제공한다.

즉 SQL Statement 하나씩 분산하는 것이 아니라 새로운 DB Connection을 Reader들에 분산한다.

```text
Connection 1
→ Reader 1

Connection 2
→ Reader 3
```

### 시험 핵심

```text
Writer Endpoint
→ Current Writer

Reader Endpoint
→ Reader Connections
→ Connection-level Load Balancing
```

---

# 7. Aurora Replica Auto Scaling

읽기 요청이 증가하면 Reader Instance의 CPU와 Connection 사용량이 증가할 수 있다.

Aurora Replica Auto Scaling을 사용하면 Read Replica 수를 자동으로 증가 또는 감소시킬 수 있다.

```text
Read Traffic 증가
      ↓
Reader CPU 증가
      ↓
Replica Auto Scaling
      ↓
Reader 추가
```

새로 추가된 Replica는 Reader Endpoint에 자동으로 포함된다.

```text
Reader Endpoint
      ↓
Reader 1
Reader 2
Reader 3
Reader 4
```

Auto Scaling Policy에서는 다음과 같은 Metric을 사용할 수 있다.

- Average CPU Utilization
- Average Database Connections

### 시험 핵심

```text
High Read Load
+
Automatically add Aurora Readers

→ Aurora Replica Auto Scaling
```

---

# 8. Custom Endpoint

Custom Endpoint는 Aurora Replica 중 **특정한 Instance들의 부분집합**을 하나의 Endpoint로 묶는 기능이다.

예:

```text
Reader 1
Small Instance

Reader 2
Small Instance

Reader 3
Large Instance

Reader 4
Large Instance
```

Large Instance만 Analytics 용도로 사용하고 싶다면:

```text
Analytics Custom Endpoint
          ↓
     Reader 3
     Reader 4
```

와 같이 구성할 수 있다.

### 차이

```text
Reader Endpoint
→ Reader 전체

Custom Endpoint
→ 선택한 Reader subset
```

### 대표적인 사용 사례

```text
Heavy Analytics Query
→ Powerful Aurora Replicas
→ Custom Endpoint
```

---

# 9. Aurora Serverless

Aurora Serverless는 실제 Database 사용량에 따라 Compute Capacity를 자동으로 조절한다.

일반 Provisioned Aurora에서는 특정 DB Instance Type을 선택한다.

```text
db.r6g.large
db.t3.medium
```

Aurora Serverless v2에서는 대신 **Aurora Capacity Unit (ACU)** 범위를 설정한다.

```text
Minimum ACU
     ↕
Maximum ACU
```

사용량에 따라 Database Capacity가 자동으로 증가하거나 감소한다.

### 적합한 Workload

- Infrequent
- Intermittent
- Unpredictable

즉 사용량을 미리 예측하기 어려운 Database workload에 적합하다.

```text
Unpredictable Workload
       ↓
Aurora Serverless
```

Capacity Planning이 필요하지 않으며 실제 사용량에 따라 비용을 지불한다.

---

# 10. Replica Auto Scaling vs Aurora Serverless

두 기능은 서로 다른 대상을 Scale한다.

```text
Replica Auto Scaling
→ Reader Instance 개수 조절

Aurora Serverless
→ Compute Capacity 조절
```

### 기억하기

```text
Replica Auto Scaling
= 몇 대?

Serverless
= 얼마나 강하게?
```

---

# 11. Aurora Global Database

Aurora Global Database는 여러 AWS Region에 걸쳐 Aurora Database를 구성한다.

```text
Primary Region
Read / Write
      │
      │ Cross-Region Replication
      ▼
Secondary Region
Read Only
```

Primary Region에서 Write가 수행되고 Secondary Region에서는 Read workload를 처리할 수 있다.

---

## 11.1 Global Database Architecture

```text
us-east-1
PRIMARY
Read / Write

       │
       │ Replication
       ▼

eu-west-1
SECONDARY
Read Only
```

PDF 기준:

- 1 Primary Region
- Up to 10 Secondary Regions
- Up to 16 Read Replicas per Secondary Region
- Typical Cross-Region Replication < 1 second
- Secondary Region Promotion RTO < 1 minute

---

## 11.2 Global Aurora Use Cases

### Global Read Performance

전 세계 사용자들이 가까운 Secondary Region에서 데이터를 읽을 수 있다.

```text
Global Users
     ↓
Nearest Aurora Region
     ↓
Lower Read Latency
```

### Disaster Recovery

Primary Region에 장애가 발생하면 Secondary Region을 새로운 Primary Region으로 Promote할 수 있다.

```text
Primary Region 💥
       ↓
Promote Secondary Region
       ↓
New Read / Write Region
```

### 시험 핵심

다음 표현이 나오면 Aurora Global Database를 고려한다.

```text
Global Application
Cross-Region Database
< 1 second replication
Low-latency global reads
Disaster Recovery
Region Failover
```

---

# 12. Aurora Machine Learning

Aurora는 AWS Machine Learning 서비스와 직접 통합할 수 있다.

지원되는 서비스:

- Amazon SageMaker
- Amazon Comprehend

Application은 SQL Query를 통해 Machine Learning Prediction을 사용할 수 있다.

```text
Application
     │
     │ SQL Query
     ▼
Amazon Aurora
     │
     ▼
SageMaker / Comprehend
     │
     │ Prediction
     ▼
Amazon Aurora
     │
     ▼
Application
```

### SageMaker

```text
SageMaker
→ General Machine Learning Models
```

### Amazon Comprehend

```text
Comprehend
→ Sentiment Analysis
```

### 대표 Use Cases

- Fraud Detection
- Advertisement Targeting
- Sentiment Analysis
- Product Recommendations

### 시험 핵심

```text
Aurora
+
ML Prediction using SQL
+
SageMaker / Comprehend

→ Aurora Machine Learning
```

---

# 13. Backtrack

Aurora Backtrack을 사용하면 Backup Restore 없이 Database를 과거 시점으로 되돌릴 수 있다.

```text
Current DB
    ↓
Backtrack
    ↓
Previous Point in Time
```

예:

```text
Current
→ Yesterday 4 PM
→ Yesterday 5 PM
```

### 핵심

```text
Aurora Backtrack
→ Rewind DB to previous time
→ Without restoring from backup
```

---

# 14. Aurora Standard vs I/O-Optimized

Aurora Cluster Storage Configuration에는 다음과 같은 옵션이 있다.

```text
Aurora Standard
→ 일반적인 workload

Aurora I/O-Optimized
→ I/O-intensive workload
```

Read와 Write I/O가 많은 workload에서는 I/O-Optimized가 적합할 수 있다.

시험에서는 세부 가격 구조보다 **I/O-intensive workload를 위한 별도 옵션이 존재한다**는 정도를 이해하면 된다.

---

# 15. Aurora 실습에서 확인할 것

실습에서 중요한 것은 콘솔 클릭 순서를 암기하는 것이 아니다.

다음 구조를 실제 AWS Console에서 확인했다는 점이 중요하다.

```text
Aurora Cluster
│
├─ Writer Instance
│
├─ Reader Instance
│
├─ Writer Endpoint
│
├─ Reader Endpoint
│
└─ Shared Storage
```

또한 다음 기능을 Console에서 구성할 수 있다.

- Replica Auto Scaling
- Cross-Region Replica
- Global Database
- Serverless
- Backup
- Backtrack
- Encryption
- Deletion Protection

Aurora 생성에는 비용이 발생할 수 있으므로 시험 학습을 위해 반드시 직접 생성할 필요는 없다.

---

# 16. Aurora 전체 구조

```text
                       Application
                    /              \
                   /                \
          Writer Endpoint       Reader Endpoint
                 │                    │
                 ▼                    ▼
          Writer Instance       Reader Instances
                 │              R   R   R   R
                 │                    │
                 └────────┬───────────┘
                          ▼
                Shared Aurora Storage
                          │
             ┌────────────┼────────────┐
             ▼            ▼            ▼
            AZ-A         AZ-B         AZ-C

               6 Copies across 3 AZ
               Self-Healing
               Auto Scaling
```

Aurora의 확장 기능을 추가하면:

```text
Reader Instances
      ↑
Replica Auto Scaling

Compute Capacity
      ↑
Aurora Serverless

Specific Readers
      ↑
Custom Endpoint

Other Regions
      ↑
Aurora Global Database
```

---

# 17. 시험 핵심 정리

```text
Aurora
→ AWS proprietary relational DB
→ MySQL / PostgreSQL Compatible
```

```text
Architecture
→ Compute / Storage Separation
→ Shared Storage
```

```text
Storage
→ 3 AZ
→ 6 Copies
→ Write 4/6
→ Read 3/6
→ Self-Healing
→ Auto Scaling
```

```text
Writer
→ Read / Write

Reader
→ Read
```

```text
Writer Endpoint
→ Current Writer

Reader Endpoint
→ Reader Connection Load Balancing
```

```text
Replica Auto Scaling
→ Reader Instance 수 자동 조절
```

```text
Custom Endpoint
→ 특정 Replica subset
```

```text
Aurora Serverless
→ Unpredictable / Intermittent Workload
→ No Capacity Planning
→ Auto Scaling Compute
```

```text
Aurora Global Database
→ 1 Primary Region
→ Up to 10 Secondary Regions
→ < 1 sec replication
→ DR + Global Reads
```

```text
Aurora Machine Learning
→ SageMaker
→ Comprehend
→ ML Prediction through SQL
```

---

# 日本語まとめ

## Amazon Aurora

Amazon Aurora は AWS が独自に開発したクラウドネイティブなリレーショナルデータベースである。

MySQL および PostgreSQL と互換性がある。

```text
Aurora
→ AWS Proprietary
→ MySQL Compatible
→ PostgreSQL Compatible
```

---

## Storage Architecture

Aurora は Compute と Storage を分離している。

Storage は 3つの AZ に 6つのコピーを保存する。

```text
Write
→ 4 / 6

Read
→ 3 / 6
```

Storage は自動拡張と Self-Healing を提供する。

---

## Writer / Reader

```text
Writer
→ Read / Write

Reader
→ Read
```

Writer に障害が発生すると Reader の1つが新しい Writer に昇格できる。

---

## Endpoints

```text
Writer Endpoint
→ 現在の Writer

Reader Endpoint
→ Reader への接続を分散

Custom Endpoint
→ 選択した Replica のグループ
```

Reader Endpoint の Load Balancing は Connection Level で行われる。

---

## Replica Auto Scaling

読み取り負荷が増加すると Aurora Replica を自動的に追加できる。

```text
Read Load ↑
→ Replica Auto Scaling
→ Reader 増加
```

---

## Aurora Serverless

Aurora Serverless は実際の利用量に基づいて Compute Capacity を自動調整する。

```text
Unpredictable Workload
→ Aurora Serverless
```

Capacity Planning が不要である。

---

## Aurora Global Database

Global Database は複数の AWS Region に Aurora を展開する。

```text
Primary Region
→ Read / Write

Secondary Region
→ Read Only
```

PDF 기준으로 최대 10개의 Secondary Region을 사용할 수 있으며 일반적인 Cross-Region Replication은 1초 미만이다.

---

## Aurora Machine Learning

Aurora는 다음 서비스와 통합할 수 있다.

- Amazon SageMaker
- Amazon Comprehend

SQL을 통해 Machine Learning Prediction을 Application에 제공할 수 있다.

---

# English Summary

## Amazon Aurora

Amazon Aurora is an AWS proprietary cloud-native relational database compatible with MySQL and PostgreSQL.

Aurora separates compute and storage.

---

## Storage

Aurora stores six copies of data across three Availability Zones.

```text
Writes
→ 4 / 6 copies

Reads
→ 3 / 6 copies
```

The storage layer is self-healing and automatically scales.

---

## Writer and Readers

```text
Writer
→ Read / Write

Reader
→ Read
```

If the Writer fails, a Reader can be promoted to become the new Writer.

---

## Endpoints

```text
Writer Endpoint
→ Current Writer

Reader Endpoint
→ Load balances connections across Readers

Custom Endpoint
→ Selected subset of Aurora Replicas
```

Reader Endpoint load balancing works at the connection level.

---

## Replica Auto Scaling

Aurora can automatically add or remove Read Replicas based on read workload.

```text
High Read Load
→ Replica Auto Scaling
```

---

## Aurora Serverless

Aurora Serverless automatically adjusts database compute capacity based on actual usage.

It is useful for infrequent, intermittent, or unpredictable workloads where capacity planning is difficult.

---

## Aurora Global Database

Aurora Global Database provides cross-region replication.

```text
Primary Region
→ Read / Write

Secondary Regions
→ Read Only
```

According to the course PDF:

- Up to 10 Secondary Regions
- Up to 16 Read Replicas per Secondary Region
- Typical replication below one second
- Region promotion RTO below one minute

---

## Aurora Machine Learning

Aurora integrates with:

- Amazon SageMaker
- Amazon Comprehend

Applications can request ML-based predictions through SQL queries.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Amazon Aurora | Amazon Aurora | 아마존 오로라 |
| Compatible | 互換性のある | 호환 가능한 |
| Shared Storage | 共有ストレージ | 공유 스토리지 |
| Writer Instance | ライターインスタンス | 라이터 인스턴스 |
| Reader Instance | リーダーインスタンス | 리더 인스턴스 |
| Writer Endpoint | ライターエンドポイント | 라이터 엔드포인트 |
| Reader Endpoint | リーダーエンドポイント | 리더 엔드포인트 |
| Custom Endpoint | カスタムエンドポイント | 사용자 지정 엔드포인트 |
| Connection Load Balancing | 接続ロードバランシング | 연결 로드 밸런싱 |
| Read Replica | リードレプリカ | 읽기 복제본 |
| Replica Auto Scaling | レプリカオートスケーリング | 복제본 자동 확장 |
| Aurora Serverless | Aurora Serverless | Aurora 서버리스 |
| Aurora Capacity Unit | Aurora Capacity Unit | Aurora 용량 단위 |
| Capacity Planning | キャパシティプランニング | 용량 계획 |
| Global Database | グローバルデータベース | 글로벌 데이터베이스 |
| Primary Region | プライマリリージョン | 기본 리전 |
| Secondary Region | セカンダリリージョン | 보조 리전 |
| Cross-Region Replication | クロスリージョンレプリケーション | 리전 간 복제 |
| Failover | フェイルオーバー | 장애 조치 |
| Promotion | 昇格 | 승격 |
| Self-Healing | 自己修復 | 자가 복구 |
| Aurora Machine Learning | Aurora Machine Learning | Aurora 머신러닝 |
| Amazon SageMaker | Amazon SageMaker | 아마존 세이지메이커 |
| Amazon Comprehend | Amazon Comprehend | 아마존 컴프리헨드 |
| Backtrack | バックトラック | 백트랙 |
| I/O-Optimized | I/O 最適化 | I/O 최적화 |

---

# Review Questions

1. Aurora와 일반 RDS MySQL의 가장 중요한 구조적 차이는 무엇인가?

2. Aurora Storage가 3개의 AZ에 6개의 Copy를 저장하는 이유는 무엇인가?

3. Aurora의 Write에는 6개 Copy 중 몇 개가 필요하고 Read에는 몇 개가 필요한가?

4. Writer Endpoint와 Reader Endpoint의 역할 차이는 무엇인가?

5. Reader Endpoint의 Load Balancing은 SQL Statement Level인가, Connection Level인가?

6. Replica Auto Scaling과 Aurora Serverless의 차이는 무엇인가?

7. 특정 고성능 Aurora Reader들만 Analytics workload에 사용하려면 어떤 기능을 사용할 수 있는가?

8. 전 세계 사용자에게 낮은 Read Latency를 제공하고 Region 장애에 대비하려면 어떤 Aurora 기능이 적합한가?

9. Aurora Global Database의 Cross-Region replication 지연시간이 1초 미만이라는 표현이 나오면 어떤 기능을 떠올려야 하는가?

10. SQL을 통해 SageMaker 또는 Comprehend의 예측 결과를 사용하려면 어떤 기능을 사용할 수 있는가?