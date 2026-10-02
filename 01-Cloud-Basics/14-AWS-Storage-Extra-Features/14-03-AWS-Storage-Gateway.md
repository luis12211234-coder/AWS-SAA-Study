# 14-03. AWS Storage Gateway

## 🇰🇷 1. Hybrid Cloud

Hybrid Cloud는 일부 Infrastructure는 On-Premises에 유지하면서 일부는 AWS Cloud를 사용하는 Architecture입니다.

```text
Hybrid Cloud

On-Premises 🏢
      +
AWS Cloud ☁️
```

모든 시스템을 즉시 Cloud로 Migration하기 어려운 경우가 있습니다.

예:

- Migration에 오랜 시간이 필요한 경우
- Security 또는 Compliance 요구사항
- 기존 On-Premises Infrastructure 유지
- Elastic Workload만 Cloud에서 실행

이러한 환경에서 On-Premises Storage와 AWS Storage를 연결하기 위해 사용할 수 있는 서비스가 **AWS Storage Gateway**입니다.

---

# 2. AWS Storage Gateway

AWS Storage Gateway는 On-Premises 환경과 AWS Cloud Storage를 연결하는 Hybrid Storage 서비스입니다.

```text
On-Premises
     │
     ↓
Storage Gateway
     │
     ↓
AWS Storage
```

대표적인 사용 사례:

```text
Backup / Restore
Disaster Recovery
Cloud Migration
Storage Extension
Local Cache
```

---

# 3. Storage Gateway 종류

```text
AWS Storage Gateway
│
├─ S3 File Gateway
├─ FSx File Gateway
├─ Volume Gateway
└─ Tape Gateway
```

각 Gateway는 서로 다른 Storage 방식과 Protocol을 지원합니다.

---

# 4. S3 File Gateway

On-Premises Application이 NFS 또는 SMB를 사용하면서 실제 데이터를 Amazon S3에 저장할 수 있도록 합니다.

```text
On-Premises

Application
    │
    │ NFS / SMB
    ↓
S3 File Gateway
    │
    │ HTTPS
    ↓
Amazon S3
```

Application 입장에서는 일반적인 File Share처럼 보이지만 실제 데이터는 S3 Object로 저장됩니다.

즉:

```text
NFS / SMB
     ↓
S3 File Gateway
     ↓
Amazon S3 Object Storage
```

---

## 5. Local Cache

최근에 사용한 데이터는 File Gateway의 Local Cache에 저장할 수 있습니다.

```text
Application
     ↓
S3 File Gateway
     │
     ├─ Local Cache ⚡
     │
     └────────→ Amazon S3
```

전체 S3 Bucket을 On-Premises에 저장하는 것이 아니라 **최근에 사용한 파일을 Cache**합니다.

이를 통해 자주 사용하는 데이터의 Access Latency를 줄일 수 있습니다.

---

## 6. S3 File Gateway와 Glacier

S3 File Gateway에서 Archive가 필요한 경우 S3 Lifecycle Policy를 이용할 수 있습니다.

```text
S3 File Gateway
      ↓
Amazon S3
      ↓
Lifecycle Policy
      ↓
S3 Glacier
```

---

## 7. Authentication

S3 Bucket에 접근하기 위해 IAM Role을 사용합니다.

SMB를 사용하는 Windows 환경에서는 Microsoft Active Directory와 통합하여 사용자 인증을 수행할 수 있습니다.

```text
User
 ↓
Active Directory
 ↓
S3 File Gateway
 ↓
Amazon S3
```

---

# 8. FSx File Gateway

FSx File Gateway는 On-Premises 환경에서 Amazon FSx for Windows File Server에 접근할 때 사용할 수 있습니다.

```text
On-Premises
SMB Client
    │
    ↓
FSx File Gateway
    │
    ↓
FSx for Windows File Server
```

FSx for Windows File Server 자체도 On-Premises에서 접근할 수 있지만 File Gateway를 사용하면 **자주 사용하는 파일을 On-Premises에 Cache**할 수 있습니다.

```text
On-Premises
     │
FSx File Gateway
     │
     ├─ Local Cache ⚡
     │
     └────────→ FSx for Windows
```

따라서 On-Premises 사용자의 File Access Latency를 줄일 수 있습니다.

---

# 9. Volume Gateway

Volume Gateway는 **Block Storage**를 제공하며 iSCSI Protocol을 사용합니다.

```text
Application Server
       │
       │ iSCSI
       ↓
Volume Gateway
       │
       ↓
AWS Cloud
```

Volume의 Point-in-Time Backup을 EBS Snapshot 형태로 생성할 수 있으며 필요하면 복구할 수 있습니다.

Volume Gateway에는 두 가지 방식이 있습니다.

```text
Volume Gateway
│
├─ Cached Volumes
└─ Stored Volumes
```

---

## 10. Cached Volumes

주요 데이터는 AWS에 저장하고 자주 사용하는 데이터만 On-Premises에 Cache합니다.

```text
AWS
= Main Data

On-Premises
= Frequently Accessed Cache
```

즉:

