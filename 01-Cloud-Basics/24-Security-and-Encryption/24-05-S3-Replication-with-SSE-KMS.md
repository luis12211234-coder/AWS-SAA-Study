# 📘 24-05. S3 Replication, SSE-KMS & MRK Caveat

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `S3 Replication` · `SSE-S3` · `SSE-KMS` · `DSSE-KMS` · `SSE-C` · `kms:Decrypt` · `kms:Encrypt` · `MRK`

## 🇰🇷 1. S3 복제와 암호화의 관계
S3 Replication은 소스 버킷 객체를 대상 버킷으로 복제한다. **객체의 암호화 방식에 따라 필요한 복제 설정과 IAM/KMS 권한이 달라진다.** S3 Replication이 데이터 암호문을 무조건 그대로 복사하는 것으로 이해하면 안 된다.

## 🇰🇷 2. 암호화 유형별 주의사항
- **SSE-S3:** S3가 관리하는 키 기반 저장 시 암호화. 일반적인 S3 복제 구성으로 처리한다.
- **SSE-C:** 고객 제공 암호화 키. 지원되는 복제 동작에 따라 구성한다.
- **SSE-KMS / DSSE-KMS:** KMS 기반 암호화. 소스 객체 복제를 **명시적으로 활성화(Opt-in)** 하고 대상 암호화에 사용할 KMS Key를 설정해야 한다.

## 🇰🇷 3. SSE-KMS 복제에서 실제로 필요한 것
```text
S3 Source Bucket (SSE-KMS, Key A)
             │
      S3 Replication Role
      ├─ 소스 접근 S3 권한
      ├─ 소스 Key A: kms:Decrypt
      └─ 대상 Key B: kms:Encrypt
             │
             ↓
S3 Destination Bucket (SSE-KMS, Key B)
```

**S3 복제 역할의 IAM Policy, 관련 KMS Key Policy, 대상 버킷 정책** 등을 서로 맞춰야 한다. 다른 계정으로 복제한다면 계정 간 권한을 추가 확인한다. 평문 객체를 SSE-KMS 기본 암호화 대상 버킷으로 복제할 때는 `kms:GenerateDataKey` 등 추가 작업이 필요할 수 있다.

## 🇰🇷 4. MRK를 써도 S3 Replication은 특례가 아니다
Multi-Region KMS Key는 원칙적으로 다른 리전의 호환 키로 암호문을 다룰 수 있다. 그러나 **Amazon S3는 MRK를 단일 리전 키처럼 취급하며 MRK의 리전 간 호환 이점을 S3 Replication에서 활용하지 않는다.** 따라서 MRK를 사용해도 필요한 KMS 복호화/암호화 작업을 건너뛸 수 없다.

AWS 공식 확인: [Replicating encrypted objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication-config-for-kms-objects.html).

## 🇰🇷 5. 시험 접근 방법
문제에 `S3 Replication + SSE-KMS`가 보이면 **옵트인, 대상 KMS Key, 복제 Role의 소스 Decrypt/대상 Encrypt 권한**을 먼저 점검한다. `MRK를 사용하면 복제 시 재암호화가 필요 없다`는 선택지는 S3에 대해서는 오답이다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- SSE-KMS/DSSE-KMS 객체 복제는 명시적 설정이 필요하다.
- 소스 KMS Decrypt, 대상 KMS Encrypt 권한 및 Key Policy를 검토한다.
- Cross-Account 복제는 S3 버킷 정책과 키 정책까지 교차 점검한다.
- S3는 MRK를 일반 리전별 키처럼 취급해 MRK의 특별한 최적화를 사용하지 않는다.
- 객체 복제와 암호화 키 복제는 별도 동작이다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. SSE-KMS 객체가 복제되지 않는다. 기본 S3 복제만 구성했다면 첫 번째로 점검할 것은?

<details>
<summary>정답 및 해설 보기</summary>

**SSE-KMS 객체 복제 활성화와 대상 KMS Key 설정.** 기본 구성만으로는 해당 객체가 복제되지 않을 수 있다.

</details>

### Q2. S3 복제 Role이 소스 KMS Key에서 수행할 대표 작업은?

<details>
<summary>정답 및 해설 보기</summary>

**kms:Decrypt.** 원본 암호화 데이터를 복제 과정에서 읽는 데 필요한 권한이다.

</details>

### Q3. 대상 객체를 SSE-KMS로 암호화하려면 복제 Role에 필요한 대표 KMS 작업은?

<details>
<summary>정답 및 해설 보기</summary>

**kms:Encrypt.** 대상 KMS Key를 사용한 암호화가 필요하다.

</details>

### Q4. S3 Cross-Region Replication에서 MRK 사용만으로 소스/대상 KMS 작업을 생략할 수 있는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** S3는 MRK의 다중 리전 특성을 복제 최적화에 사용하지 않는다.

</details>

### Q5. SSE-S3 객체와 SSE-KMS 객체의 복제 설정에서 흔히 다른 점은?

<details>
<summary>정답 및 해설 보기</summary>

**SSE-KMS는 명시적 옵트인과 KMS Key 권한**이 필요하다는 점이다.

</details>

## 🇯🇵 日本語 Summary

S3 Replication で SSE-KMS/DSSE-KMS のオブジェクトを複製するには明示的な設定と、ソースキーの `kms:Decrypt`、送信先キーの `kms:Encrypt` が必要。S3 は MRK を単一リージョンキーのように扱い、MRK 特有の利点を複製処理に利用しない。

## 🇺🇸 English Summary

Replicating SSE-KMS or DSSE-KMS encrypted S3 objects requires opt-in, a destination KMS key, and source decrypt/destination encrypt permissions. Amazon S3 treats multi-Region KMS keys as single-Region keys for replication and does not exploit MRK-specific multi-Region properties.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| S3 Replication | S3 객체 복제 | S3 レプリケーション |
| SSE-KMS | KMS 기반 서버 측 암호화 | KMS によるサーバー側暗号化 |
| Replication Role | 복제 역할 | レプリケーションロール |
| Opt-in | 명시적 활성화 | 明示的な有効化 |
| Destination KMS Key | 대상 KMS 키 | 宛先 KMS キー |
| DSSE-KMS | 이중 계층 KMS 서버 암호화 | 二重レイヤー KMS 暗号化 |
