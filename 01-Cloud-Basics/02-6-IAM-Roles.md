# IAM Roles

## Overview

IAM Role은 AWS 서비스나 애플리케이션 등이 AWS에서 작업을 수행할 수 있도록 권한을 부여하는 IAM 자격 증명이다.

IAM User가 일반적으로 실제 사람을 위해 생성되는 반면, IAM Role은 특정 주체가 필요할 때 맡아서 사용하도록 만들어진다.

대표적으로 EC2 Instance, Lambda Function, CloudFormation 등이 IAM Role을 사용할 수 있다.

---

## Why Use IAM Roles?

EC2 Instance나 Lambda Function 같은 AWS 서비스도 다른 AWS 서비스에 접근해야 할 수 있다.

예를 들어 EC2 Instance가 IAM 정보를 조회하려면 EC2용 IAM Role에 IAMReadOnlyAccess Policy를 연결할 수 있다.

AWS 서비스에 사용자의 Access Key를 직접 저장하는 대신 IAM Role을 연결하는 것이 권장된다.

---

## IAM User vs IAM Role

| 구분 | IAM User | IAM Role |
|---|---|---|
| 주요 대상 | 실제 사용자 | AWS 서비스 또는 Role을 맡는 주체 |
| 장기 자격 증명 | Password, Access Key 사용 가능 | 일반적으로 임시 자격 증명 사용 |
| 대표 예시 | 개발자, 관리자 | EC2, Lambda, CloudFormation |
| 권한 부여 | Policy 연결 | Policy 연결 |

---

## Trusted Entity

Trusted Entity는 해당 IAM Role을 맡을 수 있는 주체이다.

예를 들어 EC2용 Role에서는 EC2 서비스가 Trusted Entity가 된다.

Role을 생성할 때는 다음 두 가지를 구분해야 한다.

- 누가 이 Role을 맡을 수 있는가?
- Role을 맡은 주체가 무엇을 할 수 있는가?

---

## Permissions Policy

Permissions Policy는 Role을 맡은 주체가 수행할 수 있는 작업을 정의한다.

예시

- Role Name: DemoRoleForEC2
- Trusted Entity: EC2
- Permissions: IAMReadOnlyAccess

이 Role을 사용하는 EC2 Instance는 연결된 Policy의 범위 안에서 IAM 정보를 조회할 수 있다.

---

## Common IAM Roles

- EC2 Instance Role
- Lambda Function Role
- CloudFormation Role

각 AWS 서비스는 연결된 Role의 권한 범위 안에서만 작업을 수행할 수 있다.

---

## Summary

- IAM Role은 권한을 가진 IAM 자격 증명이다.
- IAM Role은 실제 사용자에게 고정되는 계정이 아니다.
- AWS 서비스는 IAM Role을 통해 다른 AWS 서비스에 접근할 수 있다.
- Role에는 Policy를 연결하여 권한을 부여한다.
- Trusted Entity는 Role을 맡을 수 있는 주체이다.

---

## Exam Notes

- AWS 서비스에 권한을 부여할 때 IAM Role을 사용한다.
- EC2 Instance에 개인 Access Key를 직접 저장하지 않는다.
- EC2 Instance가 AWS API를 호출해야 한다면 EC2 Role을 연결한다.
- Role도 Policy를 통해 권한을 얻는다.
- Trusted Entity는 Role을 맡을 수 있는 주체다.
- Role의 권한은 최소 권한 원칙에 따라 부여한다.
- EC2 Instance Role, Lambda Function Role, CloudFormation Role은 대표적인 예시다.

---

## Practical Example

EC2 Instance에서 S3 Bucket의 파일을 읽어야 한다고 가정한다.

잘못된 방법

- 개인 Access Key를 EC2 내부에 직접 저장
- Access Key 유출 위험 발생

권장 방법

- S3 읽기 권한이 있는 IAM Role 생성
- EC2 Instance에 Role 연결
- EC2가 임시 자격 증명으로 S3 Object 읽기

IAM Role을 사용하면 개인 Access Key를 서버에 직접 저장하지 않고 필요한 권한을 제공할 수 있다.

---

# 🇯🇵 日本語

## IAM Roleとは

IAM Roleは、AWSサービスなどに権限を付与するためのIAMアイデンティティである。

IAM Userは通常、人間のユーザーに対応する。一方、IAM RoleはEC2、Lambda、CloudFormationなどのAWSサービスで使用される。

---

## Trusted Entity

Trusted Entityは、そのRoleを引き受けることができる主体である。

EC2用のRoleでは、EC2サービスがTrusted Entityになる。RoleにPolicyをアタッチすることで、AWSサービスが実行できる操作を定義する。

---

## Summary

- AWSサービスにはIAM Roleを使用する。
- RoleにはPolicyで権限を付与する。
- Trusted EntityはRoleを引き受ける主体である。
- EC2に個人のAccess Keyを保存しない。

---

# 🇺🇸 English

## What Is an IAM Role?

An IAM Role is an IAM identity that provides permissions to a trusted entity.

AWS services such as EC2, Lambda, and CloudFormation can use IAM Roles to perform actions on your behalf.

---

## Trusted Entity and Permissions

A trusted entity is allowed to assume the Role. A permissions policy defines what the entity can do after assuming it.

---

## Summary

- Use IAM Roles to grant permissions to AWS services.
- Attach Policies to Roles.
- A trusted entity can assume a Role.
- Do not store personal Access Keys on EC2 Instances.
- Apply the Principle of Least Privilege.

---

## Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| IAM Role | IAM 역할 | IAMロール |
| Trusted Entity | 신뢰할 수 있는 주체 | 信頼されたエンティティ |
| Assume a Role | 역할을 맡다 | ロールを引き受ける |
| Permissions Policy | 권한 정책 | アクセス許可ポリシー |
| EC2 Instance Role | EC2 인스턴스 역할 | EC2インスタンスロール |
| Lambda Function Role | Lambda 함수 역할 | Lambda関数ロール |
| Temporary Credentials | 임시 자격 증명 | 一時的な認証情報 |
| On Your Behalf | 사용자를 대신하여 | ユーザーに代わって |
| Attach | 연결하다 | アタッチする |

---

## Review Questions

1. IAM User와 IAM Role의 주요 차이점은 무엇인가?
2. AWS 서비스에 권한을 부여할 때 무엇을 사용해야 하는가?
3. Trusted Entity란 무엇인가?
4. Permissions Policy는 무엇을 정의하는가?
5. EC2 Instance에 개인 Access Key를 저장하면 안 되는 이유는 무엇인가?
6. 대표적인 IAM Role에는 무엇이 있는가?
