# 11-01. Amazon S3 Lifecycle Rules

## 🇰🇷 1. S3 Lifecycle이란?

S3 Lifecycle은 Object가 시간이 지나면서 어떻게 관리될지를 자동화하는 기능입니다.

예를 들어 다음과 같은 정책을 만들 수 있습니다.

```text
Object Upload
    ↓
S3 Standard
    ↓ 30 days
S3 Standard-IA
    ↓ 90 days
Glacier
    ↓ 365 days
Delete
```

즉 사람이 직접 오래된 Object를 찾아 이동하거나 삭제할 필요 없이 S3가 Lifecycle Rule에 따라 자동으로 처리합니다.

---

## 2. Lifecycle Actions

Lifecycle Rule에서 중요한 작업은 크게 두 종류입니다.

### Transition Actions

Object를 다른 Storage Class로 이동합니다.

```text
Standard
   ↓
Standard-IA
   ↓
Glacier Flexible Retrieval
   ↓
Glacier Deep Archive
```

주로 오래된 데이터를 더 저렴한 Storage Class로 이동하여 비용을 줄이는 데 사용합니다.

### Expiration Actions

일정 시간이 지난 Object를 만료시킵니다.

```text
Object
 ↓ 365 days
Expiration
```

Versioning이 없는 일반적인 상황에서는 Object를 더 이상 유지하지 않도록 관리하는 데 사용됩니다.

---

## 3. Lifecycle Rule 적용 대상

Lifecycle Rule은 Bucket 전체 또는 특정 Object 집합에 적용할 수 있습니다.

대표적으로 다음과 같은 조건을 사용할 수 있습니다.

```text
Entire Bucket

Prefix
logs/

Tag
department=finance

Object Size
특정 크기 범위
```

예를 들어:

```text
logs/ Prefix의 Object만
90일 후 Glacier로 이동
```

과 같은 정책을 만들 수 있습니다.

---

## 4. Versioning + Lifecycle

Versioning이 활성화된 Bucket에서는 같은 Key에 여러 Version이 존재할 수 있습니다.

```text
report.pdf

v3 ← Current Version
v2 ← Noncurrent Version
v1 ← Noncurrent Version
```

Versioning은 복구에는 매우 유용하지만, 이전 Version도 실제 데이터를 저장하므로 Storage Cost가 발생합니다.

따라서 Lifecycle과 함께 사용하면 효과적입니다.

```text
Current Version
      │
새 Version Upload
      ↓
Noncurrent Version
      ↓ 30 days
Standard-IA
      ↓
Glacier
      ↓ 365 days
Permanent Delete
```

---

## 5. Noncurrent Version Lifecycle

이전 Version을 위한 별도의 Lifecycle Action을 설정할 수 있습니다.

대표적인 설정은 다음과 같습니다.

```text
NoncurrentVersionTransition
NoncurrentVersionExpiration
```

중요한 점은 `NoncurrentDays`의 기준입니다.

```text
Day 0
v1 생성

Day 100
v2 생성
→ v1이 Noncurrent가 됨

Day 130
→ v1이 Noncurrent 상태가 된 지 30일
```

따라서 `NoncurrentDays = 30`이라면 v1의 최초 생성일로부터 30일이 아니라 **v1이 Noncurrent가 된 시점부터 30일**을 계산합니다.

---

## 6. Delete Marker와 Lifecycle

Versioning이 활성화된 Bucket에서 일반적인 DELETE 요청을 수행하면 이전 Version이 즉시 삭제되는 것이 아니라 Delete Marker가 Current Version이 됩니다.

```text
Before DELETE

v3 ← Current
v2
v1

After DELETE

Delete Marker ← Current
v3
v2
v1
```

Lifecycle에서는 오래된 Version뿐 아니라 불필요해진 Delete Marker도 관리할 수 있습니다.

---

## 7. Incomplete Multipart Upload 정리

Multipart Upload가 시작되었지만 완료되지 않으면 업로드된 Part가 S3에 남아 Storage Cost를 발생시킬 수 있습니다.

```text
Multipart Upload

Part 1 ✅
Part 2 ✅
Part 3 ❌

Upload 미완료
→ Part 1, Part 2가 불필요하게 남을 수 있음
```

Lifecycle Rule을 사용하면 일정 시간이 지난 Incomplete Multipart Upload를 자동으로 중단하고 정리할 수 있습니다.

---

## 8. Storage Class Transition

Lifecycle Transition은 일반적으로 시간이 지나면서 더 낮은 비용의 Storage Class로 데이터를 이동시키는 데 사용합니다.

```text
Hot Data
   ↓
Warm Data
   ↓
Cold / Archive Data
```

예:

```text
Standard
   ↓
Standard-IA
   ↓
Glacier Flexible Retrieval
   ↓
Glacier Deep Archive
```

모든 Storage Class 사이를 자유롭게 양방향 이동하는 기능은 아닙니다.

특히 Archive Storage Class의 Object를 다시 일반 Storage Class에서 사용하려는 경우 Lifecycle을 역방향으로 설정하는 것이 아니라 Restore 및 Copy와 같은 별도의 절차가 필요할 수 있습니다.

---

## 9. Small Object와 Lifecycle Transition

작은 Object는 Transition Request 비용 때문에 Storage Class를 변경했을 때 오히려 비용 효율이 나빠질 수 있습니다.

