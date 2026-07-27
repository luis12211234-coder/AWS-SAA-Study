# IAM Security Tools and Best Practices

## Overview

AWS에서는 IAM 환경을 점검하고 불필요한 권한을 줄이기 위해 IAM Credentials Report와 IAM Access Advisor를 제공한다.

이 도구를 활용하면 사용자 자격 증명 상태를 감사하고 실제 사용 기록을 바탕으로 IAM Policy를 조정할 수 있다.

---

## IAM Credentials Report

IAM Credentials Report는 AWS 계정에 존재하는 모든 사용자의 자격 증명 상태를 보여주는 계정 수준(Account-Level)의 보고서이다.

CSV 형식으로 다운로드할 수 있으며 다음 정보를 포함한다.

- 사용자 생성 시점
- 비밀번호 활성화 여부
- 비밀번호 마지막 사용 및 변경 시점
- MFA 활성화 여부
- Access Key 활성화 여부
- Access Key 마지막 교체 및 사용 시점

오랫동안 사용되지 않은 사용자나 Access Key, MFA가 설정되지 않은 사용자를 찾을 때 유용하다.

---

## IAM Access Advisor

IAM Access Advisor는 사용자에게 부여된 서비스 권한과 해당 서비스에 마지막으로 접근한 시점을 보여주는 사용자 수준(User-Level)의 도구이다.

이를 통해 다음 사항을 확인할 수 있다.

- 사용자에게 어떤 서비스 권한이 부여되었는가?
- 사용자가 해당 서비스를 마지막으로 사용한 시점은 언제인가?
- 사용하지 않는 권한이 있는가?
- Policy에서 제거할 수 있는 불필요한 권한이 있는가?

Access Advisor의 정보를 활용하면 최소 권한 원칙에 맞게 Policy를 수정할 수 있다.

---

## Credentials Report vs Access Advisor

| 구분 | Credentials Report | Access Advisor |
|---|---|---|
| 범위 | 계정 수준 | 사용자 수준 |
| 주요 대상 | 사용자 자격 증명 상태 | 서비스 접근 권한과 사용 기록 |
| 확인 내용 | Password, MFA, Access Key | 허용된 서비스와 마지막 접근 시점 |
| 주요 목적 | 자격 증명 감사 | 불필요한 권한 제거 |

---

## IAM Best Practices

- AWS 계정 설정 외에는 Root User를 사용하지 않는다.
- 한 명의 실제 사용자에게 하나의 IAM User를 생성한다.
- 사용자를 Group에 추가하고 Group에 Policy를 연결한다.
- 강력한 Password Policy와 MFA를 사용한다.
- AWS 서비스에는 개인 Access Key 대신 IAM Role을 사용한다.
- Access Key를 공유하거나 공개 저장소에 올리지 않는다.
- Credentials Report와 Access Advisor로 IAM 환경을 정기적으로 감사한다.
- 모든 권한은 최소 권한 원칙에 따라 부여한다.

---

## IAM Section Summary

| 구성 요소 | 역할 |
|---|---|
| IAM User | 실제 사용자와 연결되는 IAM 자격 증명 |
| IAM Group | 여러 사용자의 권한 관리 |
| IAM Policy | 권한을 정의하는 JSON 문서 |
| IAM Role | AWS 서비스 등에 권한 제공 |
| Password Policy | 비밀번호 보안 규칙 설정 |
| MFA | 추가 인증 요소로 계정 보호 |
| AWS CLI | 명령줄에서 AWS 서비스 관리 |
| AWS SDK | 애플리케이션 코드에서 AWS 서비스 관리 |
| Access Keys | CLI와 SDK의 프로그래밍 방식 접근 |
| Credentials Report | 계정 수준의 자격 증명 감사 |
| Access Advisor | 사용자 수준의 서비스 접근 감사 |

---

## Summary

- Credentials Report는 계정 전체 사용자의 자격 증명 상태를 보여준다.
- Access Advisor는 사용자에게 부여된 서비스 권한과 마지막 접근 시점을 보여준다.
- Root User는 일상적인 작업에 사용하지 않는다.
- 한 사람에게 하나의 IAM User를 생성한다.
- 사용자보다 Group을 중심으로 권한을 관리한다.
- AWS 서비스에는 IAM Role을 사용한다.
- IAM User와 Access Key를 공유하지 않는다.

---

## Exam Notes

