# 10. Amazon S3 - Introduction

Amazon S3(Simple Storage Service)의 기본 구조와 보안, 정적 웹사이트 호스팅, 버전 관리, 복제, 스토리지 클래스를 학습한 섹션입니다.

S3는 AWS의 대표적인 **Object Storage Service**로, 데이터를 Bucket 안에 Object 형태로 저장합니다.

---

## 📚 Contents

| No. | Topic | Description |
|---|---|---|
| 10-01 | [S3 Basics](./10-01-S3-Basics.md) | Bucket, Object, Key, Prefix, Multipart Upload |
| 10-02 | [S3 Security](./10-02-S3-Security.md) | IAM Policy, Bucket Policy, ARN, Block Public Access |
| 10-03 | [Static Website Hosting](./10-03-Static-Website-Hosting.md) | Website Endpoint, Index Document, Object URL |
| 10-04 | [S3 Versioning](./10-04-S3-Versioning.md) | Version ID, Null Version, Delete Marker |
| 10-05 | [S3 Replication](./10-05-S3-Replication.md) | CRR, SRR, Live Replication, Batch Replication |
| 10-06 | [S3 Storage Classes](./10-06-S3-Storage-Classes.md) | Standard, IA, Glacier, Intelligent-Tiering |

---

## 🧠 Key Concepts

```text
Amazon S3
│
├── Object Storage
│   └── Bucket → Object
│
├── Security
│   ├── IAM Policy
│   ├── Bucket Policy
│   └── Block Public Access
│
├── Static Website Hosting
│   └── Website Endpoint → index.html
│
├── Versioning
│   ├── Multiple Object Versions
│   └── Delete Marker
│
├── Replication
│   ├── CRR
│   ├── SRR
│   └── Batch Replication
│
└── Storage Classes
    ├── Standard
    ├── Standard-IA
    ├── One Zone-IA
    ├── Glacier
    └── Intelligent-Tiering
```

---

## 🔑 Quick Review

- **S3** = Object Storage
- **Bucket** = Object를 저장하는 Container
- **Object Key** = Object를 식별하는 전체 이름
- **Multipart Upload** = 대용량 Object를 여러 Part로 나누어 Upload
- **Bucket Policy** = Bucket에 적용하는 Resource-based Policy
- **Versioning** = 동일한 Key의 여러 Version을 보관
- **Delete Marker** = Versioning된 Object의 논리적 삭제 표시
- **CRR** = Cross-Region Replication
- **SRR** = Same-Region Replication
- **Batch Replication** = 기존 Object 등을 일괄 복제
- **Storage Class** = Access Pattern과 보관 목적에 따라 Object의 저장 방식을 선택
- **Intelligent-Tiering** = Access Pattern에 따라 Access Tier를 자동 조정

---

## 🇯🇵 日本語まとめ

Amazon S3 は AWS の代表的なオブジェクトストレージサービスです。

このセクションでは、S3 の基本構造、セキュリティ、静的ウェブサイトホスティング、バージョニング、レプリケーション、ストレージクラスについて学習しました。

特に、Versioning と Replication の関係や、アクセス頻度に応じた Storage Class の選択が重要です。

---

## 🇺🇸 English Summary

Amazon S3 is AWS's object storage service.

This section covers the fundamentals of S3, including buckets and objects, security, static website hosting, versioning, replication, and storage classes.

Key topics include protecting and recovering objects with versioning, replicating data between buckets, and selecting appropriate storage classes based on access patterns.
