# IAM Roles for EC2

## Overview

EC2 Instance에서 AWS CLI 또는 AWS SDK를 사용하여 다른 AWS Service에 접근하려면 적절한 AWS 권한이 필요하다.

EC2 Instance 내부에 Access Key와 Secret Access Key를 직접 저장하는 방식은 보안상 좋지 않다.

대신 EC2 Instance에 **IAM Role을 연결하여 필요한 권한을 제공하는 방식**을 사용한다.

---

## EC2 Without an IAM Role

EC2 Instance에서 IAM Role 없이 AWS CLI 명령을 실행한다고 가정한다.

```bash
aws iam list-users
```

AWS CLI가 사용할 수 있는 자격 증명이 없기 때문에 요청을 수행할 수 없다.

```text
EC2 Instance
     │
     │ aws iam list-users
     ▼
AWS IAM
     │
     └─ Credentials 없음
        → 실패
```

---

## Do Not Store Access Keys on EC2

EC2 내부에서 다음 명령을 사용할 수 있다.

```bash
aws configure
```

이를 이용하면 다음과 같은 자격 증명을 설정할 수 있다.

```text
Access Key ID
Secret Access Key
Region
```

하지만 EC2 Instance에 IAM User의 Access Key와 Secret Access Key를 직접 저장하는 것은 권장되지 않는다.

```text
EC2 Instance
│
├─ Access Key ❌
└─ Secret Access Key ❌
```

Instance에 접근할 수 있는 사람이 저장된 자격 증명을 탈취할 위험이 있기 때문이다.

---

## Use IAM Roles Instead

EC2 Instance에 AWS 권한을 제공할 때는 IAM Role을 사용한다.

```text
IAM Role
   │
   │ Permissions
   ▼
EC2 Instance
   │
   │ AWS CLI / SDK
   ▼
AWS Services
```

IAM Role에는 Policy를 연결하여 EC2가 어떤 AWS API를 호출할 수 있는지 정의한다.

---

## Hands-On Example

강의에서는 다음 IAM Role을 EC2 Instance에 연결한다.

```text
DemoRoleForEC2
        │
        └─ IAMReadOnlyAccess
```

EC2 Console에서 다음과 같이 Role을 연결한다.

```text
EC2 Instance
↓
Actions
↓
Security
↓
Modify IAM Role
↓
DemoRoleForEC2
```

---

## AWS CLI with an IAM Role

Role을 연결한 후 다시 다음 명령을 실행한다.

```bash
aws iam list-users
```

이번에는 `aws configure`를 실행하지 않았음에도 요청이 성공한다.

```text
EC2
│
├─ Access Key 직접 설정 X
│
└─ IAM Role
      │
      └─ IAMReadOnlyAccess
             ↓
          AWS IAM
             ↓
        list-users 허용
```

즉, EC2 Instance는 연결된 IAM Role의 권한을 이용하여 AWS API를 호출할 수 있다.

---

## IAM Role Permissions

EC2에 IAM Role이 연결되어 있다고 해서 모든 AWS 작업을 수행할 수 있는 것은 아니다.

실제로 수행할 수 있는 작업은 **Role에 연결된 IAM Policy가 허용하는 권한**에 따라 결정된다.

강의에서는 `IAMReadOnlyAccess` Policy를 제거한 뒤 다음 명령을 다시 실행한다.

```bash
aws iam list-users
```

결과:

```text
Access Denied
```

다시 Policy를 연결하면 권한이 복원되고 명령을 실행할 수 있다.

```text
IAM Role
   │
   ├─ Policy 있음
   │     ↓
   │   Allowed
   │
   └─ Policy 없음
         ↓
       Denied
```

---

## IAM Permission Changes

IAM Policy를 추가하거나 제거한 뒤 변경 사항이 즉시 반영되지 않는 경우가 있을 수 있다.

강의에서는 Policy를 다시 연결한 직후 잠시 `Access Denied`가 발생했지만, 잠시 후 다시 실행했을 때 정상적으로 동작하는 것을 확인한다.

즉, IAM 변경 사항이 AWS 시스템에 반영되는 데 약간의 시간이 걸릴 수 있다.

---

## EC2 IAM Role Flow

전체 구조를 단순화하면 다음과 같다.

```text
IAM Policy
     ↓
IAM Role
     ↓
EC2 Instance
     ↓
AWS CLI / SDK
     ↓
AWS API
     ↓
AWS Service
```

예를 들어:

```text
EC2
↓
IAM Role
↓
S3 Read Permission
↓
Amazon S3
```

와 같은 구조로 사용할 수 있다.

---

## Why IAM Roles?

