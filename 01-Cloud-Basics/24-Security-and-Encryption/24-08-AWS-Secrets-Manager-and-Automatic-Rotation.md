# 📘 24-08. AWS Secrets Manager & Automatic Rotation

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Secrets Manager` · `Secret` · `Rotation` · `RDS` · `Aurora` · `KMS` · `Lambda Rotation` · `Parameter Store`

## 🇰🇷 1. Parameter Store 뒤에 왜 다른 서비스가 등장하는가
Parameter Store는 설정값과 SecureString을 **저장·조회**한다. 하지만 DB 비밀번호를 정기적으로 바꾸려면 **실제 DB 비밀번호**와 **앱이 조회할 비밀번호**를 일치시켜야 한다. Parameter Store의 값을 바꾸는 것만으로 DB 로그인 암호가 저절로 바뀌지는 않는다.

**AWS Secrets Manager**는 DB 자격 증명·API Key·토큰 등의 비밀정보를 저장하면서, **비밀번호 교체(Rotation) 수명 주기 관리**에 특화된 별도의 AWS 서비스다. 따라서 'Parameter Store에 비밀번호 보관 → Secrets Manager가 해당 값을 자동 수정'이 기본 구조가 아니다. 일반적으로 **Secrets Manager 자체에 Secret을 저장**한다.

## 🇰🇷 2. Rotation의 의미와 과정
```text
Secrets Manager에 기존 DB 자격 증명 보관
           ↓ 교체 일정
새 비밀번호 생성 → 실제 DB의 로그인 암호 변경
           ↓ 검증
Secrets Manager가 앱이 읽을 활성 자격 증명 갱신
           ↓
앱이 GetSecretValue로 새 암호 조회 → DB 접속
```

새 비밀번호를 **Secret에만** 반영하고 DB에는 반영하지 않으면 앱 로그인 실패가 발생한다. Rotation은 양쪽의 값을 맞추는 작업이다. 검증과 버전 레이블 관리로 교체 과정을 안전하게 수행한다.

## 🇰🇷 3. Lambda가 반드시 필요한가?
강의는 Lambda 함수로 새 비밀번호를 교체하는 방법을 강조한다. 하지만 현재는 **RDS/Aurora 등의 관리형 사용자 자격 증명은 Lambda 없는 Managed Rotation도 지원**한다. 일반적인 사용자 지정 시스템의 자격 증명 교체는 Lambda Rotation Function을 활용할 수 있다. [AWS 공식 문서](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets.html).

## 🇰🇷 4. 보안/권한과 AWS 통합
- Secret은 AWS KMS로 암호화된다. 필요한 경우 Customer Managed Key 사용.
- AWS IAM으로 `secretsmanager:GetSecretValue` 등을 허용/제한한다. Customer Managed KMS Key를 쓰면 해당 키 정책과 `kms:Decrypt`도 고려한다.
- RDS, Aurora, Redshift, DocumentDB 등과 긴밀한 자격 증명 통합을 제공한다.
- Resource-Based Policy로 Secret 자체의 접근 권한을 제어할 수 있다. 계정 간 공유는 리소스 정책과 IAM/KMS 조건을 함께 평가한다.

## 🇰🇷 5. Parameter Store와 선택 비교
| 요구사항 | 적합한 선택 |
|---|---|
| 비밀 여부와 관계없이 앱 설정값 중앙 저장 | Parameter Store |
| 간단한 암호화 문자열 저장 | Parameter Store SecureString도 가능 |
| 실제 RDS 암호와 저장된 암호를 주기적으로 자동 동기화 | **Secrets Manager** |
| DB 사용자 자격 증명과 밀접한 AWS 관리형 통합 | **Secrets Manager** |

Secrets Manager는 보통 Secret당/요청당 비용이 발생한다. 강의 가격은 **월 $0.40/Secret, API 10,000회당 $0.05**의 기본 예시이며 리전과 정책에 따라 다를 수 있다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Secrets Manager는 Parameter Store의 하위 기능이 아닌 별도 AWS 서비스다.
- 자동 교체는 저장된 Secret과 실제 대상 DB의 비밀번호를 함께 바꾸는 것이다.
- RDS/Aurora 관리형 회전은 Lambda 없는 Managed Rotation도 가능하다.
- KMS 암호화 + IAM/Resource Policy로 비밀정보 접근을 통제한다.
- SAA에서 DB 비밀번호 정기적 자동 교체 요구는 Secrets Manager 키워드.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. DB 비밀번호를 SecureString으로 보관하는 것만으로 RDS 비밀번호가 자동 변경되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** Parameter Store는 실제 DB 비밀번호를 자동으로 바꾸지 않는다.

</details>

### Q2. 매달 DB의 실제 비밀번호와 저장된 Secret을 일관되게 교체하려는 서비스는?

<details>
<summary>정답 및 해설 보기</summary>

**AWS Secrets Manager.** Rotation 기능으로 비밀번호 수명 주기를 관리한다.

</details>

### Q3. Secrets Manager가 새 비밀번호를 저장했지만 RDS 계정 비밀번호는 그대로라면 어떤 문제가 발생하는가?

<details>
<summary>정답 및 해설 보기</summary>

**애플리케이션 DB 로그인 실패.** 양쪽 자격 증명이 일치해야 한다.

</details>

### Q4. 모든 Secrets Manager Rotation에 사용자 작성 Lambda가 필수인가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 관리형 RDS/Aurora 자격 증명 등은 Lambda 없는 Managed Rotation을 지원한다.

</details>

### Q5. 앱이 CMK로 암호화한 Secret을 가져오려면 점검할 주요 권한은?

<details>
<summary>정답 및 해설 보기</summary>

**secretsmanager:GetSecretValue와 필요한 KMS Decrypt/Key Policy 권한.** Secret 접근과 암호화 키 접근을 구분한다.

</details>

## 🇯🇵 日本語 Summary

Secrets Manager は Parameter Store とは独立したサービスで、Secret の保存と自動ローテーションに特化する。ローテーションでは Secret と実際の DB 認証情報の両方を更新する。RDS/Aurora の Managed Rotation は Lambda 不要の場合があり、カスタム認証情報は Lambda 関数を利用できる。

## 🇺🇸 English Summary

AWS Secrets Manager is a standalone secret lifecycle service. Rotation updates both the stored secret and the target database/service credentials. Managed rotation for some RDS/Aurora credentials does not require Lambda, while custom rotation can use Lambda. IAM and KMS protect retrieval.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Secret | 비밀정보 | シークレット |
| Automatic Rotation | 자동 교체 | 自動ローテーション |
| Managed Rotation | 관리형 자동 교체 | マネージドローテーション |
| Rotation Lambda | 교체 Lambda 함수 | ローテーション Lambda |
| Resource-Based Policy | 리소스 기반 정책 | リソースベースポリシー |
| GetSecretValue | 비밀정보 조회 API | シークレット取得 API |
