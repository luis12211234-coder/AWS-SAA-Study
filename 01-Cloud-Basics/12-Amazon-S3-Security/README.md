# 12. Amazon S3 Security

Amazon S3의 암호화, 브라우저 기반 교차 오리진 접근, 삭제 보호, 접근 로그, 임시 객체 공유, WORM 기반 데이터 보호 및 세분화된 접근 관리 기능을 정리한 섹션입니다.

---

## 📚 Contents

| No. | Topic | File |
|---|---|---|
| 12-01 | S3 Encryption | [12-01-S3-Encryption.md](./12-01-S3-Encryption.md) |
| 12-02 | S3 CORS | [12-02-S3-CORS.md](./12-02-S3-CORS.md) |
| 12-03 | S3 MFA Delete | [12-03-S3-MFA-Delete.md](./12-03-S3-MFA-Delete.md) |
| 12-04 | S3 Server Access Logging | [12-04-S3-Server-Access-Logging.md](./12-04-S3-Server-Access-Logging.md) |
| 12-05 | S3 Presigned URLs | [12-05-S3-Presigned-URLs.md](./12-05-S3-Presigned-URLs.md) |
| 12-06 | S3 Object Lock & Glacier Vault Lock | [12-06-S3-Object-Lock-and-Glacier-Vault-Lock.md](./12-06-S3-Object-Lock-and-Glacier-Vault-Lock.md) |
| 12-07 | S3 Access Points | [12-07-S3-Access-Points.md](./12-07-S3-Access-Points.md) |
| 12-08 | S3 Object Lambda | [12-08-S3-Object-Lambda.md](./12-08-S3-Object-Lambda.md) |

---

## 🗺️ Section Map

```text
Amazon S3 Security
│
├─ 저장된 Object 암호화
│  ├─ SSE-S3
│  ├─ SSE-KMS
│  ├─ SSE-C
│  └─ Client-Side Encryption
│
├─ 다른 Origin의 Resource 접근
│  └─ CORS
│
├─ Version 영구 삭제 보호
│  └─ MFA Delete
│
├─ S3 접근 기록 및 감사
│  └─ Server Access Logging
│
├─ Private Object 임시 공유
│  └─ Presigned URLs
│
├─ WORM 기반 데이터 보호
│  ├─ S3 Object Lock
│  └─ Glacier Vault Lock
│
├─ 복잡한 S3 접근 관리
│  └─ S3 Access Points
│
└─ Object 반환 시 동적 변환
   └─ S3 Object Lambda
```

---

## 🔑 Core Concepts

### S3 Encryption

S3 Object는 Server-Side 또는 Client-Side에서 암호화할 수 있습니다.

```text
Server-Side Encryption
│
├─ SSE-S3
│  └─ S3 Managed Key
│
├─ SSE-KMS
│  └─ AWS KMS Key
│
└─ SSE-C
   └─ Customer-Provided Key

Client-Side Encryption
└─ Client가 암호화 후 S3에 업로드
```

전송 중 데이터는 HTTPS/TLS를 통해 보호할 수 있으며 Bucket Policy를 사용하여 HTTPS 사용을 강제할 수 있습니다.

---

### CORS

Browser에서 한 Origin의 Web Page가 다른 Origin의 Resource에 접근할 때 사용되는 보안 메커니즘입니다.

```text
Origin A
   │
   │ Cross-Origin Request
   ▼
Origin B
   │
   └─ CORS Configuration
```

Origin은 다음 세 요소의 조합입니다.

```text
Protocol + Host + Port
```

Resource를 제공하는 쪽에서 허용할 Origin과 HTTP Method 등을 설정합니다.

---

### MFA Delete

Versioning된 S3 Bucket에서 중요한 삭제 작업에 MFA 인증을 추가합니다.

```text
Versioning
   ↓
MFA Delete
   ↓
Permanent Version Deletion Protection
```

특히 특정 Object Version을 영구적으로 삭제하는 작업을 보호할 때 사용합니다.

---

### Server Access Logging

S3 Bucket에 들어온 Request를 감사 목적으로 기록합니다.

```text
Application
    ↓
Source Bucket
    ↓
Access Logs
    ↓
Logging Bucket
```

Source Bucket과 Logging Bucket을 동일하게 구성하면 Logging Loop가 발생할 수 있으므로 분리해야 합니다.

---

### Presigned URLs

Private S3 Object를 Public으로 변경하지 않고 제한된 시간 동안 접근할 수 있도록 합니다.

```text
Private Object 🔒
      ↓
Presigned URL
      ↓
Temporary Access
```

대표적으로 임시 Download 또는 Upload에 사용할 수 있습니다.

---

### Object Lock & Glacier Vault Lock

WORM(Write Once Read Many) 방식으로 데이터를 삭제 또는 변경하지 못하도록 보호합니다.

```text
WORM
│
├─ Glacier Vault Lock
│  └─ Vault 수준 Policy
│
└─ S3 Object Lock
   └─ Object Version 수준 보호
```

S3 Object Lock에는 두 가지 주요 Retention Mode가 있습니다.

```text
Compliance
→ 관리자도 우회 불가

Governance
→ 특별 권한 사용자는 우회 가능
```

Legal Hold를 이용하면 특정 종료 기간 없이 Object Version을 보호할 수도 있습니다.

---

### S3 Access Points

여러 Team이나 Application이 하나의 대규모 S3 Dataset을 사용하는 경우 접근 관리를 단순화합니다.

```text
                S3 Bucket
                    ↑
        ┌───────────┼───────────┐
        │           │           │
   Finance AP    Sales AP   Analytics AP
```

각 Access Point는 고유한 DNS Name과 Access Point Policy를 가질 수 있습니다.

또한 VPC Origin을 사용하면 특정 VPC에서 들어오는 요청으로 Access Point를 제한할 수 있습니다.

---

### S3 Object Lambda

S3 Object를 Application에 반환하기 전에 Lambda Function을 이용하여 데이터를 동적으로 변환합니다.

```text
S3 Original Object
        ↓
Lambda
        ↓
Transformation
        ↓
Application
```

대표적인 사용 사례:

```text
PII Redaction
XML → JSON
Image Resize
Watermark
Data Enrichment
```

원본 S3 Object는 그대로 유지됩니다.

---

## 🇯🇵 日本語 Summary

Amazon S3 Securityでは、S3データの暗号化、アクセス制御、監査、削除保護、WORMによるデータ保護について学習します。

- S3 Encryption：SSE-S3、SSE-KMS、SSE-C、Client-Side Encryption
- CORS：異なるOriginからのResource Accessを制御
- MFA Delete：Object Versionの完全削除をMFAで保護
- Server Access Logging：S3へのRequestを記録・監査
- Presigned URLs：Private Objectへの一時的なAccessを提供
- Object Lock / Vault Lock：WORMによるデータ保護
- Access Points：複数のTeamやApplicationのAccess Managementを簡素化
- Object Lambda：Objectを返す前にLambdaで動的に変換

---

## 🇺🇸 English Summary

Amazon S3 Security covers encryption, access control, auditing, deletion protection, temporary access, and WORM-based data protection.

Key topics include:

- protecting S3 objects with server-side and client-side encryption
- controlling cross-origin browser requests with CORS
- protecting permanent version deletion with MFA Delete
- auditing S3 requests with Server Access Logging
- providing temporary access to private objects with Presigned URLs
- protecting data with S3 Object Lock and Glacier Vault Lock
- simplifying access management with S3 Access Points
- dynamically transforming objects with S3 Object Lambda
