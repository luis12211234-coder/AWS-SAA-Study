# 11-02. S3 Requester Pays

## 🇰🇷 1. Requester Pays란?

일반적인 S3 Bucket에서는 Bucket Owner가 Storage뿐 아니라 Request와 Data Transfer 비용도 부담합니다.

```text
Normal S3 Bucket

Bucket Owner
├─ Storage Cost
├─ Request Cost
└─ Data Transfer Cost
```

대규모 데이터를 외부 사용자에게 제공하는 경우 문제가 생길 수 있습니다.

```text
Dataset = 100 TB

수많은 외부 사용자 Download
             ↓
Bucket Owner의 Transfer Cost 증가
```

이때 사용할 수 있는 기능이 **Requester Pays**입니다.

---

## 2. 비용 구조

Requester Pays를 활성화하면 비용 구조가 다음과 같이 바뀝니다.

```text
Requester Pays Bucket

Bucket Owner
└─ Storage Cost

Requester
├─ Request Cost
└─ Data Download / Transfer Cost
```

즉 데이터 자체를 보관하는 비용은 Owner가 부담하지만, 해당 데이터를 요청하고 다운로드하는 데 발생하는 비용은 Requester에게 부담시킬 수 있습니다.

---

## 3. 대표적인 사용 사례

```text
Research Organization
       ↓
S3에 대규모 Dataset 저장
       ↓
외부 사용자들이 Dataset Download
```

데이터 제공자는 Storage Cost만 부담하고 실제 데이터를 사용하는 Requester가 Request 및 Transfer Cost를 부담하도록 구성할 수 있습니다.

따라서 **대규모 Dataset 공유**가 대표적인 사용 사례입니다.

---

## 4. Authentication과 Authorization

Requester Pays라고 해서 아무나 Object를 가져갈 수 있는 것은 아닙니다.

Requester는 인증되어야 하며, Object에 접근할 수 있는 권한도 필요합니다.

세 개념을 분리해서 이해해야 합니다.

```text
Authentication
= "당신은 누구인가?"

Authorization
= "이 Object를 읽을 권한이 있는가?"

Requester Pays
= "이 Request 비용을 누가 지불하는가?"
```

Requester Pays는 **권한 기능이 아니라 Billing 방식에 관한 기능**입니다.

IAM Policy와 Bucket Policy 등의 권한 검사는 여전히 적용됩니다.

---

## 5. Requester의 비용 동의

Requester Pays Bucket에 요청할 때 Requester는 자신이 비용을 부담한다는 것을 명시해야 합니다.

개념적으로:

```text
Requester
   ↓
"이 요청 비용을 내가 부담하겠습니다."
   ↓
Requester Pays Bucket
```

API 또는 CLI에서는 Requester Pays를 나타내는 별도의 옵션을 사용할 수 있습니다.

SAA 수준에서는 구체적인 명령어보다 **Requester가 비용 부담을 명시해야 한다**는 개념이 중요합니다.

---

## 🔑 핵심 정리

```text
Normal S3
Owner
= Storage + Request + Transfer

Requester Pays
Owner
= Storage

Requester
= Request + Download/Transfer
```

### Exam Keyword

```text
Large Dataset
+
External Users
+
Bucket Owner가 Download Cost를 부담하고 싶지 않음

→ S3 Requester Pays
```

---

## 🇯🇵 日本語 Summary

S3 Requester Paysを使用すると、リクエストとデータ転送の料金をBucket OwnerではなくRequesterに負担させることができます。

Bucket Ownerはストレージ料金を負担し、Requesterはリクエストおよびデータダウンロード料金を負担します。

大規模なデータセットを外部ユーザーと共有する場合に便利です。

Requester Paysはアクセス権限を付与する機能ではないため、IAM PolicyやBucket PolicyなどのAuthorizationは引き続き必要です。

---

## 🇺🇸 English Summary

S3 Requester Pays shifts request and data transfer costs from the bucket owner to the requester.

The bucket owner still pays for storage, while authenticated requesters pay for their requests and data downloads.

It is useful when sharing large datasets with external users.

Requester Pays controls billing, not authorization.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Requester Pays | リクエスター支払い | 요청자 지불 |
| Requester | リクエスター | 요청자 |
| Bucket Owner | バケット所有者 | 버킷 소유자 |
| Request Cost | リクエスト料金 | 요청 비용 |
| Data Transfer | データ転送 | 데이터 전송 |
| Authentication | 認証 | 인증 |
| Authorization | 認可 | 인가 |
| Billing | 請求 | 과금 |

---

## 📝 Review Questions

<details>
<summary>Q1. Requester Pays에서 Storage Cost는 누가 부담하는가?</summary>

Bucket Owner가 부담합니다.

</details>

<details>
<summary>Q2. Requester가 주로 부담하는 비용은?</summary>

Request Cost와 Data Download/Transfer Cost입니다.

</details>

<details>
<summary>Q3. Requester Pays를 활성화하면 IAM 권한이 없어도 Object를 읽을 수 있는가?</summary>

아닙니다. Requester Pays는 Billing 기능이며 기존 Authorization은 그대로 필요합니다.

</details>
