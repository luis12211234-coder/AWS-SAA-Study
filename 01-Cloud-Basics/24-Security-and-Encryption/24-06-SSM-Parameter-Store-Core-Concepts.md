# 📘 24-06. SSM Parameter Store: Storage, Hierarchy & Tiers

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Systems Manager` · `Parameter Store` · `String` · `StringList` · `SecureString` · `Hierarchy` · `Standard` · `Advanced` · `Parameter Policy`

## 🇰🇷 1. 설정값을 코드에서 분리해야 하는 이유
쇼핑몰 Lambda가 RDS에 접속하려면 **DB 주소·사용자·비밀번호**가 필요하다. 이 값을 프로그램 소스코드에 직접 쓰는 행위를 하드코딩(Hardcoding)이라고 한다. 저장소에 비밀번호가 노출되거나 교체할 때 코드를 수정해야 하므로 적절하지 않다.

**AWS Systems Manager(SSM) Parameter Store**는 이러한 애플리케이션 설정값을 중앙에 저장하는 Systems Manager의 기능이다. **RDS**는 실제 고객·주문 정보를 보관하고, **Parameter Store**는 앱이 RDS에 접속하기 위해 참고할 설정값을 보관한다. Parameter Store는 서버리스 관리형 서비스이며 IAM 접근 통제, 버전 추적, CloudFormation 통합을 지원한다.

## 🇰🇷 2. 세 가지 매개변수 유형
| 유형 | 의미 | 사용 예 |
|---|---|---|
| `String` | 일반 문자열 | DB 엔드포인트 |
| `StringList` | 쉼표로 구분된 문자열 값 | 특정 서비스 목록 |
| `SecureString` | KMS로 암호화해 저장하는 문자열 | DB 암호, API Key |

`SecureString`의 복호화는 SSM 조회 권한과 함께 **KMS Decrypt** 권한이 필요하다. 앱이 사용할 KMS Key의 Key Policy도 적절히 허용해야 한다.

## 🇰🇷 3. Hierarchical Parameters가 필요한 이유
먼저 Parameter Store의 목적은 **하드코딩 회피**다. 그러면 설정값이 여러 앱과 환경으로 늘어날 때 어떻게 구별할까? 이 **별개의 관리 문제**를 해결하는 기능이 **계층형 경로**다.

```text
/my-department/
  my-app/
    dev/
      db-url
      db-password
    prod/
      db-url
      db-password
  other-app/
