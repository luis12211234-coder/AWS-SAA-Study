# 📘 24-09. Secrets Manager Console & Multi-Region Secrets

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Secret Type` · `JSON Secret` · `Resource Policy` · `Multi-Region Secrets` · `Replica Secret` · `Promote Replica` · `KMS`

## 🇰🇷 1. 콘솔 실습에서 Secret을 어떻게 만드는가
Secrets Manager에서 `Store a new secret`을 선택하면 저장할 자격 증명 유형을 고를 수 있다. **RDS, DocumentDB, Redshift 등의 DB 자격 증명**을 선택할 수도 있고, **Other type of secret**으로 임의의 API Key 등을 저장할 수도 있다.

```json
{
  "MySecretKey": "example-only-value",
  "API_KEY": "placeholder-not-a-real-key"
}
```

키와 값을 UI에서 입력하거나 JSON 텍스트로 편집할 수 있다. **KMS Key**를 선택해 암호화하고, 이름(예: `prod/my-secret`)을 정한 뒤 생성한다. 실습의 임의 값은 GitHub에 실제 자격 증명으로 업로드하지 않는다.

## 🇰🇷 2. 누구에게 Secret 조회를 허가하는가
**IAM Policy**는 호출 주체의 권한을, **Secret Resource Policy**는 해당 Secret 자체에 대한 접근 제어를 정의할 수 있다. S3 Bucket Policy와 비슷한 JSON 형식이다. KMS CMK를 사용하면 KMS Key Policy도 함께 확인한다.

## 🇰🇷 3. Multi-Region Secret 복제
전 세계 앱의 지연 감소 및 재해 복구를 위해 기본 리전의 Secret을 다른 리전에 복제할 수 있다.

```text
us-east-1                ap-southeast-1
Primary Secret  ────────> Replica Secret (읽기 전용)
    │                        │
    └─ 업데이트/Rotation ────> 동기화
```

**Primary Secret**이 변경되면 **Replica Secret**도 동기화된다. 리전별 암호화에 사용되는 KMS Key 설정을 고려한다. 원본 리전 장애 시 **Replica를 독립형 Secret으로 승격(Promote)**할 수 있다. 승격 후에는 원본과 별개로 관리한다.

## 🇰🇷 4. Secret 복제 ≠ 데이터베이스 복제
다중 리전 Secret은 이름·암호 등 **비밀정보를 복제하는 기능**이다. Aurora Global Database 등의 **DB 데이터**는 각 DB의 별도 복제 기능이 담당한다. 지역 DB가 동일한 자격 증명을 받아들이도록 구성되어 있어야 해당 Secret으로 로그인할 수 있다.

## 🇰🇷 5. 실습에서 선택 가능했던 부가 기능
- **Automatic Rotation:** 일정과 교체 방법 지정.
- **Resource Permissions:** 다른 AWS 계정/역할의 Secret 접근 정책 관리.
- **Cross-Region Replication:** 대상 리전과 암호화 키 지정.
- **SDK 코드 예시:** 애플리케이션에서 `GetSecretValue`로 조회.

강사는 유료 서비스의 비용 발생을 피하기 위해 실제 Secret 생성/복제 실행을 생략했다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- RDS/Aurora 자격 증명 Secret과 일반 JSON Secret을 구분한다.
- Multi-Region Secret은 Primary와 기본적으로 읽기 전용 Replica 구조다.
- 지역 장애 시 Replica Secret을 독립형으로 Promote 가능하다.
- Secret 복제는 RDS/Aurora 데이터베이스를 자동 복제하지 않는다.
- Secret Resource Policy와 KMS Key Policy는 다른 권한 계층이다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 외부 API Key를 JSON으로 저장하려면 Secrets Manager의 어떤 유형이 적절한가?

<details>
<summary>정답 및 해설 보기</summary>

**Other type of secret.** 임의의 키-값 데이터를 저장할 수 있다.

</details>

### Q2. 일본 리전 애플리케이션이 현지 리전에서 Secret을 조회하려 한다. 어떤 기능을 고려할까?

<details>
<summary>정답 및 해설 보기</summary>

**Multi-Region Secrets 복제.** 일본 리전에 Replica Secret을 둘 수 있다.

</details>

### Q3. Primary Secret을 새 값으로 변경하면 복제 리전 Secret은 어떻게 되는가?

<details>
<summary>정답 및 해설 보기</summary>

**복제본에 변경 값이 동기화된다.** 기본적으로 Replica는 읽기 전용이다.

</details>

### Q4. Primary 리전 장애로 Replica가 독립 관리되어야 한다. 사용할 작업은?

<details>
<summary>정답 및 해설 보기</summary>

**Promote Replica.** 복제본을 독립형 Secret으로 승격한다.

</details>

### Q5. Multi-Region Secret 복제만 활성화하면 Aurora DB 데이터도 다른 리전으로 자동 복제되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** Secret과 DB 데이터는 서로 다른 복제 대상이며 기능도 별개다.

</details>

## 🇯🇵 日本語 Summary

Secrets Manager では DB 認証情報または JSON 形式の任意の Secret を保存できる。Resource Policy と KMS Key でアクセス制御する。Multi-Region Secrets は Primary と読み取り専用 Replica を同期し、障害時に Replica を独立型 Secret へ昇格できる。DB データ自体は複製しない。

## 🇺🇸 English Summary

Secrets Manager stores both database credentials and arbitrary JSON key-value secrets. Secret resource policies and KMS protect access. Multi-Region Secrets replicate a primary to read-only replicas, which can be promoted during recovery. Secret replication does not replicate the database.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| JSON Secret | JSON 형식 비밀정보 | JSON 形式シークレット |
| Primary Secret | 원본 비밀정보 | プライマリシークレット |
| Replica Secret | 복제 비밀정보 | レプリカシークレット |
| Promote Replica | 복제본 승격 | レプリカの昇格 |
| Cross-Region Replication | 리전 간 복제 | クロスリージョンレプリケーション |
| Resource Policy | 리소스 정책 | リソースポリシー |
