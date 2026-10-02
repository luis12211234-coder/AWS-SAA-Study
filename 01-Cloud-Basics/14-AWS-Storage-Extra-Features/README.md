# 14. AWS Storage Extra Features

이 섹션에서는 AWS의 다양한 Storage 및 Data Transfer 서비스를 학습합니다.

기존에 학습한 Amazon S3, EBS, EFS 등의 기본 Storage 서비스에서 확장하여 대용량 데이터 마이그레이션, 고성능 파일 시스템, Hybrid Storage, FTP 기반 전송 및 Storage 간 데이터 동기화 방법을 다룹니다.

---

## 📚 Contents

| No. | Topic | 핵심 내용 |
|---|---|---|
| 14-01 | AWS Snow Family | Offline Data Migration, Edge Computing |
| 14-02 | Amazon FSx | Managed High-Performance File Systems |
| 14-03 | AWS Storage Gateway | On-Premises와 AWS Storage 연결 |
| 14-04 | AWS Transfer Family | FTP / FTPS / SFTP → S3 / EFS |
| 14-05 | AWS DataSync | 대규모 데이터 복사 및 동기화 |
| 14-06 | Storage Comparison | AWS Storage 전체 비교 및 선택 |

---

# 🗺️ Storage 전체 구조

AWS Storage 서비스를 크게 보면 다음과 같이 구분할 수 있습니다.

```text
AWS Storage
│
├─ Object Storage
│   └─ Amazon S3
│       └─ Archive → S3 Glacier
│
├─ Block Storage
│   ├─ Amazon EBS
│   └─ EC2 Instance Store
│
└─ File Storage
    ├─ Amazon EFS
    │
    └─ Amazon FSx
        ├─ Windows File Server
        ├─ Lustre
        ├─ NetApp ONTAP
        └─ OpenZFS
```

그리고 Storage 자체가 아니라 **Storage를 연결하거나 데이터를 이동하기 위한 서비스**도 존재합니다.

```text
Storage Connection / Transfer
│
├─ Storage Gateway
│   └─ Hybrid Storage
│
├─ Transfer Family
│   └─ FTP / FTPS / SFTP
│
├─ DataSync
│   └─ Large-scale Data Transfer / Sync
│
└─ Snow Family
    └─ Offline Physical Data Transfer
```

---

# 🔑 Section 핵심

```text
Snow Family
→ 네트워크 대신 물리 장비로 대용량 데이터 이동
→ Edge Computing

Amazon FSx
→ 특정 고성능 File System을 AWS가 관리

Storage Gateway
→ On-Premises ↔ AWS Storage 연결

Transfer Family
→ FTP / FTPS / SFTP 인터페이스 제공

DataSync
→ 대규모 데이터 복사 및 동기화
→ Metadata / Permission 보존
```

---

# 🎯 Exam Perspective

이 섹션에서 중요한 것은 각 서비스의 세부 설정 방법보다 **주어진 요구사항에 적합한 Storage 또는 Data Transfer 서비스를 선택하는 것**입니다.

```text
Object Storage
→ S3

EC2 Block Storage
→ EBS

Linux Shared File System
→ EFS

Specialized File System
→ FSx

Hybrid Storage
→ Storage Gateway

FTP / SFTP
→ Transfer Family

Large Data Sync
→ DataSync

Offline Migration
→ Snow Family
```

AWS Solutions Architect는 각 Storage의 특성을 이해하고 Workload에 적절한 서비스를 선택할 수 있어야 합니다.