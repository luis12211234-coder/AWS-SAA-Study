# S3 Encryption

## 🇰🇷 1. S3 암호화 방식

S3 Object를 암호화하는 방법은 크게 Server-Side와 Client-Side로 나눌 수 있습니다.

```text
S3 Encryption
│
├─ Server-Side Encryption
│  ├─ SSE-S3
│  ├─ SSE-KMS
│  ├─ DSSE-KMS
│  └─ SSE-C
│
└─ Client-Side Encryption
```

Server-Side Encryption은 S3에 데이터가 도착한 뒤 AWS 측에서 암호화를 수행합니다.

Client-Side Encryption은 S3에 보내기 전에 Client가 직접 데이터를 암호화합니다.

---

## 2. SSE-S3

```text
SSE-S3
= Server-Side Encryption
+ S3 Managed Keys
```

AWS가 암호화 Key를 관리합니다.

```text
Object
   ↓
Amazon S3
   ↓
S3 Managed Key
   ↓
Encrypted Object
```

특징:

```text
Key 관리
→ Amazon S3

Encryption
→ AES-256

사용자 Key 관리
→ 필요 없음
```

현재 모든 S3 Bucket은 기본적으로 SSE-S3 기반 암호화가 적용됩니다.

---

## 3. SSE-KMS

SSE-KMS는 AWS Key Management Service의 KMS Key를 사용하는 Server-Side Encryption입니다.

```text
Object
   ↓
S3
   +
KMS Key
   ↓
Encrypted Object
```

SSE-S3보다 Key에 대한 제어와 감사 기능이 강화됩니다.

대표적인 특징:

```text
KMS Key 관리
CloudTrail을 통한 Key 사용 감사
Key Policy 관리
```

Object를 읽으려면 S3 Object 접근 권한뿐 아니라 필요한 KMS Key 권한도 필요합니다.

```text
Object Permission
        +
KMS Permission
        ↓
Object Access
```

---

## 4. SSE-KMS와 API Quota

SSE-KMS를 사용하면 암호화 및 복호화 과정에서 AWS KMS API가 사용됩니다.

예:

```text
GenerateDataKey
Decrypt
```

따라서 매우 높은 S3 Request 처리량에서 KMS API Request Quota가 병목이 될 수 있습니다.

```text
Very High S3 Traffic
        ↓
Many KMS API Calls
        ↓
KMS Quota
        ↓
Possible Throttling
```

SAA에서 SSE-KMS + 매우 높은 Request 처리량이 함께 등장하면 KMS Quota를 생각합니다.

---

## 5. S3 Bucket Key

SSE-KMS 사용 시 S3 Bucket Key를 사용할 수 있습니다.

목적:

```text
S3 → KMS API 호출 감소
        ↓
KMS Request Cost 감소
```

즉:

> **SSE-KMS 비용 최적화 = S3 Bucket Key**

로 기억할 수 있습니다.

---

## 6. SSE-C

SSE-C는 Customer-Provided Key를 사용하는 Server-Side Encryption입니다.

```text
Customer
│
├─ Object
└─ Encryption Key
       ↓
      S3
       ↓
Encrypted Object
```

Key는 고객이 관리하지만 실제 암호화 작업은 S3가 수행합니다.

중요한 특징:

```text
Key 관리
→ Customer

Encryption 수행
→ S3

S3가 Key 저장
→ X
```

Object를 다시 읽을 때도 올바른 Key를 제공해야 합니다.

Key를 네트워크를 통해 S3로 전달하므로 HTTPS가 필요합니다.

---

## 7. Client-Side Encryption

Client가 S3에 업로드하기 전에 직접 암호화합니다.

```text
Original File
      ↓
Client Encryption
      ↓
Encrypted File
      ↓
Amazon S3
```

다운로드 후 복호화 역시 Client에서 수행합니다.

```text
S3
 ↓
Encrypted File
 ↓
Client Decryption
 ↓
Original File
```

즉:

> **S3에는 처음부터 암호화된 데이터만 전달됩니다.**

Key와 암호화 과정 전체를 Client가 관리합니다.

---

## 8. Server-Side vs Client-Side

```text
SSE
Client → 원본 전달 → S3가 암호화

Client-Side
Client가 암호화 → 암호화된 파일 전달 → S3 저장
```

핵심 차이는 **암호화를 어디에서 수행하는가**입니다.

---

## 9. Encryption in Transit

S3에 데이터를 전송하는 동안의 암호화는 HTTPS를 사용합니다.

```text
Client
   │
 HTTPS / TLS
   │
   ▼
Amazon S3
```

SSE-C에서는 Encryption Key도 요청에 포함되므로 HTTPS 사용이 특히 중요합니다.

---

## 10. HTTPS 강제

Bucket Policy에서 `aws:SecureTransport` Condition을 이용해 HTTP 요청을 거부할 수 있습니다.

개념적으로:

```text
aws:SecureTransport = false
        ↓
      DENY
```

따라서 HTTPS 연결만 허용하도록 강제할 수 있습니다.

---

## 11. Default Encryption vs Bucket Policy