- Credentials Report는 Account-Level이다.
- Access Advisor는 User-Level이다.
- Credentials Report는 Password, MFA, Access Key 상태를 확인한다.
- Access Advisor는 서비스 권한과 마지막 접근 시점을 확인한다.
- Access Advisor를 이용해 사용하지 않는 권한을 제거할 수 있다.
- Root User는 계정 설정 외에는 사용하지 않는다.
- One Physical User = One IAM User
- 사용자에게 직접 권한을 반복해서 부여하기보다 Group을 활용한다.
- AWS 서비스에는 IAM Role을 사용한다.
- IAM User와 Access Key는 절대 공유하지 않는다.

---

## Practical Example

Credentials Report에서 Alice의 비밀번호와 Access Key가 180일 이상 사용되지 않았고 MFA도 비활성화되어 있다고 가정한다.

보안 관리자는 Alice가 현재 근무 중인지 확인하고, 사용하지 않는 Access Key를 비활성화하거나 삭제하며 MFA 활성화를 요청할 수 있다.

Access Advisor에서 Alice가 S3만 사용하고 EC2는 사용한 적이 없다면 EC2 권한 제거도 검토할 수 있다.

---

# 🇯🇵 日本語

## IAM Credentials Report

IAM Credentials Reportは、アカウント内のすべてのユーザーについて認証情報の状態を確認できるAccount-Levelのレポートである。

パスワード、MFA、Access Keyの状態などを確認できる。

---

## IAM Access Advisor

IAM Access Advisorは、ユーザーに付与されたサービス権限と最後にアクセスした時刻を表示するUser-Levelの機能である。

使用されていない権限を確認し、Policyを見直すために利用できる。

---

## Best Practices

- Root Userを日常的に使用しない。
- 1人のユーザーに1つのIAM Userを作成する。
- Groupを利用して権限を管理する。
- 強力なPassword PolicyとMFAを使用する。
- AWSサービスにはIAM Roleを使用する。
- Access Keyを共有しない。

---

## Summary

- Credentials ReportはAccount-Levelである。
- Access AdvisorはUser-Levelである。
- 使用していない認証情報と権限を削除する。
- 最小権限の原則を適用する。

---

# 🇺🇸 English

## IAM Credentials Report

The IAM Credentials Report is an account-level report that lists users and the status of their credentials.

It includes information about passwords, MFA, and Access Keys.

---

## IAM Access Advisor

IAM Access Advisor is a user-level tool that shows granted service permissions and when those services were last accessed.

This information can be used to remove unnecessary permissions.

---

## Best Practices

- Do not use the Root User for daily tasks.
- Create one IAM User for each physical user.
- Manage permissions through Groups.
- Use a strong Password Policy and MFA.
- Use IAM Roles for AWS services.
- Never share IAM Users or Access Keys.
- Audit IAM permissions regularly.

---

## Summary

- Credentials Report is account-level.
- Access Advisor is user-level.
- Remove unused credentials and permissions.
- Follow the Principle of Least Privilege.

---

## Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Credentials Report | 자격 증명 보고서 | 認証情報レポート |
| Access Advisor | 액세스 어드바이저 | アクセスアドバイザー |
| Account-Level | 계정 수준 | アカウントレベル |
| User-Level | 사용자 수준 | ユーザーレベル |
| Last Accessed | 마지막 접근 시점 | 最終アクセス |
| Audit | 감사하다, 점검하다 | 監査する |
| Credential Status | 자격 증명 상태 | 認証情報の状態 |
| Best Practice | 모범 사례 | ベストプラクティス |
| Granted Permission | 부여된 권한 | 付与された権限 |
| Unused Permission | 사용하지 않는 권한 | 未使用の権限 |
| Physical User | 실제 사용자 | 実際のユーザー |

---

## Review Questions

1. Credentials Report와 Access Advisor의 범위 차이는 무엇인가?
2. Credentials Report에서 확인할 수 있는 정보는 무엇인가?
3. Access Advisor는 최소 권한 원칙을 적용하는 데 어떻게 도움이 되는가?
4. Root User는 언제 사용해야 하는가?
5. 한 IAM User를 여러 사람이 공유하면 안 되는 이유는 무엇인가?
6. AWS 서비스에 Access Key 대신 IAM Role을 사용하는 이유는 무엇인가?
7. IAM 환경에서 정기적으로 감사해야 할 항목은 무엇인가?