```

물리적인 폴더를 만드는 것이 아니라 `/`를 포함하는 **매개변수 이름**을 저장한다. IAM 정책과 `GetParametersByPath` 호출에서 경로를 사용할 수 있다. Dev Lambda에는 Dev 경로, Prod Lambda에는 Prod 경로의 권한만 주는 방식이 유용하다.

**주의:** 부모 경로의 `GetParametersByPath`를 너무 넓게 허용하면 민감한 하위 경로도 읽힐 수 있다. AWS 공식 문서는 **경로별 최소 권한**과 부모 경로의 조회 권한을 특히 주의하라고 안내한다. [AWS 공식 문서](https://docs.aws.amazon.com/systems-manager/latest/userguide/ps-restrict-parameter-access.html).

## 🇰🇷 4. Standard와 Advanced
| 비교 | Standard | Advanced |
|---|---|---|
| 최대 값 크기 | 4KB | 8KB |
| 매개변수 개수(일반 할당량) | 10,000 | 100,000 |
| Parameter Policy | 불가 | 가능 |
| 저장 요금 | 기본 매개변수 저장 무료 | 개당 월 과금(강의 예시 $0.05) |

API 처리량 옵션과 운영 조건에 따른 과금은 별도 확인한다. **Parameter Policy**는 IAM Policy와 다르다. 접근 권한이 아니라 매개변수의 수명과 알림을 관리한다.

- **Expiration:** 특정 시점에 매개변수 삭제.
- **ExpirationNotification:** 만료 일정 전에 EventBridge 이벤트 발생.
- **NoChangeNotification:** 정해진 기간 값이 수정되지 않으면 이벤트 발생.

알림만으로 실제 DB 비밀번호가 자동 교체되는 것은 아니다. 필요한 자동화는 별도로 구현한다.

## 🇰🇷 5. 기타 통합
- **CloudFormation:** SSM 매개변수를 템플릿이나 스택의 설정값으로 참조.
- **AWS Public Parameters:** Amazon Linux 최신 AMI ID 등 AWS가 공개하는 매개변수 사용.
- **Secrets Manager 참조:** `/aws/reference/secretsmanager/` 경로에서 Secrets Manager의 Secret을 참조할 수 있다. 두 저장소에 비밀번호를 이중으로 저장해야 한다는 뜻은 아니다.
- **Parameter Versioning:** 갱신할 때 버전·수정 시각 등을 추적한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Parameter Store는 Systems Manager의 하위 기능, Secrets Manager는 별도 서비스다.
- String, StringList, SecureString의 차이를 구분한다.
- SecureString 사용에는 SSM 조회 권한과 KMS 복호화 권한이 분리되어 필요하다.
- Advanced Tier만 Parameter Policy(만료/알림)를 지원한다.
- 상위 경로의 GetParametersByPath는 민감 하위 항목 노출 위험이 있다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 앱이 사용하는 DB 접속 비밀번호를 코드에서 제거해 관리하려면 어떤 서비스 기능을 사용할 수 있는가?

<details>
<summary>정답 및 해설 보기</summary>

**SSM Parameter Store.** 코드가 아니라 별도 구성값 저장소에서 값을 조회한다.

</details>

### Q2. DB 주소와 DB 암호 각각을 저장하려 한다. 적절한 매개변수 유형은?

<details>
<summary>정답 및 해설 보기</summary>

**주소는 String, 암호는 SecureString.** SecureString은 KMS로 암호화한다.

</details>

### Q3. Dev/Prod 설정값을 /my-app/dev와 /my-app/prod로 나누는 이유는?

<details>
<summary>정답 및 해설 보기</summary>

**환경별 설정을 구분하고 경로 기준 조회·권한 관리를 쉽게 하려는 것.** 암호화 자체의 필수 조건은 아니다.

</details>

### Q4. 매개변수를 만료 시각에 삭제하도록 정책을 붙이려면 어느 티어가 필요한가?

<details>
<summary>정답 및 해설 보기</summary>

**Advanced Tier.** Expiration 등 Parameter Policy는 Advanced에서 사용할 수 있다.

</details>

### Q5. ExpirationNotification을 사용하면 RDS의 실제 비밀번호도 자동 변경되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 이벤트를 알릴 뿐 DB 비밀번호 갱신 자동화는 별도로 구현해야 한다.

</details>

## 🇯🇵 日本語 Summary

Parameter Store は AWS Systems Manager の機能で、アプリの設定値を中央管理する。String、StringList、KMS で暗号化する SecureString がある。階層的なパスは環境別の整理と権限管理に役立つ。Advanced Tier は Expiration などの Parameter Policy に対応する。

## 🇺🇸 English Summary

Parameter Store is a Systems Manager feature for central application configuration. Types include String, StringList, and KMS-encrypted SecureString. Hierarchical paths help segregate environments and IAM access. Advanced Tier supports parameter policies such as expiration and notifications.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Parameter Store | 매개변수 저장소 | パラメータストア |
| SecureString | 암호화 문자열 유형 | 暗号化文字列 |
| Hierarchical Parameter | 계층형 매개변수 | 階層型パラメータ |
| Standard Tier | 표준 티어 | 標準ティア |
| Advanced Tier | 고급 티어 | アドバンストティア |
| Expiration Policy | 만료 정책 | 有効期限ポリシー |
| Public Parameter | 공개 매개변수 | 公開パラメータ |
