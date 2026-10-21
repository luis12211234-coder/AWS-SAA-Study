
# 📘 28-01. AWS CloudFormation & Service Roles

> [!NOTE]
> **AWS SAA-C03 Study Note** · `GitHub Section 28` · `Udemy Section 30`
> **핵심 키워드:** IaC, Template, Stack, Change Set, Replacement, Drift Detection, Service Role, iam:PassRole

## 🇰🇷 1. CloudFormation이 필요한 이유

**AWS CloudFormation은 인프라를 코드로 정의하고 반복 배포하는 IaC(Infrastructure as Code) 서비스**다. 콘솔에서 VPC, 보안 그룹, 서버를 일일이 생성하는 대신 **원하는 인프라의 최종 상태**를 YAML/JSON 템플릿에 작성한다.

- **Template:** 리소스와 설정을 기술한 설계도.
- **Stack:** Template을 실제로 배포하여 함께 관리하는 리소스 묶음.
- **Resources:** 생성할 AWS 리소스의 유형과 속성.
- **Parameters / Outputs:** 배포 시 입력값 / 배포 후 공유할 결과값.

```text
CloudFormation Template (YAML)
           ↓ Create Stack
      Stack: dev-web
       ├─ S3 Bucket
       ├─ Security Group
       └─ EC2 Instance
           ↓ Update Stack
   변경된 리소스만 업데이트
```

핵심은 **선언형(Declarative)** 이라는 점이다. '어떤 API를 몇 번째로 호출할지'보다 '어떤 리소스가 어떤 설정을 가져야 하는지'를 정의한다. 단, 애플리케이션 코드나 실제 운영 데이터까지 자동으로 관리한다는 뜻은 아니다.

## 🇰🇷 2. 읽어 보는 최소 YAML 예시

다음은 암호화된 S3 버킷을 선언하는 **학습용 예시**다. 실제 배포 시에는 계정 정책, 보안 요구사항, 비용을 확인한다.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Encrypted S3 bucket example

Resources:
  StudyBucket:
    Type: AWS::S3::Bucket
    DeletionPolicy: Retain
    Properties:
      BucketEncryption:
        ServerSideEncryptionConfiguration:
          - ServerSideEncryptionByDefault:
              SSEAlgorithm: AES256

Outputs:
  BucketName:
    Value: !Ref StudyBucket
```

- `Type`은 생성할 리소스 종류이며, `Properties`는 설정값이다.
- `!Ref StudyBucket`은 생성된 버킷 이름을 참조한다.
- `DeletionPolicy: Retain`은 Stack을 삭제해도 **버킷을 보존**하도록 지시한다. Stack 관리에서 분리된 리소스의 후속 관리는 별도 책임이다.

즉 **Template = 설계도**, **Stack = 배포된 실제 묶음**이다.

## 🇰🇷 3. Stack 업데이트와 삭제

### Change Set: 적용 전에 변경 미리 보기

기존 Stack의 템플릿을 바꾸면 CloudFormation은 리소스를 **Add / Modify / Remove** 할 수 있다. **Change Set**은 변경을 실행하기 전에 영향 범위를 확인하는 기능이다.

특히 `Replacement: True`는 기존 리소스를 제자리 수정하는 대신 **새 리소스로 교체**해야 할 수 있다는 뜻이다. 식별자·엔드포인트 변경, 일시 중단, 데이터 이전 문제를 점검해야 한다. **Change Set은 미리 보기이지, 무중단 배포를 보장하는 기능이 아니다.**

### Stack Delete와 DeletionPolicy

Stack 삭제 시 관리 대상 리소스도 일반적으로 삭제된다. 그러나 `DeletionPolicy: Retain`은 리소스를 남기고, 일부 지원 리소스의 `Snapshot`은 삭제 전 스냅샷을 남긴다. 예를 들어 비어 있지 않은 S3 버킷이나 삭제 보호가 걸린 리소스 때문에 삭제가 실패할 수도 있다.

### Drift Detection: 수동 변경 찾아내기

```text
Template: SecurityGroup 인바운드 443만 허용
             ↓ 운영자가 콘솔에서 22 추가
실제 상태: 443 + 22 허용
             ↓ Drift Detection
       템플릿과 실제 상태 불일치
```

**Drift Detection**은 지원되는 리소스와 속성 범위에서 이런 차이를 찾는다. **자동으로 모든 차이를 복구하는 기능은 아니다.**

## 🇰🇷 4. CloudFormation Service Role과 IAM

CloudFormation이 리소스를 생성하려면 결국 AWS API를 호출할 권한이 필요하다.

**경우 A: Service Role 미지정**

```text
개발자 IAM 권한 ── CreateStack ──> CloudFormation
                                         ↓
                          개발자 권한으로 리소스 작업
```

**경우 B: Service Role 지정**

```text
개발자: cloudformation 권한 + iam:PassRole
                 ↓ 기존 Role 지정
CloudFormation ── AssumeRole ──> CFN-ServiceRole
                                     ↓
                              역할 권한으로 S3/EC2 생성
