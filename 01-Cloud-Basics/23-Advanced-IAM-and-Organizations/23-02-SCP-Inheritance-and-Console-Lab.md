# 📘 23-02. Service Control Policies (SCP) & Organizations Console Lab

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `SCP` · `FullAWSAccess` · `Explicit Deny` · `Inherited Policy` · `Deny S3` · `OU`

## 🇰🇷 1. Service Control Policy(SCP)의 목적

**SCP는 Organizations의 멤버 계정에 속한 주체가 행사할 수 있는 권한의 최대 범위(Guardrail)를 제한**한다. SCP의 `Allow`는 IAM 사용자에게 실제 권한을 주지 않는다. 사용자가 작업하려면 IAM 등 관련 정책의 허용도 필요하다.

- SCP는 **OU / 계정 / 조직 Root 컨테이너**에 연결한다.
- **Member Account의 Root User**도 SCP 제한을 받는다.
- **Management Account의 사용자·역할·Root User**는 SCP의 적용 대상이 아니다. 단, 그들이 모두 관리자 권한을 가진다는 뜻은 아니다.
- SCP는 리소스 기반 정책 자체처럼 특정 S3 버킷에 붙이는 정책이 아니다.

## 🇰🇷 2. FullAWSAccess와 Allow/Deny 평가

SCP 활성화 시 `FullAWSAccess`가 기본 연결될 수 있다. 이름과 달리 **관리자 권한을 주는 IAM 정책이 아니다**. 단지 SCP 수준에서 모든 작업을 허용 범위에 둔다.

**최종 허용의 기본 구조:** 계정으로 내려오는 **Root → 상위 OU → 하위 OU → Member Account** 모든 단계에 해당 작업에 대한 SCP Allow가 있어야 하고, 경로 어느 단계에도 Explicit Deny가 없어야 한다. 이후 IAM 등 실제 권한 허용을 판단한다.

```text
Organizations Root: FullAWSAccess
  └─ Prod OU: FullAWSAccess
     └─ Finance OU: FullAWSAccess + DenyS3
        └─ Finance Account: FullAWSAccess
                              └─ S3는 불가, 다른 서비스는 IAM 권한 필요
```

- **Deny-list 전략:** `FullAWSAccess`를 유지하고 금지할 동작을 `Deny`.
- **Allow-list 전략:** 특정 계층에서 `FullAWSAccess`를 제거하고 제한된 서비스만 `Allow`. 경로상 모든 계층의 Allow가 필요하다.
- 같은 FullAWSAccess가 상속 경로 여러 곳에 표시되더라도 권한이 중첩되어 커지지 않는다.

## 🇰🇷 3. 강의 콘솔 실습을 영상 없이 재구성

1. 두 AWS 계정 준비: `aws-course-master`와 `aws-course-child`.
2. Master 계정에서 **Organizations 생성**. Master가 Management Account가 되고 Organizations Root 생성.
3. **Add an AWS account → Invite an existing AWS account**로 Child 초대.
4. Child 계정에서 Invitations를 열어 초대를 수락. 초대 유효기간은 **15일**.
5. Root 아래에 `Dev`, `Test`, `Prod` OU를 만들고, `Prod` 아래 `HR`, `Finance` OU 생성.
6. Child 계정을 **Move**하여 `Prod → Finance` OU에 배치.
7. Organizations → **Policies → Service control policies 활성화**.
8. `DenyAccessS3` SCP 생성 후 **Finance OU**에 Attach.
9. Child 계정에서 S3 콘솔로 버킷 목록 조회 시도 → 권한 거부. **Child의 Root User라도 거부**.

콘솔 UI와 경로는 AWS가 변경할 수 있다. 기억할 것은 **상위 OU에 SCP를 붙이면 하위 멤버 계정에 상속된다**는 흐름이다.

