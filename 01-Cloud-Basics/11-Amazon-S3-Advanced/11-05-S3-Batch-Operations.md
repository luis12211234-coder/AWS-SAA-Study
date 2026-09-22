# 11-05. S3 Batch Operations

## 🇰🇷 1. S3 Batch Operations란?

S3 Batch Operations는 많은 기존 S3 Object에 동일한 작업을 대량으로 수행하기 위한 기능입니다.

예를 들어 S3에 1,000,000개의 Object가 있다고 가정합니다.

```text
S3 Bucket
├─ Object 1
├─ Object 2
├─ Object 3
├─ ...
└─ Object 1,000,000
```

이 모든 Object의 Tag 또는 Encryption 설정 등을 변경해야 한다면 하나씩 처리하는 것은 비효율적입니다.

```text
Object List
     ↓
S3 Batch Operations
     ↓
Bulk Action
     ↓
많은 Object 처리
```

---

## 2. 대표적인 Operations

S3 Batch Operations를 사용하여 많은 Object에 다음과 같은 작업을 수행할 수 있습니다.

```text
Object Copy
Tag 변경
ACL 변경
Encryption 관련 작업
Glacier Object Restore
Lambda Function Invoke
```

Lambda를 호출하면 기본 Operation만으로 처리하기 어려운 Custom Logic도 수행할 수 있습니다.

```text
Object List
     ↓
Batch Operations
     ↓
Lambda
     ↓
Custom Processing
```

---

## 3. 직접 Script를 만드는 것과의 차이

직접 Script를 작성하면 대량 작업뿐 아니라 운영 로직까지 직접 관리해야 합니다.

```text
for each object:
    작업 수행
    실패 확인
    Retry
    Progress 저장
    Result 기록
```

Object 수가 매우 많아지면 관리가 복잡해집니다.

S3 Batch Operations는 이러한 대량 작업 관리 기능을 제공합니다.

```text
S3 Batch Operations

├─ Large-scale Processing
├─ Retry Management
├─ Progress Tracking
├─ Completion Notification
└─ Completion Report
```

---

## 4. Manifest

Batch Operations는 어떤 Object를 처리해야 하는지 알아야 합니다.

작업 대상 Object 목록을 **Manifest**라고 합니다.

개념적으로:

```text
Manifest

bucket-a, object-001
bucket-a, object-002
bucket-a, object-003
...
```

즉:

```text
Manifest
= "이 Object들을 처리하세요."
```

---

## 5. S3 Inventory

대량의 Object 목록을 파악할 때 S3 Inventory를 활용할 수 있습니다.

```text
S3 Bucket
    ↓
S3 Inventory
    ↓
Object 목록 및 관련 정보
```

Inventory를 분석하여 원하는 Object를 선별하고 Batch Operations의 대상 목록을 구성할 수 있습니다.

```text
S3 Inventory
      ↓
Query / Filter
      ↓
Target Object List
      ↓
S3 Batch Operations
```

---

## 6. 대표적인 Scenario

많은 기존 Object 중 특정 조건의 Object를 찾아 일괄 처리해야 한다고 가정합니다.

```text
S3 Bucket
├─ Object A
├─ Object B
├─ Object C
├─ ...
└─ Millions of Objects
```

흐름:

```text
① S3 Inventory
      ↓
② Object 목록 확인 / 분석
      ↓
③ Target Object List
      ↓
④ S3 Batch Operations
      ↓
⑤ Bulk Action
```

---

## 7. Batch Operations vs Inventory

둘의 역할을 구별해야 합니다.

```text
S3 Inventory
= "어떤 Object들이 있는가?"

S3 Batch Operations
= "그 Object들에게 작업을 수행하라."
```

즉 Inventory는 **목록/정보**, Batch Operations는 **Action**입니다.

---

## 8. Batch Replication과 연결

기존 Object를 대규모로 Replication해야 하는 경우 S3 Batch Replication을 사용할 수 있습니다.

일반적인 Live Replication이 새로운 Object Version을 대상으로 하는 것과 달리 기존 Object 또는 Replication이 필요한 Object를 대량 처리하는 데 Batch 방식이 활용됩니다.

---

## 🔑 핵심 정리

시험에서 다음과 같은 표현이 나오면 S3 Batch Operations를 생각합니다.

```text
Millions / Billions of existing objects
Bulk Operation
Modify Tags
Modify ACL
Encryption
Restore Glacier Objects
Invoke Lambda
Progress Tracking
Retry
Completion Report
```

한 줄 정리:

> **많은 기존 S3 Object를 한꺼번에 처리한다 = S3 Batch Operations**

---

## 🇯🇵 日本語 Summary

S3 Batch Operationsは、大量の既存S3オブジェクトに対して同じ処理を一括実行するための機能です。

主な処理：

- Object Copy
- Tag変更
- ACL変更
- Encryption関連処理
- Glacier Object Restore
- Lambda Function Invoke

S3 Inventoryを利用してオブジェクト一覧を取得し、対象を選択してBatch Operationsで一括処理できます。

Retry、進捗管理、完了レポートなども管理できます。

---

## 🇺🇸 English Summary

S3 Batch Operations performs large-scale operations on existing S3 objects.

Supported use cases include copying objects, modifying tags and ACLs, encryption-related operations, restoring archived objects, and invoking Lambda functions.

S3 Inventory can be used to identify objects, while Batch Operations performs actions on those objects.

Batch Operations also provides operational features such as retries, progress tracking, and completion reports.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Batch Operations | バッチオペレーション | 배치 작업 |
| Bulk Operation | 一括処理 | 대량 작업 |
| Manifest | マニフェスト | 작업 대상 목록 |
| Inventory | インベントリ | 인벤토리 |
| Retry | 再試行 | 재시도 |
| Progress Tracking | 進捗追跡 | 진행 상황 추적 |
| Completion Report | 完了レポート | 완료 보고서 |
| Restore | 復元 | 복원 |
| Custom Processing | カスタム処理 | 사용자 정의 처리 |

---

## 📝 Review Questions

<details>
<summary>Q1. 수백만 개의 기존 S3 Object에 동일한 작업을 수행해야 한다면?</summary>

S3 Batch Operations를 사용할 수 있습니다.

</details>

<details>
<summary>Q2. Batch Operations에서 Manifest의 역할은?</summary>

작업을 수행할 대상 Object 목록을 정의합니다.

</details>

<details>
<summary>Q3. S3 Inventory와 Batch Operations의 차이는?</summary>

Inventory는 Object 목록과 정보를 파악하는 데 사용하고, Batch Operations는 해당 Object들에 실제 대량 작업을 수행합니다.

</details>

<details>
<summary>Q4. 기본 Batch Operation으로 해결할 수 없는 Custom 작업은 어떻게 수행할 수 있는가?</summary>

Batch Operations에서 Lambda Function을 호출하여 Custom Logic을 실행할 수 있습니다.

</details>