현재 Lifecycle 설정에서는 작은 Object의 Transition에 기본적인 제한 동작이 존재하며, 필요한 경우 Object Size Filter 등을 이용하여 대상 범위를 명시적으로 구성할 수 있습니다.

핵심은 다음과 같습니다.

```text
아주 작은 Object
      ↓
무조건 Archive로 이동
      ↓
항상 비용 절감되는 것은 아님
```

Object 수, 크기, Transition Request Cost를 함께 고려해야 합니다.

---

## 10. S3 Storage Class Analysis

S3 Storage Class Analysis는 Object의 Access Pattern을 분석하여 Storage Class 최적화를 판단하는 데 도움을 줍니다.

```text
Object Access Pattern
       ↓
Storage Class Analysis
       ↓
"이 데이터를 Standard-IA로
전환하는 것이 적절한가?"
```

Lifecycle과의 차이는 다음과 같습니다.

```text
Storage Class Analysis
= 분석

Lifecycle Rule
= 실제 자동 Transition / Expiration 실행
```

---

## 11. Lifecycle Hands-on Flow

콘솔에서는 다음과 같은 Lifecycle Action을 구성할 수 있습니다.

```text
Current Version Transition
Noncurrent Version Transition
Current Version Expiration
Noncurrent Version Permanent Deletion
Expired Delete Marker Cleanup
Incomplete Multipart Upload Cleanup
```

예시:

```text
Current Version

30d  → Standard-IA
60d  → Intelligent-Tiering
90d  → Glacier Instant Retrieval
180d → Glacier Flexible Retrieval
365d → Glacier Deep Archive
700d → Expiration
```

Lifecycle Rule은 생성 후 S3가 백그라운드에서 자동으로 적용합니다.

### Important

Current Object를 다른 Storage Class로 Lifecycle Transition하기 위해 Versioning이 반드시 필요한 것은 아닙니다.

Versioning은 특히 Noncurrent Version의 Transition 및 Expiration을 관리할 때 중요합니다.

---

## 12. Exam Scenarios

### Regenerable Thumbnail

Thumbnail은 원본 이미지에서 다시 생성할 수 있고 자주 사용하지 않는 데이터라고 가정합니다.

```text
Original Image
     ↓
Thumbnail
     ↓
One Zone-IA
     ↓ 60 days
Expiration
```

재생성 가능한 데이터라면 One Zone-IA가 선택지가 될 수 있습니다.

### Old Versions

```text
Deleted Object
      ↓
Versioning으로 이전 Version 보존
      ↓
Noncurrent Version
      ↓
Lifecycle
      ↓
Cheaper Storage Class
      ↓
Permanent Delete
```

Versioning으로 복구 가능성을 확보하고 Lifecycle로 오래된 Version의 비용을 관리할 수 있습니다.

---

## 🔑 핵심 정리

```text
S3 Lifecycle
= 시간에 따른 Object 관리 자동화

Transition
= Storage Class 변경

Expiration
= Object 만료

Versioning + Lifecycle
= Noncurrent Version 비용 관리

Incomplete Multipart Upload
= Lifecycle로 자동 정리 가능
```

---

## 🇯🇵 日本語 Summary

S3 Lifecycleは、時間の経過に応じてS3オブジェクトを自動的に管理する機能です。

主な機能：

- Transition：別のStorage Classへ移行
- Expiration：一定期間後にオブジェクトを期限切れにする
- Noncurrent Versionの移行・削除
- Delete Markerの管理
- 未完了Multipart Uploadの削除

VersioningとLifecycleを組み合わせることで、古いバージョンを安価なStorage Classへ移行し、一定期間後に削除できます。

---

## 🇺🇸 English Summary

S3 Lifecycle automates object management over time.

Main capabilities include:

- transitioning objects to different storage classes
- expiring objects
- managing noncurrent versions
- cleaning up delete markers
- aborting incomplete multipart uploads

Lifecycle is especially useful with S3 Versioning because old versions can be transitioned to cheaper storage and eventually deleted.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Lifecycle Rule | ライフサイクルルール | 수명 주기 규칙 |
| Transition | 移行 | 전환 |
| Expiration | 有効期限切れ | 만료 |
| Current Version | 現行バージョン | 현재 버전 |
| Noncurrent Version | 非現行バージョン | 이전 버전 |
| Delete Marker | 削除マーカー | 삭제 마커 |
| Multipart Upload | マルチパートアップロード | 멀티파트 업로드 |
| Storage Class | ストレージクラス | 스토리지 클래스 |
| Prefix | プレフィックス | 접두사 |
| Object Size Filter | オブジェクトサイズフィルター | 객체 크기 필터 |

---

## 📝 Review Questions

<details>
<summary>Q1. S3 Lifecycle의 두 가지 대표적인 Action은?</summary>

Transition과 Expiration입니다.

</details>

<details>
<summary>Q2. NoncurrentDays는 언제부터 계산되는가?</summary>

Object Version이 처음 생성된 시점이 아니라 해당 Version이 Noncurrent가 된 시점부터 계산됩니다.

</details>

<details>
<summary>Q3. Current Object의 Lifecycle Transition에 Versioning이 반드시 필요한가?</summary>

아닙니다. 일반적인 Current Object Transition 자체에는 Versioning이 필수가 아닙니다.

</details>

<details>
<summary>Q4. 미완료 Multipart Upload를 자동 정리할 수 있는 기능은?</summary>

S3 Lifecycle Rule입니다.

</details>
