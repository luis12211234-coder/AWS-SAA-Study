# 17-03. Amazon DynamoDB

## 1. DynamoDB

Amazon DynamoDB는 AWS의 **Serverless Fully Managed NoSQL Database**이다.

특징:

- Serverless
- Fully Managed
- NoSQL
- Multi-AZ High Availability
- 대규모 Scaling
- Single-digit millisecond 성능
- IAM Integration
- Transaction 지원
- 유지보수 및 Patch 관리 불필요

---

# 2. Table Structure

DynamoDB의 기본 구조:

```text
Table
 └── Item
      └── Attribute
```

관계형 DB와 대략 비교하면:

| DynamoDB | RDBMS |
|---|---|
| Table | Table |
| Item | Row |
| Attribute | Column과 유사 |

하지만 DynamoDB는 Flexible Schema를 지원하기 때문에 각 Item의 일반 Attribute 구성이 서로 달라도 된다.

---

# 3. Primary Key

모든 DynamoDB Table에는 Primary Key가 필요하다.

두 가지 구조가 있다.

### Partition Key Only

```text
UserID (PK)
-----------
1001
1002
1003
```

Partition Key 값은 각 Item을 고유하게 식별해야 한다.

### Partition Key + Sort Key

```text
UserID (PK) | GameID (SK) | Score
------------|-------------|------
1001        | 1           | 80
1001        | 2           | 95
1001        | 3           | 72
```

이 경우:

```text
Primary Key
= Partition Key + Sort Key
```

Partition Key가 같아도 Sort Key가 다르면 서로 다른 Item이다.

Partition Key는 데이터의 내부 분산에도 중요한 역할을 한다.

---

# 4. Item Size

DynamoDB의 한 Item 최대 크기는:

```text
400 KB
```

이는 Attribute 하나가 아니라 **Item 전체 크기**이다.

큰 이미지나 동영상 같은 Object는 일반적으로 S3에 저장하고 DynamoDB에는 Metadata나 S3 위치를 저장한다.

---

# 5. Data Types

대표적인 데이터 타입:

### Scalar

- String
- Number
- Binary
- Boolean
- Null

### Document

- List
- Map

### Set

- String Set
- Number Set
- Binary Set

---

# 6. Capacity Modes

## Provisioned Capacity

필요한 처리량을 미리 설정한다.

```text
RCU = Read Capacity Unit
WCU = Write Capacity Unit
```

예측 가능한 Traffic에 적합하다.

Auto Scaling을 함께 사용할 수 있다.

## On-Demand Capacity

미리 RCU/WCU를 프로비저닝하지 않고 요청에 따라 처리한다.

```text
Unpredictable Traffic
Sudden Spike
        ↓
    On-Demand
```

용량 계획이 필요하지 않으며 실제 요청량을 기반으로 비용이 발생한다.

---

## 🎯 Exam Notes

```text
DynamoDB
= Serverless
= NoSQL
= Key-Value / Document
= Multi-AZ
= Millisecond Latency
```

- Primary Key 필수
- Partition Key 또는 Partition Key + Sort Key
- Item 최대 400 KB
- Predictable Traffic → Provisioned
- Unpredictable / Sudden Spike → On-Demand

## 💡 Practical Example

게임 기록:

```text
UserID | GameID | Score
-------|--------|------
1001   | 001    | 90
1001   | 002    | 85
1002   | 001    | 77
```

```text
Partition Key = UserID
Sort Key = GameID
```

동일한 사용자가 여러 게임 기록을 저장할 수 있다.

## 🇯🇵 日本語 Summary

Amazon DynamoDBは、AWSのフルマネージド・サーバーレスNoSQLデータベースです。Partition KeyとオプションのSort Keyを使用してデータを識別し、大規模なワークロードに自動的に対応できます。

## 🇺🇸 English Summary

Amazon DynamoDB is a fully managed serverless NoSQL database. Tables use a partition key and optionally a sort key as the primary key. DynamoDB provides scalable, highly available, low-latency storage.

## 📚 Vocabulary

| Term | Meaning |
|---|---|
| Item | DynamoDB의 데이터 한 건 |
| Attribute | Item을 구성하는 데이터 |
| Partition Key | Item 식별 및 데이터 분산에 사용되는 Key |
| Sort Key | 동일 Partition Key 내 Item을 구별하는 Key |
| RCU | Read Capacity Unit |
| WCU | Write Capacity Unit |

## 📝 Review Questions

### Q1. DynamoDB Item의 최대 크기는?

<details>
<summary>정답 보기</summary>

400 KB

</details>

### Q2. 트래픽이 매우 불규칙하고 갑자기 증가한다면 어떤 Capacity Mode가 적합한가?

<details>
<summary>정답 보기</summary>

On-Demand Capacity Mode

</details>