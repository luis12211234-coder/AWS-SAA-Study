# EBS Encryption

## Overview

Amazon EBS는 AWS KMS를 사용하여
EBS Volume과 Snapshot을 암호화할 수 있다.

Encrypted EBS를 사용하면 다음 데이터가 암호화된다.

```text
Data at Rest
EC2 ↔ EBS Data Transfer
EBS Snapshots
Volumes Created from Encrypted Snapshots
```

Encryption / Decryption은 AWS가 백그라운드에서 처리하며
사용자에게 거의 투명하게 동작한다.

---

# 1. What Gets Encrypted

Encrypted EBS Volume을 사용하면:

```text
Encrypted EBS
↓
Encrypted Snapshot
↓
Encrypted EBS Volume
```

암호화 상태가 Snapshot과 새 Volume으로 이어진다.

또한 EC2 Instance와 EBS Volume 사이의 전송 데이터도 암호화된다.

```text
EC2
⇄
EBS

Encrypted in Transit
```

---

# 2. AWS KMS

EBS Encryption은 AWS KMS와 통합된다.

```text
EBS
↓
AWS KMS
↓
Encryption Key
```

강의에서는 AES-256 기반 암호화를 설명한다.

대표적인 AWS Managed Key:

```text
aws/ebs
```

암호화 및 복호화 과정은
AWS가 자동으로 처리한다.

---

# 3. Encrypting an Unencrypted EBS Volume

강의에서 사용하는 기본 흐름:

```text
Unencrypted EBS
↓
Create Snapshot
↓
Unencrypted Snapshot
↓
Copy Snapshot
+ Enable Encryption
+ Select KMS Key
↓
Encrypted Snapshot
↓
Create Volume
↓
Encrypted EBS
```

즉 기존 데이터를 Snapshot을 이용하여
암호화된 새 EBS Volume으로 변환할 수 있다.

---

# 4. Creating an Encrypted Volume from a Snapshot

강의에서는 다른 방법도 확인했다.

```text
Unencrypted Snapshot
↓
Create Volume
↓
Enable Encryption
↓
Select KMS Key
↓
Encrypted EBS
```

따라서 Snapshot에서 Volume을 생성할 때
Encryption을 활성화할 수도 있다.

---

# 5. Performance

EBS Encryption은 Encryption / Decryption을
백그라운드에서 처리한다.

강의에서는 Latency에 미치는 영향이 매우 작다고 설명한다.

```text
Encryption
→ Minimal Performance / Latency Impact
```

---

# 6. Hands-On Summary

이번 실습에서는 다음 흐름을 확인했다.

```text
Create Unencrypted EBS
↓
Create Snapshot
↓
Snapshot is Unencrypted
```

이후:

```text
Copy Snapshot
↓
Enable Encryption
↓
Select KMS Key
↓
Encrypted Snapshot
```

그리고:

```text
Encrypted Snapshot
↓
Create Volume
↓
Encrypted EBS
```

또한 Unencrypted Snapshot에서 Volume을 생성할 때
직접 Encryption을 활성화할 수도 있음을 확인했다.

---

# Exam Notes

Encrypted EBS를 사용하면:

```text
Data at Rest
→ Encrypted
```

```text
EC2 ↔ EBS
→ Encrypted in Transit
```

```text
Encrypted EBS
↓
Snapshot
↓
Encrypted Snapshot
```

```text
Encrypted Snapshot
↓
New Volume
↓
Encrypted EBS
```

기존 Unencrypted EBS를 암호화된 Volume으로 만들려면:

```text
Snapshot
↓
Encrypt / Copy
↓
Create Encrypted Volume
```

KMS 관련 핵심:

```text
EBS Encryption
→ AWS KMS
→ AES-256
```

---

# Summary

```text
EBS Encryption
= KMS-based Encryption
```

핵심:

- EBS 저장 데이터 암호화
- EC2와 EBS 사이 전송 데이터 암호화
- Encrypted EBS의 Snapshot도 암호화
- Encrypted Snapshot에서 만든 Volume도 암호화
- KMS Key 사용
- Encryption / Decryption은 AWS가 자동 처리

한 줄 암기:

```text
Encrypted EBS
→ Snapshot도 암호화
→ 새 Volume도 암호화
```

---

# Japanese Summary

**EBS Encryption**は、
AWS KMSを利用してEBS Volumeを暗号化する機能です。

暗号化される対象：

```text
保存データ
EC2とEBS間の転送データ
EBS Snapshot
Snapshotから作成したVolume
```

暗号化されていないEBSを暗号化する場合は、
Snapshotを利用して新しい暗号化Volumeを作成できます。

```text
Unencrypted EBS
↓
Snapshot
↓
Encrypt
↓
Encrypted Snapshot
↓
Encrypted EBS
```

Encryption / DecryptionはAWSによって自動的に処理されます。

---

# English Summary

**EBS Encryption** uses AWS KMS to encrypt EBS volumes.

The following are encrypted:

```text
Data at rest
Data between EC2 and EBS
EBS snapshots
Volumes created from encrypted snapshots
```

An unencrypted EBS volume can be converted into a new encrypted volume
by using a snapshot workflow.

```text
Unencrypted EBS
↓
Snapshot
↓
Encrypt
↓
Encrypted Snapshot
↓
Encrypted EBS
```

Encryption and decryption are handled automatically by AWS.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Encryption | 暗号化 | 데이터를 읽을 수 없도록 암호화하는 것 |
| Decryption | 復号 | 암호화된 데이터를 원래 상태로 복원하는 것 |
| Data at Rest | 保存時のデータ | 저장 중인 데이터 |
| Data in Transit | 転送中のデータ | 네트워크를 통해 전송 중인 데이터 |
| AWS KMS | AWS Key Management Service | AWS 암호화 키 관리 서비스 |
| Encryption Key | 暗号化キー | 데이터를 암호화하는 데 사용하는 키 |
| AES-256 | AES-256暗号化 | 256비트 AES 암호화 방식 |
| Encrypted Snapshot | 暗号化されたスナップショット | 암호화된 EBS 스냅샷 |
| Unencrypted Volume | 暗号化されていないボリューム | 암호화되지 않은 EBS 볼륨 |
| AWS Managed Key | AWS管理キー | AWS가 관리하는 KMS 키 |

---

# Review Questions

### Q1. Encrypted EBS Volume에서 암호화되는 데이터는?

```text
Data at Rest
EC2 ↔ EBS Data Transfer
Snapshots
Volumes Created from Encrypted Snapshots
```

### Q2. EBS Encryption에 사용되는 AWS 서비스는?

```text
AWS KMS
```

### Q3. 암호화된 EBS에서 Snapshot을 생성하면?

Snapshot도 암호화된다.

### Q4. 암호화된 Snapshot에서 Volume을 생성하면?

새 Volume도 암호화된다.

### Q5. 암호화되지 않은 EBS를 암호화하려면?

Snapshot을 생성한 뒤,
암호화하여 새 EBS Volume을 생성한다.

### Q6. EBS Encryption이 애플리케이션에서 직접 Encryption / Decryption을 처리해야 하는가?

아니다.

AWS가 백그라운드에서 처리한다.