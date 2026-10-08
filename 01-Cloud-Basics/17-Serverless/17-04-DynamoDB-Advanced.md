# 17-04. DynamoDB Advanced

## 1. DynamoDB Accelerator (DAX)

DAX는 DynamoDB 전용 **In-Memory Cache**이다.

```text
Application
    ↓
   DAX
    ↓ Cache Miss
DynamoDB
```

특징:

- Fully Managed
- Highly Available
- DynamoDB API Compatible
- Cached Data에 Microsecond Latency
- Read-heavy Workload에 유용

기본 Cache TTL은 5분이다.

### DAX vs ElastiCache

```text
DAX
→ DynamoDB 데이터의 Cache

ElastiCache
→ 범용 In-Memory Cache
```

DynamoDB Read 성능 개선이 핵심 요구사항이면 DAX를 우선적으로 고려한다.

---

# 2. DynamoDB Streams

DynamoDB Item의 변경 사항을 기록한다.

```text
Create
Update
Delete
   ↓
DynamoDB Streams
   ↓
Lambda
```

대표적인 활용:

- 데이터 변경에 반응
- Lambda Trigger
- Analytics
- 파생 데이터 생성

Stream Record 보존 기간은 **24시간**이다.

---

# 3. DynamoDB Streams vs Kinesis Data Streams

```text
DynamoDB Streams
→ DynamoDB 변경 사항 전용

Kinesis Data Streams
→ 범용 Streaming
```

DynamoDB Streams는 DynamoDB 변경 처리에 간단하게 사용할 수 있다.

Kinesis Data Streams는 더 긴 Retention과 다양한 Consumer가 필요한 Streaming Architecture에 적합하다.

---

# 4. Global Tables

DynamoDB Global Tables는 여러 Region에 Table을 구성하여 **Multi-Region Active-Active** 구조를 제공한다.

```text
Users Asia
   ↓
DynamoDB Tokyo
       ↕
DynamoDB Virginia
   ↑
Users US
```

장점:

- Multi-Region
- Active-Active
- 글로벌 사용자 Low Latency

---

# 5. Time To Live (TTL)

Item에 만료 시간을 설정할 수 있다.

```text
Session Item
    ↓
TTL 도달
    ↓
자동 삭제
```

대표적인 활용:

- Web Session
- 임시 데이터
- 오래된 데이터 자동 정리

TTL 삭제는 만료 시각에 정확히 즉시 실행되는 것을 보장하지 않는다.

---

# 6. Backup

## Point-in-Time Recovery

Continuous Backup 기능이다.

- Recovery Window 설정 가능
- 최대 35일
- 특정 시점으로 복원
- 복원 시 새로운 Table 생성

## On-Demand Backup

필요할 때 직접 Backup을 생성한다.

명시적으로 삭제하기 전까지 보관할 수 있다.

---

# 7. DynamoDB + S3

### Export to S3

```text
DynamoDB
   ↓ Export
   S3
   ↓
Athena / Analytics
```

PITR을 이용한 Export는 Table의 Read Capacity를 소비하지 않는다.

### Import from S3

```text
S3
 ↓
Import
 ↓
New DynamoDB Table
```

지원되는 대표 Format:

- CSV
- DynamoDB JSON
- Amazon Ion

Import는 새로운 DynamoDB Table을 생성하며 Table의 Write Capacity를 소비하지 않는다.

---

## 🎯 Exam Notes

- DynamoDB Read Cache → DAX
- DAX → Microsecond Latency
- Item 변경 추적 → DynamoDB Streams
- Streams Retention → 24 Hours
- Multi-Region Active-Active → Global Tables
- 자동 Item 만료 → TTL
- 최대 35일 시점 복구 → PITR
- DynamoDB Data 분석 → Export to S3

## 💡 Practical Example

사용자 Session 관리:

```text
User Login
   ↓
DynamoDB
Session 저장
   ↓
TTL 설정
   ↓
시간 경과
   ↓
자동 삭제
```

## 🇯🇵 日本語 Summary

DynamoDBには、DAXによる高速キャッシュ、Streamsによる変更データ処理、Global Tablesによるマルチリージョン構成、TTLによる自動削除、PITRやオンデマンドバックアップなどの機能があります。

## 🇺🇸 English Summary

DynamoDB provides advanced features including DAX for caching, Streams for change processing, Global Tables for multi-Region active-active architectures, TTL for automatic expiration, and multiple backup and S3 integration options.

## 📚 Vocabulary

| Term | Meaning |
|---|---|
| DAX | DynamoDB 전용 In-Memory Cache |
| Stream | 데이터 변경 Event의 연속 |
| Global Table | Multi-Region DynamoDB Table |
| TTL | 데이터 만료 시간 |
| PITR | Point-in-Time Recovery |
| Active-Active | 여러 위치에서 동시에 Read/Write 가능한 구성 |

## 📝 Review Questions

### Q1. DynamoDB에서 Microsecond Read Latency가 필요하다면?

<details>
<summary>정답 보기</summary>

DAX를 사용한다.

</details>

### Q2. DynamoDB Item의 변경을 Lambda로 처리하려면?

<details>
<summary>정답 보기</summary>

DynamoDB Streams를 사용할 수 있다.

</details>

### Q3. Multi-Region Active-Active DynamoDB가 필요하다면?

<details>
<summary>정답 보기</summary>

DynamoDB Global Tables

</details>