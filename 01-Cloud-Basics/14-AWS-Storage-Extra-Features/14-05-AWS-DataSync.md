# 14-05. AWS DataSync

## 🇰🇷 1. AWS DataSync란?

AWS DataSync는 대량의 데이터를 Storage 간에 복사하고 동기화하기 위한 서비스입니다.

```text
Storage A
    │
    │ Large Amount of Data
    ↓
AWS DataSync
    ↓
Storage B
```

대표적인 사용 사례:

```text
On-Premises ↔ AWS

AWS ↔ AWS

Other Storage Environment ↔ AWS
```

---

# 2. On-Premises → AWS

On-Premises의 NFS 또는 SMB File Server 데이터를 AWS Storage로 Migration할 수 있습니다.

```text
On-Premises

NFS / SMB Server
       │
       ↓
DataSync Agent
       │
       │ Encrypted Connection
       ↓
AWS DataSync
       │
       ├─ Amazon S3
       ├─ Amazon EFS
       └─ Amazon FSx
```

On-Premises Storage에 연결하기 위해 **DataSync Agent**가 필요합니다.

---

# 3. AWS Storage 간 DataSync

AWS Storage Service 간에도 데이터를 복사할 수 있습니다.

```text
S3
 ↓
DataSync
 ↓
EFS
```

또는:

```text
EFS
 ↓
DataSync
 ↓
FSx
```

AWS Storage 간의 Data Transfer에서는 별도의 DataSync Agent가 필요하지 않습니다.

```text
On-Prem ↔ AWS
→ Agent 필요

AWS ↔ AWS
→ Agent 필요 없음
```

---

# 4. Supported AWS Storage

강의에서 다룬 주요 Storage Destination:

```text
Amazon S3
Amazon EFS
Amazon FSx
```

Amazon S3의 여러 Storage Class와 함께 사용할 수 있습니다.

---

# 5. Scheduled Synchronization

DataSync는 지속적인 Real-Time Replication 서비스가 아닙니다.

```text
Continuous Real-Time Sync
❌
```

Schedule에 따라 Data Transfer를 실행할 수 있습니다.

예:

```text
Hourly
Daily
Weekly
```

즉:

```text
Scheduled Data Synchronization
```

이 핵심입니다.

---

# 6. Metadata와 Permission 보존 ⭐

DataSync의 중요한 시험 포인트입니다.

파일을 이동하면서 파일 자체뿐만 아니라 Metadata와 Permission을 보존할 수 있습니다.

```text
File
│
├─ Data
├─ Owner
├─ Permission
├─ Timestamp
└─ Metadata
```

예:

```text
NFS
→ POSIX Metadata / Permission

SMB
→ SMB Permission
```

따라서 다음과 같은 Scenario에서는 DataSync를 고려합니다.

```text
Large File Migration
+
Preserve Metadata
+
Preserve Permissions
        ↓
AWS DataSync
```

---

# 7. Performance

DataSync Agent의 Task는 높은 Network Throughput을 사용할 수 있습니다.

필요한 경우 Bandwidth Limit을 설정하여 회사 Network를 과도하게 사용하지 않도록 제한할 수 있습니다.

```text
DataSync
    │
    ├─ High-Speed Transfer
    │
    └─ Bandwidth Limit
```

---

# 8. DataSync + Snowcone

Snowcone에는 DataSync Agent가 사전 설치되어 있습니다.

```text
Remote Location
      │
      ↓
   Snowcone
      │
      ├─ Local Data
      └─ DataSync Agent
```

따라서 Network 환경이 제한적인 Edge Location에서도 DataSync와 Snowcone을 함께 활용할 수 있습니다.

---

# 9. DataSync vs Storage Gateway

두 서비스의 목적은 다릅니다.

### Storage Gateway

```text
On-Premises
     ↕
Storage Gateway
     ↕
AWS Storage
```

**Hybrid Storage 연결**

### DataSync

```text
Storage A
    │
    ↓
 DataSync
    ↓
Storage B
```

**대량 데이터 복사 / 동기화**

즉:

```text
Storage Gateway
= 연결해서 사용

DataSync
= 데이터를 이동 / 동기화
```

---

# 10. DataSync vs Snow Family

```text
DataSync
→ Network 기반 대량 Data Transfer

Snow Family
→ Physical Device 기반 Offline Transfer
```

네트워크를 통해 대량 데이터를 전송할 수 있다면 DataSync를 사용할 수 있습니다.

Network Capacity가 부족하여 대량 전송이 현실적으로 어렵다면 Snow Family를 고려합니다.

---

## 🔑 핵심 정리

```text
AWS DataSync
= Large-scale Data Transfer / Synchronization

On-Prem ↔ AWS
→ DataSync Agent

AWS ↔ AWS
→ Agent 필요 없음

Storage
├─ S3
├─ EFS
└─ FSx

특징
├─ Scheduled Sync
├─ Metadata 보존
├─ File Permission 보존
├─ High-Speed Transfer
└─ Bandwidth Limit
```

시험 핵심:

```text
Large Amount of Data
+
NFS / SMB
+
Migration / Synchronization
+
Metadata / Permission Preservation

→ AWS DataSync
```

---

## 🇯🇵 日本語 Summary

AWS DataSyncは、大量のデータをストレージ間でコピー・同期するためのサービスです。

オンプレミスとAWS間でデータを転送する場合、DataSync Agentを使用します。

AWSストレージサービス間の転送ではAgentは必要ありません。

DataSyncはAmazon S3、Amazon EFS、Amazon FSxなどと連携できます。

また、ファイルのMetadataやPermissionを維持しながらデータを移行できる点が重要です。

リアルタイムの継続的な同期ではなく、スケジュールに基づいて同期を実行できます。

---

## 🇺🇸 English Summary

AWS DataSync is a managed service for large-scale data transfer and synchronization.

A DataSync Agent is required when connecting on-premises file systems to AWS. Transfers between supported AWS storage services do not require an agent.

DataSync can preserve file metadata and permissions during migration.

Synchronization is scheduled rather than continuous real-time replication.

---

## 📝 Review Questions

<details>
<summary>Q1. On-Premises NFS Server의 대량 데이터를 S3로 Migration하려면?</summary>

AWS DataSync를 사용할 수 있습니다.

</details>

<details>
<summary>Q2. On-Premises와 AWS 사이에서 DataSync를 사용할 때 필요한 것은?</summary>

DataSync Agent가 필요합니다.

</details>

<details>
<summary>Q3. AWS Storage 간 DataSync에서도 Agent가 필요한가?</summary>

아닙니다.

</details>

<details>
<summary>Q4. 기존 File Permission과 Metadata를 유지하면서 Migration해야 한다면?</summary>

AWS DataSync가 적합합니다.

</details>

<details>
<summary>Q5. DataSync는 Continuous Real-Time Synchronization인가?</summary>

아닙니다. Schedule에 따라 Synchronization Task를 실행할 수 있습니다.

</details>