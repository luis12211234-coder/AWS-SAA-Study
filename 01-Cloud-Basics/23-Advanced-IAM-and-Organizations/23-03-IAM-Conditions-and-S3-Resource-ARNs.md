# 📘 23-03. IAM Conditions, S3 ARN & PrincipalOrgID

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `IAM Condition` · `aws:SourceIp` · `aws:RequestedRegion` · `ResourceTag` · `PrincipalTag` · `MFA` · `S3 ARN` · `PrincipalOrgID`

## 🇰🇷 1. IAM 정책의 Condition

IAM Policy의 기본 질문은 **누가(Principal), 무엇을(Action), 어디에(Resource), 허용/거부(Effect)**이다. `Condition`은 여기에 **언제, 어떤 환경과 속성에서?**를 더한다. Identity-Based Policy와 Resource-Based Policy를 비롯해 조건을 지원하는 여러 정책 유형에 사용한다.

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": "ec2:DescribeInstances",
    "Resource": "*",
    "Condition": {
      "StringEquals": {"aws:RequestedRegion": "ap-northeast-1"}
    }
  }]
}
```

이 문장에서는 도쿄 리전 엔드포인트를 대상으로 한 `DescribeInstances`를 **Allow**한다. 이것만으로 다른 정책의 허용까지 차단하는 건 아니다. 특정 리전을 **확실히 금지**하고자 한다면 적합한 Explicit Deny를 고려한다.

## 🇰🇷 2. 주요 Condition Key

| Key | 검사 내용 | 대표 문제 표현 |
|---|---|---|
| `aws:SourceIp` | 호출자의 공인 IP 대역 | 회사 네트워크에서만 AWS API 허용 |
| `aws:RequestedRegion` | API 요청 대상 AWS 엔드포인트 리전 | 허용된 리전만 AWS 서비스 사용 |
| `ec2:ResourceTag/Project` | EC2 리소스의 Project 태그 | 프로젝트 태그에 따른 EC2 관리 |
| `aws:PrincipalTag/Department` | 호출 주체(User/Role 세션 등)의 Department 태그 | 개발팀 직원만 접근 |
| `aws:MultiFactorAuthPresent` | 요청 컨텍스트에 MFA 인증 정보가 있는지 | 중요 작업에 MFA 요구 |
| `aws:PrincipalOrgID` | 주체가 속한 AWS Organizations ID | 특정 조직 구성원만 S3 접근 |

**조건의 판단 대상이 다르다.** `ResourceTag`는 **접근받는 객체**, `PrincipalTag`는 **호출하는 사람/역할**, `SourceIp`는 **호출 위치**를 검사한다. 같은 문장에 여러 조건을 적으면 모두 충족해야 하는 방식(AND)으로 평가되는 것이 일반적이다.

### SourceIp와 RequestedRegion 주의점

- `NotIpAddress` + `Deny`로 허용 회사 IP 범위 밖을 막는 패턴이 있다. VPC Endpoint를 이용한 요청은 `aws:SourceVpce` 같은 조건 키를 함께 검토해야 할 수 있다.
- `aws:RequestedRegion`은 **API 엔드포인트 리전**을 검사한다. 글로벌 서비스와 교차 리전 영향이 있는 API까지 '데이터 저장 위치를 완벽히 강제'하는 조건이라고 단정하면 안 된다.

### MFA 조건

`Bool`/`BoolIfExists`와 `aws:MultiFactorAuthPresent`를 조합해 MFA 없이 실행한 민감 작업을 거부한다. 장기 액세스 키를 사용한 API 요청처럼 해당 키 자체가 존재하지 않을 수 있으므로 **키가 없을 때도 거부해야 한다면 `BoolIfExists`와 Deny의 평가 동작**을 검토한다.

## 🇰🇷 3. S3 버킷 ARN과 객체 ARN

S3의 **버킷 수준 작업**과 **객체 수준 작업**은 리소스 ARN이 다르다.

| 작업 | ARN 예시 | 이유 |
|---|---|---|
| `s3:ListBucket` | `arn:aws:s3:::example-bucket` | 버킷을 대상으로 함 |
| `s3:GetObject` | `arn:aws:s3:::example-bucket/*` | 버킷 내 객체를 대상으로 함 |
| `s3:PutObject` | `arn:aws:s3:::example-bucket/*` | 객체 생성/업로드 |
| `s3:DeleteObject` | `arn:aws:s3:::example-bucket/*` | 객체 삭제 |

`/*`는 해당 버킷 안의 모든 객체 키를 나타낸다. 두 유형을 모두 허용할 땐 정책에서 각 작업의 적절한 ARN을 지정한다.

## 🇰🇷 4. Resource-Based Policy와 aws:PrincipalOrgID

중앙 S3 버킷을 같은 Organizations 구성원에게만 공유하고 싶다면 Bucket Policy에 `aws:PrincipalOrgID` 조건을 넣을 수 있다.

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Sid": "AllowReadForOrganization",
    "Effect": "Allow",
    "Principal": "*",
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::example-bucket/*",
    "Condition": {
      "StringEquals": {"aws:PrincipalOrgID": "o-example123456"}
    }
  }]
}
```

이 정책은 **해당 조직에 속한 Principal에게 이 Allow를 적용**한다. 다른 정책에서 외부 접근을 허용하면 여전히 허용될 수 있으므로 **조직 밖 접근 전체를 보장되게 차단하려면 정책 전체의 Explicit Deny 등**을 함께 검토해야 한다. 와일드카드 Principal은 조직 ID처럼 강한 조건과 조합할 때만 신중히 사용한다.

## 🇰🇷 5. 시험에서의 빠른 구분

- **회사 IP에서만 AWS API 요청:** `aws:SourceIp`.
- **도쿄 리전에만 API 요청:** `aws:RequestedRegion`.
- **태그가 DataAnalytics인 EC2만 관리:** `ec2:ResourceTag/Project`.
- **Data 부서 직원만 허용:** `aws:PrincipalTag/Department`.
- **MFA 후 종료 가능:** `aws:MultiFactorAuthPresent`.
- **조직 소속 AWS 계정만 S3 사용:** `aws:PrincipalOrgID`.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- `Condition`은 특정 Allow/Deny가 적용되는 상황을 제한한다. 다른 정책의 Allow까지 배제하려면 Explicit Deny가 필요한 경우가 있다.
- `aws:RequestedRegion`은 요청의 엔드포인트 리전이며 엄밀한 데이터 거주 지역 통제와 다르다.
- `ResourceTag`는 대상 리소스의 태그, `PrincipalTag`는 요청 주체의 속성이다.
- `s3:ListBucket`은 **버킷 ARN**, `GetObject/PutObject`는 **객체 ARN(`/*`)**이 필요하다.
- `aws:PrincipalOrgID`를 사용하는 Allow는 조직 구성원에게 허용하는 문장이지, 모든 외부 접근의 자동 Explicit Deny가 아니다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 회사 공인 IP 203.0.113.0/24 범위 밖에서 모든 AWS API 요청을 차단하려면 어떤 조건 키와 전략을 고려할까?

<details>
<summary>정답 및 해설 보기</summary>

**`aws:SourceIp` + `NotIpAddress`를 조건으로 한 Explicit Deny.** VPC Endpoint 트래픽은 별도 조건을 검토한다.

</details>

### Q2. EC2의 `Project=DataAnalytics` 태그와 직원의 `Department=Data` 태그를 각각 검사하는 키는?

<details>
<summary>정답 및 해설 보기</summary>

**`ec2:ResourceTag/Project`와 `aws:PrincipalTag/Department`.** 대상 리소스의 속성과 요청 주체의 속성을 구분한다.

</details>

### Q3. S3 버킷 `sample`의 파일 읽기와 버킷 목록 보기 권한에 각각 어떤 ARN을 지정하는가?

<details>
<summary>정답 및 해설 보기</summary>

**객체 읽기:** `arn:aws:s3:::sample/*`, **버킷 목록:** `arn:aws:s3:::sample`. 객체 작업과 버킷 작업의 리소스 단위가 다르다.

</details>

### Q4. IAM Policy에 도쿄만 `Allow`하는 RequestedRegion 조건을 추가하면 다른 정책이 허용하는 오사카 리전 요청도 자동 차단되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 해당 Allow가 도쿄에서만 유효할 뿐이다. 다른 허용을 제어하려면 적용 가능한 Explicit Deny 등의 설계가 필요하다.

</details>

### Q5. S3 Bucket Policy에 `PrincipalOrgID=o-123`인 Allow를 넣었다. 이것만으로 조직 외 모든 접근에 명시적 거부가 생기는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 그 Allow가 조직 외 주체에게 적용되지 않을 뿐 다른 Allow 경로를 막는 Explicit Deny는 아니다.

</details>

## 🇯🇵 日本語 Summary

IAM Condition は IP、API 要求リージョン、リソースタグ、プリンシパルタグ、MFA の状態などに基づいて Allow/Deny を適用する。`aws:SourceIp` は接続元、`aws:RequestedRegion` は API エンドポイントのリージョン、`ResourceTag` は対象リソース、`PrincipalTag` は呼び出し側の属性を示す。S3 は `ListBucket` にバケット ARN、`GetObject` にオブジェクト ARN（`/*`）を用いる。

**試験の判断:** 組織単位の S3 共有条件は `aws:PrincipalOrgID`。Allow 条件だけでは他の許可経路を自動的に拒否しない。

## 🇺🇸 English Summary

IAM conditions evaluate context such as caller IP, requested Region, resource tags, principal tags, and MFA authentication. `aws:RequestedRegion` restricts API endpoint Regions rather than guaranteeing data residency. S3 bucket operations use bucket ARNs, while object operations use object ARNs ending with `/*`. `aws:PrincipalOrgID` can scope a bucket policy Allow to members of an AWS organization.

**Exam decision:** Identify whether the condition describes the caller, target resource, authentication state, or request endpoint.

## 📚 Vocabulary

> GitHub 검색 시 영어 표제어 그대로 검색하면 관련 설명으로 이동하기 쉽다.

| English | 한국어 | 日本語 |
|---|---|---|
| Condition Key | 조건 키 | 条件キー |
| Source IP | 요청자 IP | 送信元IP |
| Requested Region | 요청 리전 | 要求リージョン |
| Resource Tag | 리소스 태그 | リソースタグ |
| Principal Tag | 주체 태그 | プリンシパルタグ |
| MFA Present | MFA 인증 여부 | MFA認証の有無 |
| Principal Organization ID | 주체 소속 조직 ID | プリンシパルの組織ID |
| Bucket ARN | 버킷 ARN | バケットARN |
| Object ARN | 객체 ARN | オブジェクトARN |
| Attribute-Based Access Control (ABAC) | 속성 기반 접근 제어 | 属性ベースアクセス制御 |
