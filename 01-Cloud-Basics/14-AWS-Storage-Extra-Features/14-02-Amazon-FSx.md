# 14-02. Amazon FSx

## 🇰🇷 1. Amazon FSx란?

Amazon FSx는 특정 고성능 File System을 AWS에서 완전 관리형으로 제공하는 서비스입니다.

RDS와 비교하면 이해하기 쉽습니다.

```text
Amazon RDS
→ MySQL / PostgreSQL 등의 Database를
  AWS가 관리

Amazon FSx
→ Windows / Lustre / NetApp ONTAP / OpenZFS 등의
  File System을 AWS가 관리
```

즉:

> **특정 File System이 필요할 때 이를 직접 구축하고 운영하는 대신 AWS의 Managed Service로 사용한다.**

---

## 2. S3와 File System의 차이

Amazon S3는 Object Storage입니다.

```text
Amazon S3
│
├─ Object
├─ Object
└─ Object
```

반면 File System은 일반적으로 익숙한 File / Directory 구조를 제공합니다.

```text
File System
│
├─ Directory
│   ├─ File
│   └─ File
│
└─ Directory
    └─ File
```

Amazon FSx는 이러한 File System을 AWS에서 관리형으로 제공합니다.

---

## 3. Amazon FSx 종류

SAA에서 중요한 FSx는 다음 네 가지입니다.

```text
Amazon FSx
│
├─ FSx for Windows File Server
├─ FSx for Lustre
├─ FSx for NetApp ONTAP
└─ FSx for OpenZFS
```

각 서비스는 서로 다른 File System과 Workload를 대상으로 합니다.

---

# 4. FSx for Windows File Server

AWS에서 완전 관리형 Windows File Server를 제공합니다.

핵심 기술:

```text
FSx for Windows File Server
│
├─ Windows
├─ SMB
├─ NTFS
├─ Microsoft Active Directory
└─ ACL
```

Windows 기반이지만 Linux EC2 Instance에서도 마운트할 수 있습니다.

---

## 5. Active Directory Integration

기업의 Windows 환경에서는 사용자와 그룹에 따라 File 접근 권한을 관리해야 합니다.

```text
Microsoft Active Directory
          ↓
사용자 / 그룹 인증
          ↓
FSx for Windows
          ↓
Shared Files
```

따라서 FSx for Windows File Server는 Microsoft Active Directory와 통합할 수 있습니다.

시험에서는 다음 조합을 기억합니다.

```text
Windows
+
SMB
+
Active Directory

→ FSx for Windows File Server
```

Single-AZ와 Multi-AZ 배포를 지원하며 SSD 또는 HDD를 선택할 수 있습니다.

---

# 6. FSx for Lustre

Lustre는 대규모 병렬 연산을 위한 고성능 분산 File System입니다.

```text
Lustre
≈ Linux + Cluster
```

대표적인 사용 사례:

```text
HPC
Machine Learning
Video Processing
Financial Modeling
Electronic Design Automation
```

즉:

> **대규모 데이터를 매우 빠르게 병렬 처리해야 하는 Workload = FSx for Lustre**

---

## 7. Lustre + Amazon S3

FSx for Lustre는 Amazon S3와 통합할 수 있습니다.

별도의 서비스가 아니라 **S3에 저장된 데이터를 Lustre의 고성능 File System을 이용해 처리할 수 있도록 연동하는 기능**입니다.

```text
Amazon S3
     │
     │ Dataset
     ↓
FSx for Lustre
     │
     ↓
Compute / HPC / ML
     │
     │ Result
     ↓
Amazon S3
```

따라서 다음과 같은 Scenario에서 중요합니다.

```text
Large Dataset in S3
+
High Performance Processing
+
HPC / ML

→ FSx for Lustre
```

---

# 8. Lustre Deployment Types

FSx for Lustre에는 두 가지 중요한 Deployment Type이 있습니다.

```text
FSx for Lustre
│
├─ Scratch
│
└─ Persistent
```

### Scratch

임시 처리와 높은 성능을 중시합니다.

```text
Scratch
│
├─ Temporary
├─ No Data Replication
├─ High Performance
└─ 장애 시 Data Loss 가능
```

