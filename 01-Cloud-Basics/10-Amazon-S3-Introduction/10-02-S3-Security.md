# 10-02. Amazon S3 Security

## 1. S3 Security

S3 Bucket과 Object는 기본적으로 Private하게 관리하며, 필요한 대상에게만 접근 권한을 부여하는 것이 기본 원칙이다.

S3 접근 제어에서 중요한 요소는 다음과 같다.

```text
S3 Security
│
├── IAM Policy
├── Bucket Policy
├── Block Public Access
├── ACL
└── Encryption
```

이 중 접근 권한을 이해할 때 가장 중요한 것은 **IAM Policy와 Bucket Policy의 차이**이다.

---

## 2. IAM Policy

IAM Policy는 IAM User, Group, Role 등의 **Identity에 연결되는 Policy**이다.

즉 다음 질문에 답한다.

> 이 Identity는 어떤 AWS Resource에 어떤 작업을 할 수 있는가?

```text
IAM User / Role
       │
       │ IAM Policy
       ▼
      S3
```

예를 들어 EC2가 S3 Object를 읽어야 한다면 EC2에 IAM Role을 연결하고 해당 Role에 필요한 S3 Permission을 부여할 수 있다.

```text
EC2
 │
 │ IAM Role
 ▼
S3
```

Access Key를 EC2 내부에 직접 저장하는 것보다 IAM Role을 사용하는 것이 적절하다.

---

## 3. Bucket Policy

Bucket Policy는 S3 Bucket에 직접 연결하는 **Resource-based Policy**이다.

즉 다음 질문에 답한다.

> 이 Bucket에 누가 어떤 작업을 할 수 있는가?

예를 들어 Bucket 내부 Object를 Public Read로 허용하는 Policy는 다음과 같은 형태이다.

```json
{
  "Effect": "Allow",
  "Principal": "*",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::example-bucket/*"
}
```

각 요소의 의미는 다음과 같다.

| Element | 의미 |
|---|---|
| Effect | Allow 또는 Deny |
| Principal | 누가 접근하는가 |
| Action | 어떤 작업을 허용하는가 |
| Resource | 어떤 Resource에 적용하는가 |

이를 간단하게 기억하면:

```text
Principal → WHO?
Action    → WHAT?
Resource  → WHERE?
Effect    → ALLOW or DENY?
```

---

## 4. IAM Policy vs Bucket Policy

두 Policy의 가장 중요한 차이는 **어디에 붙어 있는가**이다.

```text
IAM Policy
= Identity-based Policy

Bucket Policy
= Resource-based Policy
```

구조로 보면:

```text
IAM User / Role
      │
      │ IAM Policy
      ▼
     S3 Bucket
      ▲
      │ Bucket Policy
      │
   Resource
```

### IAM Policy

```text
"이 User / Role은 무엇을 할 수 있는가?"
```

### Bucket Policy

```text
"이 Bucket에 누가 무엇을 할 수 있는가?"
```

---

## 5. Explicit Deny

AWS Permission 평가에서 중요한 원칙은 **Explicit Deny가 Allow보다 우선한다는 것**이다.

```text
Allow
+
Explicit Deny

↓

DENY
```

즉 어떤 Policy에서 Action을 Allow하고 있더라도 다른 적용 가능한 Policy에서 명시적으로 Deny한다면 요청은 거부된다.

```text
Explicit Deny
> Allow
```

시험에서도 자주 사용되는 기본 원칙이다.

---

## 6. ARN

ARN은 **Amazon Resource Name**의 약자이다.

AWS Resource를 고유하게 식별하기 위해 사용한다.

S3 Bucket의 ARN:

```text
arn:aws:s3:::my-bucket
```

특정 Object의 ARN:

```text
arn:aws:s3:::my-bucket/coffee.jpg
```

Bucket 내부 모든 Object:

```text
arn:aws:s3:::my-bucket/*
```

---

## 7. Bucket ARN vs Object ARN

S3 Permission에서는 **Bucket 자체에 대한 작업인지 Object에 대한 작업인지** 구분하는 것이 중요하다.

예를 들어:

```text
s3:ListBucket
```

은 Bucket의 Object 목록을 확인하는 작업이므로 Bucket 자체 ARN을 사용한다.

```text
arn:aws:s3:::my-bucket
```

반면:

```text
s3:GetObject
```

는 Object를 읽는 작업이므로 Object ARN을 사용한다.

```text
arn:aws:s3:::my-bucket/*
```

### 기억하기

```text
Bucket ARN
= 통 자체

arn:aws:s3:::my-bucket


Bucket ARN/*
= 통 안의 Object

arn:aws:s3:::my-bucket/*
```

---

## 8. Public Read Bucket Policy

Bucket 내부의 모든 Object를 누구나 읽을 수 있게 허용한다고 가정해 보자.

```json
{
  "Effect": "Allow",
  "Principal": "*",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

이를 해석하면:

```text
Effect
→ Allow

Principal: "*"
→ 모든 Principal

Action: s3:GetObject
→ Object 읽기

