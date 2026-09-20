# IAM Policies

## Overview

IAM Policy는 사용자(User), 그룹(Group), 또는 역할(Role)에 권한을 부여하는 JSON 형식의 정책 문서이다.

IAM의 Identity-based Policy에는 AWS Managed Policy, Customer Managed Policy, Inline Policy가 있다.

---

## Policy Types

### AWS Managed Policy

AWS가 생성하고 관리하는 독립형 정책이다.

여러 사용자, 그룹, 역할에서 재사용할 수 있다.

### Customer Managed Policy

사용자가 자신의 AWS 계정에서 생성하고 관리하는 독립형 정책이다.

여러 사용자, 그룹, 역할에 연결할 수 있으며 필요에 맞게 수정할 수 있다.

### Inline Policy

특정 사용자, 그룹 또는 역할에만 연결되는 정책이다.

하나의 Identity와 1:1 관계를 유지하므로 다른 Identity에서 재사용할 수 없다.

---

## Permission Inheritance

사용자는 여러 정책을 동시에 가질 수 있다.

예를 들어

- AdministratorAccess
- IAMReadOnlyAccess
- Developers Group Policy

요청과 관련된 모든 정책이 함께 평가된다.

기본적으로 요청은 암시적으로 거부되며, 관련 정책에 명시적 Allow가 있어야 허용된다. 명시적 Deny가 하나라도 있으면 Allow보다 우선한다.

---

## IAM Policy Structure

IAM Policy는 JSON 형식으로 작성된다.

주요 구성 요소

- Version
- Id (Optional)
- Statement
- Sid (Optional)
- Effect
- Principal
- Action
- Resource
- Condition (Optional)

Identity-based Policy에서 특히 중요한 요소는 다음과 같다.

- Effect
- Action
- Resource
- Condition (Optional)

Principal은 Resource-based Policy와 IAM Role의 Trust Policy에서 사용한다. IAM User, Group, Role에 연결하는 Identity-based Policy에는 Principal을 사용하지 않는다. 정책이 연결된 Identity가 암시적으로 Principal이 된다.

---

## Effect

권한의 동작을 정의한다.

- Allow
- Deny

---

## Principal

정책이 적용될 인증 주체이다.

예시

- User
- Account
- Role

Principal은 Resource-based Policy와 Trust Policy에서 사용하며 Identity-based Policy에서는 사용할 수 없다.

---

## Action

허용하거나 거부할 AWS API 호출이다.

예시

- iam:GetUser
- iam:ListUsers
- ec2:DescribeInstances

---

## Resource

Action이 적용될 AWS 리소스이다.

예시

- EC2 Instance
- S3 Bucket
- IAM User

---

## Wildcards

AWS는 * 문자를 사용할 수 있다.

예시

```
Action: "*"
```

모든 Action 허용

```
Resource: "*"
```

모든 Resource 허용

```
Get*
```

Get으로 시작하는 모든 API 허용

```
List*
```

List로 시작하는 모든 API 허용

---

## ReadOnlyAccess

IAMReadOnlyAccess 정책은

조회(Read)는 가능하지만

생성(Create), 수정(Update), 삭제(Delete)는 불가능하다.

---

## Custom Policy

사용자는 Visual Editor 또는 JSON Editor를 이용하여 직접 정책을 생성할 수 있다.

필요한 API만 선택하여 최소 권한 정책을 만들 수 있다.

---

## Summary

- IAM Policy는 JSON 형식이다.
- AWS Managed Policy, Customer Managed Policy, Inline Policy가 있다.
- Identity-based Policy에서는 Effect, Action, Resource와 선택적 Condition을 확인한다.
- Principal은 Resource-based Policy와 Trust Policy에서 사용한다.
- 명시적 Deny는 Allow보다 우선한다.
- *는 모든 대상을 의미한다.
- ReadOnlyAccess는 조회만 가능하다.
- Custom Policy를 생성할 수 있다.

---

## Exam Notes

- Policy는 JSON 문서이다.
- Allow와 Deny를 사용한다.
- 명시적 Deny는 명시적 Allow보다 우선한다.
- Action은 API 호출이다.
- Resource는 대상 리소스이다.
- Action "*"은 모든 작업을 의미한다.
- Resource "*"은 모든 리소스를 의미한다.
- Get*와 List*는 Wildcard이다.
- Identity-based Policy에는 Principal을 사용하지 않는다.
- Principal은 Resource-based Policy와 Trust Policy에서 사용한다.

---

## Practical Example

AdministratorAccess

```
Effect: Allow
Action: *
Resource: *
```

↓

모든 AWS 서비스와 리소스에 대한 전체 권한

---

IAMReadOnlyAccess

```
Action

Get*
List*
```

↓

조회만 가능

↓

생성, 수정, 삭제 불가

---

# 🇯🇵 日本語

## IAM Policy

IAM PolicyはJSON形式の権限設定ファイルである。

AWS Managed PolicyはAWSが管理し、Customer Managed Policyはユーザーが管理する。どちらも複数のIdentityで再利用できる。

Inline Policyは特定のユーザーやグループだけに適用される。

Identity-based Policyの主な要素

- Effect
- Action
- Resource
- Condition（任意）

PrincipalはResource-based PolicyとTrust Policyで使用し、Identity-based Policyでは使用しない。

---

## Summary

- IAM PolicyはJSON形式である。
- AllowとDenyを使用する。
- 明示的なDenyはAllowより優先される。
- "*"はすべてを意味する。
- ReadOnlyAccessは読み取り専用である。

---

# 🇺🇸 English

## IAM Policies

IAM Policies are JSON documents that define permissions.

AWS managed policies are created and managed by AWS. Customer managed policies are created and managed in your AWS account. Both are reusable.

Inline Policies apply to only one identity.

The main elements of an identity-based policy are:

- Effect
- Action
- Resource
- Condition (optional)

`Principal` is used in resource-based policies and role trust policies. It is not used in identity-based policies.

---

## Summary

- IAM Policies are written in JSON.
- Use Allow or Deny.
- An explicit Deny overrides an Allow.
- "*" means all.
- ReadOnlyAccess allows read operations only.

---

## Vocabulary

| English | 한국어 | 日本語 |
|----------|---------|---------|
| AWS Managed Policy | AWS 관리형 정책 | AWS管理ポリシー |
| Customer Managed Policy | 고객 관리형 정책 | カスタマー管理ポリシー |
| Inline Policy | 인라인 정책 | インラインポリシー |
| Effect | 효과 | Effect |
| Principal | 적용 대상 | プリンシパル |
| Action | 작업(API 호출) | アクション |
| Resource | 리소스 | リソース |
| Statement | 정책 문장 | ステートメント |
| Condition | 조건 | 条件 |
| Wildcard | 와일드카드 | ワイルドカード |
| ReadOnlyAccess | 읽기 전용 권한 | 読み取り専用権限 |

---

## Review Questions

1. Managed Policy와 Inline Policy의 차이점은 무엇인가?
2. IAM Policy는 어떤 형식으로 작성되는가?
3. Identity-based Policy에서 주로 확인해야 할 요소는 무엇인가?
4. Action과 Resource의 차이점은 무엇인가?
5. `Action: "*"`의 의미는 무엇인가?
6. IAMReadOnlyAccess의 특징은 무엇인가?
7. Principal은 어떤 Policy에서 사용하며, Identity-based Policy에서 사용할 수 있는가?
8. Allow와 명시적 Deny가 동시에 적용되면 어떤 결과가 발생하는가?
