# 11-06. S3 Storage Lens

## 🇰🇷 1. S3 Storage Lens란?

S3 Storage Lens는 여러 AWS Account, Region, Bucket에 분산되어 있는 S3 Storage 환경을 중앙에서 분석하고 시각화하는 기능입니다.

핵심 목적은 다음과 같습니다.

```text
Understand
   ↓
Analyze
   ↓
Optimize
```

즉:

> **조직 전체의 S3 사용 현황을 한눈에 보는 분석 Dashboard**

라고 이해할 수 있습니다.

---

## 2. Organization 전체 분석

AWS 환경이 커지면 S3 Bucket도 여러 Account와 Region에 분산될 수 있습니다.

```text
AWS Organization
│
├─ Account A
│   ├─ Region A
│   │   ├─ Bucket 1
│   │   └─ Bucket 2
│   │
│   └─ Region B
│       └─ Bucket 3
│
└─ Account B
    └─ Region C
        ├─ Bucket 4
        └─ Bucket 5
```

Storage Lens는 이러한 데이터를 집계하여 중앙에서 분석할 수 있도록 합니다.

```text
Organization
     ↓
Accounts
     ↓
Regions
     ↓
Buckets
     ↓
Prefixes
     ↓
S3 Storage Lens
     ↓
Dashboard
```

---

## 3. 무엇을 분석하는가?

Storage Lens를 통해 크게 다음과 같은 관점에서 S3를 분석할 수 있습니다.

```text
S3 Storage Lens
│
├─ Usage
├─ Cost Efficiency
├─ Data Protection
├─ Access Management
├─ Performance
└─ Activity
```

모든 Metric 이름을 외우는 것보다 **Storage Lens가 S3 환경을 여러 관점에서 분석한다**는 개념이 중요합니다.

---

## 4. Usage Metrics

S3 Storage 사용 현황을 파악할 수 있습니다.

예:

```text
Total Storage
Object Count
Average Object Size
Bucket Count
```

이를 통해 다음과 같은 질문에 답할 수 있습니다.

```text
"어느 Bucket이 가장 빠르게 커지고 있는가?"

"사용량이 거의 변하지 않는 Bucket은 무엇인가?"

"Object가 가장 많은 Bucket은 어디인가?"
```

---

## 5. Cost Optimization

Storage 비용을 줄일 수 있는 부분을 찾는 데 도움을 줍니다.

예:

```text
Noncurrent Versions
Incomplete Multipart Uploads
Storage Class 사용 현황
```

예를 들어 Versioning으로 인해 오래된 Noncurrent Version이 지나치게 많이 저장되어 있거나 미완료 Multipart Upload가 많은 Storage를 사용하고 있다는 사실을 발견할 수 있습니다.

```text
Storage Lens
     ↓
"오래된 Version이 너무 많은데?"
     ↓
Lifecycle Rule 검토
```

Storage Lens는 문제를 **분석하고 발견**하는 역할이며 Lifecycle이 실제 자동 관리 작업을 수행합니다.

---

## 6. Data Protection

Storage Lens는 S3의 Data Protection 관련 설정 상태도 분석할 수 있습니다.

예:

```text
Versioning
Encryption
Replication
MFA Delete 관련 상태
```

이를 통해 조직에서 Data Protection Best Practice를 따르지 않는 Bucket을 찾는 데 도움을 받을 수 있습니다.

---

## 7. Default Dashboard

S3 Storage Lens에는 기본 Dashboard가 제공됩니다.

Default Dashboard는 Amazon S3가 미리 구성합니다.

특징:

```text
Multi-Account Data
+
Multi-Region Data
+
Summarized Insights
+
Trends
```

따라서 여러 Account와 Region에 분산된 S3 데이터를 중앙에서 확인할 수 있습니다.

Default Dashboard는 삭제할 수 없지만 비활성화할 수 있습니다.

필요한 경우 Custom Dashboard를 구성할 수도 있습니다.

---

## 8. Filtering / Aggregation

Storage Lens 데이터는 다양한 범위로 집계하거나 분석할 수 있습니다.

```text
Organization
Account
Region
Bucket
Prefix
```

즉 조직 전체를 볼 수도 있고 특정 Bucket이나 Prefix 수준으로 내려가 분석할 수도 있습니다.

---

## 9. Metrics Export

Storage Lens의 Metric과 Report는 S3 Bucket으로 Export하도록 구성할 수 있습니다.

대표적인 형식:

```text
CSV
Parquet
```

이를 통해 Storage Lens의 분석 데이터를 추가적인 데이터 분석 Workflow에서 활용할 수 있습니다.

---

## 10. Free Metrics와 Advanced Metrics

Storage Lens에는 기본적으로 제공되는 Metric과 추가적인 Advanced Metric 및 Recommendation 기능이 존재합니다.