## 🇰🇷 4. S3를 거부하는 SCP 예제

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyAllS3Actions",
      "Effect": "Deny",
      "Action": "s3:*",
      "Resource": "*"
    }
  ]
}
```

- `s3:*`: S3의 모든 작업.
- `Resource: "*"`: 정책 문장에서 지정한 모든 리소스.
- **Deny가 우선**하므로 `FullAWSAccess`가 있어도 S3 API는 거부한다.
- S3 콘솔에서 버킷 목록이 표시되지 않는 것은 `s3:ListAllMyBuckets` 등의 작업이 SCP 거부에 걸리는 대표적 결과다.

## 🇰🇷 5. SCP 이외 Organizations 정책

- **Tag Policy:** 조직 차원의 태그 키/값 사용 표준화 및 준수 상태 관리.
- **Backup Policy:** 계정·OU에 일관된 AWS Backup 계획 배포.
- 주의: Tag Policy의 표준화 기능을 모든 리소스 생성의 자동 차단과 동일시하면 안 된다. 실제 적용 범위와 강제 방식은 설정·지원 리소스에 따라 다르다.

**공식 참고:** [SCP evaluation](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps_evaluation.html), [Organizations invitations](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_reference_limits.html).

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **SCP는 최대 권한의 상한**이며 직접 권한을 부여하지 않는다.
- **SCP는 Member Account의 Root User도 제한**한다. Management Account 자체에는 적용되지 않는다.
- 상속 경로의 **모든 계층에 Allow**, 어느 계층에도 **Explicit Deny 없음**이 필요하다.
- `FullAWSAccess` + `Deny s3:*`는 S3 거부이며, FullAWSAccess가 Deny를 덮지 못한다.
- 기존 계정 초대, OU Move, SCP Attach는 Organizations에서 수행한다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Finance OU에 `Deny s3:*` SCP를 붙이면 Finance 계정에 AdministratorAccess가 있어도 S3를 사용할 수 있나?

<details>
<summary>정답 및 해설 보기</summary>

**불가.** SCP의 Explicit Deny가 IAM AdministratorAccess보다 우선한다.

</details>

### Q2. SCP의 `FullAWSAccess`를 붙인 새 IAM User에게 자동으로 EC2 생성 권한이 생길까?

<details>
<summary>정답 및 해설 보기</summary>

**생기지 않는다.** SCP는 권한 상한이며 실제 API 허용에는 Identity-Based Policy 등 적절한 Allow가 필요하다.

</details>

### Q3. 멤버 계정 Root User가 SCP에 의해 S3를 금지당할 수 있을까?

<details>
<summary>정답 및 해설 보기</summary>

**그렇다.** 멤버 계정 Root User 역시 SCP 적용 대상이다.

</details>

### Q4. OU에서 FullAWSAccess를 제거하고 EC2만 Allow한 경우, 상위 Root가 모든 서비스를 Allow해도 S3는 가능한가?

<details>
<summary>정답 및 해설 보기</summary>

**불가.** SCP 허용은 Root부터 대상 계정까지 각 계층에 있어야 한다. 해당 OU에 S3 Allow가 없다.

</details>

### Q5. 조직의 Management Account에 `Deny ec2:*` SCP를 적용하면 그 안의 IAM 사용자도 EC2를 못 쓰는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** SCP는 Management Account의 Principal에는 적용되지 않는다. 해당 계정에서는 IAM 등 다른 정책으로 제한해야 한다.

</details>

## 🇯🇵 日本語 Summary

SCP は Member Account の権限の**上限**を定める。権限そのものは付与しない。Root → OU → Account の全階層に必要な Allow があり、どこにも Explicit Deny がないことが必要。`FullAWSAccess` は IAM の管理者権限ではない。Member Account の Root User も SCP によって制限されるが、Management Account には SCP は適用されない。

**試験の判断:** OU 全体で S3 を禁止するなら `Deny s3:*` を OU にアタッチする。

## 🇺🇸 English Summary

Service Control Policies establish organization-level permission guardrails. They restrict member-account principals, including member-account root users, but do not grant access. SCP evaluation follows the root-to-account path: each level needs an applicable Allow, and any explicit Deny overrides all Allows. Management account principals are exempt from SCPs.

**Exam decision:** Attach an SCP to an OU to consistently prevent member accounts from performing restricted actions.

## 📚 Vocabulary

> GitHub 검색 시 영어 표제어 그대로 검색하면 관련 설명으로 이동하기 쉽다.

| English | 한국어 | 日本語 |
|---|---|---|
| Service Control Policy (SCP) | 서비스 제어 정책 | サービスコントロールポリシー |
| Guardrail | 권한·거버넌스 안전장치 | ガードレール |
| FullAWSAccess | SCP 차원의 전체 허용 범위 | FullAWSAccess |
| Explicit Deny | 명시적 거부 | 明示的な拒否 |
| Implicit Deny | 암시적 거부 | 暗黙的な拒否 |
| Inherited Policy | 상속 정책 | 継承ポリシー |
| Deny-list Strategy | 거부 목록 방식 | 拒否リスト方式 |
| Allow-list Strategy | 허용 목록 방식 | 許可リスト方式 |
| Service Control | 서비스 사용 제한 | サービス制御 |
