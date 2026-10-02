# 14-01. AWS Snow Family

## 🇰🇷 1. AWS Snow Family란?

AWS Snow Family는 대용량 데이터를 AWS 안팎으로 이동하거나, 네트워크 연결이 제한된 Edge 환경에서 데이터를 저장하고 처리하기 위한 물리적 장치입니다.

핵심 목적은 두 가지입니다.

```text
AWS Snow Family
│
├─ Data Migration
│  └─ 물리 장비를 이용한 Offline Data Transfer
│
└─ Edge Computing
   └─ 현장에서 Local Compute / Storage 수행
```

즉:

> **대용량 데이터를 네트워크 대신 물리적으로 이동하거나, AWS Region과 연결하기 어려운 현장에서 직접 데이터를 처리한다.**

---

## 2. 왜 Snow Family가 필요한가?

대용량 데이터를 인터넷으로 AWS에 전송하면 상당한 시간이 필요할 수 있습니다.

```text
On-Premises
     │
     │ Internet
     ↓
    AWS
```

특히 다음과 같은 상황에서 문제가 발생합니다.

```text
Large Data
+
Limited Bandwidth
+
Unstable Network
+
High Network Cost
```

이 경우 Snow Family를 사용할 수 있습니다.

```text
On-Premises
     ↓
Snow Device 📦
     ↓
데이터 저장
     ↓
AWS로 장비 배송
     ↓
Amazon S3
```

시험에서는 대용량 데이터의 네트워크 전송에 일주일 이상 걸리는 상황 등이 Snow Family를 고려하는 대표적인 시나리오입니다.

---

## 3. Snowcone vs Snowball Edge

### Snowcone

Snow Family의 소형 장치입니다.

```text
Snowcone
→ Small
→ Portable
→ 비교적 적은 데이터
→ Data Migration
→ Edge Computing 가능
```

작은 크기와 휴대성이 필요한 환경에 적합합니다.

---

### Snowball Edge

Snowcone보다 훨씬 큰 Storage와 Compute 자원을 제공하는 장치입니다.

```text
Snowball Edge
│
├─ Storage Optimized
│  └─ Storage 중심
│
└─ Compute Optimized
   └─ Compute 중심
```

따라서 대규모 Data Migration뿐만 아니라 Edge Computing에도 사용할 수 있습니다.

---

## 4. Data Migration

Snow Family의 대표적인 사용 사례입니다.

일반적인 Network Transfer:

```text
On-Premises
     │
     │ Internet
     ↓
Amazon S3
```

Snow Family:

```text
On-Premises
     ↓
Snowcone / Snowball Edge
     ↓
데이터 복사
     ↓
AWS로 장비 반송
     ↓
Amazon S3
```

기본적인 과정은 다음과 같습니다.

```text
Snow Device 주문
      ↓
장비 수령
      ↓
데이터 복사
      ↓
장비 반송
      ↓
AWS가 S3로 Import
      ↓
장비 데이터 삭제
```

장비 관리는 Snowball Client 또는 AWS OpsHub 등을 사용할 수 있습니다.

---

## 5. Edge Computing

Edge Computing은 데이터를 AWS Region까지 보내기 전에 **데이터가 생성되는 현장에서 직접 처리하는 것**입니다.

예:

```text
🚢 Ship
⛏️ Mining Site
🚚 Vehicle
🏭 Remote Factory
```

이러한 환경은 인터넷 연결이 느리거나 불안정하거나 아예 없을 수 있습니다.

일반적인 Cloud Computing:

```text
Data
 ↓
Internet
 ↓
AWS Region
 ↓
Compute
```

Edge Computing:

```text
Data
 ↓
Snow Device
 ↓
Local Compute
 ↓
Local Processing
```

핵심은:

> **데이터를 Compute가 있는 곳으로 보내는 대신 Compute를 데이터가 생성되는 곳 가까이 가져간다.**

---

## 6. Snowball Edge는 작은 AWS Region이 아니다

Snowball Edge는 AWS의 모든 서비스를 담은 장비가 아닙니다.

```text
Snowball Edge ≠ Mini AWS Region
```

보다 정확하게는:

```text
Snowball Edge
│
├─ CPU / Memory
├─ Local Storage
├─ Device Management
└─ Supported Local Compute
```

즉 AWS가 제공하는 물리적인 Edge Server / Storage 장비에 가깝습니다.

필요한 AMI와 Workload를 준비하여 현장에서 Compute 작업을 수행할 수 있습니다.

---

## 7. Edge Computing + Data Migration

두 기능은 함께 사용할 수도 있습니다.

예:

```text
Remote Site
    │
    │ Data 생성
    ↓
Snowball Edge
    │
    ├─ Local Processing
    │
    ├─ Local Storage
    │
    └─ 장비 반송
           ↓
       Amazon S3
```

즉 현장에서 데이터를 먼저 처리하고 저장한 뒤, 장비를 AWS로 보내 대량 데이터를 Migration할 수도 있습니다.

