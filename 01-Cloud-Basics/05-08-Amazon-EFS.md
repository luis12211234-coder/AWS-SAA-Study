# Amazon EFS (Elastic File System)

## Overview

**Amazon EFS (Elastic File System)**는
AWS에서 제공하는 Managed NFS File System이다.

여러 Linux EC2 Instance가
동일한 File System을 동시에 Mount하여 사용할 수 있다.

```text
          Amazon EFS
        /     |      \
       /      |       \
    EC2-A   EC2-B    EC2-C
     AZ-A    AZ-B     AZ-C
```

핵심 특징:

```text
Managed NFS
Shared File System
Linux / POSIX
Multi-AZ
Automatic Scaling
Pay per Use
```

---

# 1. EFS Architecture

EFS는 Network File System이며
NFSv4.1 Protocol을 사용한다.

```text
EC2
↓
NFS
↓
EFS
```

여러 EC2 Instance가
동일한 File System을 동시에 사용할 수 있다.

대표적인 Use Case:

```text
Content Management
Web Serving
Data Sharing
WordPress
```

---

# 2. EFS vs EBS

EBS는 Block Storage이고
일반적으로 EC2 Instance 중심으로 사용한다.

EFS는 File Storage이며
여러 EC2 Instance가 공유할 수 있다.

| | EBS | EFS |
|---|---|---|
| Storage Type | Block Storage | File Storage |
| Protocol | Block Device | NFS |
| Multi EC2 | 제한적 | 가능 |
| Multi-AZ Access | X | O |
| Main OS | Linux / Windows | Linux |
| Capacity | Provision | Automatically Scales |

핵심:

```text
EBS
→ Block Storage

EFS
→ Shared Network File System
```

---

# 3. Linux and POSIX

EFS는 Linux 기반 AMI와 호환되며
POSIX File System을 제공한다.

```text
Linux
→ EFS

Windows
→ Not EFS
```

Windows Network File System이 필요하다면
Amazon FSx for Windows File Server와 같은 서비스를 고려한다.

---

# 4. Automatic Scaling

EFS는 File System의 용량을
미리 Provision할 필요가 없다.

```text
Data increases
↓
EFS automatically grows
```

사용한 Storage 용량에 따라 비용을 지불한다.

```text
No Capacity Planning
+
Pay per Use
```

---

# 5. Availability Options

## Regional

여러 Availability Zone에 걸쳐
EFS를 사용할 수 있다.

```text
AZ-A
AZ-B
AZ-C
 ↓
Same EFS
```

적합한 환경:

```text
Production
High Availability
High Durability
```

## One Zone

하나의 Availability Zone에만
File System을 배치한다.

```text
Single AZ
↓
Lower Cost
```

적합한 환경:

```text
Development
Cost-sensitive workloads
```

---

# 6. Mount Targets

Regional EFS는 VPC 내부의
Availability Zone마다 Mount Target을 구성할 수 있다.

```text
AZ-A
EC2
 ↓
Mount Target
 ↓
EFS
```

Mount Target은 EC2 Instance가
EFS에 접근하기 위한 Network Endpoint 역할을 한다.

---

# 7. Security Groups

EFS 접근은 Security Group으로 제어한다.

EFS는 NFS를 사용하므로:

```text
Protocol: NFS
Port: TCP 2049
```

가 필요하다.

대표적인 설정:

```text
EFS Security Group

Inbound:
NFS
TCP 2049
Source = EC2 Security Group
```

시험 포인트:

```text
EC2 cannot mount EFS
↓
Check Security Group
↓
TCP 2049
```

---

# 8. Encryption

EFS는 AWS KMS를 이용하여
Encryption at Rest를 활성화할 수 있다.

```text
EFS
↓
AWS KMS
↓
Encrypted Data at Rest
```

---

# 9. Performance Modes

EFS의 Performance Mode에는 다음 옵션이 있다.

## General Purpose

Default Mode이다.

```text
Low Latency
↓
Web Server
CMS
General Applications
```

## Max I/O

더 높은 Parallelism과 Throughput을 제공하지만
Latency가 증가할 수 있다.

```text
High Parallelism
High Throughput
Higher Latency
↓
Big Data
Media Processing
```

---

# 10. Throughput Modes

## Bursting

Storage 사용량을 기준으로
Throughput이 확장된다.

```text
More Storage
→ More Throughput
```

## Elastic

Workload에 따라 Throughput을
자동으로 확장하거나 축소한다.

```text
Unpredictable Workload
↓
Elastic Throughput
```

## Provisioned

필요한 Throughput을
직접 지정한다.

```text
Known Throughput Requirement
↓
Provisioned Throughput
```

시험 핵심:

```text
Unpredictable
→ Elastic

Known Requirement
→ Provisioned

Storage-based Throughput
→ Bursting
```

---

# 11. Storage Classes

EFS는 Access Frequency에 따라
여러 Storage Class를 제공한다.

## Standard

자주 접근하는 File.

```text
Frequently Accessed
→ Standard
```

## EFS-IA

자주 접근하지 않는 File.

```text
Infrequent Access
→ Lower Storage Cost
→ Retrieval Cost
```

## Archive

거의 접근하지 않는 File.

```text
Rarely Accessed
→ Archive
```

---

# 12. Lifecycle Management

Lifecycle Policy를 이용하면
파일의 Access Pattern에 따라 Storage Class를 자동 변경할 수 있다.

예:

```text
EFS Standard
↓
30 Days No Access
↓
EFS-IA
↓
90 Days No Access
↓
Archive
```

