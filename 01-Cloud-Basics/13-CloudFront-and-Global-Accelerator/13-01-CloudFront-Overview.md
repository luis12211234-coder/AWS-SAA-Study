# 13-01. CloudFront Overview

## 🇰🇷 1. CloudFront란?

Amazon CloudFront는 AWS의 CDN(Content Delivery Network) 서비스입니다.

웹사이트의 콘텐츠를 전 세계 Edge Location에 캐싱하여 사용자에게 가까운 위치에서 제공합니다.

```text
Origin
   ↓
CloudFront Edge
   ↓
User
```

핵심 목적:

```text
Global Content Delivery
+
Low Latency
+
Caching
```

시험에서 `CDN`이 나오면 CloudFront를 우선 떠올립니다.

---

## 2. Edge Location

CloudFront는 Origin에서 모든 사용자에게 직접 콘텐츠를 보내는 대신 전 세계에 분산된 Edge Location을 사용합니다.

```text
Origin
   ↓
AWS Network
   ↓
Edge Location
   ↓
Nearby User
```

한 번 가져온 콘텐츠는 Edge에 Cache될 수 있기 때문에 같은 지역의 다른 사용자가 동일한 콘텐츠를 요청할 때 Origin까지 다시 갈 필요가 없습니다.

---

## 3. Cache Hit / Cache Miss

### Cache Hit

요청한 콘텐츠가 Edge Cache에 이미 존재합니다.

```text
User
 ↓
Edge
 ↓
Cached Content
```

Origin까지 가지 않고 바로 응답합니다.

### Cache Miss

Edge에 콘텐츠가 없다면 Origin에서 가져옵니다.

```text
User
 ↓
Edge
 ↓
Cache Miss
 ↓
Origin
 ↓
Content
 ↓
Edge Cache
 ↓
User
```

가져온 콘텐츠는 Cache되어 이후 요청에 사용할 수 있습니다.

---

## 4. Origin

Origin은 CloudFront가 원본 데이터를 가져오는 Backend입니다.

```text
CloudFront
    │
    ├─ S3
    ├─ EC2
    ├─ ALB
    ├─ S3 Website Endpoint
    └─ Other HTTP Backend
```

즉 Origin은 S3에만 국한된 개념이 아닙니다.

> **Origin = CloudFront가 원본 데이터를 가져오는 곳**

---

## 5. CloudFront는 읽기 전용인가?

아닙니다.

설정에 따라 CloudFront는 Origin으로 GET뿐 아니라 PUT, POST, DELETE 등의 HTTP Request도 전달할 수 있습니다.

하지만 SAA에서 먼저 잡아야 할 핵심 이미지는 다음입니다.

```text
CloudFront
= CDN
= Edge Cache
= Global Content Delivery
```

---

## 6. CloudFront vs S3 CRR

CloudFront와 S3 Cross-Region Replication은 목적이 다릅니다.

| CloudFront | S3 CRR |
|---|---|
| Edge에 Cache | 다른 Region의 S3에 복제 |
| CDN | Replication |
| 전 세계 사용자 대상 | 선택한 Region 간 복제 |
| Cache된 Copy | 실제 Object Copy |

```text
CloudFront
→ 사용자에게 콘텐츠를 빠르게 전달

S3 CRR
→ 다른 Region에 실제 Object를 복제
```

---

## 🔑 핵심 정리

```text
CloudFront
= AWS CDN

Edge Location
= 사용자 가까이에서 Content 제공

Cache Hit
= Edge에서 바로 제공

Cache Miss
= Origin에서 가져와 Cache

Origin
= 원본 Backend

CloudFront ≠ S3 CRR
```

한 줄 정리:

> **전 세계 Edge Location에 콘텐츠를 캐싱하여 낮은 Latency로 제공한다 = CloudFront**

---

## 🇯🇵 日本語 Summary

Amazon CloudFrontはAWSのCDNサービスです。

コンテンツを世界中のEdge Locationにキャッシュし、ユーザーに近い場所から配信することでLatencyを削減します。

Cache Hitの場合はEdgeから直接配信し、Cache Missの場合はOriginからコンテンツを取得します。

OriginにはS3、EC2、ALB、その他のHTTP Backendなどを利用できます。

---

## 🇺🇸 English Summary

Amazon CloudFront is AWS's Content Delivery Network service.

It caches content at global Edge Locations to reduce latency.

A cache hit serves content directly from the Edge, while a cache miss retrieves content from the origin.

Origins can include S3, EC2, ALB, and other HTTP backends.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| CDN | コンテンツ配信ネットワーク | 콘텐츠 전송 네트워크 |
| Edge Location | エッジロケーション | 엣지 로케이션 |
| Origin | オリジン | 원본 |
| Cache | キャッシュ | 캐시 |
| Cache Hit | キャッシュヒット | 캐시 적중 |
| Cache Miss | キャッシュミス | 캐시 미스 |
| Latency | レイテンシー | 지연 시간 |

---

## 📝 Review Questions

<details>
<summary>Q1. AWS의 CDN 서비스는?</summary>

Amazon CloudFront입니다.

</details>

<details>
<summary>Q2. Cache Hit이 발생하면?</summary>

CloudFront Edge에 이미 콘텐츠가 있으므로 Origin까지 가지 않고 Edge에서 바로 제공합니다.

</details>

<details>
<summary>Q3. Origin은 S3만 의미하는가?</summary>

아닙니다. S3, EC2, ALB 및 다른 HTTP Backend 등이 Origin이 될 수 있습니다.

</details>

<details>
<summary>Q4. CloudFront와 S3 CRR의 핵심 차이는?</summary>

CloudFront는 Edge Cache를 이용한 콘텐츠 전송 서비스이고, CRR은 실제 S3 Object를 다른 Region으로 복제하는 기능입니다.

</details>