```

- **Service Role:** CloudFormation이 맡아서 사용하는 IAM Role. Trust Policy는 `cloudformation.amazonaws.com`을 신뢰해야 한다.
- **`iam:PassRole`:** 이미 존재하는 Role을 CloudFormation에 **전달하도록 허용**하는 권한. Role을 생성하는 `iam:CreateRole`과 다르다.
- **EC2 Instance Profile:** 배포된 EC2 애플리케이션 자체가 S3 등에 접근할 때 필요한 별도의 역할. **CloudFormation Service Role이 EC2에 자동 부착되지 않는다.**

> [!WARNING]
> Stack에 Service Role을 연결하면 이후 Stack 작업에도 사용된다. 연결된 Role을 단순 제거할 수 없고, Stack 작업 권한이 있는 사용자가 나중에 그 Role의 권한을 간접적으로 사용할 수 있다. **최소 권한 원칙**이 매우 중요하다.

## 🇰🇷 5. 실제 선택 예시

- 개발·테스트·운영 환경을 반복 재현하고 싶다 → **CloudFormation Template/Stack**.
- Stack 업데이트 전에 EC2가 교체되는지 확인하고 싶다 → **Change Set**.
- 콘솔에서 누군가 보안 그룹을 수정한 흔적을 찾고 싶다 → **Drift Detection**.
- 개발자에게 직접 S3 생성 권한을 주지 않고 승인된 배포만 허용하고 싶다 → **제한된 CloudFormation Service Role + 적절한 IAM 제어**.

## 🎯 Exam Notes

> [!IMPORTANT]
> **Template → Stack**, **변경 미리 보기 → Change Set**, **수동 변경 감지 → Drift Detection**, **배포 권한 위임 → Service Role**.

- `Replacement: True`는 리소스 교체 위험을 검토하라는 신호다.
- `iam:PassRole` ≠ `iam:CreateRole` ≠ EC2 실행 권한.
- `DeletionPolicy: Retain`은 Stack 삭제 후에도 해당 리소스를 남길 수 있다.
- CloudFormation은 **선언형 인프라 관리**이지 자동으로 모든 변경을 감지·수정하는 만능 도구는 아니다.

## 📝 Review Questions

> [!TIP]
> 답을 먼저 떠올리고 펼쳐 확인한다.

### Q1. Template과 Stack의 차이는?

<details>
<summary>정답 및 해설 보기</summary>

Template은 인프라 설계도(YAML/JSON), Stack은 템플릿을 적용해 생성한 관리 대상 리소스 집합이다.

</details>

### Q2. 업데이트 Change Set에 `Replacement: True`가 표시된다. 무엇을 주의해야 할까?

<details>
<summary>정답 및 해설 보기</summary>

기존 리소스가 새 리소스로 교체될 수 있으므로 중단, 식별자 변경, 데이터 보존과 이전 여부를 검토해야 한다.

</details>

### Q3. 개발자에게 S3 생성 권한이 없는데 CloudFormation으로 S3를 생성하려면?

<details>
<summary>정답 및 해설 보기</summary>

S3 생성 권한이 부여된 CloudFormation Service Role을 지정할 수 있다. 개발자에게는 필요한 CloudFormation 권한과 역할 지정 시 `iam:PassRole`이 필요하다.

</details>

### Q4. 운영자가 콘솔에서 CloudFormation 관리 보안 그룹을 수동 수정했다. 어떤 기능을 쓸까?

<details>
<summary>정답 및 해설 보기</summary>

Drift Detection이다. 단, 지원되는 리소스와 속성에 한해서 차이를 탐지하며 자동 복구와는 다르다.

</details>

### Q5. Stack에 지정한 CloudFormation Service Role이 EC2에도 자동으로 붙을까?

<details>
<summary>정답 및 해설 보기</summary>

아니다. CloudFormation의 배포 권한과 EC2 애플리케이션의 실행 권한은 다르다. EC2에 필요한 IAM Instance Profile은 별도로 설정해야 한다.

</details>

## 🇯🇵 日本語 Summary

CloudFormation は **Infrastructure as Code (IaC)** により AWS リソースを宣言的に管理する。**Template** は設計図、**Stack** はデプロイ済みリソースの管理単位である。**Change Set** は更新内容を事前に確認し、**Drift Detection** はテンプレートとの差異を検出する。**Service Role** は CloudFormation が利用する IAM 権限で、ロールの受け渡しには `iam:PassRole` が必要になる。

## 🇺🇸 English Summary

CloudFormation provisions AWS resources declaratively using templates and stacks. Change Sets preview modifications and possible resource replacements. Drift Detection checks for differences between declared and actual configurations. A CloudFormation service role grants deployment permissions; `iam:PassRole` allows a caller to designate an existing role, not create one.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Infrastructure as Code | 코드형 인프라 | Infrastructure as Code |
| Template | 템플릿·설계도 | テンプレート |
| Stack | 리소스 관리 묶음 | スタック |
| Change Set | 변경 미리 보기 | 変更セット |
| Replacement | 리소스 교체 | リソースの置き換え |
| Drift Detection | 구성 차이 탐지 | ドリフト検出 |
| Service Role | 서비스 역할 | サービスロール |
| PassRole | 역할 전달 권한 | ロールの受け渡し権限 |

## 📖 References

- [CloudFormation concepts](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)
- [CloudFormation Service Role](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-iam-servicerole.html)
- [Updating stacks with Change Sets](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-changesets.html)
