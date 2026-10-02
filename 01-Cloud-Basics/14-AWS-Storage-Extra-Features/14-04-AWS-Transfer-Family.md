# 14-04. AWS Transfer Family

## 🇰🇷 1. AWS Transfer Family란?

AWS Transfer Family는 기존 FTP 계열 Protocol을 사용하여 Amazon S3 또는 Amazon EFS에 파일을 전송할 수 있도록 하는 완전 관리형 서비스입니다.

```text
FTP Client
    │
    │ FTP / FTPS / SFTP
    ↓
AWS Transfer Family
    │
    ├─ Amazon S3
    └─ Amazon EFS
```

즉 기존 Application을 S3 API 또는 EFS NFS 방식으로 변경하지 않고 기존 File Transfer 방식을 유지할 수 있습니다.

---

# 2. 지원 Protocol

강의에서 다룬 주요 Protocol은 다음과 같습니다.

| Protocol | 의미 | Encryption |
|---|---|---|
| FTP | File Transfer Protocol | ❌ |
| FTPS | FTP over SSL/TLS | ⭕ |
| SFTP | Secure File Transfer Protocol | ⭕ |

FTP는 암호화되지 않으며 FTPS와 SFTP는 전송 중 Encryption을 제공합니다.

---

# 3. Transfer Family는 Storage가 아니다

Transfer Family 자체가 데이터를 저장하는 것은 아닙니다.

```text
Transfer Family
≠ Storage
```

실제 Backend Storage는 다음과 같습니다.

```text
Transfer Family
      │
      ├─ Amazon S3
      └─ Amazon EFS
```

Transfer Family는 FTP 계열 Endpoint를 제공하는 역할을 합니다.

---

# 4. Authentication

Transfer Family는 기존 Authentication System과 통합할 수 있습니다.

예:

```text
Microsoft Active Directory
LDAP
Okta
Amazon Cognito
Custom Identity Provider
```

사용자 인증과 AWS Storage 접근 권한은 구분할 수 있습니다.

```text
                  Authentication
                        ↑
               AD / LDAP / etc.
                        │
User ──SFTP──→ Transfer Family
                        │
                        │ IAM Role
                        ↓
                     S3 / EFS
```

외부 Authentication System은 사용자를 인증하고 IAM Role은 AWS Storage에 대한 접근 권한을 제공합니다.

---

# 5. Route 53

선택적으로 Route 53을 이용하여 Transfer Family Endpoint에 Custom Hostname을 제공할 수 있습니다.

```text
files.example.com
       ↓
Route 53
       ↓
Transfer Family
       ↓
Amazon S3 / EFS
```

---

# 6. Use Cases

대표적인 사용 사례:

```text
Existing FTP Application
+
AWS Storage
```

예:

- File Sharing
- Public Dataset Sharing
- CRM
- ERP
- 기존 FTP/SFTP Application Migration

---

# 7. Exam Scenario

```text
Existing SFTP Application
        +
Store Files in S3
        +
Application 변경 최소화
        ↓
AWS Transfer Family
```

시험에서는 다음 조합을 기억합니다.

```text
FTP / FTPS / SFTP
       +
    S3 / EFS
       ↓
Transfer Family
```

---

## 🔑 핵심 정리

```text
AWS Transfer Family
= Managed FTP Interface for AWS Storage

Protocols
├─ FTP
├─ FTPS
└─ SFTP

Backend
├─ Amazon S3
└─ Amazon EFS

Authentication
→ AD / LDAP / Okta / Cognito / Custom

Authorization
→ IAM Role
```

---

## 🇯🇵 日本語 Summary

AWS Transfer Familyは、FTP、FTPS、SFTPなどのファイル転送プロトコルを使用してAmazon S3またはAmazon EFSへアクセスできるフルマネージドサービスです。

既存のFTPベースのアプリケーションを大きく変更せずにAWSストレージを利用できます。

FTPは暗号化されませんが、FTPSとSFTPは転送中の暗号化をサポートします。

外部認証システムとの統合も可能です。

---

## 🇺🇸 English Summary

AWS Transfer Family provides fully managed FTP, FTPS, and SFTP endpoints for Amazon S3 and Amazon EFS.

It is useful when existing applications rely on traditional file transfer protocols and need to use AWS storage without major application changes.

FTP is unencrypted, while FTPS and SFTP provide encryption in transit.

---

## 📝 Review Questions

<details>
<summary>Q1. 기존 SFTP Application을 변경하지 않고 S3를 사용하려면?</summary>

AWS Transfer Family를 사용할 수 있습니다.

</details>

<details>
<summary>Q2. Transfer Family 자체가 Storage인가?</summary>

아닙니다. 실제 Backend Storage는 Amazon S3 또는 Amazon EFS입니다.

</details>

<details>
<summary>Q3. FTP, FTPS, SFTP 중 암호화되지 않는 것은?</summary>

FTP입니다.

</details>