원본 데이터가 S3 등에 있고 다시 처리할 수 있는 단기 Workload에 적합합니다.

```text
S3 Original Data
      ↓
Lustre Scratch
      ↓
Fast Processing
```

---

### Persistent

장기적인 Storage와 안정성을 중시합니다.

```text
Persistent
│
├─ Long-term
├─ Data Replication
└─ Server Failure 대응
```

데이터는 동일한 AZ 내부에서 복제됩니다.

핵심 비교:

```text
Scratch
= Temporary + Performance

Persistent
= Long-term + Reliability
```

---

# 9. FSx for NetApp ONTAP

NetApp은 기업용 Storage 솔루션을 제공하는 회사이며, ONTAP은 NetApp의 Storage 운영 및 Data Management Platform입니다.

Amazon FSx for NetApp ONTAP은 이를 AWS에서 완전 관리형으로 제공합니다.

```text
On-Premises

NetApp ONTAP
      │
      │ Migration
      ↓
AWS

FSx for NetApp ONTAP
```

기존 NetApp ONTAP 또는 NAS 환경의 Workload를 AWS로 Migration할 때 유용합니다.

---

## 10. ONTAP의 다양한 Protocol 지원

FSx for NetApp ONTAP은 여러 Storage Protocol을 지원합니다.

```text
FSx for NetApp ONTAP
│
├─ NFS
├─ SMB
└─ iSCSI
```

따라서 다양한 환경에서 사용할 수 있습니다.

```text
Linux
Windows
macOS
VMware
EC2
ECS
EKS
...
```

핵심 캐릭터는:

> **다양한 환경과 호환되는 Enterprise Storage**

입니다.

---

## 11. Storage Efficiency

FSx for NetApp ONTAP은 Storage 효율화를 위한 기능을 제공합니다.

```text
Storage Efficiency
│
├─ Deduplication
└─ Compression
```

### Deduplication

중복된 데이터를 제거하여 Storage 사용량을 줄입니다.

```text
AAAA
AAAA
AAAA

 ↓ Deduplication

AAAA
```

### Compression

데이터를 압축하여 필요한 Storage 공간을 줄입니다.

```text
Large Data
    ↓
Compression
    ↓
Smaller Storage Usage
```

이외에도 Snapshot, Replication, Point-in-time Clone 등의 기능을 지원합니다.

---

# 12. FSx for OpenZFS

OpenZFS File System을 AWS에서 완전 관리형으로 제공하는 서비스입니다.

주요 목적 중 하나는 기존 ZFS 기반 Workload를 AWS로 Migration하는 것입니다.

```text
Existing ZFS
     │
     │ Migration
     ↓
FSx for OpenZFS
```

NFS Protocol을 지원하며 Linux, Windows, macOS 등의 환경에서 사용할 수 있습니다.

주요 기능:

```text
Snapshots
Compression
Point-in-time Clone
```

NetApp ONTAP과 달리 Deduplication은 지원하지 않습니다.

---

# 13. 네 가지 FSx 비교

| Service | 대표 목적 | 핵심 키워드 |
|---|---|---|
| FSx for Windows File Server | Windows File Sharing | SMB, NTFS, Active Directory |
| FSx for Lustre | High Performance Computing | HPC, ML, S3 Integration |
| FSx for NetApp ONTAP | Enterprise / NetApp Storage | NFS, SMB, iSCSI, Deduplication |
| FSx for OpenZFS | ZFS Workload | NFS, ZFS Migration, Snapshot |

시험에서는 세부 설정값보다 **Scenario에 맞는 File System을 선택하는 것**이 중요합니다.

---

## 14. Exam Scenario

```text
Windows
+
SMB
+
Active Directory

→ FSx for Windows File Server
```

```text
HPC / ML
+
Very High Performance
+
S3 Dataset

→ FSx for Lustre
```

```text
Existing NetApp / NAS
+
NFS / SMB / iSCSI
+
Storage Efficiency

→ FSx for NetApp ONTAP
```

```text
Existing ZFS Workload
+
NFS

→ FSx for OpenZFS
```

---

## 🔑 핵심 정리

