# S3 Object Lambda

## 🇰🇷 1. S3 Object Lambda란?

S3 Object Lambda는 S3 Object가 Application에 반환되기 전에 Lambda Function을 이용해 데이터를 동적으로 변환하는 기능입니다.

핵심 아이디어:

```text
S3 Original Object
        ↓
Lambda Transformation
        ↓
Modified Response
        ↓
Application
```

원본 Object의 여러 변형본을 별도의 Bucket에 저장하지 않고 요청 시점에 데이터를 변환할 수 있습니다.

---

## 2. 왜 필요한가?

하나의 Dataset을 여러 Application이 사용한다고 가정합니다.

```text
S3 Bucket
   │
   ├─ E-Commerce App
   ├─ Analytics App
   └─ Marketing App
```

각 Application이 원하는 데이터 형태가 다를 수 있습니다.

```text
E-Commerce
→ Original Data

Analytics
→ PII 제거

Marketing
→ Customer Loyalty Data 추가
```

각각 별도의 Object를 저장하면 데이터 복제와 관리가 필요합니다.

Object Lambda는 원본 하나를 유지하면서 요청 시 필요한 형태로 변환할 수 있습니다.

---

## 3. 기본 구조

Object Lambda는 S3 Access Point와 Lambda Function을 사용합니다.

```text
Application
     ↓
Object Lambda Access Point
     ↓
Lambda Function
     ↓
Supporting S3 Access Point
     ↓
S3 Bucket
```

Lambda Function이 Object를 처리한 뒤 변환된 결과를 Application에 반환합니다.

---

## 4. 원본 Object는 변경되지 않는다

Object Lambda의 핵심은 원본 Object를 직접 수정하는 것이 아닙니다.

```text
S3 Original
    │
    ▼
Lambda
    │
    ▼
Transformed Response
```

S3에 저장된 원본은 그대로 유지됩니다.

---

## 5. PII Redaction

대표적인 사용 사례는 개인식별정보 제거입니다.

```text
Original Data

Name
Phone Number
Address
Purchase Amount

       ↓ Lambda

Analytics Data

Purchase Amount
```

분석 Application에는 필요한 정보만 반환할 수 있습니다.

---

## 6. Data Format 변환

데이터 형식을 요청 시점에 변환할 수도 있습니다.

```text
S3
XML
 ↓
Lambda
 ↓
JSON
 ↓
Application
```

원본 XML Object를 별도의 JSON Object로 미리 저장할 필요가 없습니다.

---

## 7. Image Transformation

이미지 크기 변경이나 Watermark 적용도 가능합니다.

```text
Original Image
     ↓
Lambda
     ↓
Resize / Watermark
     ↓
User
```

S3의 Original Image는 그대로 유지됩니다.

---

## 8. Data Enrichment

외부 Data Source의 정보를 추가하여 Object를 보강할 수도 있습니다.

```text
S3 Data
   │
   ▼
Lambda ← Loyalty Database
   │
   ▼
Enriched Data
   │
   ▼
Marketing App
```

---

## 9. Access Point와의 관계

일반 S3 Access Point는 접근 관리를 단순화하는 것이 주 목적입니다.

```text
S3 Access Point
= "어떤 입구로 S3에 접근할 것인가?"
```

Object Lambda는 Object를 반환하기 전에 변환하는 것이 목적입니다.

```text
S3 Object Lambda
= "Object를 어떤 형태로 바꿔서 반환할 것인가?"
```

---

## ⚠️ Current AWS Note

2025년 11월 7일부터 S3 Object Lambda는 신규 고객에게 일반적으로 제공되지 않습니다.

현재는 기존 S3 Object Lambda 고객과 일부 AWS Partner Network(APN) Partner가 계속 사용할 수 있습니다.

AWS는 유사한 요구사항에 대해 CloudFront 기반 변환, API Gateway / Function URL 등을 통한 Lambda 호출 등의 대안을 안내하고 있습니다.

중요:

```text
AWS Lambda 자체
→ 계속 사용 가능

S3 Object Lambda
→ 신규 고객 사용 제한
```

따라서 강의와 SAA 학습에서는 개념을 이해하되 현재 신규 Architecture 설계에서는 이 Availability 변경을 알고 있어야 합니다.

---

## 🎯 Exam Scenario

강의 기준 다음과 같은 요구사항에서 S3 Object Lambda를 생각합니다.

```text
S3 Original Object
+
Do not create multiple copies
+
Transform data when requested
        ↓
S3 Object Lambda
```

대표 키워드:

```text
PII Redaction
XML → JSON
Image Resize
Watermark
Data Enrichment
```

---

## 🔑 핵심 정리

```text
S3 Object Lambda
= Object 반환 시 Lambda로 동적 변환

원본
= 그대로 유지

사용 사례
= PII 제거
+ Format 변환
+ Resize
+ Watermark
+ Data Enrichment

필요 구성
= Object Lambda Access Point
+ Lambda
+ Supporting Access Point

현재
= 신규 고객 사용 제한
```

한 줄 정리:

> **S3 원본을 복제하지 않고 요청 시점에 데이터를 변환해서 반환한다 = S3 Object Lambda**

---

## 🇯🇵 日本語 Summary

S3 Object Lambdaは、S3 ObjectをApplicationへ返す前にAWS Lambdaを使用してデータを動的に変換する機能です。

PIIの削除、データ形式の変換、画像のResize、Watermark、Data Enrichmentなどに利用できます。

元のS3 Objectは変更されません。

2025年11月7日以降、S3 Object Lambdaは原則として新規Customerには提供されていません。

---

## 🇺🇸 English Summary

S3 Object Lambda uses AWS Lambda to dynamically transform S3 data before it is returned to an application.

Common use cases include PII redaction, format conversion, image resizing, watermarking, and data enrichment.

The original S3 object remains unchanged.

As of November 7, 2025, S3 Object Lambda is generally available only to existing customers and select APN partners.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Object Lambda | オブジェクトLambda | 객체 람다 |
| Transformation | 変換 | 변환 |
| Redaction | マスキング / 削除 | 민감정보 제거 |
| PII | 個人識別情報 | 개인식별정보 |
| Watermark | ウォーターマーク | 워터마크 |
| Data Enrichment | データ拡張 | 데이터 보강 |
| Supporting Access Point | サポートアクセスポイント | 지원 액세스 포인트 |

---

## 📝 Review Questions

<details>
<summary>Q1. S3 원본을 복제하지 않고 Application마다 다른 형태의 데이터를 제공하려면?</summary>

강의 기준 S3 Object Lambda를 사용할 수 있습니다.

</details>

<details>
<summary>Q2. Object Lambda로 PII를 제거하면 S3의 원본 Object에서도 PII가 삭제되는가?</summary>

아닙니다. 반환되는 데이터를 동적으로 변환하며 원본 Object는 그대로 유지됩니다.

</details>

<details>
<summary>Q3. Object Lambda의 대표적인 사용 사례는?</summary>

PII Redaction, Format Conversion, Image Resize, Watermark, Data Enrichment 등이 있습니다.

</details>

<details>
<summary>Q4. 신규 AWS 고객은 현재 S3 Object Lambda를 일반적으로 새로 사용할 수 있는가?</summary>

아닙니다. 2025년 11월 7일부터 기존 고객과 일부 APN Partner를 중심으로 제공됩니다.

</details>

<details>
<summary>Q5. S3 Object Lambda 제한이 AWS Lambda 자체의 신규 사용 제한을 의미하는가?</summary>

아닙니다. 제한되는 것은 S3 Object Lambda 기능이며 AWS Lambda 자체는 계속 사용할 수 있습니다.

</details>
