# 📘 23-08. AWS Control Tower: Landing Zone & Governance Controls

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `AWS Control Tower` · `Landing Zone` · `Account Factory` · `Preventive Control` · `Detective Control` · `Proactive Control` · `SCP` · `AWS Config`

## 🇰🇷 1. AWS Control Tower의 목적

**AWS Control Tower**는 AWS Organizations 등을 기반으로 **다중 계정 환경을 보안·운영 모범 사례에 맞게 표준 구성·관리**할 수 있게 하는 서비스다. Organizations만으로는 계정 및 OU를 관리할 수 있지만 중앙 로깅·감사와 지속적인 준수 규칙까지 설계해야 하는 부담이 있다.

- **Landing Zone:** 보안과 거버넌스가 사전에 설계된 표준 다중 계정 AWS 환경.
- **Account Factory:** 계정을 사전 정의된 설정에 맞춰 프로비저닝하고 관리.
- **Log Archive Account:** 여러 계정의 중앙 로깅 자료 수집.
- **Audit Account:** 보안·감사 목적으로 분리된 계정.
- **Dashboard:** Controls의 준수·위반 상태를 중앙에서 확인.

```text
AWS Control Tower / Landing Zone
    ├─ AWS Organizations / OU / Accounts
    ├─ Account Factory (계정 생성 표준화)
    ├─ Log Archive / Audit Accounts
    └─ Controls (규정 준수 통제)
```

## 🇰🇷 2. Preventive Control(예방형 가드레일)

**규정에 어긋나는 API 작업 자체를 사전에 막는다.** 강의에서 주로 **Organizations SCP**를 활용하는 경우를 설명했다.

예: 승인되지 않은 AWS 리전에서 EC2를 생성하는 작업 제한. `Deny` 조건에 해당하는 작업을 시도해도 수행할 수 없다.

- 예방형은 **행위 자체의 차단**을 목표로 한다.
- 현재 AWS Control Tower는 SCP뿐 아니라 **RCP(Resource Control Policy)**와 **선언형 정책(Declarative Policies)** 등을 통해 예방형 통제를 구현할 수 있다.
- SCP는 Management Account의 주체를 제한하지 않는다.

## 🇰🇷 3. Detective Control(탐지형 가드레일)

**리소스가 이미 잘못 설정되었는지 사후 평가**한다. 대표적으로 **AWS Config Rules**를 사용한다.

예: 모든 EC2에 필수 `Project` 태그가 있어야 한다는 규칙을 적용했는데, 태그 없는 EC2를 발견하면 `NON_COMPLIANT`로 표시.

```text
EC2 구성 변경
   → AWS Config Rule 평가
   → NON_COMPLIANT 발견
   → EventBridge / SNS 알림 (추가 구성)
   → Lambda 또는 SSM Automation 교정 (추가 구성)
```

**중요:** Detective Control이 자동으로 교정 조치를 반드시 실행하는 것은 아니다. 알림·자동 교정에는 EventBridge, SNS, Lambda, Systems Manager Automation 등의 별도 연결이 필요하다.

## 🇰🇷 4. Proactive Control(사전 배포 검사형)

현재 Control Tower의 Control은 예방형·탐지형에 더해 **Proactive Control**도 있다.

- **AWS CloudFormation Hooks**를 사용해 CloudFormation을 통한 리소스 프로비저닝 전 규칙 준수 여부를 검사한다.
- 규칙을 위반하면 **리소스 생성 전에 배포를 차단**한다.
- 모든 API 경로에서 모든 리소스 생성을 포괄적으로 차단하는 장치는 아니다. CloudFormation 프로비저닝 경로 및 지원 리소스 범위에 유의한다.

| Control 유형 | 시점 | 대표 구현 | 예시 |
|---|---|---|---|
| **Preventive** | 작업 호출 시 | SCP / RCP 등 | 금지된 리전에서 EC2 시작 거부 |
| **Detective** | 구성/상태 발생 후 | AWS Config Rule | 태그 누락 리소스 탐지 |
| **Proactive** | CloudFormation 배포 전 | CloudFormation Hooks | 규정 위반 리소스 배포 차단 |

## 🇰🇷 5. Organizations·SCP·Config와의 관계

| 요구사항 | 선택 |
|---|---|
| 조직·OU·통합 결제·SCP 기본 관리 | **AWS Organizations** |
| 보안 표준형 다중 계정 환경 빠른 구축 | **AWS Control Tower + Landing Zone** |
| 새 계정의 표준화된 생성 | **Account Factory** |
| 금지된 API 작업 거부 | **Preventive Control / SCP 등** |
| 설정 위반 검사 | **Detective Control / AWS Config** |
| 위반 탐지 후 자동 수정 | **AWS Config + Automation/Lambda 등** |
| CloudFormation 배포 전에 위반 차단 | **Proactive Control** |

