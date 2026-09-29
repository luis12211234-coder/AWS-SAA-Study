# 13-03. CloudFront Custom Origins

## 🇰🇷 1. Custom Origin이란?

CloudFront의 Origin은 S3에만 국한되지 않습니다.

HTTP를 통해 콘텐츠를 제공하는 Backend도 Origin으로 사용할 수 있습니다.

```text
CloudFront
    │
    ├─ EC2
    ├─ ALB
    ├─ HTTP Server
    └─ S3 Website Endpoint
```

이러한 HTTP 기반 Backend를 Custom Origin으로 사용할 수 있습니다.

---

## 2. S3 Website Endpoint

여기서 중요한 구분이 있습니다.

```text
일반 S3 Bucket Origin
→ S3 Origin
→ OAC 사용 가능
```

반면 Static Website Hosting을 활성화한 S3 Website Endpoint는:

```text
S3 Website Endpoint
→ HTTP Website
→ Custom Origin
```

으로 취급됩니다.

즉 이름은 S3지만 CloudFront 입장에서는 일반 HTTP Website와 같은 방식으로 접근합니다.

---

## 3. EC2를 직접 Origin으로 사용

EC2를 직접 CloudFront Origin으로 사용할 수도 있습니다.

```text
User
 ↓
CloudFront
 ↓
Public EC2
```

CloudFront가 EC2에 접근할 수 있도록 Network와 Security Group을 구성해야 합니다.

---

## 4. ALB를 Origin으로 사용

ALB를 CloudFront Origin으로 두고 실제 EC2를 뒤에 배치할 수도 있습니다.

```text
User
 ↓
CloudFront
 ↓
Public ALB
 ↓
Private EC2
```

CloudFront는 ALB에 접근하고 ALB가 Backend EC2로 Traffic을 분산합니다.

따라서 Backend EC2는 Private으로 둘 수 있습니다.

---

## 5. Security Group

요청 흐름을 그대로 따라가면 됩니다.

```text
CloudFront
    ↓
ALB Security Group
    ↓
ALB
    ↓
EC2 Security Group
    ↓
EC2
```

따라서:

```text
ALB SG
→ CloudFront에서 오는 Traffic 허용

EC2 SG
→ ALB에서 오는 Traffic 허용
```

EC2 입장에서 직접 요청을 보내는 대상은 CloudFront가 아니라 ALB입니다.

---

## 6. 서비스 역할 구분

```text
CloudFront
= Global CDN / Cache

ALB
= Application Traffic 분산

EC2
= 실제 Application Server
```

---

## 🔑 핵심 정리

```text
Origin
≠ S3 전용 개념

Custom Origin
= HTTP Backend

대표 예
= EC2 / ALB / S3 Website Endpoint
```

대표 구조:

```text
CloudFront
 ↓
Public ALB
 ↓
Private EC2
```

한 줄 정리:

> **CloudFront 뒤에는 S3뿐 아니라 ALB, EC2 등의 HTTP Backend도 연결할 수 있다.**

---

## 🇯🇵 日本語 Summary

CloudFrontでは、S3だけでなくEC2、ALB、HTTP ServerなどをCustom Originとして利用できます。

S3 Website EndpointもHTTPベースのCustom Originとして扱われます。

ALBをOriginとして利用する場合、ALBをPublicにし、Backend EC2をPrivateにする構成が可能です。

---

## 🇺🇸 English Summary

CloudFront can use HTTP-based custom origins such as EC2 instances, Application Load Balancers, and HTTP servers.

An S3 website endpoint is also treated as a custom HTTP origin.

A common architecture uses a public ALB as the CloudFront origin while keeping backend EC2 instances private.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Custom Origin | カスタムオリジン | 사용자 지정 원본 |
| Website Endpoint | ウェブサイトエンドポイント | 웹사이트 엔드포인트 |
| Application Load Balancer | Application Load Balancer | 애플리케이션 로드 밸런서 |
| Backend | バックエンド | 백엔드 |
| Security Group | セキュリティグループ | 보안 그룹 |

---

## 📝 Review Questions

<details>
<summary>Q1. CloudFront Origin은 S3만 가능한가?</summary>

아닙니다. EC2, ALB 및 다른 HTTP Backend도 사용할 수 있습니다.

</details>

<details>
<summary>Q2. S3 Website Endpoint는 CloudFront에서 어떻게 취급되는가?</summary>

HTTP 기반 Custom Origin으로 취급됩니다.

</details>

<details>
<summary>Q3. CloudFront → ALB → EC2 구조에서 EC2가 직접 받는 요청은 누구에게서 오는가?</summary>

ALB에서 옵니다.

</details>