필요한 경우 다시 접근했을 때
Standard로 이동하도록 설정할 수도 있다.

---

# 13. Hands-On

이번 실습에서는 Regional EFS를 생성하고
서로 다른 Availability Zone의 EC2 Instance 두 개에 Mount했다.

```text
        Amazon EFS
         /      \
        /        \
  Instance A   Instance B
  eu-west-1a   eu-west-1b
```

EC2 Console에서 EFS를 Shared File System으로 추가하고:

```text
Mount Point
/mnt/efs/fs1
```

에 Mount했다.

Instance A에서:

```bash
sudo su
echo "hello world" > /mnt/efs/fs1/hello.txt
cat /mnt/efs/fs1/hello.txt
```

결과:

```text
hello world
```

Instance B에서도:

```bash
ls /mnt/efs/fs1
cat /mnt/efs/fs1/hello.txt
```

동일한 결과를 확인했다.

```text
hello.txt
hello world
```

즉 서로 다른 AZ의 두 EC2 Instance가
동일한 EFS File System을 공유한다는 것을 확인했다.

```text
Instance A
     \
      EFS
     /
Instance B
```

File Copy나 Synchronization이 아니라
두 Instance가 동일한 Shared File System을 Mount하고 있는 것이다.

---

# Exam Notes

가장 중요한 EFS 특징:

```text
Managed NFS
Linux
POSIX
Multiple EC2
Multi-AZ
Shared File System
Automatic Scaling
```

Network:

```text
EFS
→ NFS
→ TCP 2049
→ Security Group
```

Availability:

```text
Production
→ Regional

Development / Lower Cost
→ One Zone
```

Throughput:

```text
Unpredictable Workload
→ Elastic

Known Throughput
→ Provisioned

Storage-based Throughput
→ Bursting
```

Storage Classes:

```text
Frequently Accessed
→ Standard

Infrequent
→ IA

Rarely Accessed
→ Archive
```

---

# Summary

```text
Amazon EFS
= Managed NFS
= Shared Linux File System
= Multi-AZ
= Multiple EC2
```

핵심 암기:

```text
EBS
→ Block Storage

EFS
→ Shared File Storage
```

그리고:

```text
EFS
→ NFS TCP 2049
```

---

# Japanese Summary

**Amazon EFS (Elastic File System)**は、
AWSが提供するManaged NFS File Systemです。

複数のLinux EC2 Instanceから
同じFile Systemを同時にMountできます。

```text
Managed NFS
Linux / POSIX
Multiple EC2
Multi-AZ
Automatic Scaling
```

EFSへのアクセスにはSecurity Groupを使用し、
NFSのTCP 2049 Portを利用します。

```text
Production
→ Regional

Development
→ One Zone
```

Lifecycle Policyを利用して
Standard、IA、ArchiveなどのStorage Class間で
Fileを自動的に移動できます。

---

# English Summary

**Amazon EFS (Elastic File System)** is a managed NFS file system.

Multiple Linux EC2 instances can mount and access
the same file system simultaneously, even across Availability Zones.

```text
Managed NFS
Linux / POSIX
Multiple EC2
Multi-AZ
Automatic Scaling
```

EFS access is controlled using Security Groups,
and NFS uses TCP port 2049.

Regional EFS is suitable for production workloads,
while One Zone is a lower-cost option.

Lifecycle policies can automatically move files
between Standard, IA, and Archive storage classes.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Elastic File System | Elastic File System | 탄력적으로 확장되는 관리형 파일 시스템 |
| Network File System | ネットワークファイルシステム | 네트워크를 통해 사용하는 파일 시스템 |
| NFS | NFS | 네트워크 파일 시스템 프로토콜 |
| Mount | マウント | 파일 시스템을 OS에 연결하는 작업 |
| Mount Target | マウントターゲット | EFS에 접근하기 위한 네트워크 엔드포인트 |
| POSIX | POSIX | Unix 계열 운영체제의 표준 인터페이스 |
| Regional | リージョナル | 여러 AZ에 걸친 EFS 구성 |
| One Zone | ワンゾーン | 하나의 AZ에 배치되는 EFS 구성 |
| Lifecycle Policy | ライフサイクルポリシー | 파일을 스토리지 계층 간 자동 이동시키는 정책 |
| Infrequent Access | 低頻度アクセス | 자주 접근하지 않는 데이터 |
| Archive | アーカイブ | 거의 접근하지 않는 데이터용 저장 계층 |
| Bursting Throughput | バーストスループット | 저장량을 기반으로 확장되는 처리량 |
| Elastic Throughput | エラスティックスループット | 워크로드에 따라 자동 조절되는 처리량 |
| Provisioned Throughput | プロビジョンドスループット | 미리 지정한 처리량 |
| Shared File System | 共有ファイルシステム | 여러 서버가 함께 사용하는 파일 시스템 |

---

# Review Questions

### Q1. Amazon EFS란?

AWS가 제공하는 Managed NFS File System이다.

### Q2. 서로 다른 AZ의 여러 EC2가 같은 EFS를 사용할 수 있는가?

가능하다.

### Q3. EFS는 어떤 OS 계열과 주로 호환되는가?

```text
Linux
```

### Q4. EFS에서 사용하는 NFS Port는?

```text
TCP 2049
```

### Q5. Production 환경에서 적합한 EFS 유형은?

```text
Regional
```

### Q6. 예측하기 어려운 Workload에 적합한 Throughput Mode는?

```text
Elastic
```

### Q7. 자주 접근하지 않는 파일의 비용을 줄이기 위해 사용하는 기능은?

```text
EFS Storage Classes
+
Lifecycle Management
```