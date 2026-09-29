# S3 Security

Amazon S3의 보안, 암호화, 접근 제어 및 데이터 보호 기능을 정리합니다.

이 폴더에서는 다음 내용을 다룹니다.

```text
S3 Security
│
├─ Encryption
├─ CORS
├─ MFA Delete
├─ Server Access Logging
├─ Presigned URLs
├─ WORM / Object Lock
├─ Access Points
└─ S3 Object Lambda
```

---

## 📂 Contents

| File | Topic |
|---|---|
| 12-01-S3-Encryption.md | S3 암호화 방식 |
| 12-02-CORS.md | Cross-Origin Resource Sharing |
| 12-03-MFA-Delete.md | MFA 기반 Version 영구 삭제 보호 |
| 12-04-S3-Access-Logs.md | S3 요청 기록 및 감사 |
| 12-05-Presigned-URLs.md | Private Object 임시 접근 |
| 12-06-S3-Object-Lock-and-Glacier-Vault-Lock.md | WORM 및 데이터 보존 |
| 12-07-S3-Access-Points.md | S3 접근 관리 단순화 |
| 12-08-S3-Object-Lambda.md | Object 요청 시 데이터 변환 |

---

## 🔑 전체 구조

```text
Encryption
→ 데이터를 어떻게 보호할 것인가?

CORS
→ 다른 Origin의 Browser 요청을 허용할 것인가?

MFA Delete
→ Version 영구 삭제를 어떻게 보호할 것인가?

Access Logs
→ 누가 S3에 접근했는가?

Presigned URL
→ Private Object를 임시로 공유하려면?

Object / Vault Lock
→ 데이터를 삭제할 수 없게 보존하려면?

Access Points
→ 여러 사용자 / Application의 접근을 어떻게 분리할 것인가?

Object Lambda
→ Object를 반환하기 전에 변환하려면?
```

---

## 🎯 SAA 핵심 구분

```text
암호화 Key를 AWS가 관리
→ SSE-S3

KMS Key + 감사 / 제어
→ SSE-KMS

고객이 Key 제공
→ SSE-C

Client가 직접 암호화
→ Client-Side Encryption

Private Object 임시 공유
→ Presigned URL

Object Version 삭제 방지
→ S3 Object Lock

Glacier Vault 전체 WORM
→ Glacier Vault Lock

복잡한 Bucket 접근 관리
→ S3 Access Points

Object 반환 시 변환
→ S3 Object Lambda
```

---

## 🇯🇵 日本語 Summary

Amazon S3には、暗号化、アクセス制御、監査、データ保護などのさまざまなセキュリティ機能があります。

SAAでは、それぞれの機能の詳細な設定方法よりも、要件に応じて適切な機能を選択できることが重要です。

---

## 🇺🇸 English Summary

Amazon S3 provides multiple security and data protection mechanisms including encryption, access control, auditing, temporary access, WORM protection, and Access Points.

For the SAA exam, focus on identifying the correct S3 security feature for each requirement.