Resource: arn:aws:s3:::my-bucket/*
→ Bucket 내부 모든 Object
```

즉:

> 누구나 `my-bucket` 내부 Object를 읽을 수 있다.

라는 의미이다.

---

## 9. Block Public Access

S3에는 실수로 Bucket이나 Object가 Public 상태가 되는 것을 막기 위한 **Block Public Access** 기능이 있다.

```text
Block Public Access ON
        ↓
Public Access 차단
```

중요한 점은 Block Public Access를 OFF한다고 해서 자동으로 Public Permission이 생기는 것은 아니라는 것이다.

```text
Block Public Access OFF
        ↓
Public Access 차단 장치 해제
        ↓
아직 Public인 것은 아님
```

Public Read를 허용하려면 Bucket Policy 등에서 실제 Permission도 허용되어야 한다.

예:

```text
1. Block Public Access 설정 확인

            ↓

2. Bucket Policy에서 Public Read 허용

            ↓

3. Public Object Access 가능
```

따라서:

```text
Block Public Access OFF
≠
자동 Public
```

이다.

---

## 10. ACL

ACL은 **Access Control List**의 약자이다.

S3에서 Bucket이나 Object에 대한 접근 권한을 관리하는 오래된 방식 중 하나이다.

하지만 현대적인 S3 구성에서는 IAM Policy와 Bucket Policy를 이용한 Policy 기반 접근 제어가 일반적으로 권장된다.

새로운 S3 Bucket에서는 일반적으로 다음과 같은 구성이 사용된다.

```text
Object Ownership
→ Bucket owner enforced

ACL
→ Disabled
```

따라서 특별한 이유가 없다면 ACL보다는 Policy 기반 접근 제어를 우선적으로 고려한다.

---

## 11. Object Ownership

S3에서는 Object를 Upload한 AWS Account와 Bucket Owner가 서로 다를 수도 있다.

Object Ownership 기능은 이러한 Object의 소유권과 ACL 사용 방식을 관리한다.

대표적인 설정:

```text
Bucket owner enforced
```

이 설정에서는 ACL이 비활성화되고 Bucket Owner가 Bucket 내부 Object에 대한 소유권을 가진다.

```text
Bucket owner enforced
        ↓
ACL Disabled
        ↓
Policy 기반 Permission 관리
```

---

## 12. AWS Service와 IAM Role

IAM Role은 사람에게만 사용하는 것이 아니다.

AWS Service가 다른 AWS Resource에 접근할 때도 IAM Role을 사용할 수 있다.

예를 들어 EC2가 S3에 접근해야 한다면:

```text
EC2
 │
 │ Assume / Use IAM Role
 ▼
IAM Role
 │
 │ Permission
 ▼
S3
```

S3 Replication에서도 S3가 Source Bucket의 Object를 읽고 Destination Bucket에 Object를 생성할 Permission이 필요하다.

```text
Source S3
    │
    │
    ▼
IAM Role
    │
    │ Read / Write Permission
    ▼
Destination S3
```

따라서 IAM을 단순히:

```text
"사람 계정의 권한 관리"
```

라고 이해하면 부족하다.

보다 정확하게는:

```text
AWS에서
누가(Principal)
어떤 Resource에
어떤 Action을 수행할 수 있는가
```

를 관리하는 시스템이라고 이해하면 된다.

---

## 13. S3 Encryption

S3의 Permission과 Encryption은 서로 다른 보안 개념이다.

```text
Permission
→ 누가 Object에 접근할 수 있는가?

Encryption
→ 저장된 데이터를 어떻게 보호하는가?
```

따라서 Object가 암호화되어 있다고 해서 자동으로 Private이 되는 것도 아니고, Private Object라고 해서 Encryption 자체가 필요 없는 것도 아니다.

```text
Authorization
≠
Encryption
```

두 보안 계층을 별도로 생각해야 한다.

---

## 14. Presigned URL

Private S3 Object를 일정 시간 동안 특정 사용자에게 접근시키고 싶은 경우 **Presigned URL**을 사용할 수 있다.

```text
Private Object
      │
      │ Presigned URL
      ▼
Temporary Access
```

Presigned URL은 유효한 AWS Credential을 기반으로 서명된 URL이다.

이를 통해 Bucket이나 Object 자체를 Public으로 만들지 않고도 제한된 시간 동안 Object 접근을 허용할 수 있다.

```text
Public Object
→ 누구나 접근 가능

Private Object + Presigned URL
→ 서명된 URL을 가진 사용자가 제한된 시간 동안 접근
```

따라서 Private Object를 임시로 공유해야 할 때 유용하다.

---

# 핵심 정리

```text
S3 Security
│
├── IAM Policy
│   └── Identity-based
│
├── Bucket Policy
│   └── Resource-based
│
├── Block Public Access
│   └── Public 노출 방지 안전장치
│
├── ACL
│   └── 기존 Access Control 방식
│
└── Encryption
    └── 데이터 암호화


IAM Policy
→ "이 Identity는 무엇을 할 수 있는가?"

Bucket Policy
→ "이 Bucket에 누가 무엇을 할 수 있는가?"


Explicit Deny
> Allow


Bucket ARN
arn:aws:s3:::my-bucket
→ Bucket 자체

Object ARN
arn:aws:s3:::my-bucket/*
→ Bucket 내부 Object


Block Public Access OFF
≠ 자동 Public


IAM Role
→ User뿐 아니라 AWS Service에도 사용


Authorization
≠ Encryption


Presigned URL
→ Private Object에 임시 접근 제공
```

---

# 🇯🇵 日本語まとめ

Amazon S3 のアクセス制御では、IAM Policy と Bucket Policy の違いが重要です。

**IAM Policy** は User や Role などの Identity に設定する Identity-based Policy です。

**Bucket Policy** は S3 Bucket に設定する Resource-based Policy です。

```text
IAM Policy
→ この Identity は何ができるか？

Bucket Policy
→ この Bucket に誰が何をできるか？
```

また、Explicit Deny は Allow より優先されます。

Block Public Access は Bucket の意図しない公開を防ぐための安全機能です。Block Public Access を無効にしただけでは Bucket は自動的に Public にはなりません。

Private Object を一時的に共有する場合は Presigned URL を利用できます。

---

# 🇺🇸 English Summary

Amazon S3 provides multiple security mechanisms including IAM policies, bucket policies, Block Public Access, ACLs, and encryption.

An **IAM Policy** is an identity-based policy attached to identities such as IAM users and roles.

A **Bucket Policy** is a resource-based policy attached directly to an S3 bucket.

```text
IAM Policy
→ What can this identity do?

Bucket Policy
→ Who can access this bucket and what can they do?
```

An explicit Deny takes precedence over an Allow.

Block Public Access protects S3 resources from unintended public exposure. Disabling it does not automatically make a bucket public.

IAM roles can also be used by AWS services such as EC2 and S3 Replication.

Presigned URLs provide temporary access to private S3 objects without making the objects public.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| IAM Policy | IAMポリシー | IAM 정책 |
| Bucket Policy | バケットポリシー | 버킷 정책 |
| Identity-based Policy | アイデンティティベースポリシー | 자격 증명 기반 정책 |
| Resource-based Policy | リソースベースポリシー | 리소스 기반 정책 |
| Principal | プリンシパル | 권한 주체 |
| Action | アクション | 작업 |
| Resource | リソース | 리소스 |
| Explicit Deny | 明示的な拒否 | 명시적 거부 |
| ARN | Amazonリソースネーム | Amazon 리소스 이름 |
| Block Public Access | パブリックアクセスブロック | 퍼블릭 액세스 차단 |
| ACL | アクセスコントロールリスト | 액세스 제어 목록 |
| Object Ownership | オブジェクト所有権 | 객체 소유권 |
| Encryption | 暗号化 | 암호화 |
| Presigned URL | 署名付きURL | 사전 서명된 URL |

---

# Review Questions

<details>
<summary>1. IAM Policy와 Bucket Policy의 가장 큰 차이는?</summary>

IAM Policy는 User나 Role 등의 **Identity에 연결되는 Identity-based Policy**이다.

Bucket Policy는 S3 Bucket에 직접 연결되는 **Resource-based Policy**이다.

</details>

<details>
<summary>2. Allow와 Explicit Deny가 동시에 적용되면 어떻게 되는가?</summary>

Explicit Deny가 우선한다.

```text
Explicit Deny > Allow
```

</details>

<details>
<summary>3. s3:GetObject에 Bucket ARN 자체를 사용하면 되는가?</summary>

Object에 대한 Permission이므로 Object ARN이 필요하다.

```text
arn:aws:s3:::my-bucket/*
```

반면 `s3:ListBucket`은 Bucket 자체에 대한 Action이므로:

```text
arn:aws:s3:::my-bucket
```

을 사용한다.

</details>

<details>
<summary>4. Block Public Access를 OFF하면 Bucket이 자동으로 Public이 되는가?</summary>

아니다.

Public Access를 막는 안전장치를 해제하는 것이며, 실제 Public Permission은 Bucket Policy 등에서 별도로 허용되어야 한다.

</details>

<details>
<summary>5. IAM Role은 사람에게만 사용하는가?</summary>

아니다.

EC2와 같은 AWS Service 또는 Workload가 다른 AWS Resource에 접근할 때도 IAM Role을 사용할 수 있다.

S3 Replication 역시 Source와 Destination에 접근할 수 있는 적절한 IAM Permission이 필요하다.

</details>

<details>
<summary>6. Encryption과 Permission은 같은 개념인가?</summary>

아니다.

Permission은 누가 Resource에 접근할 수 있는지를 제어하고, Encryption은 데이터를 암호화하여 보호한다.

</details>

<details>
<summary>7. Private Object를 Public으로 변경하지 않고 임시 공유하려면?</summary>

Presigned URL을 사용할 수 있다.

서명된 URL을 통해 제한된 시간 동안 Private Object에 접근할 수 있다.

</details>
