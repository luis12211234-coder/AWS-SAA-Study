# RDS & Aurora Security

Amazon RDS와 Aurora는 **Encryption, Authentication, Network Security, Audit Logging**을 통해 Database를 보호한다.

---

# 1. Encryption at Rest

RDS와 Aurora에 저장된 데이터는 **AWS KMS**를 사용해 암호화할 수 있다.

```text
RDS / Aurora
     ↓
Database Storage
     ↓
AWS KMS Encryption
```

- Database와 Read Replica의 저장 데이터를 암호화
- Encryption은 Database 생성 시 설정
- Source Database가 암호화되지 않았다면 해당 Read Replica도 암호화할 수 없음

## 기존 Unencrypted Database 암호화

기존의 암호화되지 않은 Database를 직접 암호화하는 것이 아니라 **Snapshot과 Restore**를 사용한다.

```text
Unencrypted DB
      ↓
   Snapshot
      ↓
Restore as Encrypted
      ↓
New Encrypted DB
```

### 시험 핵심

`Encryption at Rest → AWS KMS`

`Unencrypted DB → Snapshot → Restore as Encrypted`

---

# 2. Encryption in Transit

Application과 RDS/Aurora 사이에서 이동하는 데이터는 **TLS**를 사용해 암호화할 수 있다.

```text
Application
     │
     │ TLS
     ▼
RDS / Aurora
```

Client는 AWS에서 제공하는 TLS Root Certificate를 사용할 수 있다.

```text
At Rest    → KMS
In Transit → TLS
```

---

# 3. Database Authentication

RDS와 Aurora는 전통적인 Username / Password 방식과 **IAM Database Authentication**을 지원한다.

```text
Username + Password
        OR
IAM Authentication
        ↓
    RDS / Aurora
```

IAM Authentication을 사용하면 AWS IAM을 통해 Database 접근 권한을 관리할 수 있다.

### 시험 핵심

`RDS / Aurora + IAM-based DB access → IAM Database Authentication`

---

# 4. Security Groups

Security Group은 RDS/Aurora에 대한 **Network Access**를 제어한다.

```text
Application
     ↓
Security Group
     ↓
RDS / Aurora
```

특정 Port, IP 또는 다른 Security Group을 Source로 허용할 수 있다.

예:

```text
Application EC2 SG
        ↓
TCP 3306
        ↓
RDS MySQL SG
```

구분:

```text
Security Group
→ 네트워크에서 접근 가능한가?

Authentication
→ 접근하려는 사용자가 누구인가?
```

---

# 5. SSH Access

일반 RDS와 Aurora는 Managed Service이므로 underlying instance에 SSH로 접근할 수 없다.

```text
RDS / Aurora
→ SSH ❌

RDS Custom
→ SSH / SSM ⭕
```

Underlying OS에 접근하거나 직접 설정해야 하는 요구사항이 있다면 **RDS Custom**을 고려한다.

---

# 6. Audit Logs

RDS와 Aurora는 Database 활동을 확인하기 위한 Audit Logging을 지원한다.

Audit Logs를 장기간 보관해야 하는 경우 **CloudWatch Logs**로 전송한다.

```text
RDS / Aurora
     ↓
 Audit Logs
     ↓
CloudWatch Logs
     ↓
Long-term Retention
```

### 시험 핵심

`RDS / Aurora Audit Logs + Long-term Retention → CloudWatch Logs`

---

# 7. 시험 핵심 정리

```text
Encryption at Rest
→ AWS KMS

Encryption in Transit
→ TLS

Unencrypted DB → Encrypted DB
→ Snapshot
→ Restore as Encrypted

Authentication
→ Username / Password
→ IAM Authentication

Network Access
→ Security Groups

SSH
→ Normal RDS / Aurora ❌
→ RDS Custom ⭕

Audit Logs 장기 보관
→ CloudWatch Logs
```

---

# 日本語まとめ

## RDS / Aurora Security

RDSとAuroraはEncryption、Authentication、Security Groups、Audit Logsなどのセキュリティ機能を提供する。

### Encryption

- **At Rest** → AWS KMS
- **In Transit** → TLS
- 暗号化されていないDatabaseを暗号化する場合は、Snapshotを作成して暗号化されたDatabaseとしてRestoreする。

### Authentication

Username / Passwordだけでなく、IAM Database Authenticationも利用できる。

### Network Security

Security Groupsを使用してDatabaseへのNetwork Accessを制御する。

### SSH

通常のRDS / AuroraではSSH Accessはできない。

RDS Customではunderlying instanceへのアクセスが可能。

### Audit Logs

Audit Logsを長期間保存する場合はCloudWatch Logsへ送信する。

---

# English Summary

## RDS / Aurora Security

RDS and Aurora provide encryption, authentication, network security, and audit logging features.

### Encryption

- **Encryption at Rest** → AWS KMS
- **Encryption in Transit** → TLS
- To encrypt an existing unencrypted database, create a snapshot and restore it as an encrypted database.

### Authentication

RDS and Aurora support traditional username/password authentication as well as IAM Database Authentication.

### Network Security

Security Groups control network access to RDS and Aurora databases.

### SSH

SSH access is not available for standard RDS or Aurora.

RDS Custom is the exception when access to the underlying instance is required.

### Audit Logs

Audit Logs can be sent to CloudWatch Logs for longer retention.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Encryption at Rest | 保存時の暗号化 | 저장 데이터 암호화 |
| Encryption in Transit | 転送中の暗号化 | 전송 중 데이터 암호화 |
| AWS KMS | AWS KMS | AWS 키 관리 서비스 |
| TLS | TLS | 전송 계층 보안 |
| Authentication | 認証 | 인증 |
| IAM Authentication | IAM認証 | IAM 인증 |
| Security Group | セキュリティグループ | 보안 그룹 |
| Audit Log | 監査ログ | 감사 로그 |
| CloudWatch Logs | CloudWatch Logs | CloudWatch 로그 |
| Root Certificate | ルート証明書 | 루트 인증서 |

---

# Review Questions

1. RDS와 Aurora의 Encryption at Rest에는 어떤 AWS 서비스가 사용되는가?
2. 기존 Unencrypted Database를 암호화하려면 어떤 과정을 거쳐야 하는가?
3. Encryption at Rest와 Encryption in Transit에는 각각 무엇이 사용되는가?
4. Security Group과 Database Authentication의 역할 차이는 무엇인가?
5. 일반 RDS/Aurora에서 SSH Access가 불가능한 이유는 무엇이며, 예외는 무엇인가?
6. RDS/Aurora의 Audit Logs를 장기간 보관하려면 어디로 전송해야 하는가?
