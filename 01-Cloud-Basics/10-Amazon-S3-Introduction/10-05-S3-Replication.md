# 10-05. S3 Replication

## 1. S3 Replication이란?

S3 Replication은 Source Bucket의 Object를 다른 S3 Bucket으로 **비동기적으로 복제**하는 기능이다.

```text
Source Bucket
      │
      │ Replication
      ▼
Destination Bucket
```

S3 Replication에는 두 가지 형태가 있다.

```text
S3 Replication
│
├── CRR
│   └── Cross-Region Replication
│
└── SRR
    └── Same-Region Replication
```

둘은 완전히 별개의 복제 기술이라기보다, **Source와 Destination의 Region 관계에 따라 구분되는 S3 Replication 방식**이다.

---

## 2. CRR - Cross-Region Replication

CRR은 Source와 Destination Bucket이 **서로 다른 AWS Region**에 존재하는 경우이다.

```text
Source Bucket
ap-northeast-2
      │
      │ CRR
      ▼
Destination Bucket
ap-northeast-1
```

대표적인 사용 사례:

- Compliance 요구사항
- Geographic Separation
- 다른 Region에 데이터 사본 보관
- Cross-Account Replication
- 다른 Region의 사용자와 가까운 위치에 데이터 배치

---

## 3. SRR - Same-Region Replication

SRR은 Source와 Destination Bucket이 **같은 AWS Region**에 존재하는 경우이다.

```text
Source Bucket A
ap-northeast-2
      │
      │ SRR
      ▼
Destination Bucket B
ap-northeast-2
```

대표적인 사용 사례:

- Log Aggregation
- Production / Test 데이터 분리
- 동일 Region에서 데이터 사본 분리
- 서로 다른 AWS Account 사이의 데이터 복제

---

## 4. Replication의 요구사항

S3 Replication에서 중요한 조건은 **Source와 Destination Bucket 모두 Versioning이 활성화되어 있어야 한다는 것**이다.

```text
Source Bucket
Versioning ON
      │
      │ Replication
      ▼
Destination Bucket
Versioning ON
```

Replication은 Object의 Version을 기반으로 동작하기 때문이다.

### 핵심

```text
Source Versioning
→ Required

Destination Versioning
→ Required
```

---

## 5. Replication과 IAM Role

Replication을 설정했다고 해서 S3가 아무 Permission 없이 다른 Bucket에 접근할 수 있는 것은 아니다.

S3가 다음 작업을 수행할 Permission이 필요하다.

```text
Source Bucket
→ Object 읽기

Destination Bucket
→ Object 쓰기
```

이를 위해 S3 Replication에서 사용할 **IAM Role**을 구성할 수 있다.

```text
Source S3
    │
    │ Read
    ▼
IAM Role
    │
    │ Write
    ▼
Destination S3
```

즉 IAM Role은 사람이나 EC2에만 사용하는 개념이 아니다.

AWS Service 역시 필요한 Permission을 가진 Role을 사용하여 다른 AWS Resource에 접근할 수 있다.

---

## 6. Replication은 비동기 방식

S3 Replication은 **Asynchronous Replication**이다.

즉 Source에 Object를 Upload했다고 해서 Destination에 정확히 같은 순간 Object가 생성되는 것은 아니다.

```text
Object Upload
     ↓
Source Bucket
     ↓
Replication 처리
     ↓
Destination Bucket
```

따라서 Source와 Destination 사이에는 짧은 시간 차이가 발생할 수 있다.

---

## 7. Live Replication

Replication Rule을 설정하면 이후 Rule의 대상이 되는 새로운 Object Version이 자동으로 복제될 수 있다.

예를 들어:

```text
beach.jpg
coffee.jpg

──────── Replication Rule 생성 ────────

cat.jpg
```

일반적인 Live Replication에서는 Rule 생성 이후 만들어진 `cat.jpg`가 Replication 대상이 된다.

기존 `beach.jpg`, `coffee.jpg`가 자동으로 과거까지 거슬러 올라가 모두 복제되는 것은 아니다.

```text
Rule 이전 Object
→ 자동 Backfill X

Rule 이후 Object Version
→ Live Replication 대상
```

