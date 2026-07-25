# IAM (Identity and Access Management)

## Overview

IAM(Identity and Access Management)은 AWS에서 사용자(User), 그룹(Group), 권한(Permission)을 관리하는 서비스이다.

IAM은 Global Service이며, AWS 계정에 대한 접근 권한을 안전하게 관리할 수 있도록 지원한다.

---

## Root User

AWS 계정을 생성하면 Root User가 자동으로 생성된다.

Root User는 모든 권한을 가진 계정이며, 계정 생성 이후에는 가능한 사용하지 않는 것이 권장된다.

일반적인 작업은 IAM User를 생성하여 수행해야 하며, Root User 계정은 공유해서는 안 된다.

---

## IAM User

IAM User는 AWS 계정을 사용하는 한 명의 사용자를 의미한다.

예를 들어 조직의 직원 한 명이 하나의 IAM User가 된다.

---

## IAM Group

IAM Group은 여러 IAM User를 하나의 그룹으로 묶어 권한을 관리하는 기능이다.

예시

- Developers
- Operations
- Auditors

특징

- 그룹에는 사용자(User)만 포함할 수 있다.
- 그룹 안에 다른 그룹을 포함할 수 없다.
- 한 사용자는 여러 그룹에 속할 수 있다.
- 그룹에 속하지 않는 사용자도 생성할 수 있다.

---

## IAM Policy

IAM Policy는 사용자(User) 또는 그룹(Group)에 부여하는 권한 문서이다.

정책은 JSON 형식으로 작성되며, 어떤 AWS 서비스에 접근할 수 있는지 정의한다.

예를 들어 EC2, S3, CloudWatch 등에 대한 권한을 설정할 수 있다.

---

## Principle of Least Privilege

AWS는 최소 권한의 원칙(Principle of Least Privilege)을 따른다.

사용자에게 필요한 권한만 부여하여 보안 위험과 불필요한 비용 발생을 방지한다.

---

## Summary

- IAM은 사용자와 권한을 관리하는 Global Service이다.
- Root User는 계정 생성 시 자동 생성된다.
- 일반 작업은 IAM User를 사용한다.
- IAM Group은 여러 사용자를 관리하기 위한 기능이다.
- IAM Policy는 JSON 형식의 권한 문서이다.
- AWS는 최소 권한 원칙을 따른다.

---

## Exam Notes

- IAM은 Global Service이다.
- Root User는 계정 생성 시 자동 생성된다.
- Root User는 일상적인 작업에 사용하지 않는다.
- IAM Group에는 사용자만 포함할 수 있다.
- 그룹 안에 다른 그룹을 포함할 수 없다.
- 한 사용자는 여러 그룹에 속할 수 있다.
- IAM Policy는 JSON 형식의 권한 문서이다.
- AWS는 Principle of Least Privilege를 적용한다.

---

## Practical Example

회사에 다음과 같은 조직이 있다고 가정한다.

Developers

- Alice
- Bob
- Charles

Operations

- David
- Edward

Developers 그룹에 EC2와 S3 권한을 부여하면,

그룹에 속한 모든 개발자가 동일한 권한으로 AWS 서비스를 사용할 수 있다.

새로운 개발자가 입사하면 Developers 그룹에 추가하기만 하면 동일한 권한이 자동으로 적용된다.

---

# 🇯🇵 日本語

## IAMとは

IAM（Identity and Access Management）は、AWSのユーザー・グループ・権限を管理するGlobal Serviceである。

Root Userはアカウント作成時に自動生成される。

通常の作業ではIAM Userを利用し、Root Userは日常的に使用しない。

---

## IAM Group

- ユーザーのみ追加できる
- グループの中にグループは追加できない
- 1人のユーザーは複数のグループに所属できる

---

## IAM Policy

IAM PolicyはJSON形式の権限設定ファイルである。

必要最小限の権限（Least Privilege）を付与することが推奨される。

---

## Summary

- IAMはGlobal Serviceである。
- Root Userは通常使用しない。
- IAM PolicyはJSON形式で権限を管理する。
- 最小権限の原則を適用する。

---

# 🇺🇸 English

## What is IAM?

IAM (Identity and Access Management) is an AWS Global Service used to manage users, groups, and permissions.

The Root User is created automatically when an AWS account is created and should not be used for daily tasks.

---

## IAM Group

- Contains users only
- Cannot contain other groups
- A user can belong to multiple groups

---

## IAM Policy

IAM Policy is a JSON document that defines permissions for users or groups.

AWS follows the Principle of Least Privilege by granting only the permissions required.

---

## Summary

- IAM is a Global Service.
- Use IAM Users instead of the Root User.
- IAM Policies are written in JSON.
- Follow the Principle of Least Privilege.

---

## Vocabulary

| English | 한국어 | 日本語 |
|----------|---------|---------|
| Identity | 신원 | アイデンティティ |
| Access | 접근 | アクセス |
| Management | 관리 | 管理 |
| IAM | 신원 및 접근 관리 | IAM |
| Root User | 루트 사용자 | ルートユーザー |
| IAM User | IAM 사용자 | IAMユーザー |
| IAM Group | IAM 그룹 | IAMグループ |
| IAM Policy | IAM 정책 | IAMポリシー |
| Permission | 권한 | 権限 |
| JSON | JSON 형식 | JSON形式 |
| Principle of Least Privilege | 최소 권한 원칙 | 最小権限の原則 |

---

## Review Questions

1. IAM은 Global Service인가, Regional Service인가?

2. Root User는 언제 사용해야 하는가?

3. IAM User와 IAM Group의 차이점은 무엇인가?

4. IAM Group에는 무엇을 포함할 수 있는가?

5. IAM Policy는 무엇이며 어떤 형식으로 작성되는가?

6. Principle of Least Privilege란 무엇인가?