Default Encryption과 Bucket Policy는 역할이 다릅니다.

### Default Encryption

```text
"별도 지정이 없다면 이 방식으로 암호화한다."
```

예:

```text
Default Encryption
= SSE-S3
```

Client가 별도 암호화 방식을 지정하지 않아도 S3가 SSE-S3로 암호화합니다.

### Bucket Policy

```text
"이 암호화 조건을 만족하지 않으면 요청 자체를 거부한다."
```

예:

```text
Bucket Policy
= SSE-KMS 필수

PUT Request
│
├─ SSE-KMS 지정 → 허용 가능
└─ SSE-KMS 없음 → DENY
```

따라서 Default Encryption은 기본 행동이고 Bucket Policy는 요구사항을 강제하는 Access Control입니다.

Bucket Policy가 먼저 평가되므로 정책 조건을 만족하지 않는 PUT 요청은 Default Encryption이 적용되기 전에 거부될 수 있습니다.

---

## 12. Object Encryption 변경과 Versioning

Versioning이 활성화된 Bucket에서 Object의 Encryption 설정을 변경하면 새로운 Object Version이 생성될 수 있습니다.

```text
coffee.jpg

v2 ← Current
SSE-KMS

v1
SSE-S3
```

기존 Version의 암호화 속성만 단순히 수정하는 개념이 아니라 새로운 Version으로 처리될 수 있다는 점을 기억합니다.

---

## ⚠️ Current AWS Note

현재 모든 S3 Bucket은 기본적으로 SSE-S3를 사용하여 새 Object를 암호화합니다.

또한 2026년 4월부터 새 General Purpose Bucket에서는 SSE-C를 사용한 새로운 Write Request가 기본적으로 비활성화됩니다.

SSE-C가 필요한 경우 명시적으로 활성화해야 합니다.

이는 기존 강의 제작 시점 이후 변경된 현재 AWS 동작입니다.

---

## 🎯 Exam Scenario

```text
AWS가 Key 관리
→ SSE-S3

Key 사용 감사 / 세밀한 제어
→ SSE-KMS

고객이 직접 Key 제공
→ SSE-C

AWS에 보내기 전에 암호화
→ Client-Side Encryption

HTTP 차단
→ aws:SecureTransport

SSE-KMS 비용 감소
→ S3 Bucket Key
```

---

## 🔑 핵심 정리

```text
SSE-S3
= S3 Managed Key

SSE-KMS
= KMS Key + Control + Audit

SSE-C
= Customer가 Key 제공
+ S3는 Key 저장 X

Client-Side
= Client가 암호화 / 복호화

HTTPS
= Encryption in Transit

Bucket Policy
= 특정 암호화 방식 강제 가능
```

한 줄 정리:

> **S3 암호화 문제는 누가 Key를 관리하고 어디에서 암호화하는지를 구분한다.**

---

## 🇯🇵 日本語 Summary

S3の暗号化方式にはSSE-S3、SSE-KMS、SSE-C、Client-Side Encryptionがあります。

SSE-S3ではAmazon S3がKeyを管理し、SSE-KMSではAWS KMSを利用してKeyをより細かく制御できます。

SSE-CではCustomerがKeyを提供しますが、暗号化処理はS3側で行われます。

Client-Side EncryptionではClientがデータを暗号化してからS3へアップロードします。

---

## 🇺🇸 English Summary

Amazon S3 supports SSE-S3, SSE-KMS, SSE-C, and client-side encryption.

SSE-S3 uses S3-managed keys, while SSE-KMS provides additional key control and auditing through AWS KMS.

With SSE-C, the customer provides the encryption key but S3 performs the encryption.

With client-side encryption, data is encrypted before it is uploaded to S3.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Encryption | 暗号化 | 암호화 |
| Decryption | 復号化 | 복호화 |
| Server-Side Encryption | サーバー側暗号化 | 서버 측 암호화 |
| Client-Side Encryption | クライアント側暗号化 | 클라이언트 측 암호화 |
| KMS Key | KMSキー | KMS 키 |
| Bucket Key | バケットキー | 버킷 키 |
| Encryption in Transit | 転送中の暗号化 | 전송 중 암호화 |
| Throttling | スロットリング | 스로틀링 |

---

## 📝 Review Questions

<details>
<summary>Q1. AWS가 Key를 완전히 관리하는 가장 기본적인 S3 암호화 방식은?</summary>

SSE-S3입니다.

</details>

<details>
<summary>Q2. KMS Key 사용 기록을 감사하고 싶다면?</summary>

SSE-KMS를 사용합니다.

</details>

<details>
<summary>Q3. 고객이 직접 Encryption Key를 제공하지만 S3가 암호화를 수행하는 방식은?</summary>

SSE-C입니다.

</details>

<details>
<summary>Q4. SSE-KMS에서 KMS API 호출 비용을 줄이는 기능은?</summary>

S3 Bucket Key입니다.

</details>

<details>
<summary>Q5. HTTP 요청을 차단하고 HTTPS만 강제할 때 사용할 수 있는 Condition은?</summary>

`aws:SecureTransport`입니다.

</details>
