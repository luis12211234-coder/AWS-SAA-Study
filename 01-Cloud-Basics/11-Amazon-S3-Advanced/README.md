# 11. Amazon S3 Advanced

Amazon S3의 기본 개념을 넘어 객체의 수명 주기 관리, 이벤트 기반 처리, 성능 최적화, 대량 객체 작업, 조직 전체의 스토리지 분석 기능을 정리한 섹션입니다.

---

## 📚 Contents

| No. | Topic | File |
|---|---|---|
| 11-01 | S3 Lifecycle Rules | [11-01-S3-Lifecycle-Rules.md](./11-01-S3-Lifecycle-Rules.md) |
| 11-02 | S3 Requester Pays | [11-02-S3-Requester-Pays.md](./11-02-S3-Requester-Pays.md) |
| 11-03 | S3 Event Notifications | [11-03-S3-Event-Notifications.md](./11-03-S3-Event-Notifications.md) |
| 11-04 | S3 Performance | [11-04-S3-Performance.md](./11-04-S3-Performance.md) |
| 11-05 | S3 Batch Operations | [11-05-S3-Batch-Operations.md](./11-05-S3-Batch-Operations.md) |
| 11-06 | S3 Storage Lens | [11-06-S3-Storage-Lens.md](./11-06-S3-Storage-Lens.md) |

---

## 🗺️ Section Map

```text
Amazon S3 Advanced
│
├─ 객체를 시간에 따라 자동 관리
│  └─ S3 Lifecycle Rules
│
├─ 요청/다운로드 비용을 요청자에게 부담
│  └─ Requester Pays
│
├─ S3에서 발생한 이벤트에 반응
│  ├─ S3 Event Notifications
│  └─ Amazon EventBridge
│
├─ S3 성능 최적화
│  ├─ Prefix
│  ├─ Multipart Upload
│  ├─ Transfer Acceleration
│  └─ Byte-Range Fetch
│
├─ 기존 Object 대량 처리
│  └─ S3 Batch Operations
│
└─ 전체 S3 환경 분석
   └─ S3 Storage Lens
```

---

## 🔑 Core Concepts

### Lifecycle Rules

시간이 지나면서 Object를 더 저렴한 Storage Class로 이동하거나 삭제하는 작업을 자동화합니다.

```text
Standard
   ↓
Standard-IA
   ↓
Glacier
   ↓
Expiration
```

Versioning을 사용하는 경우 Current Version과 Noncurrent Version을 별도로 관리할 수 있습니다.

---

### Requester Pays

일반적으로 S3 비용은 Bucket Owner가 부담하지만, Requester Pays를 사용하면 요청 및 데이터 다운로드 비용을 Requester에게 부담시킬 수 있습니다.

```text
Normal Bucket
Owner → Storage + Request + Transfer

Requester Pays
Owner     → Storage
Requester → Request + Data Transfer
```

---

### Event Notifications

S3에서 Object 생성, 삭제 등의 이벤트가 발생했을 때 다른 AWS 서비스가 반응하도록 구성할 수 있습니다.

```text
S3 Event
   │
   ├─ SNS
   ├─ SQS
   └─ Lambda
```

더 복잡한 이벤트 필터링과 라우팅이 필요한 경우 Amazon EventBridge를 사용할 수 있습니다.

---

### S3 Performance

```text
많은 Request
→ Prefix를 통한 병렬 처리

Large Object Upload
→ Multipart Upload

장거리 S3 Transfer
→ Transfer Acceleration

Large Object Download
→ Byte-Range Fetch
```

---

### Batch Operations

많은 기존 S3 Object에 동일한 작업을 한꺼번에 수행합니다.

```text
Object List
    ↓
S3 Batch Operations
    ↓
Bulk Action
```

---

### Storage Lens

여러 Account, Region, Bucket의 S3 사용 현황을 중앙에서 분석합니다.

```text
Organization
   ↓
Accounts
   ↓
Regions
   ↓
Buckets
   ↓
Storage Lens
   ↓
Dashboard
```

---

## 🇯🇵 日本語 Summary

Amazon S3 Advancedでは、S3オブジェクトのライフサイクル管理、イベント処理、パフォーマンス最適化、大量オブジェクト処理、ストレージ分析について学習します。

- Lifecycle Rules：オブジェクトの移行・削除を自動化
- Requester Pays：リクエストやデータ転送コストをリクエスターが負担
- Event Notifications：S3イベントをSNS、SQS、Lambdaなどに通知
- S3 Performance：Multipart Upload、Transfer Acceleration、Byte-Range Fetchなどによる最適化
- Batch Operations：大量の既存オブジェクトを一括処理
- Storage Lens：組織全体のS3利用状況を分析・可視化

---

## 🇺🇸 English Summary

Amazon S3 Advanced covers advanced object management and optimization features.

Key topics include:

- automating object transitions and expiration with Lifecycle Rules
- shifting request and transfer costs with Requester Pays
- reacting to S3 events using Event Notifications and EventBridge
- improving S3 performance
- processing large numbers of existing objects with Batch Operations
- analyzing S3 usage across accounts and Regions with Storage Lens