---

## 8. 기존 Object를 다시 Upload하면?

Replication Rule을 만들기 전에 `beach.jpg`가 있었다고 하자.

```text
Source

beach.jpg
└── v1
```

이후 Replication을 설정한다.

기존 `v1`은 자동으로 Live Replication되지 않는다.

하지만 Versioning이 활성화된 상태에서 같은 Key의 Object를 다시 Upload하면 새로운 Version이 생성된다.

```text
Source

beach.jpg
├── v2 ← Rule 생성 이후
└── v1 ← Rule 생성 이전
```

`v2`는 Replication Rule의 대상이 될 수 있다.

결과적으로 Destination에는:

```text
Destination

beach.jpg
└── v2
```

만 존재할 수도 있다.

---

## 9. Source와 Destination의 Version 수

Replication을 사용한다고 해서 Source와 Destination이 항상 완전히 동일한 Version History를 가지는 것은 아니다.

예:

```text
Source

beach.jpg
├── v3
├── v2
└── v1


Destination

beach.jpg
├── v3
└── v2
```

이런 상태도 가능하다.

즉 Replication은:

```text
"Bucket 전체를 완벽하게 복사하여
언제나 똑같은 상태로 유지"
```

라고 이해하면 안 된다.

보다 정확하게는:

```text
Replication Rule의 대상이 되는
Object Version을 Destination으로 복제
```

한다고 이해하는 것이 좋다.

---

## 10. S3 Batch Replication

Replication 설정 전에 존재했던 Object 등 기존 데이터를 복제해야 할 수 있다.

이때 사용할 수 있는 것이 **S3 Batch Replication**이다.

```text
Live Replication
→ 새로운 Object Version을 지속적으로 복제

Batch Replication
→ 기존 Object 등을 일괄적으로 복제
```

예:

```text
Source Bucket

old-1.jpg
old-2.jpg
old-3.jpg

↓ S3 Batch Replication

Destination Bucket

old-1.jpg
old-2.jpg
old-3.jpg
```

기존 Object나 이전에 Replication에 실패한 Object 등을 복제할 때 사용할 수 있다.

### 기억하기

```text
앞으로 들어올 것
→ Live Replication

이미 들어있던 것
→ Batch Replication
```

---

## 11. Delete Marker Replication

Versioning된 Bucket에서 Object를 일반 Delete하면 **Delete Marker**가 생성된다.

```text
Source

coffee.jpg
├── Delete Marker ← Current
├── v2
└── v1
```

Replication 설정에서 **Delete Marker Replication**을 활성화하면 이 Delete Marker를 Destination으로 복제할 수 있다.

```text
Source
Delete Marker
      │
      │ Replication
      ▼
Destination
Delete Marker
```

결과적으로 Destination에서도 Object가 삭제된 것처럼 보이게 할 수 있다.

---

## 12. 특정 Version의 영구 삭제

여기서 Delete Marker와 **특정 Version의 영구 삭제**를 구분해야 한다.

Source에서:

```text
DELETE coffee.jpg
VersionId=v2
```

처럼 특정 Version을 영구 삭제했다고 하자.

이 삭제 작업이 Destination에 그대로 전파되어 Destination의 동일 Version까지 자동 삭제되는 것은 아니다.

```text
Source

v2 → DELETE


Destination

v2 → 유지
```

따라서:

```text
Delete Marker
→ 설정에 따라 Replication 가능

특정 Version Permanent Delete
→ Destination에 자동 전파 X
```

라고 구분한다.

---

## 13. Replication Chaining

Replication에서 특히 헷갈리기 쉬운 부분이다.

다음과 같은 Replication Rule이 있다고 하자.

```text
A → B
B → C
```

그러면 자연스럽게:

```text
A → B → C
```

가 될 것처럼 보인다.

하지만 **Live Replication은 이런 방식으로 자동 Relay되지 않는다.**

A에서 직접 Upload한 Object가 B로 Replication되면:

```text
A
│
│ Replication
▼
B
```

B에 생성된 Object는 A에서 넘어온 **Replica**이다.

