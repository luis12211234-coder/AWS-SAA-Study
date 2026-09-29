# S3 Object Lock & Glacier Vault Lock

## 🇰🇷 1. WORM이란?

WORM은 다음을 의미합니다.

```text
Write Once
Read Many
```

즉 데이터를 저장한 뒤 일정 조건에서 수정 또는 삭제하지 못하도록 보호하는 모델입니다.

```text
Write
 ↓
Lock 🔒
 ↓
Read
Read
Read
```

S3에서 관련된 대표 기능은:

```text
Glacier Vault Lock
S3 Object Lock
```

입니다.

---

## 2. Glacier Vault Lock

Glacier Vault Lock은 Glacier Vault에 Vault Lock Policy를 적용하여 WORM 형태의 보존 정책을 강제하는 기능입니다.

```text
Glacier Vault
      │
      └─ Vault Lock Policy 🔒
```

정책을 최종적으로 Lock하면 변경할 수 없는 보존 정책을 강제할 수 있습니다.

대표 사용 사례:

```text
Compliance
Regulatory Requirements
Long-Term Data Retention
```

즉:

> **Vault 전체에 강력한 보존 정책을 적용**

하는 개념입니다.

---

## 3. S3 Object Lock

S3 Object Lock은 특정 Object Version이 삭제되거나 덮어쓰이는 것을 일정 기간 보호합니다.

Object Lock을 사용하려면 Versioning이 필요합니다.

```text
Versioning
   ↓
Object Lock
```

보호 대상의 핵심은 **Object Version**입니다.

---

## 4. Retention Mode

S3 Object Lock에는 두 가지 주요 Retention Mode가 있습니다.

```text
Object Lock
│
├─ Compliance Mode
└─ Governance Mode
```

---

## 5. Compliance Mode

가장 강력한 보호 모드입니다.

Retention 기간 동안:

```text
Object Version 삭제 ❌
Overwrite ❌
Retention 기간 단축 ❌
Retention Mode 변경 ❌
```

관리자를 포함해 보호를 우회할 수 없습니다.

```text
Compliance
= 매우 강력한 규정 준수 보호
```

---

## 6. Governance Mode

Governance Mode에서는 일반 사용자는 Object Version을 삭제하거나 Retention 설정을 우회할 수 없습니다.

하지만 특별한 권한을 가진 사용자는 Governance Retention을 우회할 수 있습니다.

```text
Normal User
→ 삭제 ❌

Authorized Admin
→ 특별 권한으로 우회 가능
```

따라서 Compliance Mode보다 유연합니다.

---

## 7. Compliance vs Governance

```text
Compliance
→ 관리자도 우회 불가
→ 강력한 규정 준수

Governance
→ 일반 사용자는 우회 불가
→ 특별 권한 사용자는 우회 가능
```

SAA에서는 이 차이를 확실히 구분합니다.

---

## 8. Retention Period

Retention Mode에는 Object를 보호할 기간을 지정할 수 있습니다.

```text
Object Version
      ↓
Retention Period
      ↓
Protected
```

필요한 경우 Retention 기간을 연장할 수 있습니다.

---

## 9. Legal Hold

Legal Hold는 Retention Period와 별개로 Object Version을 보호할 수 있는 기능입니다.

```text
Object
 ↓
Legal Hold ON
 ↓
기간과 관계없이 보호
```

특정 종료 날짜를 미리 지정할 필요가 없습니다.

예:

```text
Legal Investigation
Litigation
Audit
```

필요한 권한을 가진 사용자가 Legal Hold를 설정하거나 제거할 수 있습니다.

따라서:

```text
Retention
= 기간 기반 보호

Legal Hold
= 명시적 해제 전까지 보호
```

로 구분합니다.

---

## 10. Vault Lock vs Object Lock

```text
Glacier Vault Lock
→ Vault 수준 Policy
→ 강력한 WORM / Compliance

S3 Object Lock
→ Object Version 수준 보호
→ Compliance / Governance / Legal Hold
```

---

## 🎯 Exam Scenario

```text
WORM + Glacier Vault
→ Glacier Vault Lock

S3 Object Version + 관리자도 삭제 불가
→ Object Lock Compliance Mode

일반 사용자는 삭제 불가 + 일부 관리자 우회 가능
→ Governance Mode

법적 조사 종료 시까지 무기한 보호
→ Legal Hold
```

---

## 🔑 핵심 정리

```text
WORM
= Write Once Read Many

Glacier Vault Lock
= Vault 전체 보존 Policy

S3 Object Lock
= Object Version 보호

Compliance
= 누구도 우회 불가

Governance
= 특별 권한 사용자는 우회 가능

Legal Hold
= 기간 없이 명시적 해제까지 보호
```

한 줄 정리:

> **Vault 전체의 WORM 정책은 Glacier Vault Lock, S3 Object Version 보호는 Object Lock**

---

## 🇯🇵 日本語 Summary

Glacier Vault LockとS3 Object Lockは、WORMモデルによるデータ保護に使用されます。

Glacier Vault LockはVaultレベルのRetention Policyを強制します。

S3 Object LockはObject Versionを保護し、Compliance ModeとGovernance Modeがあります。

Legal Holdを使用すると、Retention Periodとは別にObject Versionを保護できます。

---

## 🇺🇸 English Summary

Glacier Vault Lock and S3 Object Lock provide WORM-style data protection.

Glacier Vault Lock enforces a retention policy at the vault level.

S3 Object Lock protects individual object versions using Compliance or Governance retention modes.

Legal Hold protects an object version independently of a retention period.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| WORM | Write Once Read Many | 한 번 쓰고 여러 번 읽기 |
| Vault Lock | ボールトロック | 볼트 잠금 |
| Object Lock | オブジェクトロック | 객체 잠금 |
| Compliance Mode | コンプライアンスモード | 규정 준수 모드 |
| Governance Mode | ガバナンスモード | 거버넌스 모드 |
| Retention Period | 保持期間 | 보존 기간 |
| Legal Hold | リーガルホールド | 법적 보존 |

---

## 📝 Review Questions

<details>
<summary>Q1. WORM은 무엇의 약자인가?</summary>

Write Once Read Many입니다.

</details>

<details>
<summary>Q2. 관리자를 포함해 Retention을 우회할 수 없는 Object Lock Mode는?</summary>

Compliance Mode입니다.

</details>

<details>
<summary>Q3. 특별한 권한을 가진 관리자가 Retention을 우회할 수 있는 Mode는?</summary>

Governance Mode입니다.

</details>

<details>
<summary>Q4. 종료 날짜 없이 법적 조사 기간 동안 Object를 보호하려면?</summary>

Legal Hold를 사용합니다.

</details>

<details>
<summary>Q5. Glacier Vault 전체에 WORM 보존 정책을 적용하려면?</summary>

Glacier Vault Lock을 사용합니다.

</details>
