# 📘 24-17. Amazon Macie: S3 Sensitive Data Discovery

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Amazon Macie` · `PII` · `Sensitive Data Discovery` · `S3` · `Pattern Matching` · `Machine Learning` · `EventBridge`

## 🇰🇷 1. Macie는 왜 필요한가
회사가 S3에 고객 주문 내역과 CSV 파일을 많이 저장하면 어느 파일에 **주민등록번호·이메일·전화번호·신용카드번호** 등 민감정보가 들어 있는지 일일이 알기 어렵다.

**Amazon Macie**는 머신러닝 및 패턴 매칭을 활용해 **Amazon S3에 저장된 민감정보를 발견하고 분류·보고**하는 관리형 데이터 보안/개인정보 보호 서비스다. GuardDuty가 이상한 *행동*, Inspector가 *취약점*, Macie가 *데이터 내용*을 찾는다는 차이가 핵심이다.

## 🇰🇷 2. PII(Personal Identifiable Information)
**PII**는 개인 식별이 가능한 정보를 가리킨다. 이름, 이메일, 전화번호, 주민등록번호 등은 문맥에 따라 PII로 분류될 수 있다. Macie는 사전 정의된 데이터 식별자와 맞춤 식별자 등으로 S3 객체 내용을 검사할 수 있다.

## 🇰🇷 3. S3 민감정보 발견 동작
```text
S3 Bucket: 고객정보 CSV, 보고서, 이미지 등
             ↓
    Amazon Macie 민감정보 분석
             ↓
   PII/민감정보 포함 객체 발견
             ↓
       Finding 생성
             ↓
EventBridge → SNS 알림 / Lambda 대응
```

Macie는 S3 버킷의 보안·접근 상태 등을 자동 평가할 수 있지만 **모든 S3 객체의 본문을 활성화 즉시 무조건 완전 검사하는 것은 아니다.** 민감정보 발견을 위해 다음 기능을 구성한다.

- **Automated Sensitive Data Discovery:** 객체를 샘플링하며 지속적인 민감정보 탐색.
- **Sensitive Data Discovery Job:** 선택한 버킷/객체 범위에 대해 실행하는 맞춤 탐색 작업.

## 🇰🇷 4. 발견 후 해야 할 일
Macie는 위험한 개인정보 파일을 찾고 Finding을 생성한다. 개인정보를 자동으로 삭제하거나 마스킹하고 접근을 모두 차단하는 서비스가 아니다. 발견 결과를 기반으로 **버킷 정책 개선, S3 접근 차단, KMS 암호화 정책 확인, Lambda 자동화** 같은 대응을 설계해야 한다.

## 🇰🇷 5. 시험 선택 기준
문제에 `S3` + `개인정보(PII)` + `민감 데이터 탐색/분류`가 나오면 **Macie**가 유력하다. S3 데이터 이벤트의 **수상한 액세스 탐지**라면 GuardDuty S3 Protection, S3 버킷에서 **어떤 객체에 개인정보가 있는지 찾는 일**은 Macie다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Macie = S3 저장 객체의 개인정보/민감정보 탐색과 분류.
- PII = 개인 식별 정보. ML·Pattern Matching 기반 탐색.
- Automated Discovery와 Discovery Job 등 분석 작업이 별도로 필요할 수 있다.
- Finding → EventBridge → SNS/Lambda 알림·대응 가능.
- Macie는 민감정보를 발견하되 데이터 자동 삭제/마스킹 서비스는 아니다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. S3의 수십만 CSV 중 주민등록번호를 포함한 객체를 발견해야 한다. 적합한 서비스는?

<details>
<summary>정답 및 해설 보기</summary>

**Amazon Macie.** S3 객체의 민감정보/PII를 탐색한다.

</details>

### Q2. 고객 개인 식별 정보를 뜻하는 보안 용어는?

<details>
<summary>정답 및 해설 보기</summary>

**PII(Personal Identifiable Information).** 개인을 식별할 수 있는 정보다.

</details>

### Q3. Macie 활성화 직후 모든 버킷의 모든 객체가 완전 분석된다고 단정할 수 있는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 민감정보 검색에 Automated Discovery 또는 Discovery Job 설정을 사용한다.

</details>

### Q4. Macie Finding 후 운영팀 이메일/SMS 계열 알림을 연계하려 한다. 어떤 흐름인가?

<details>
<summary>정답 및 해설 보기</summary>

**EventBridge → SNS.** Finding을 이벤트 라우팅으로 알림화할 수 있다.

</details>

### Q5. S3 객체에 개인정보가 포함됐다는 사실 자체를 Macie가 발견하면 파일이 자동 삭제되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 발견과 보고가 주 역할이며 삭제 등 대응은 별도 구성한다.

</details>

## 🇯🇵 日本語 Summary

Amazon Macie は S3 内の PII や機密データを機械学習とパターンマッチングで検出する。Automated Sensitive Data Discovery や Discovery Job を利用し、Finding を EventBridge/SNS/Lambda に連携可能。検出した情報を自動削除するサービスではない。

## 🇺🇸 English Summary

Amazon Macie discovers and classifies sensitive data and PII in Amazon S3 using machine learning and pattern matching. Automated discovery and discovery jobs analyze objects; findings integrate with EventBridge. Macie identifies data exposure risks but does not automatically delete all sensitive objects.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Personally Identifiable Information | 개인 식별 정보 | 個人識別情報 |
| Sensitive Data | 민감정보 | 機密データ |
| Sensitive Data Discovery | 민감정보 탐색 | 機密データ検出 |
| Pattern Matching | 패턴 일치 검사 | パターンマッチング |
| Discovery Job | 탐색 작업 | 検出ジョブ |
| Data Classification | 데이터 분류 | データ分類 |