이 Replica가 다시 B → C Rule을 타고 자동으로 C까지 복제되는 것은 아니다.

```text
A
│
▼
B
│
X
▼
C
```

### 기억하기

```text
S3 Replication은
릴레이 경주가 아니다.
```

---

## 14. B에서 직접 생성한 Object는?

그렇다고 B → C Replication Rule 자체가 아무 의미가 없는 것은 아니다.

B에 **직접 새로운 Object를 Upload**하면 해당 Object는 B → C Replication Rule의 대상이 될 수 있다.

```text
A에서 생성

A → B → X → C


B에서 직접 생성

B → C
```

즉 문제는 B → C Replication 자체가 아니라:

> 다른 Replication을 통해 B에 들어온 Replica가 다시 자동 Replication되지 않는 것

이다.

---

## 15. A의 Object를 B와 C 모두에 복제하려면?

A의 데이터를 B와 C 모두에 복제해야 한다면 단순한 Relay 구조로 생각하면 안 된다.

```text
A → B → C
```

보다는 Source A에서 필요한 Destination으로 Replication을 구성하는 형태로 생각한다.

```text
      ┌──→ B
A ────┤
      └──→ C
```

즉:

```text
A의 Object
→ B에도 필요
→ C에도 필요

A에서 각각의 Destination으로 Replication
```

이라는 구조이다.

---

## 16. Cross-Account Replication

S3 Replication은 같은 AWS Account 내부에서만 사용할 수 있는 것은 아니다.

서로 다른 AWS Account의 Bucket 사이에서도 Replication을 구성할 수 있다.

```text
AWS Account A

Source Bucket
      │
      │ Replication
      ▼
AWS Account B

Destination Bucket
```

이 경우 Source와 Destination에 필요한 IAM 및 Bucket Permission을 적절하게 구성해야 한다.

---

## 17. Replication 전체 흐름

전체 구조를 한 번에 보면 다음과 같다.

```text
Source Bucket
Versioning ON
      │
      │
      │ IAM Permission
      │
      │ Asynchronous
      │ Live Replication
      ▼
Destination Bucket
Versioning ON
```

기존 Object가 있다면:

```text
Existing Objects
      │
      ▼
S3 Batch Replication
      │
      ▼
Destination
```

삭제의 경우:

```text
Normal Delete
→ Delete Marker
→ 설정에 따라 Replication 가능

Specific Version Delete
→ Permanent Delete
→ Destination에 자동 전파 X
```

그리고:

```text
A → B → C

Replica의 자동 Chaining
→ X
```

이다.

---

# 핵심 정리

```text
S3 Replication
= Object Version을 다른 Bucket으로 비동기 복제


CRR
= Cross-Region Replication

Source Region
≠
Destination Region


SRR
= Same-Region Replication

Source Region
=
Destination Region


필수 조건

Source Versioning ON
Destination Versioning ON


IAM Role
→ S3가 Source를 읽고
  Destination에 쓸 Permission


Live Replication
→ Rule 이후의 새로운 Object Version


Batch Replication
→ 기존 Object 등의 일괄 복제


Source와 Destination
→ Version History가 항상 동일한 것은 아님


Delete Marker
→ 설정에 따라 Replication 가능


Specific Version Permanent Delete
→ Destination으로 자동 전파 X


A → B → C

A의 Replica가
B에서 C로 자동 Relay
→ X
```

---

# 🇯🇵 日本語まとめ

S3 Replication は、Source Bucket の Object Version を Destination Bucket に非同期で複製する機能です。

異なる Region 間の Replication を **CRR (Cross-Region Replication)**、同じ Region 内の Replication を **SRR (Same-Region Replication)** と呼びます。

Source と Destination の両方で Versioning を有効にする必要があります。

```text
CRR
→ 異なる Region

SRR
→ 同じ Region
```

通常の Live Replication は Replication Rule の対象となる新しい Object Version を複製します。

既存 Object などを複製する場合は **S3 Batch Replication** を利用できます。

Delete Marker は設定によって Replication できますが、特定 Version の Permanent Delete は Destination に自動的に伝播しません。

