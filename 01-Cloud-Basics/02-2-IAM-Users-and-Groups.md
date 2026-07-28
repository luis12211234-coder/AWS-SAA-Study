# IAM Users and Groups

## Overview

IAM Users and Groups는 AWS 계정의 사용자를 생성하고 권한을 효율적으로 관리하기 위한 기능이다.

IAM User가 필요한 경우 사용자에게 직접 권한을 반복해서 부여하기보다, 일반적으로는 그룹(Group)에 권한을 부여한 후 사용자를 그룹에 추가하는 방식이 관리에 효율적이다.

사람의 AWS 접근에는 장기 자격 증명을 가진 IAM User보다 IAM Identity Center나 역할(Role)을 통한 임시 자격 증명을 우선하는 것이 현재 AWS 보안 권장 사항이다.

---

## Creating IAM Users

IAM은 Global Service이므로 특정 Region에 종속되지 않는다.

IAM User를 생성하면 모든 Region에서 동일한 계정을 사용할 수 있다.

Root User는 필요한 작업에만 사용해야 한다. 사람의 일상적인 접근에는 가능한 경우 임시 자격 증명을 사용하고, IAM User가 필요한 경우 MFA와 최소 권한을 적용한다.

---

## IAM Groups

IAM Group은 여러 사용자를 하나의 그룹으로 관리하기 위한 기능이다.

일반적인 예시

- Admin
- Developers
- Operations
- Auditors

특징

- 그룹에는 사용자(User)만 포함할 수 있다.
- 그룹 안에 다른 그룹은 포함할 수 없다.
- 한 사용자는 여러 그룹에 속할 수 있다.
- 그룹에 속하지 않는 사용자도 존재할 수 있다.

---

## Permission Inheritance

일반적으로 권한은 사용자보다 그룹에 부여한다.

사용자는 자신이 속한 그룹의 권한을 자동으로 상속받는다.

예를 들어

Admin Group

↓

AdministratorAccess

↓

Stephane

Stephane은 AdministratorAccess 권한을 자동으로 상속받는다.

---

## Direct Permission vs Group Permission

권한을 부여하는 방법은 두 가지가 있다.

### Group Permission

- 그룹에 정책을 연결
- 그룹의 모든 사용자가 권한을 상속

### Direct Permission

- 특정 사용자에게 직접 정책을 연결
- 해당 사용자에게만 적용

---

## Account Alias

AWS Account Alias를 생성하면 로그인 URL을 쉽게 기억할 수 있다.

예시

```
https://aws-account-alias.signin.aws.amazon.com/console
```

---

## Tags

Tag는 AWS 리소스에 추가하는 메타데이터이다.

예시

- Department
- Owner
- Environment

Tag는 리소스 관리와 검색을 쉽게 해준다.

---

## Summary

- IAM은 Global Service이다.
- Root User는 필요한 작업에만 사용한다.
- 사람의 접근에는 가능한 경우 임시 자격 증명을 사용한다.
- 권한은 그룹을 통해 관리하는 것이 효율적이다.
- 사용자는 그룹의 권한을 상속받는다.
- Account Alias는 로그인 URL을 간소화한다.
- Tag는 리소스 메타데이터이다.

---

## Exam Notes

- IAM은 Global Service이다.
- IAM User는 모든 Region에서 사용할 수 있다.
- 그룹에는 사용자만 포함할 수 있다.
- 그룹은 다른 그룹을 포함할 수 없다.
- 한 사용자는 여러 그룹에 속할 수 있다.
- 권한은 그룹을 통해 관리하는 것이 Best Practice이다.
- Account Alias는 로그인 URL을 단순화한다.
- Tag는 메타데이터이다.

---

## Practical Example

회사 조직

Admin Group

- Alice
- Bob

Developers Group

- Charles
- David

Admin Group에 AdministratorAccess 정책을 연결하면,

그룹에 속한 모든 사용자가 동일한 관리자 권한을 상속받는다.

새로운 관리자는 Admin Group에 추가하기만 하면 된다.

---

# 🇯🇵 日本語

## IAM Users & Groups

IAM UserはAWSを利用するユーザーである。

IAM Groupは複数のユーザーをまとめて管理するための機能である。

通常はグループに権限を付与し、ユーザーはその権限を継承する。

Root Userは必要な作業だけに使用する。人のアクセスには可能な限り一時的な認証情報を使用し、IAM Userが必要な場合はMFAと最小権限を適用する。

---

## Summary

- IAMはGlobal Serviceである。
- 権限はグループで管理する。
- ユーザーはグループの権限を継承する。
- Account AliasでログインURLを簡略化できる。

---

# 🇺🇸 English

## IAM Users & Groups

IAM Users represent individual users in an AWS account.

IAM Groups simplify permission management by assigning permissions to groups instead of individual users.

Users inherit permissions from the groups they belong to.

Use the Root User only for tasks that require it. Prefer temporary credentials for human access, and apply MFA and least privilege when an IAM User is required.

---

## Summary

- IAM is a Global Service.
- Manage permissions through groups.
- Users inherit group permissions.
- Account Alias simplifies the login URL.

---

## Vocabulary

| English | 한국어 | 日本語 |
|----------|---------|---------|
| Inherit | 상속받다 | 継承する |
| AdministratorAccess | 관리자 권한 | 管理者権限 |
| Account Alias | 계정 별칭 | アカウントエイリアス |
| Login URL | 로그인 URL | ログインURL |
| Tag | 태그 | タグ |
| Metadata | 메타데이터 | メタデータ |
| Permission Inheritance | 권한 상속 | 権限継承 |

---

## Review Questions

1. IAM은 Global Service인가?
2. Root User 대신 무엇을 사용하는 것이 권장되는가?
3. 그룹에 권한을 부여하는 이유는 무엇인가?
4. 사용자는 어떻게 권한을 상속받는가?
5. Account Alias의 역할은 무엇인가?
6. Tag의 용도는 무엇인가?
