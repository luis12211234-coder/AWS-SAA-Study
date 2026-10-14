# 📘 24-02. AWS KMS: Keys, Policies, Rotation & CLI

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `AWS KMS` · `AWS Managed Key` · `Customer Managed Key` · `Key Policy` · `IAM` · `Rotate Key` · `AWS CLI` · `CloudTrail`

## 🇰🇷 1. AWS KMS는 어떤 서비스를 담당하는가
AWS Key Management Service(KMS)는 AWS 서비스가 암호화에 사용하는 **KMS Key와 권한을 관리**한다. 키 접근은 Key Policy와 IAM 등으로 통제하며 KMS API 사용은 CloudTrail 감사와 연결할 수 있다. KMS Key는 기본적으로 특정 AWS 리전에 속한다.

## 🇰🇷 2. KMS Key의 종류
| 구분 | 누가 관리하는가 | 계정 간 키 사용 허가 | 주요 특징 |
|---|---|---|---|
| AWS Owned Key | 서비스 운영 주체인 AWS | 고객이 직접 제어하지 않음 | 고객 KMS 계정에 키 노출되지 않을 수 있음 |
| AWS Managed Key (`aws/ebs` 등) | AWS | 고객이 Key Policy를 수정할 수 없어 사실상 불가 | 서비스가 대신 생성·관리 |
| Customer Managed Key(CMK) | 고객 | Key Policy 등으로 가능 | 직접 정책·별칭·교체 관리, 키 비용 발생 |

서비스마다 선택 가능한 키 유형이 다를 수 있으므로 세 유형 모두를 모든 서비스에서 마음대로 고를 수 있다고 가정하지 않는다.

**대칭 키**는 동일한 비밀 키 재료로 암호화/복호화한다. **비대칭 키**는 공개키/개인키 쌍을 사용한다. KMS 비대칭 개인키는 KMS 외부로 내보낼 수 없다. **BYOK(Imported Key Material)**은 외부에서 생성한 실제 암호화 키 재료를 KMS로 안전하게 가져오는 방식이다.

## 🇰🇷 3. Key Policy와 IAM의 관계
KMS Key는 IAM Allow 하나만 존재한다고 항상 사용 가능한 것이 아니다. **Key Policy가 IAM 정책의 권한 위임을 허용해야** 해당 IAM Allow가 유효하다(또는 Key Policy가 직접 해당 주체를 허용). 기본 Key Policy는 보통 계정에서 IAM으로 제어하도록 허용한다. 계정 간 키 공유에는 **원본 키 소유 계정의 Key Policy + 대상 계정의 IAM 권한**을 함께 확인한다.

`kms:Decrypt`, `kms:Encrypt`, `kms:GenerateDataKey`, `kms:CreateGrant`, `kms:DescribeKey`는 각각 복호화, 암호화, Data Key 생성, 서비스에 권한 위임, 키 정보 확인에 사용된다.

## 🇰🇷 4. KMS Key Rotation: 자동 vs 수동
**지원되는 대칭 Customer Managed Key(AWS_KMS 원본 키 재료)**의 자동 교체 주기는 현재 **90~2,560일** 사이로 설정 가능하며, 기본값은 365일이다. 자동 교체 시 **Key ID/ARN/정책/별칭은 유지**되고 내부 키 재료가 교체되며, 이전 키 재료는 과거 암호문 복호화를 위해 보관된다. 이미 저장된 S3/EBS 데이터가 전부 재암호화되는 것은 아니다.

**수동 교체**는 새 KMS Key를 만들고 alias 또는 서비스 설정을 새 키로 옮기는 것. 기존 데이터의 복호화에는 이전 키가 필요하므로 삭제하면 안 된다. 비대칭 키, 가져온 키 재료, Custom Key Store 등은 일반적인 자동 교체 대상이 아니다. AWS Managed Key는 고객이 교체 주기를 지정할 수 없다.