Advanced 기능에서는 보다 상세한 Activity, Cost Optimization, Data Protection 및 Status Code 관련 Insight를 활용할 수 있습니다.

시험에서는 세부 Metric 개수나 보존 기간 숫자를 모두 외우는 것보다 다음 구분을 우선적으로 기억합니다.

```text
Free Metrics
→ 기본적인 S3 Usage Insight

Advanced Metrics / Recommendations
→ 더 상세한 분석과 최적화 Insight
```

---

## 11. Storage Lens는 직접 수정하는 서비스가 아니다

이 구분이 중요합니다.

```text
Storage Lens 👀
= 분석 / 시각화 / Insight

Lifecycle 🔄
= 시간 기반 자동 관리

Batch Operations 🔨
= 기존 Object 대량 작업
```

예를 들어:

```text
Storage Lens
"Bucket A에 Noncurrent Version이 너무 많습니다."
        ↓
관리자가 문제 확인
        ↓
Lifecycle Rule 구성
        ↓
오래된 Version 자동 관리
```

Storage Lens 자체가 Object를 자동으로 Glacier로 이동하거나 삭제하는 기능은 아닙니다.

---

## 12. Exam Scenario

다음과 같은 문제가 나오면 Storage Lens를 생각합니다.

```text
Company
+
Many AWS Accounts
+
Many Regions
+
Many S3 Buckets
+
Centralized Visibility
+
Storage Usage / Cost Optimization / Data Protection
```

정답 후보:

```text
S3 Storage Lens
```

---

## 🔑 핵심 정리

```text
S3 Storage Lens
= S3 전체 현황 분석 Dashboard

범위
= Organization / Account / Region / Bucket / Prefix

목적
= Understand / Analyze / Optimize

주요 Insight
= Usage
+ Cost Efficiency
+ Data Protection

Default Dashboard
= Multi-Account + Multi-Region

Storage Lens
= 분석

Lifecycle / Batch Operations
= 실제 관리 작업
```

한 줄 정리:

> **여러 Account와 Region에 흩어진 S3를 중앙에서 분석하고 최적화 포인트를 찾는다 = S3 Storage Lens**

---

## 🇯🇵 日本語 Summary

S3 Storage Lensは、AWS Organization全体のS3ストレージを中央で分析・可視化する機能です。

Account、Region、Bucket、Prefixなどのデータを集約し、ストレージ使用量、コスト効率、データ保護などのInsightを提供します。

Default DashboardではMulti-Account、Multi-Regionのデータを確認できます。

Storage Lensは分析・可視化を行う機能であり、オブジェクトを直接移行・削除する機能ではありません。

---

## 🇺🇸 English Summary

S3 Storage Lens provides centralized visibility into S3 storage usage across an AWS Organization.

It can aggregate information across:

- accounts
- Regions
- buckets
- prefixes

Storage Lens provides insights into storage usage, cost efficiency, data protection, and other S3 characteristics.

The default dashboard provides multi-account and multi-Region visibility.

Storage Lens analyzes and visualizes the environment; it does not directly perform lifecycle transitions or bulk object modifications.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Storage Lens | ストレージレンズ | 스토리지 렌즈 |
| Dashboard | ダッシュボード | 대시보드 |
| Organization | 組織 | 조직 |
| Aggregate | 集約 | 집계 |
| Insight | インサイト | 인사이트 |
| Usage Metric | 使用量メトリクス | 사용량 지표 |
| Cost Efficiency | コスト効率 | 비용 효율성 |
| Data Protection | データ保護 | 데이터 보호 |
| Activity Metric | アクティビティメトリクス | 활동 지표 |
| Optimization | 最適化 | 최적화 |

---

## 📝 Review Questions

<details>
<summary>Q1. 여러 AWS Account와 Region의 S3 사용 현황을 중앙에서 분석하려면?</summary>

S3 Storage Lens를 사용할 수 있습니다.

</details>

<details>
<summary>Q2. Storage Lens의 대표적인 분석 영역은?</summary>

Storage Usage, Cost Efficiency, Data Protection 등이 있습니다.

</details>

<details>
<summary>Q3. Storage Lens가 오래된 Object를 직접 Glacier로 이동시키는가?</summary>

아닙니다. Storage Lens는 분석과 Insight를 제공하며 실제 시간 기반 Object 관리는 Lifecycle Rule 등이 담당합니다.

</details>

<details>
<summary>Q4. Default Dashboard의 중요한 특징은?</summary>

Multi-Account와 Multi-Region 데이터를 중앙에서 확인할 수 있습니다.

</details>

<details>
<summary>Q5. Storage Lens와 Batch Operations의 가장 큰 차이는?</summary>

Storage Lens는 S3 환경을 분석하고 시각화하며, Batch Operations는 기존 Object에 실제 대량 작업을 수행합니다.

</details>
