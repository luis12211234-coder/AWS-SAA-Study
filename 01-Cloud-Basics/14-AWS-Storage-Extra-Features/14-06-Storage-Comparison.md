# 14-06. AWS Storage Comparison

## 🇰🇷 1. AWS Storage 전체 비교

AWS Solutions Architect에게 중요한 것은 모든 Storage의 세부 구현을 외우는 것이 아니라 **Workload의 요구사항에 따라 적절한 Storage를 선택하는 것**입니다.

---

# 2. Object / Block / File Storage

```text
AWS Storage
│
├─ Object
│   └─ Amazon S3
│
├─ Block
│   ├─ Amazon EBS
│   └─ EC2 Instance Store
│
└─ File
    ├─ Amazon EFS
    └─ Amazon FSx
```

---

# 3. Amazon S3

```text
Amazon S3
= Object Storage
```

대표적인 사용 사례:

- Object Storage
- Backup
- Static Content
- Data Storage

장기 Archive가 필요한 경우 S3 Glacier Storage Class를 사용할 수 있습니다.

```text
S3
 ↓
Archive
 ↓
S3 Glacier
```

---

# 4. Amazon EBS

EC2 Instance에 연결하는 Network Block Storage입니다.

```text
EC2
 │
 ↓
EBS
```

일반적으로 하나의 EC2 Instance에 연결하여 사용합니다.

일부 Provisioned IOPS Volume에서는 Multi-Attach 기능도 지원됩니다.

---

# 5. EC2 Instance Store

EC2 Host에 직접 연결된 Physical Storage입니다.

```text
EC2
 │
 └─ Local Physical Storage
```

Network Storage가 아닌 매우 높은 I/O 성능의 Local Storage가 필요한 경우 사용할 수 있습니다.

---

# 6. Amazon EFS

Linux 기반의 Shared Network File System입니다.

```text
             EFS
          ↗   ↑   ↖
       EC2   EC2   EC2
```

주요 특징:

```text
NFS
POSIX
Shared File System
Multi-AZ
```

시험 키워드:

```text
Linux
+
Shared File System
+
Multi-AZ
→ Amazon EFS
```

---

# 7. Amazon FSx

특정 Workload를 위한 Managed File System입니다.

| Service | 대표 키워드 |
|---|---|
| FSx for Windows File Server | Windows, SMB, NTFS, Active Directory |
| FSx for Lustre | HPC, ML, High Performance, S3 Integration |
| FSx for NetApp ONTAP | NFS, SMB, iSCSI, Enterprise Storage |
| FSx for OpenZFS | Managed ZFS, NFS |

---

# 8. Storage Connection / Migration Services

Storage 자체 외에도 Storage를 연결하거나 데이터를 이동하기 위한 서비스가 있습니다.

```text
Connection / Migration
│
├─ Storage Gateway
├─ Transfer Family
├─ DataSync
└─ Snow Family
```

---

# 9. Storage Gateway

```text
On-Premises
     ↕
Storage Gateway
     ↕
AWS Storage
```

핵심:

> Hybrid Storage

| Gateway | 핵심 |
|---|---|
| S3 File Gateway | NFS/SMB → S3 |
| FSx File Gateway | SMB → FSx Windows + Cache |
| Volume Gateway | iSCSI Block Storage |
| Tape Gateway | VTL / Tape Backup |

---

# 10. AWS Transfer Family

```text
FTP / FTPS / SFTP
        ↓
Transfer Family
        ↓
     S3 / EFS
```

기존 FTP 계열 Application에서 AWS Storage를 사용해야 하는 경우 적합합니다.

---

# 11. AWS DataSync

```text
Storage A
    ↓
DataSync
    ↓
Storage B
```

대량의 데이터를 복사하거나 동기화할 때 사용합니다.

주요 특징:

```text
Large-scale Transfer
Scheduled Sync
Metadata Preservation
Permission Preservation
```

---

# 12. AWS Snow Family

Network Capacity가 부족하여 대량 데이터를 온라인으로 전송하기 어려운 경우 Physical Device를 이용합니다.

```text
On-Premises
     ↓
Snow Device 📦
     ↓
Physical Transport
     ↓
AWS
```

```text
Snowcone
Snowball Edge
```

또한 Edge Computing에도 사용할 수 있습니다.

---

# 13. 서비스 선택표 ⭐