> **강의/퀴즈 정정:** 예전에는 3개월 주기 자동 교체가 불가능하다고 출제됐지만 현재 **90일 자동 교체가 가능**하다(지원 대상 키). [AWS 공식 문서](https://docs.aws.amazon.com/kms/latest/developerguide/rotating-keys-enable.html).

## 🇰🇷 5. CLI 암호화 실습 원리
- `aws kms encrypt`: 소량의 평문을 지정된 KMS Key로 암호화 → `CiphertextBlob` 반환.
- CLI 출력에는 암호문을 Base64 문자열로 표시할 수 있음. Base64를 디코딩해도 여전히 **암호문**이다.
- `aws kms decrypt`: 암호문을 적합한 KMS Key로 복호화. 성공하려면 권한 및 암호화 컨텍스트 등 조건이 맞아야 한다.
- 대용량 데이터에는 `GenerateDataKey`와 Envelope Encryption을 사용한다.
- 암호문과 평문을 로그·콘솔에 불필요하게 남기지 않는다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- AWS Managed Key의 Key Policy는 고객이 수정할 수 없으므로 계정 간 공유 암호화에는 CMK가 핵심이다.
- Key Policy는 KMS 권한 평가에서 필수다. IAM Allow만 보고 판단하지 않는다.
- 지원되는 CMK 자동 교체는 90~2,560일, 기본 365일이다. 강의의 오래된 퀴즈에 주의.
- 자동 교체는 Key ID를 유지하며 과거 데이터 복호화를 가능하게 한다.
- Imported Key Material/BYOK는 임의의 별칭이 아니라 외부 생성 키 재료다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 다른 AWS 계정이 암호화된 EBS 스냅샷의 키를 사용하게 해야 한다. 적합한 키 유형은?

<details>
<summary>정답 및 해설 보기</summary>

**Customer Managed Key.** 소유자가 Key Policy를 수정해 다른 계정에 키 사용을 허가할 수 있다.

</details>

### Q2. CMK의 IAM Role에 kms:Decrypt를 Allow 했지만 Key Policy가 IAM 위임도 역할의 접근도 허용하지 않는다. 복호화할 수 있는가?

<details>
<summary>정답 및 해설 보기</summary>

**불가능하다.** Key Policy까지 허용해야 정상적으로 키 접근을 평가할 수 있다.

</details>

### Q3. 2026년 기준 대칭 AWS_KMS 원본 CMK에 대해 90일 자동 교체를 설정할 수 있는가?

<details>
<summary>정답 및 해설 보기</summary>

**가능하다.** 지원 대상 대칭 CMK는 90~2,560일 주기를 지정할 수 있다.

</details>

### Q4. KMS 자동 교체 후 이전에 암호화된 EBS 데이터는 모두 즉시 다시 암호화되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 내부 키 재료가 갱신되지만 저장 데이터 자체가 자동으로 재암호화되지는 않는다.

</details>

### Q5. 외부에서 직접 랜덤 비밀 키를 생성하여 KMS에 넣는 기능과 단순 별칭 변경은 같은가?

<details>
<summary>정답 및 해설 보기</summary>

**다르다.** Imported Key Material(BYOK)은 키 재료 자체의 원본을 외부로 두는 것이다.

</details>

## 🇯🇵 日本語 Summary

AWS KMS は KMS Key と暗号化権限を管理する。AWS Managed Key の Key Policy は顧客が変更できず、クロスアカウント共有には Customer Managed Key が必要。Key Policy と IAM の両方を評価する。対応する対称 CMK の自動ローテーションは 90～2,560 日で、Key ID は変わらない。

## 🇺🇸 English Summary

AWS KMS manages encryption keys and permissions. Customer managed keys support configurable key policies and cross-account use. IAM permissions must be consistent with key policies. Eligible symmetric CMKs support 90–2,560-day automatic key rotation without changing the key ID. KMS CLI Encrypt/Decrypt handles small payloads; data keys handle bulk encryption.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Key Policy | 키 정책 | キーポリシー |
| Customer Managed Key | 고객 관리형 키 | カスタマーマネージドキー |
| AWS Managed Key | AWS 관리형 키 | AWS マネージドキー |
| Imported Key Material | 가져온 키 재료 | インポートされたキーマテリアル |
| Key Rotation | 키 교체 | キーローテーション |
| CreateGrant | 서비스 사용 권한 위임 | グラント作成 |
| Ciphertext Blob | 암호문 블롭 | 暗号文ブロブ |
