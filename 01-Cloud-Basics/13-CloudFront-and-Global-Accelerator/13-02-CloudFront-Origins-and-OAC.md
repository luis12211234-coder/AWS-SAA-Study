# 13-02. CloudFront Origins & OAC

## 🇰🇷 1. S3를 CloudFront Origin으로 사용

S3 Bucket을 CloudFront의 Origin으로 사용할 수 있습니다.

```text
User
 ↓
CloudFront
 ↓
S3 Bucket
```

이때 S3 Bucket 자체를 Public으로 공개할 필요는 없습니다.

---

## 2. Private S3 + CloudFront

권장 구조:

```text
Internet
   ↓
CloudFront
   ↓
  OAC
   ↓
Private S3
```

사용자는 S3에 직접 접근하지 않고 CloudFront를 통해 콘텐츠를 받습니다.

---

## 3. OAC

OAC는 Origin Access Control의 약자입니다.

CloudFront Distribution이 Private S3 Origin에 접근할 수 있도록 구성하는 방식입니다.

```text
User
 ↓
CloudFront Distribution
 ↓
OAC
 ↓
Private S3
```

기존에는 OAI(Origin Access Identity)가 사용되었지만 현재는 OAC가 권장됩니다.

```text
OAI
→ Legacy

OAC
→ Recommended
```

---

## 4. Bucket Policy

OAC를 생성하는 것만으로 끝나는 것은 아닙니다.

S3 Bucket Policy에서도 해당 CloudFront Distribution의 접근을 허용해야 합니다.

```text
CloudFront Distribution
        ↓
     GetObject
        ↓
   Bucket Policy
        ↓
     S3 Object
```

실습에서는 AWS가 생성해 준 Policy를 S3 Bucket Policy에 추가했습니다.

즉:

```text
CloudFront
+
OAC
+
S3 Bucket Policy
```

가 함께 동작합니다.

---

## 5. 전체 실습 흐름

```text
1. Private S3 Bucket 생성
        ↓
2. Object Upload
        ↓
3. CloudFront Distribution 생성
        ↓
4. S3를 Origin으로 지정
        ↓
5. OAC 생성
        ↓
6. Bucket Policy 수정
        ↓
7. CloudFront Domain으로 접근
```

S3 Object URL을 통한 직접 접근은 차단하면서 CloudFront를 통한 접근은 허용할 수 있습니다.

---

## 6. Default Root Object

CloudFront Domain의 루트 주소로 접속했을 때 기본적으로 제공할 Object를 지정할 수 있습니다.

```text
Default Root Object
= index.html
```

그러면:

```text
https://distribution-domain/
```

으로 접근해도 `index.html`을 제공할 수 있습니다.

---

## 🔑 핵심 정리

```text
Private S3
+
CloudFront
+
OAC
+
Bucket Policy
```

```text
OAC
= CloudFront → Private S3 접근 제어

OAI
= 이전 방식

OAC
= 권장 방식
```

한 줄 정리:

> **Private S3를 Public으로 만들지 않고 CloudFront를 통해 제공한다 = OAC + Bucket Policy**

---

## 🇯🇵 日本語 Summary

Private S3 BucketをCloudFront Originとして利用する場合、OACを使用できます。

OACを利用することでS3 Bucket自体をPublicにせず、CloudFront Distributionからアクセスできます。

S3 Bucket Policyでは、指定されたCloudFront Distributionに必要なアクセスを許可します。

OACは従来のOAIを置き換える推奨方式です。

---

## 🇺🇸 English Summary

A private S3 bucket can be used as a CloudFront origin.

Origin Access Control allows CloudFront to securely access the private S3 bucket.

The S3 bucket policy must allow the appropriate CloudFront distribution to access the objects.

OAC is the recommended replacement for the older OAI approach.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Origin Access Control | オリジンアクセスコントロール | 원본 접근 제어 |
| Origin Access Identity | オリジンアクセスアイデンティティ | 원본 접근 ID |
| Private Bucket | プライベートバケット | 비공개 버킷 |
| Bucket Policy | バケットポリシー | 버킷 정책 |
| Distribution | ディストリビューション | 배포 |
| Default Root Object | デフォルトルートオブジェクト | 기본 루트 객체 |

---

## 📝 Review Questions

<details>
<summary>Q1. Private S3 Bucket을 CloudFront Origin으로 안전하게 사용하기 위한 권장 기능은?</summary>

Origin Access Control(OAC)입니다.

</details>

<details>
<summary>Q2. OAC를 사용하면 S3 Bucket을 Public으로 만들어야 하는가?</summary>

아닙니다. S3 Bucket을 Private으로 유지할 수 있습니다.

</details>

<details>
<summary>Q3. OAC와 함께 S3에서 확인해야 할 중요한 설정은?</summary>

CloudFront Distribution의 접근을 허용하는 S3 Bucket Policy입니다.

</details>
