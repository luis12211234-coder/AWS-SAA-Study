# IAM Policies

## Overview

IAM Policy는 사용자(User), 그룹(Group), 또는 역할(Role)에 권한을 부여하는 JSON 형식의 정책 문서이다.

AWS는 관리형 정책(Managed Policy)과 사용자 정의 정책(Custom Policy)을 제공한다.

---

## Policy Types

### Managed Policy

AWS에서 제공하거나 사용자가 생성한 정책이다.

여러 사용자, 그룹, 역할에서 재사용할 수 있다.

### Inline Policy

특정 사용자, 그룹 또는 역할에만 연결되는 정책이다.

재사용할 수 없다.

---

## Permission Inheritance

사용자는 여러 정책을 동시에 가질 수 있다.

예를 들어

- AdministratorAccess
- IAMReadOnlyAccess
- Developers Group Policy

모든 정책이 함께 적용된다.

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

시험에서는 다음 네 가지가 가장 중요하다.

- Effect
- Principal
- Action
- Resource

---

## Effect

권한의 동작을 정의한다.

- Allow
- Deny

---

## Principal

정책이 적용될 대상이다.

예시

- User
- Account
- Role

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
- Managed Policy와 Inline Policy가 있다.
- Effect, Principal, Action, Resource가 핵심이다.
- *는 모든 대상을 의미한다.
- ReadOnlyAccess는 조회만 가능하다.
- Custom Policy를 생성할 수 있다.

---

## Exam Notes

- Policy는 JSON 문서이다.
- Allow와 Deny를 사용한다.
- Action은 API 호출이다.
- Resource는 대상 리소스이다.
- Action "*"은 모든 작업을 의미한다.
- Resource "*"은 모든 리소스를 의미한다.
- Get*와 List*는 Wildcard이다.
- 시험에서는 Effect / Principal / Action / Resource를 기억한다.

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

Managed Policyは再利用できる。

Inline Policyは特定のユーザーやグループだけに適用される。

重要な要素

- Effect
- Principal
- Action
- Resource

---

## Summary

- IAM PolicyはJSON形式である。
- AllowとDenyを使用する。
- "*"はすべてを意味する。
- ReadOnlyAccessは読み取り専用である。

---

# 🇺🇸 English

## IAM Policies

IAM Policies are JSON documents that define permissions.

Managed Policies are reusable.

Inline Policies apply to only one identity.

The most important elements are:

- Effect
- Principal
- Action
- Resource

---

## Summary

- IAM Policies are written in JSON.
- Use Allow or Deny.
- "*" means all.
- ReadOnlyAccess allows read operations only.

---

## Vocabulary

| English | 한국어 | 日本語 |
|----------|---------|---------|
| Managed Policy | 관리형 정책 | 管理ポリシー |
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
3. Policy의 핵심 요소 네 가지는 무엇인가?
4. Action과 Resource의 차이점은 무엇인가?
5. `Action: "*"`의 의미는 무엇인가?
6. IAMReadOnlyAccess의 특징은 무엇인가?