```text
Amazon FSx
= Fully Managed File Systems

Windows File Server
→ Windows / SMB / NTFS / AD

Lustre
→ HPC / ML / High Performance
→ S3 Integration
→ Scratch vs Persistent

Scratch
→ Temporary + Performance

Persistent
→ Long-term + Reliability

NetApp ONTAP
→ Enterprise Storage
→ NFS + SMB + iSCSI
→ Deduplication / Compression

OpenZFS
→ ZFS Workload
→ NFS
→ Snapshot / Compression / Clone
```

한 줄 정리:

> **특정 File System과 Workload가 필요할 때 AWS가 해당 File System의 운영을 관리해준다 = Amazon FSx**

---

## 🇯🇵 日本語 Summary

Amazon FSxは、特定の高性能ファイルシステムをAWS上で完全マネージド型として提供するサービスです。

主なサービスは以下の4つです。

- FSx for Windows File Server：SMB、NTFS、Microsoft Active Directoryを使用するWindows環境
- FSx for Lustre：HPC、機械学習などの高性能処理
- FSx for NetApp ONTAP：NFS、SMB、iSCSIをサポートするエンタープライズストレージ
- FSx for OpenZFS：既存のZFSワークロードをAWSへ移行

FSx for LustreはAmazon S3と統合でき、S3の大規模データセットを高性能に処理できます。

LustreにはScratchとPersistentのデプロイタイプがあり、Scratchは一時的な高性能処理、Persistentは長期利用と信頼性を重視します。

---

## 🇺🇸 English Summary

Amazon FSx provides fully managed file systems for specific workloads.

The major FSx services covered are:

- FSx for Windows File Server for Windows, SMB, NTFS, and Active Directory environments
- FSx for Lustre for HPC, machine learning, and high-performance workloads
- FSx for NetApp ONTAP for enterprise storage using NFS, SMB, and iSCSI
- FSx for OpenZFS for ZFS-based workloads

FSx for Lustre integrates with Amazon S3 and can process large S3 datasets using a high-performance file system.

Lustre provides Scratch deployments for temporary high-performance workloads and Persistent deployments for long-term, reliable storage.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| File System | ファイルシステム | 파일 시스템 |
| Fully Managed | フルマネージド | 완전 관리형 |
| File Server | ファイルサーバー | 파일 서버 |
| Active Directory | Active Directory | 액티브 디렉터리 |
| High Performance Computing | 高性能コンピューティング | 고성능 컴퓨팅 |
| Distributed File System | 分散ファイルシステム | 분산 파일 시스템 |
| Scratch | スクラッチ | 임시 파일 시스템 |
| Persistent | 永続 | 영구 파일 시스템 |
| Deduplication | 重複排除 | 중복 제거 |
| Compression | 圧縮 | 압축 |
| Snapshot | スナップショット | 스냅샷 |
| Replication | レプリケーション | 복제 |
| Workload | ワークロード | 워크로드 |

---

## 📝 Review Questions

<details>
<summary>Q1. Windows, SMB, Microsoft Active Directory가 필요한 File System은?</summary>

FSx for Windows File Server입니다.

</details>

<details>
<summary>Q2. HPC와 Machine Learning을 위한 고성능 File System은?</summary>

FSx for Lustre입니다.

</details>

<details>
<summary>Q3. S3의 대규모 Dataset을 고성능으로 처리해야 한다면?</summary>

Amazon S3와 FSx for Lustre를 연동할 수 있습니다.

</details>

<details>
<summary>Q4. Lustre의 Scratch와 Persistent의 핵심 차이는?</summary>

Scratch는 임시 데이터와 높은 성능을 중시하며 데이터가 복제되지 않습니다. Persistent는 장기 사용과 안정성을 위해 데이터를 복제합니다.

</details>

<details>
<summary>Q5. NFS, SMB, iSCSI를 모두 지원하며 기존 NetApp 환경의 Migration에 적합한 것은?</summary>

FSx for NetApp ONTAP입니다.

</details>

<details>
<summary>Q6. 기존 ZFS Workload를 AWS로 Migration하려면?</summary>

FSx for OpenZFS를 사용할 수 있습니다.

</details>
