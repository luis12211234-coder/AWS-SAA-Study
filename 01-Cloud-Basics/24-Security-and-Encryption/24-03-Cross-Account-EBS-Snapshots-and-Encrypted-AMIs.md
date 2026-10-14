# 📘 24-03. Cross-Account EBS Snapshots & Encrypted AMIs

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Encrypted EBS Snapshot` · `AMI` · `Launch Permission` · `KMS Key Policy` · `Cross-Account` · `ReEncrypt`

## 🇰🇷 1. 문제: 리소스 공유와 암호화 키 공유는 별도다
A 계정의 EBS 스냅샷이나 AMI를 B 계정이 사용하려 할 때 **리소스 접근 권한**만으로는 부족할 수 있다. 데이터가 A 계정의 Customer Managed KMS Key로 암호화되어 있다면 B는 그 키의 사용 권한도 필요하기 때문이다.

## 🇰🇷 2. 암호화된 EBS 스냅샷 공유
1. **A 계정:** 스냅샷 공유 권한을 B 계정에 부여한다.
2. **A 계정:** 원본 Customer Managed Key의 **Key Policy**에서 B의 적절한 키 사용을 허용한다.
3. **B 계정:** 사용할 IAM Role/User에 필요한 KMS 권한을 부여한다.
4. **B 계정:** 공유 스냅샷을 복사하면서 **B 소유 CMK**로 다시 암호화할 수 있다. 복사 후 새 스냅샷은 B가 관리한다.

A의 원본과 B가 복사한 스냅샷은 별개의 리소스다. A의 KMS Key 사용 권한을 취소하더라도 이미 B 키로 독립 복사한 스냅샷은 B 키로 복호화할 수 있다.

## 🇰🇷 3. 암호화된 AMI로 다른 계정에서 EC2 생성
AMI는 EC2를 만드는 템플릿이며 EBS 스냅샷을 참조할 수 있다. A가 B에게 **AMI Launch Permission**을 부여하고, AMI 기반 EBS 볼륨에 필요한 A의 **KMS Key 권한**도 제공해야 한다.

```text
A 계정: Encrypted AMI + A의 CMK
     │
     ├─ AMI Launch Permission → B 계정
     └─ A의 Key Policy → B 계정의 KMS 사용 허용
                             ↓
             B 계정 IAM Role/User 권한
                             ↓
                 AMI로 B 계정 EC2 실행
                 (선택: B의 CMK로 재암호화)
```

키 작업에는 상황에 따라 `kms:DescribeKey`, `kms:Decrypt`, `kms:ReEncrypt*`, `kms:CreateGrant`, `kms:GenerateDataKey*` 등이 필요하다. 권한은 실제 필요한 키·리소스와 작업으로 최소화한다.

## 🇰🇷 4. 왜 `aws/ebs`로 암호화하면 공유가 어려운가
`aws/ebs`는 **AWS Managed Key**이므로 사용자가 Key Policy를 수정해 외부 계정에 권한을 부여할 수 없다. 따라서 **공유할 AMI/스냅샷은 Customer Managed Key로 암호화**해야 한다. 이미 `aws/ebs`로 암호화된 AMI는 동일 계정에서 CMK로 재암호화한 사본을 만든 뒤 그 사본을 공유하는 접근을 취한다.

## 🇰🇷 5. 시험에서 구분할 관계
**AMI 공유 ≠ KMS Key 공유**이고 **스냅샷 공유 ≠ 스냅샷 복사**다. 교차 계정 설계는 이 두 축의 권한/소유권을 각각 확인해야 한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Cross-Account KMS 암호화에는 AWS Managed Key가 아닌 CMK가 중요.
- AMI 공유는 Launch Permission + KMS 권한이 결합되어야 한다.
- 공유 스냅샷과 자신의 키로 복사한 스냅샷은 소유권/키 의존성이 다르다.
- KMS Key Policy(키 소유 계정) + IAM(사용 계정) 두 층을 점검한다.
- B가 재암호화한 독립 사본은 B의 KMS Key를 사용한다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. A가 B에게 AMI Launch Permission만 주었다. 해당 AMI의 EBS 스냅샷이 A의 CMK로 암호화되어 있다. B가 추가로 확인할 것은?

<details>
<summary>정답 및 해설 보기</summary>

**KMS 키 사용 권한.** AMI를 실행할 권한과 암호화 키를 사용할 권한은 별개다.

</details>

### Q2. A가 `aws/ebs`로 암호화한 AMI를 B에 공유하려고 한다. 적합한 해결은?

<details>
<summary>정답 및 해설 보기</summary>

**A 계정에서 CMK로 재암호화한 AMI 사본을 만들어 공유.** AWS Managed Key의 Key Policy를 외부 계정에 맞게 수정할 수 없다.

</details>

### Q3. B가 공유 스냅샷을 B의 CMK로 복사했다. A가 원본 스냅샷을 삭제하면 B의 사본도 함께 삭제되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 복사한 스냅샷은 독립 리소스다.

</details>

### Q4. 원본 CMK 소유 계정에서 B에게 키 사용을 허용했지만 B의 Role에 KMS 권한이 없다. 해결하려면?

<details>
<summary>정답 및 해설 보기</summary>

**B Role의 IAM 정책**에 필요한 KMS 작업을 허용하고 Key Policy 조건도 확인한다.

</details>

### Q5. AMI 공유와 KMS 암호화 사이에서 가장 흔한 실수는?

<details>
<summary>정답 및 해설 보기</summary>

**AMI 사용 권한만 부여하고 키 사용 권한을 빠뜨리는 것.** 두 권한은 분리해 평가한다.

</details>

## 🇯🇵 日本語 Summary

暗号化された EBS スナップショットや AMI のクロスアカウント利用には、リソース共有権限と KMS Key の利用権限の両方が必要。AMI は Launch Permission を付与する。AWS Managed Key (`aws/ebs`) はキーポリシーを編集できないため、共有には Customer Managed Key を使う。

## 🇺🇸 English Summary

Cross-account encrypted snapshot/AMI access requires both resource permissions and KMS key permissions. AMI launch permission alone is insufficient. AWS managed `aws/ebs` keys cannot be shared through editable key policies; use a customer managed key and optionally re-encrypt copies under the destination account key.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Launch Permission | AMI 시작 권한 | AMI 起動許可 |
| Encrypted AMI | 암호화된 AMI | 暗号化された AMI |
| Encrypted Snapshot | 암호화된 스냅샷 | 暗号化スナップショット |
| Cross-Account Access | 계정 간 접근 | クロスアカウントアクセス |
| Re-encryption | 재암호화 | 再暗号化 |
| Key Policy | 키 정책 | キーポリシー |
