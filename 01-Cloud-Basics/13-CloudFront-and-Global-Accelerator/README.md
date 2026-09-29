# 13. CloudFront & AWS Global Accelerator

Amazon CloudFront를 이용한 글로벌 콘텐츠 전송과 캐싱, 그리고 AWS Global Accelerator를 이용한 글로벌 네트워크 가속을 정리한 섹션입니다.

---

## 📚 Contents

| No. | Topic | File |
|---|---|---|
| 13-01 | CloudFront Overview | [13-01-CloudFront-Overview.md](./13-01-CloudFront-Overview.md) |
| 13-02 | CloudFront Origins & OAC | [13-02-CloudFront-Origins-and-OAC.md](./13-02-CloudFront-Origins-and-OAC.md) |
| 13-03 | CloudFront Custom Origins | [13-03-CloudFront-Custom-Origins.md](./13-03-CloudFront-Custom-Origins.md) |
| 13-04 | CloudFront Geo Restriction | [13-04-CloudFront-Geo-Restriction.md](./13-04-CloudFront-Geo-Restriction.md) |
| 13-05 | CloudFront Price Classes | [13-05-CloudFront-Price-Classes.md](./13-05-CloudFront-Price-Classes.md) |
| 13-06 | CloudFront Cache Invalidation | [13-06-CloudFront-Cache-Invalidation.md](./13-06-CloudFront-Cache-Invalidation.md) |
| 13-07 | AWS Global Accelerator | [13-07-AWS-Global-Accelerator.md](./13-07-AWS-Global-Accelerator.md) |
| 13-08 | CloudFront vs Global Accelerator | [13-08-CloudFront-vs-Global-Accelerator.md](./13-08-CloudFront-vs-Global-Accelerator.md) |

---

## 🗺️ Section Map

```text
CloudFront & Global Accelerator
│
├─ Global Content Delivery
│  └─ Amazon CloudFront
│      ├─ Edge Location
│      ├─ Cache
│      ├─ Origin
│      │   ├─ S3 + OAC
│      │   └─ Custom Origin
│      ├─ Geo Restriction
│      ├─ Price Classes
│      └─ Cache Invalidation
│
└─ Global Network Acceleration
   └─ AWS Global Accelerator
       ├─ Static Anycast IP
       ├─ AWS Global Network
       ├─ Endpoint Group
       ├─ Health Check
       └─ Failover
```

---

## 🔑 Core Concepts

### CloudFront

CloudFront는 AWS의 CDN(Content Delivery Network) 서비스입니다.

전 세계 Edge Location에 콘텐츠를 캐싱하여 사용자에게 낮은 Latency로 콘텐츠를 제공합니다.

```text
User
 ↓
Nearest Edge
 ↓
CloudFront Cache
 ↓
Origin
```

---

### CloudFront Origins

Origin은 CloudFront가 원본 콘텐츠를 가져오는 Backend입니다.

대표적인 Origin:

```text
S3
EC2
ALB
S3 Website Endpoint
HTTP Backend
```

Private S3 Bucket은 OAC를 이용해 CloudFront를 통한 접근만 허용하도록 구성할 수 있습니다.

---

### Cache Management

```text
Cache Hit
→ Edge에서 바로 응답

Cache Miss
→ Origin에서 가져온 후 Cache

Cache Invalidation
→ 기존 Cache 강제 제거
```

---

### CloudFront Additional Features

```text
Geo Restriction
→ 국가별 접근 제어

Price Classes
→ Edge 범위와 비용 조절

Cache Invalidation
→ 변경된 콘텐츠 즉시 반영
```

---

### AWS Global Accelerator

Global Accelerator는 Static Anycast IP와 AWS Global Network를 이용해 사용자의 Traffic을 Application Endpoint로 빠르게 전달합니다.

```text
User
 ↓
Static Anycast IP
 ↓
Nearest AWS Edge
 ↓
AWS Global Network
 ↓
Application
```

CloudFront와 달리 콘텐츠를 Cache하지 않습니다.

---

### CloudFront vs Global Accelerator

```text
CloudFront
= CDN
= Cache
= Content Delivery

Global Accelerator
= Network Acceleration
= No Cache
= Traffic Routing
```

---

## 🇯🇵 日本語 Summary

Amazon CloudFrontは、世界中のEdge Locationにコンテンツをキャッシュして低レイテンシーで配信するCDNサービスです。

S3やHTTP BackendなどをOriginとして利用でき、Geo Restriction、Price Class、Cache Invalidationなどの機能を提供します。

AWS Global Acceleratorは、Static Anycast IPとAWS Global Networkを利用してTrafficをApplication Endpointへ高速にRoutingします。

CloudFrontはコンテンツ配信、Global Acceleratorはネットワーク高速化を主な目的とします。

---

## 🇺🇸 English Summary

Amazon CloudFront is AWS's Content Delivery Network service.

It caches content at global Edge Locations and delivers it to users with low latency.

CloudFront supports S3 and HTTP-based origins as well as Geo Restriction, Price Classes, and Cache Invalidation.

AWS Global Accelerator uses static Anycast IP addresses and the AWS Global Network to route traffic to application endpoints.

CloudFront focuses on content delivery and caching, while Global Accelerator focuses on network acceleration and traffic routing.