---

## 8. Snowball과 S3 Glacier

Snowball을 통해 데이터를 S3 Glacier로 직접 Import하는 것은 아닙니다.

```text
Snowball
   ↓
S3 Glacier

❌
```

먼저 Amazon S3로 데이터를 가져온 뒤 Lifecycle Rule을 이용합니다.

```text
Snowball
   ↓
Amazon S3
   ↓
Lifecycle Rule
   ↓
S3 Glacier
```

중요한 점은 Glacier가 반드시 Lifecycle을 통해서만 사용되는 것이 아니라는 것입니다.

이 시나리오의 핵심은:

```text
Snowball → Glacier 직접 Import ❌

Snowball → S3 → Lifecycle → Glacier ⭕
```

입니다.

---

## 9. Exam Scenario

다음과 같은 키워드가 나오면 Snow Family를 생각합니다.

```text
Huge Amount of Data
+
Limited / No Internet
+
Network Transfer takes too long
+
Offline Data Migration
```

또는:

```text
Remote Location
+
Limited / No Internet
+
Local Data Processing
+
Local Compute

→ Edge Computing
```

---

## 🔑 핵심 정리

```text
AWS Snow Family
= Physical Edge Devices

주요 목적
├─ Data Migration
└─ Edge Computing

Snowcone
= Small / Portable

Snowball Edge
├─ Storage Optimized
└─ Compute Optimized

Data Migration
= 물리 장비로 대용량 데이터 이동

Edge Computing
= 현장에서 Local Compute / Storage

Snowball → Glacier 직접 ❌
Snowball → S3 → Lifecycle → Glacier ⭕
```

한 줄 정리:

> **네트워크로 보내기 어려운 대용량 데이터를 물리적으로 이동하거나, 네트워크가 제한된 현장에서 직접 처리한다 = AWS Snow Family**

---

## 🇯🇵 日本語 Summary

AWS Snow Familyは、大容量データのオフライン移行とエッジコンピューティングに使用される物理デバイスです。

主なデバイスにはSnowconeとSnowball Edgeがあります。

Snowconeは小型でポータブルなデバイスであり、Snowball Edgeはより大きなストレージとコンピューティング能力を提供します。

ネットワーク接続が遅い、または利用できない環境では、Snowball Edgeを使用して現場でデータを保存・処理することもできます。

SnowballからS3 Glacierへ直接データをインポートするのではなく、まずAmazon S3へインポートし、Lifecycle Ruleを使用してGlacierへ移行します。

---

## 🇺🇸 English Summary

AWS Snow Family provides physical devices for offline data migration and edge computing.

Snowcone is a small and portable device, while Snowball Edge provides larger storage and compute capabilities.

Snow Family is useful when transferring large datasets over the network would take too long or when network connectivity is limited or unavailable.

Snowball Edge can also perform local processing at edge locations.

Data imported using Snowball is first loaded into Amazon S3. A Lifecycle Rule can then transition the objects to an S3 Glacier storage class.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Data Migration | データ移行 | 데이터 마이그레이션 |
| Edge Computing | エッジコンピューティング | 엣지 컴퓨팅 |
| Offline Transfer | オフライン転送 | 오프라인 전송 |
| Snowcone | Snowcone | 스노우콘 |
| Snowball Edge | Snowball Edge | 스노우볼 엣지 |
| Storage Optimized | ストレージ最適化 | 스토리지 최적화 |
| Compute Optimized | コンピューティング最適化 | 컴퓨팅 최적화 |
| Workload | ワークロード | 워크로드 |
| Lifecycle Rule | ライフサイクルルール | 수명 주기 규칙 |
| Local Processing | ローカル処理 | 로컬 처리 |

---

## 📝 Review Questions

<details>
<summary>Q1. AWS Snow Family의 두 가지 주요 사용 목적은?</summary>

Data Migration과 Edge Computing입니다.

</details>

<details>
<summary>Q2. 인터넷 연결이 매우 느리거나 없는 현장에서 데이터를 처리해야 한다면?</summary>

Snowball Edge 또는 Snowcone의 Edge Computing 기능을 사용할 수 있습니다.

</details>

<details>
<summary>Q3. Snowball Edge는 작은 AWS Region인가?</summary>

아닙니다. Compute와 Storage 및 지원되는 Local 기능을 제공하는 물리적인 Edge 장비입니다.

</details>

<details>
<summary>Q4. Snowball에서 S3 Glacier로 데이터를 직접 Import할 수 있는가?</summary>

아닙니다. 먼저 Amazon S3로 Import한 후 Lifecycle Rule을 이용해 Glacier Storage Class로 Transition할 수 있습니다.

</details>

<details>
<summary>Q5. Snowball Edge Storage Optimized와 Compute Optimized의 기본적인 차이는?</summary>

Storage Optimized는 Storage 중심이며, Compute Optimized는 현장에서 더 많은 연산이 필요한 Edge Computing에 적합합니다.

</details>
