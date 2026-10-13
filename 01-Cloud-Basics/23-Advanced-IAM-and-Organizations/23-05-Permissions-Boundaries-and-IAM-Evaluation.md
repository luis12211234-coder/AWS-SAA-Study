# 📘 23-05. Permissions Boundaries & IAM Policy Evaluation

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Permissions Boundary` · `Identity-Based Policy` · `SCP` · `Session Policy` · `Explicit Deny` · `Implicit Deny`

## 🇰🇷 1. Permissions Boundary(권한 경계)

**Permissions Boundary**는 IAM 사용자 또는 IAM 역할이 **자격 증명 기반 정책을 통해 획득할 수 있는 권한의 최대 범위**를 정의하는 고급 정책이다. **IAM Group 자체에는 부착하지 못한다.** Boundary는 권한을 부여하지 않는다.

```text
IAM Policy:          S3 / EC2 / IAM / RDS 허용 (AdministratorAccess)
Permissions Boundary: S3만 허용 (AmazonS3FullAccess)
교집합:              S3 작업만 IAM Policy로 허용 가능
```

예를 들어 IAM Policy가 `iam:CreateUser`만 허용하지만 Boundary가 EC2/S3/CloudWatch만 허용한다면 **CreateUser는 불가**하다. IAM Policy가 허용한 IAM API가 Boundary 범위 밖이기 때문이다.

## 🇰🇷 2. John 개발자의 사례

John 사용자에 다음을 설정한다.

- **Identity-Based Policy:** `AdministratorAccess` (모든 서비스 허용)
- **Permissions Boundary:** `AmazonS3FullAccess` (S3 관련 권한만 허용 범위)

이 상황에서 John은 S3 작업은 다른 제약이 없다면 수행할 수 있지만, EC2 생성이나 IAM 사용자 생성은 Boundary에 해당 허용이 없어 실행할 수 없다.

**주의:** 실제 AWS 정책 평가에는 Resource-Based Policy 등 예외와 SCP가 영향을 준다. 이 교집합 도식은 **Identity-Based Policy + Boundary**의 기본 모델이다.

## 🇰🇷 3. SCP와 무엇이 다를까?

| 구분 | Permissions Boundary | Organizations SCP |
|---|---|---|
| 붙이는 곳 | IAM User / IAM Role | Root 컨테이너 / OU / Member Account |
| 기본 목적 | **특정 사용자·역할**의 권한 상한 | **조직 멤버 계정**의 권한 상한 |
| IAM Group에 직접 적용 | X | SCP의 계층 대상 자체가 AWS 계정 단위 |
| 계정 Root User 제한 | X | **멤버 계정 Root User** 제한 가능 |
| 직접 권한 부여 | X | X |

IAM 권한 + Boundary + SCP가 함께 작동할 때는 각 정책의 평가 논리가 적용되고, **Explicit Deny는 항상 우선**한다.

## 🇰🇷 4. 위임 관리(Delegated Administration)

관리자가 개발자 John에게 새 IAM 사용자를 생성·관리할 권한을 위임하려고 한다. 그런데 John이 새 사용자에게 무제한 관리자 권한을 부여하면 위험하다.

- John이 새 사용자를 생성할 때 **관리자가 승인한 Boundary를 반드시 지정하도록** IAM 조건(`iam:PermissionsBoundary`)을 활용해 제한할 수 있다.
- **Boundary 제거 또는 변경 권한**도 별도로 제한해 우회하지 못하도록 한다.
- 이렇게 하면 신규 사용자를 만들 권한은 위임하되 그들이 가질 수 있는 권한의 상한은 유지된다.

## 🇰🇷 5. IAM 정책 평가: Allow vs Deny

1. AWS는 기본적으로 **Implicit Deny(암시적 거부)** 상태에서 시작한다.
2. 해당 작업을 유효하게 허용하는 정책이 있어야 한다.
3. 관련 정책에서 **Explicit Deny(명시적 거부)**가 하나라도 맞으면 무조건 거부한다.
4. SCP, Permissions Boundary, Session Policy 등은 기본 권한 허용 경로를 추가로 제한한다.
5. Resource-Based Policy에서 특정 Principal에게 직접 권한을 부여하는 경우에는 **Boundary의 암시적 거부에 관한 예외**가 있으므로, 언제나 단순한 '모든 정책의 교집합'만은 아니다.

### 강의의 SQS 문제

```json
{
  "Version":"2012-10-17",
  "Statement":[
    {"Effect":"Deny", "Action":"sqs:*", "Resource":"*"},
    {"Effect":"Allow", "Action":"sqs:DeleteQueue", "Resource":"*"}
  ]
}
```

| 호출 작업 | 이 정책만 있을 때 | 이유 |
|---|---|---|
| `sqs:CreateQueue` | Deny | `sqs:*` Explicit Deny |
| `sqs:DeleteQueue` | Deny | Allow 존재해도 Explicit Deny 우선 |
| `ec2:DescribeInstances` | Deny | EC2 Allow가 없어서 Implicit Deny. 단 다른 정책에서 허용하면 달라질 수 있음 |

### Resource-Based Policy 평가 시 주의할 예외

- 같은 계정의 **IAM User ARN** 또는 **Assumed Role Session ARN**에 직접 허용하는 Resource-Based Policy는 Boundary나 자격 증명 정책의 **Implicit Deny**로 항상 제한되는 것은 아니다.
- Role ARN을 대상으로 하는 Resource-Based Policy 등에는 다른 평가 규칙이 적용된다.
- **Explicit Deny는 이런 예외에도 우선**한다.

**공식 참고:** [Permissions Boundaries](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html), [Policy Evaluation Logic](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic_policy-eval-denyallow.html).

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Boundary는 **IAM User/Role에 부착**, **IAM Group에는 부착 불가**.
- Boundary의 Allow는 **직접 권한을 부여하지 않는다**. IAM Policy와 Boundary의 기본 교집합으로 생각한다.
- SCP는 **계정/OU 수준**, Boundary는 **특정 User/Role 수준** 제한이다.
- 권한 위임 시 `iam:PermissionsBoundary` 조건으로 새 주체에 경계를 필수 적용하도록 설계한다.
- **Explicit Deny > Allow**, Allow가 전혀 없으면 Implicit Deny. Resource-Based Policy의 동일 계정 Principal 직접 허용 예외는 유의.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. John에게 AdministratorAccess가 있고 Boundary는 AmazonS3FullAccess다. EC2 인스턴스를 생성할 수 있나?

<details>
<summary>정답 및 해설 보기</summary>

**불가.** Identity-Based Policy는 허용하지만 Boundary에서 EC2 권한이 허용되지 않는다.

</details>

### Q2. Boundary에 EC2 권한이 있지만 John의 Identity-Based Policy에는 S3 읽기만 있다. EC2 사용이 가능한가?

<details>
<summary>정답 및 해설 보기</summary>

**불가.** Boundary는 권한을 새로 부여하지 않는다.

</details>

### Q3. 관리자가 개발자에게 IAM 사용자 생성 권한을 위임하되 새 사용자가 관리자 권한을 못 갖게 하려면?

<details>
<summary>정답 및 해설 보기</summary>

**Permissions Boundary를 강제**한다. `iam:PermissionsBoundary` 조건과 Boundary 변경/제거 제한을 함께 고려한다.

</details>

### Q4. SQS 전체 거부와 SQS DeleteQueue 허용이 동일한 요청에 모두 해당된다. 최종 결과는?

<details>
<summary>정답 및 해설 보기</summary>

**Explicit Deny**. `sqs:*`의 거부가 `sqs:DeleteQueue` Allow보다 우선한다.

</details>

### Q5. 모든 유효 권한이 무조건 IAM Policy, Boundary, Resource-Based Policy의 단순한 교집합인가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 같은 계정 IAM 사용자 또는 Role 세션에 직접 권한을 부여하는 Resource-Based Policy에는 Boundary 등의 Implicit Deny 예외가 있다. Explicit Deny는 언제나 우선한다.

</details>

## 🇯🇵 日本語 Summary

Permissions Boundary は IAM User/Role に適用する権限の上限であり、IAM Group には直接設定できない。Identity-Based Policy の Allow と Boundary の許可範囲の共通部分が基本的な有効権限となる。SCP は組織の Member Account に対する上限。委任管理には `iam:PermissionsBoundary` の条件が役立つ。Explicit Deny はすべての Allow より優先する。

**試験の注意点:** Resource-Based Policy でセッションプリンシパルを直接許可する場合など、単純な共通部分では表せない例外がある。

## 🇺🇸 English Summary

A permissions boundary limits permissions that an IAM user or role may receive through identity-based policies; it does not grant access. Boundaries cannot be attached to IAM groups. SCPs set organization-level ceilings for member accounts. Explicit Deny always overrides Allow, while missing Allow normally results in an implicit deny. Resource-based policy grants to certain same-account principals have important implicit-deny exceptions.

**Exam decision:** Use boundaries for delegated IAM administration and SCPs for account-wide organizational guardrails.

## 📚 Vocabulary

> GitHub 검색 시 영어 표제어 그대로 검색하면 관련 설명으로 이동하기 쉽다.

| English | 한국어 | 日本語 |
|---|---|---|
| Permissions Boundary | 권한 경계 | アクセス許可の境界 |
| Effective Permissions | 유효 권한 | 実効アクセス許可 |
| Implicit Deny | 암시적 거부 | 暗黙的な拒否 |
| Explicit Deny | 명시적 거부 | 明示的な拒否 |
| Delegated Administration | 관리 권한 위임 | 委任管理 |
| Session Policy | 세션 정책 | セッションポリシー |
| AdministratorAccess | 관리자 권한 관리형 정책 | 管理者アクセス |
| Policy Evaluation | 정책 평가 | ポリシー評価 |
| IAM Group | IAM 그룹 | IAM グループ |