IAM Role을 사용하면 EC2 Instance에 장기 Access Key를 직접 저장하지 않고 AWS Service에 필요한 권한을 제공할 수 있다.

따라서 다음 방식보다 안전한 접근 방식이다.

```text
❌ EC2
   └─ aws configure
      ├─ Access Key
      └─ Secret Access Key
```

대신:

```text
✅ EC2
   └─ IAM Role
      └─ Required Permissions
```

을 사용한다.

---

## Summary

- EC2에서 AWS API를 호출하려면 AWS 권한이 필요하다.
- EC2 내부에 IAM User의 Access Key와 Secret Access Key를 직접 저장하지 않는다.
- EC2 Instance에는 IAM Role을 연결하여 권한을 제공한다.
- IAM Role에 연결된 Policy가 EC2의 AWS API 권한을 결정한다.
- Role의 권한을 제거하면 해당 API 요청이 `Access Denied`될 수 있다.
- IAM 변경 사항이 반영되는 데 약간의 시간이 걸릴 수 있다.

---

## Exam Notes

```text
EC2 needs AWS permissions?
→ IAM Role
```

- EC2에 AWS 권한 제공 → IAM Role
- EC2 내부에 Access Key / Secret Access Key 직접 저장 → 피해야 함
- IAM Role → Policy를 통해 권한 부여
- EC2 → Role의 권한으로 AWS API 호출
- Policy에서 허용하지 않은 작업 → Access Denied

핵심 구조:

```text
Policy
↓
IAM Role
↓
EC2
↓
AWS Service
```

---

## Practical Example

EC2 Application이 Amazon S3의 데이터를 읽어야 한다고 가정한다.

잘못된 방식:

```text
EC2
↓
Hard-coded Access Key
↓
Amazon S3
```

권장 방식:

```text
S3 Read Permission
↓
IAM Role
↓
EC2
↓
Amazon S3
```

EC2 Instance에는 필요한 S3 권한을 가진 IAM Role을 연결한다.

---

# 🇯🇵 日本語

## IAM Roles for EC2

EC2 InstanceからAWS Serviceへアクセスする場合、IAM Roleを利用して必要な権限を付与できる。

EC2 Instance内にAccess KeyやSecret Access Keyを直接保存する方法は避ける。

```text
IAM Policy
↓
IAM Role
↓
EC2 Instance
↓
AWS Service
```

IAM Roleに接続されたPolicyによって、EC2 Instanceが実行できるAWS API Actionが決まる。

### Summary

- EC2にはIAM Roleを利用してAWS権限を付与する。
- Access KeyをEC2に直接保存しない。
- IAM PolicyがRoleの権限を定義する。
- 必要な権限がない場合はAccess Deniedになる。

---

# 🇺🇸 English

## IAM Roles for EC2

IAM Roles should be used to provide AWS permissions to EC2 instances.

Avoid storing IAM user access keys and secret access keys directly on an EC2 instance.

```text
IAM Policy
↓
IAM Role
↓
EC2 Instance
↓
AWS Service
```

The policies attached to the IAM Role determine which AWS API actions the EC2 instance can perform.

### Summary

- Use IAM Roles to provide AWS permissions to EC2.
- Do not store long-term access keys directly on EC2.
- IAM Policies define the permissions of the Role.
- Missing permissions can result in Access Denied.

---

## Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| IAM Role | IAM 역할 | IAMロール |
| IAM Policy | IAM 정책 | IAMポリシー |
| Permission | 권한 | 権限 |
| Access Key | 액세스 키 | アクセスキー |
| Secret Access Key | 비밀 액세스 키 | シークレットアクセスキー |
| Credentials | 자격 증명 | 認証情報 |
| AWS CLI | AWS 명령줄 인터페이스 | AWS CLI |
| Access Denied | 접근 거부 | アクセス拒否 |
| Attach | 연결 | アタッチ |
| API | 애플리케이션 프로그래밍 인터페이스 | API |

---

## Review Questions

1. EC2 Instance에서 AWS Service에 접근하기 위해 권한이 필요할 때 무엇을 사용하는가?
2. EC2 내부에서 `aws configure`로 IAM User의 Access Key를 저장하는 방식이 권장되지 않는 이유는 무엇인가?
3. IAM Role에 연결된 Policy는 어떤 역할을 하는가?
4. IAM Role을 EC2에 연결한 후 AWS CLI가 AWS API를 호출할 수 있는 이유는 무엇인가?
5. Role에서 필요한 Permission을 제거하면 어떤 결과가 발생할 수 있는가?
6. IAM Policy 변경 직후 일시적으로 이전 권한 상태가 보일 수 있는 이유는 무엇인가?
