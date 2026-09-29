# S3 MFA Delete

## 🇰🇷 1. MFA Delete란?

MFA Delete는 Versioning된 S3 Bucket에서 중요한 삭제 작업을 수행할 때 MFA 인증을 추가로 요구하는 보호 기능입니다.

```text
Important Delete Operation
        ↓
MFA Code Required
        ↓
Operation Allowed
```

---

## 2. Versioning이 필요하다

MFA Delete는 S3 Version을 보호하기 위한 기능이므로 Bucket Versioning이 활성화되어 있어야 합니다.

```text
Versioning
   ↓
MFA Delete
```

MFA Delete는 Object 하나에 별도의 Lock을 거는 기능이 아니라 **Versioning된 Bucket의 파괴적인 Version 작업을 보호하는 기능**입니다.

---

## 3. MFA가 필요한 작업

대표적으로 다음 작업에 MFA가 필요합니다.

```text
특정 Object Version 영구 삭제
Versioning 상태 변경
```

반대로 단순한 Version 조회 같은 작업에는 MFA가 필요하지 않습니다.

---

## 4. 일반 Delete와 영구 Delete

Versioning이 활성화된 Bucket에서 일반 Delete를 수행하면 Object가 즉시 완전히 사라지는 것이 아니라 Delete Marker가 생성됩니다.

```text
file.jpg

Delete Marker ← Current
v2
v1
```

하지만 특정 Version ID를 직접 삭제하면 해당 Version을 영구적으로 삭제할 수 있습니다.

```text
Delete v1
   ↓
Permanent Delete
   ↓
MFA Delete Protection
```

MFA Delete가 주로 보호하려는 부분이 바로 이러한 파괴적인 작업입니다.

---

## 5. Root Account

MFA Delete의 활성화 및 비활성화는 Bucket Owner의 Root Account가 수행합니다.

강의 실습에서는 AWS CLI와 Root MFA Device를 사용하여 설정했습니다.

```text
Root Account
+
MFA Device
+
AWS CLI
      ↓
Enable / Disable MFA Delete
```

일상적인 작업에서 Root Access Key를 계속 사용하는 것은 권장되지 않습니다.

---

## 🎯 Exam Scenario

다음과 같은 요구사항이면 MFA Delete를 생각합니다.

```text
Versioning Enabled
+
Prevent Permanent Version Deletion
+
Require MFA
```

---

## 🔑 핵심 정리

```text
MFA Delete
= Version 영구 삭제 보호

필수
= Versioning

일반 Delete
→ Delete Marker

특정 Version 영구 삭제
→ MFA 필요

Enable / Disable
→ Root Account
```

한 줄 정리:

> **Versioning된 S3에서 중요한 Version 삭제 작업에 MFA를 추가한다 = MFA Delete**

---

## 🇯🇵 日本語 Summary

MFA Deleteは、Versioningが有効なS3 Bucketで重要な削除操作を保護する機能です。

Object Versionを完全に削除する場合などにMFA認証を要求できます。

MFA Deleteを利用するにはVersioningが必要です。

---

## 🇺🇸 English Summary

MFA Delete adds MFA protection to destructive operations in a versioned S3 bucket.

It is primarily used to protect against permanent deletion of object versions and changes to versioning state.

Bucket Versioning must be enabled to use MFA Delete.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| MFA Delete | MFA削除 | MFA 삭제 |
| Versioning | バージョニング | 버저닝 |
| Object Version | オブジェクトバージョン | 객체 버전 |
| Delete Marker | 削除マーカー | 삭제 마커 |
| Permanent Delete | 完全削除 | 영구 삭제 |
| Root Account | ルートアカウント | 루트 계정 |

---

## 📝 Review Questions

<details>
<summary>Q1. MFA Delete를 사용하기 위한 필수 기능은?</summary>

S3 Versioning입니다.

</details>

<details>
<summary>Q2. Versioning된 Bucket에서 일반 Delete를 하면 무엇이 생성되는가?</summary>

Delete Marker가 생성됩니다.

</details>

<details>
<summary>Q3. MFA Delete의 핵심 목적은?</summary>

Object Version의 영구 삭제와 같은 파괴적인 작업을 MFA로 보호하는 것입니다.

</details>