```text
Cached Volume
→ AWS 중심
→ Local Cache
```

---

## 11. Stored Volumes

전체 Dataset을 On-Premises에 저장하고 AWS에 Backup합니다.

```text
On-Premises
= Main Data

AWS
= Off-site Backup
```

즉:

```text
Stored Volume
→ On-Premises 중심
→ AWS Backup
```

### Cached vs Stored

| | Cached Volume | Stored Volume |
|---|---|---|
| Main Data | AWS | On-Premises |
| On-Prem 역할 | Local Cache | 전체 Dataset |
| AWS 역할 | Main Storage | Backup |

---

# 12. Tape Gateway

기존 Physical Tape 기반 Backup System을 AWS Cloud Storage와 연결합니다.

```text
Backup Application
       │
       │ iSCSI / VTL
       ↓
Tape Gateway
       ↓
Amazon S3
       ↓
Glacier / Deep Archive
```

VTL은 **Virtual Tape Library**를 의미합니다.

기존 Backup Software와 Tape 기반 Process를 유지하면서 실제 Storage를 AWS로 이전할 수 있습니다.

```text
Physical Tape
     ↓
Virtual Tape
     ↓
AWS Storage
```

---

# 13. Storage Gateway Hardware Appliance

Storage Gateway는 On-Premises 환경에서 실행되어야 합니다.

일반적으로 다음과 같은 Virtualization Platform을 사용할 수 있습니다.

```text
VMware
Hyper-V
Linux KVM
```

Gateway를 실행할 적절한 Virtual Infrastructure가 없는 경우 AWS의 **Storage Gateway Hardware Appliance**를 사용할 수 있습니다.

```text
Hardware Appliance
       ↓
Storage Gateway
       ↓
AWS
```

Hardware Appliance는 새로운 Gateway 종류가 아니라 **Storage Gateway를 실행하기 위한 물리 장비**입니다.

---

# 14. Storage Gateway 비교

| Gateway | Protocol | AWS Destination | 핵심 |
|---|---|---|---|
| S3 File Gateway | NFS / SMB | S3 | File Interface + S3 |
| FSx File Gateway | SMB | FSx Windows | Local Cache |
| Volume Gateway | iSCSI | AWS Storage / Snapshot | Block Storage |
| Tape Gateway | iSCSI / VTL | S3 / Glacier | Tape Backup |

---

## 🔑 핵심 정리

```text
Storage Gateway
= On-Premises ↔ AWS Storage Bridge

S3 File Gateway
→ NFS / SMB → S3

FSx File Gateway
→ SMB → FSx Windows
→ Local Cache

Volume Gateway
→ iSCSI / Block
├─ Cached = AWS Main + Local Cache
└─ Stored = On-Prem Main + AWS Backup

Tape Gateway
→ VTL
→ 기존 Tape Backup을 AWS로

Hardware Appliance
→ Gateway 실행용 물리 장비
```

---

## 🇯🇵 日本語 Summary

AWS Storage Gatewayは、オンプレミス環境とAWSクラウドストレージを接続するハイブリッドストレージサービスです。

主な種類は以下の4つです。

- S3 File Gateway：NFSまたはSMBを使用してAmazon S3へアクセス
- FSx File Gateway：Amazon FSx for Windows File Serverへのアクセスとローカルキャッシュ
- Volume Gateway：iSCSIを使用するブロックストレージ
- Tape Gateway：既存のテープバックアップをAWSへ移行

Volume GatewayにはCached VolumesとStored Volumesがあります。

Cached Volumesでは主なデータをAWSに保存し、頻繁に使用するデータをローカルにキャッシュします。

Stored Volumesでは全データをオンプレミスに保存し、AWSにバックアップします。

---

## 🇺🇸 English Summary

AWS Storage Gateway connects on-premises environments with AWS cloud storage.

The four major gateway types are:

- S3 File Gateway for NFS/SMB access to Amazon S3
- FSx File Gateway for cached access to FSx for Windows File Server
- Volume Gateway for iSCSI block storage
- Tape Gateway for virtual tape backup using AWS storage

Cached Volumes store primary data in AWS while keeping frequently accessed data locally.

Stored Volumes keep the full dataset on-premises and use AWS for off-site backup.

---

## 📝 Review Questions

<details>
<summary>Q1. On-Premises에서 NFS/SMB를 사용하여 S3에 접근하려면?</summary>

S3 File Gateway를 사용합니다.

</details>

<details>
<summary>Q2. FSx for Windows의 파일을 On-Premises에 Cache하려면?</summary>

FSx File Gateway를 사용합니다.

</details>

<details>
<summary>Q3. Cached Volume과 Stored Volume의 차이는?</summary>

Cached Volume은 주요 데이터를 AWS에 저장하고 자주 사용하는 데이터만 On-Premises에 Cache합니다.

Stored Volume은 전체 데이터를 On-Premises에 저장하고 AWS를 Backup 용도로 사용합니다.

</details>

<details>
<summary>Q4. 기존 Tape Backup Process를 AWS로 Migration하려면?</summary>

Tape Gateway를 사용합니다.

</details>