また、Replica が次の Replication Rule を自動的に利用する Replication Chaining は行われません。

```text
A → B → C

A から B に複製された Replica
→ C に自動 Replication されない
```

---

# 🇺🇸 English Summary

S3 Replication asynchronously copies eligible object versions from a source bucket to a destination bucket.

**Cross-Region Replication (CRR)** replicates objects between different AWS Regions, while **Same-Region Replication (SRR)** replicates objects between buckets in the same Region.

Versioning must be enabled on both the source and destination buckets.

Live Replication handles eligible new object versions after the replication configuration is established. Existing objects can be handled using **S3 Batch Replication**.

Delete markers can be replicated when configured, while permanent deletion of a specific object version is not automatically propagated to the destination.

Live replication does not automatically chain replicas.

```text
A → B → C

A's replica in B
does not automatically replicate to C.
```

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Replication | レプリケーション | 복제 |
| Source Bucket | ソースバケット | 원본 버킷 |
| Destination Bucket | 宛先バケット | 대상 버킷 |
| Cross-Region Replication | クロスリージョンレプリケーション | 교차 리전 복제 |
| Same-Region Replication | 同一リージョンレプリケーション | 동일 리전 복제 |
| Live Replication | ライブレプリケーション | 실시간 복제 |
| Batch Replication | バッチレプリケーション | 배치 복제 |
| Replica | レプリカ | 복제본 |
| Delete Marker Replication | 削除マーカーレプリケーション | 삭제 마커 복제 |
| Replication Rule | レプリケーションルール | 복제 규칙 |
| Asynchronous | 非同期 | 비동기 |
| Cross-Account | クロスアカウント | 교차 계정 |

---

# Review Questions

<details>
<summary>1. CRR과 SRR의 차이는?</summary>

CRR은 서로 다른 AWS Region 사이에서 수행하는 Replication이다.

SRR은 같은 AWS Region의 Bucket 사이에서 수행하는 Replication이다.

</details>

<details>
<summary>2. S3 Replication을 사용하기 위한 중요한 조건은?</summary>

Source와 Destination Bucket 모두 Versioning이 활성화되어 있어야 한다.

또한 S3가 Source Object를 읽고 Destination에 쓸 수 있는 적절한 Permission이 필요하다.

</details>

<details>
<summary>3. Replication Rule을 생성하기 전에 존재하던 Object도 Live Replication으로 자동 복제되는가?</summary>

일반적으로 아니다.

기존 Object 등을 복제하려면 S3 Batch Replication을 사용할 수 있다.

</details>

<details>
<summary>4. Source에 Version이 3개라면 Destination에도 반드시 3개가 존재하는가?</summary>

아니다.

Replication Rule이 적용된 시점과 대상 Version에 따라 Source와 Destination의 Version History가 다를 수 있다.

</details>

<details>
<summary>5. S3 Batch Replication은 언제 사용하는가?</summary>

Replication 설정 이전부터 존재하던 Object나 기존 데이터 등을 일괄적으로 복제할 때 사용할 수 있다.

</details>

<details>
<summary>6. Delete Marker도 Replication할 수 있는가?</summary>

Replication 설정에서 Delete Marker Replication을 활성화하면 가능하다.

</details>

<details>
<summary>7. Source에서 특정 Version을 영구 삭제하면 Destination의 동일 Version도 자동 삭제되는가?</summary>

아니다.

특정 Version의 Permanent Delete는 Destination으로 자동 전파되지 않는다.

</details>

<details>
<summary>8. A → B와 B → C Replication Rule이 있으면 A의 Object가 자동으로 C까지 복제되는가?</summary>

아니다.

A에서 B로 Replication된 Replica가 다시 B → C Live Replication을 통해 자동으로 전달되지는 않는다.

</details>

<details>
<summary>9. B에서 직접 새로운 Object를 생성하면 B → C Replication이 가능한가?</summary>

가능하다.

Replication을 통해 B에 들어온 Replica의 자동 Chaining이 되지 않는 것이지, B에서 직접 생성된 적격 Object까지 Replication할 수 없는 것은 아니다.

</details>