**공식 참고:** [Control Behavior](https://docs.aws.amazon.com/controltower/latest/controlreference/control-behavior.html), [Shared Accounts](https://docs.aws.amazon.com/controltower/latest/userguide/what-shared.html).

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Control Tower = **보안·규정 준수 다중 계정 Landing Zone 자동 구성/관리**.
- Account Factory = **계정 표준화 및 생성**, Audit/Log Archive 계정은 보안 및 중앙 로깅 역할.
- **Preventive:** 작업 자체를 차단. 강의는 주로 SCP, 최신 구현에는 RCP 등도 포함.
- **Detective:** AWS Config 기반 규정 위반 탐지. 자동 교정은 추가 구성 필요.
- **Proactive:** CloudFormation Hooks로 배포 전에 검증/차단. CloudFormation 범위에 주의.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 새 AWS 조직을 만들며 로깅·감사 계정, 보안 표준, 계정 생성 과정을 자동 구성하려 한다. 어떤 서비스를 선택하는가?

<details>
<summary>정답 및 해설 보기</summary>

**AWS Control Tower Landing Zone.** Organizations를 기반으로 공통 보안·관리 환경을 표준화한다.

</details>

### Q2. 신규 AWS 계정을 매번 표준 설정으로 자동 생성하려면 Control Tower의 어떤 기능인가?

<details>
<summary>정답 및 해설 보기</summary>

**Account Factory.** 새 계정을 표준화된 구성으로 프로비저닝한다.

</details>

### Q3. 전사 계정에서 지정되지 않은 리전의 EC2 API 호출을 아예 거부하려면 Control Tower의 어떤 유형이 적합한가?

<details>
<summary>정답 및 해설 보기</summary>

**Preventive Control.** SCP 등의 조직 정책을 통해 허용되지 않은 API 작업을 사전에 차단한다.

</details>

### Q4. EC2 필수 태그 누락을 발견해 관리자가 조치하도록 알림을 받고 싶다. 어떤 Control이며 추가 자동화는?

<details>
<summary>정답 및 해설 보기</summary>

**Detective Control(AWS Config Rule)**로 탐지한다. 알림은 EventBridge/SNS 등, 교정은 Lambda/SSM Automation 같은 추가 구성으로 처리한다.

</details>

### Q5. CloudFormation 템플릿 배포 전에 보안 규정을 검사하고 위반 리소스 생성을 막으려면?

<details>
<summary>정답 및 해설 보기</summary>

**Proactive Control(AWS CloudFormation Hooks).** 배포 전 검사이며 CloudFormation 프로비저닝 범위에 한정된다.

</details>

## 🇯🇵 日本語 Summary

AWS Control Tower は AWS Organizations を使い、セキュリティのベストプラクティスに基づく Landing Zone を構築・運用する。Account Factory はアカウントの標準化、Log Archive/Audit は中央監査を担当。Preventive Control は SCP/RCP などによって操作を防止し、Detective Control は AWS Config で違反を検知する。Proactive Control は CloudFormation Hooks で作成前に検証する。

**試験の判断:** マルチアカウント環境の標準構築は Control Tower、単純なアカウント組織管理は Organizations。

## 🇺🇸 English Summary

AWS Control Tower establishes a governed multi-account landing zone on top of AWS Organizations. Account Factory standardizes account provisioning, while shared log archive and audit accounts support central governance. Preventive controls block prohibited actions through SCPs, RCPs, and related mechanisms. Detective controls use AWS Config to detect noncompliance, and proactive controls use CloudFormation hooks before deployment.

**Exam decision:** Pick Control Tower for an automated, secure multi-account baseline and identify the control behavior by when it acts.

## 📚 Vocabulary

> GitHub 검색 시 영어 표제어 그대로 검색하면 관련 설명으로 이동하기 쉽다.

| English | 한국어 | 日本語 |
|---|---|---|
| AWS Control Tower | AWS 다중 계정 거버넌스 서비스 | AWS Control Tower |
| Landing Zone | 표준 다중 계정 환경 | ランディングゾーン |
| Account Factory | 계정 자동 생성·표준화 | アカウントファクトリー |
| Guardrail / Control | 가드레일·거버넌스 제어 | ガードレール／コントロール |
| Preventive Control | 예방형 제어 | 予防的コントロール |
| Detective Control | 탐지형 제어 | 発見的コントロール |
| Proactive Control | 사전 검증형 제어 | 事前検証コントロール |
| CloudFormation Hooks | 배포 전 검사 후크 | CloudFormation フック |
| Log Archive Account | 중앙 로그 보관 계정 | ログアーカイブアカウント |
| Audit Account | 보안 감사 계정 | 監査アカウント |
| Compliance | 규정 준수 | コンプライアンス |
| Remediation | 교정 조치 | 修復 |