| 요구사항 | AWS Service |
|---|---|
| Object Storage | Amazon S3 |
| Archive | S3 Glacier |
| EC2 Network Block Storage | Amazon EBS |
| EC2 Local High-Performance Storage | Instance Store |
| Linux/POSIX Shared File System | Amazon EFS |
| Windows / SMB / AD | FSx for Windows File Server |
| HPC / ML / High Performance File System | FSx for Lustre |
| NetApp / NFS + SMB + iSCSI | FSx for NetApp ONTAP |
| Managed ZFS | FSx for OpenZFS |
| On-Prem ↔ AWS Hybrid Storage | Storage Gateway |
| FTP / FTPS / SFTP → S3/EFS | AWS Transfer Family |
| Large-scale Data Sync / Migration | AWS DataSync |
| Metadata / Permission 보존 Migration | AWS DataSync |
| Offline Large-scale Migration | AWS Snow Family |

---

# 14. 시험 문제 접근법

문제를 읽고 먼저 **Storage Type**을 찾습니다.

```text
Object?
Block?
File?
```

File이라면:

```text
Linux Shared?
→ EFS

Windows?
→ FSx Windows

HPC / ML?
→ Lustre

NetApp?
→ ONTAP

ZFS?
→ OpenZFS
```

Storage 자체가 아니라 Migration/Connection 문제라면:

```text
Hybrid Storage?
→ Storage Gateway

FTP / SFTP?
→ Transfer Family

Large Data Sync?
→ DataSync

Network 부족?
→ Snow Family
```

---

# 🔥 Final Mental Map

```text
                    AWS STORAGE
                         │
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
      Object           Block             File
        │                │                │
       S3          ┌─────┴─────┐     ┌───┴────┐
        │          ↓           ↓     ↓        ↓
     Glacier      EBS     Instance  EFS      FSx
                           Store             │
                                      ┌──────┼──────┐
                                      ↓      ↓      ↓
                                   Windows Lustre ONTAP
                                                    │
                                                 OpenZFS
```

그리고:

```text
              DATA CONNECTION / TRANSFER
                         │
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
 Storage Gateway     Transfer Family    DataSync
 Hybrid Storage       FTP/SFTP         Data Move
                                           │
                                           ↓
                                      Snow Family
                                     Offline Move
```

---

## 🇯🇵 日本語 Summary

AWSにはObject、Block、Fileなど、さまざまなストレージサービスがあります。

Amazon S3はObject Storage、Amazon EBSはEC2向けのBlock Storage、Amazon EFSはLinux向けの共有File Systemです。

Amazon FSxはWindows、Lustre、NetApp ONTAP、OpenZFSなどの特定のFile Systemをマネージドサービスとして提供します。

また、Storage GatewayはオンプレミスとAWSストレージを接続し、Transfer FamilyはFTP系プロトコルを提供します。

DataSyncは大量データのコピーと同期に使用し、ネットワークでの転送が困難な場合はSnow Familyを利用できます。

---

## 🇺🇸 English Summary

AWS provides multiple storage options for different workloads.

Amazon S3 provides object storage, Amazon EBS provides network block storage for EC2, and Amazon EFS provides a shared Linux file system.

Amazon FSx provides managed specialized file systems including Windows File Server, Lustre, NetApp ONTAP, and OpenZFS.

Storage Gateway connects on-premises environments with AWS storage.

Transfer Family provides FTP-based access to S3 and EFS.

DataSync performs large-scale data transfer and synchronization, while Snow Family provides physical devices for offline migration.

---

## 📝 Final Review Questions

<details>
<summary>Q1. 여러 Linux EC2에서 동시에 사용하는 Multi-AZ Shared File System은?</summary>

Amazon EFS

</details>

<details>
<summary>Q2. HPC 및 Machine Learning을 위한 고성능 File System은?</summary>

FSx for Lustre

</details>

<details>
<summary>Q3. 기존 SFTP Application을 유지하면서 S3에 데이터를 저장하려면?</summary>

AWS Transfer Family

</details>

<details>
<summary>Q4. On-Premises NFS의 대량 데이터를 Permission과 Metadata를 유지하면서 AWS로 Migration하려면?</summary>

AWS DataSync

</details>

<details>
<summary>Q5. 네트워크 대역폭이 부족하여 수백 TB의 데이터를 온라인으로 전송하기 어렵다면?</summary>

AWS Snow Family

</details>

<details>
<summary>Q6. On-Premises Application에서 NFS/SMB로 접근하지만 실제 데이터는 S3에 저장하고 싶다면?</summary>

S3 File Gateway

</details>

<details>
<summary>Q7. EC2에 매우 빠른 Local Physical Storage가 필요하다면?</summary>

EC2 Instance Store

</details>

<details>
<summary>Q8. Windows + SMB + Active Directory가 요구된다면?</summary>

FSx for Windows File Server

</details>