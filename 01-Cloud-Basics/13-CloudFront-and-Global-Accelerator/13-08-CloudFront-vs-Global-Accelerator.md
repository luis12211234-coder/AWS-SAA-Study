# 13-08. CloudFront vs Global Accelerator

## 🇰🇷 1. 공통점

CloudFront와 Global Accelerator는 모두 AWS의 Global Infrastructure와 Edge Location을 활용하여 전 세계 사용자에 대한 성능을 개선합니다.

```text
Global Users
     ↓
AWS Edge Infrastructure
     ↓
AWS Services
```

둘 다 AWS Shield와 통합되어 DDoS Protection을 제공합니다.

하지만 두 서비스의 핵심 목적은 완전히 다릅니다.

---

## 2. CloudFront

CloudFront의 핵심은 CDN과 Cache입니다.

```text
Origin
 ↓
CloudFront Edge
 ↓
Cache
 ↓
User
```

사용자는 많은 경우 Origin이 아니라 Edge에 저장된 콘텐츠를 받습니다.

대표 사용 사례:

```text
Images
Videos
Static Website Content
Web Content
API / Dynamic Content Delivery
```

핵심:

```text
CloudFront
= Content Delivery
```

---

## 3. Global Accelerator

Global Accelerator는 콘텐츠를 Edge에 저장하지 않습니다.

Edge는 Traffic이 AWS Global Network로 들어가는 진입점 역할을 합니다.

```text
User
 ↓
Static Anycast IP
 ↓
AWS Edge
 ↓
AWS Global Network
 ↓
Application
```

Traffic은 실제 Application Endpoint 방향으로 전달됩니다.

대표 사용 사례:

```text
Gaming
IoT
VoIP
TCP / UDP
Static Global IP
Fast Regional Failover
```

핵심:

```text
Global Accelerator
= Network Acceleration
```

---

## 4. 핵심 비교

| Feature | CloudFront | Global Accelerator |
|---|---|---|
| 핵심 목적 | Content Delivery | Network Acceleration |
| Edge Cache | O | X |
| 주요 Protocol | HTTP / HTTPS | TCP / UDP |
| Edge에서 Content 제공 | O | X |
| Static Anycast IP | 핵심 기능 아님 | O |
| Endpoint Health Check / Failover | 핵심 목적 아님 | O |
| 대표 사용 사례 | Website, Image, Video | Gaming, VoIP, IoT |

---

## 5. 그림으로 구분

### CloudFront

```text
User
 ↓
Edge
 ↓
"Cache에 있나?"
 ↓ YES
Content 바로 제공
```

콘텐츠 자체를 사용자 가까이에 가져옵니다.

### Global Accelerator

```text
User
 ↓
Edge
 ↓
AWS Global Network
 ↓
Application
```

Edge가 콘텐츠를 Cache하는 것이 아니라 Traffic을 실제 Application으로 전달합니다.

---

## 6. Exam Scenario

다음 키워드가 나오면 CloudFront를 생각합니다.

```text
CDN
Cache
Static Content
Image
Video
Content Delivery
```

다음 키워드가 나오면 Global Accelerator를 생각합니다.

```text
Static Anycast IP
TCP / UDP
Gaming
IoT
VoIP
Multi-Region Failover
Network Acceleration
```

---

## 🔑 핵심 정리

```text
CloudFront
= 콘텐츠를 사용자 가까이에 가져온다
= Cache

Global Accelerator
= 사용자의 Traffic을 AWS Network에 빠르게 태운다
= No Cache
```

가장 중요한 차이:

```text
CloudFront
Origin → Edge → User

Global Accelerator
User → Edge → Application
```

한 줄 정리:

> **콘텐츠를 Edge에 캐싱한다 = CloudFront / Traffic을 Edge를 통해 Application으로 가속한다 = Global Accelerator**

---

## 🇯🇵 日本語 Summary

CloudFrontとGlobal Acceleratorは、どちらもAWSのGlobal Infrastructureを利用しますが、目的が異なります。

CloudFrontはCDNであり、コンテンツをEdge Locationにキャッシュしてユーザーへ配信します。

Global Acceleratorはコンテンツをキャッシュせず、Static Anycast IPとAWS Global Networkを利用してTrafficをApplication EndpointへRoutingします。

```text
CloudFront
= Content Delivery + Cache

Global Accelerator
= Network Acceleration + No Cache
```

---

## 🇺🇸 English Summary

CloudFront and Global Accelerator both use AWS global infrastructure, but they solve different problems.

CloudFront is a CDN that caches content at Edge Locations and serves that content to users.

Global Accelerator does not cache content. It uses static Anycast IP addresses and the AWS Global Network to route traffic to application endpoints.

```text
CloudFront
= Content Delivery + Cache

Global Accelerator
= Network Acceleration + No Cache
```

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Content Delivery | コンテンツ配信 | 콘텐츠 전송 |
| Network Acceleration | ネットワーク高速化 | 네트워크 가속 |
| Cache | キャッシュ | 캐시 |
| Anycast IP | エニーキャストIP | 애니캐스트 IP |
| Global Network | グローバルネットワーク | 글로벌 네트워크 |
| Failover | フェイルオーバー | 장애 조치 |
| DDoS Protection | DDoS保護 | DDoS 보호 |

---

## 📝 Review Questions

<details>
<summary>Q1. 이미지와 비디오를 전 세계 사용자에게 Cache하여 제공하려면?</summary>

Amazon CloudFront를 사용합니다.

</details>

<details>
<summary>Q2. TCP/UDP Application을 AWS Global Network를 통해 가속하려면?</summary>

AWS Global Accelerator를 사용합니다.

</details>

<details>
<summary>Q3. Static Anycast IP가 중요한 요구 사항이라면?</summary>

AWS Global Accelerator를 생각합니다.

</details>

<details>
<summary>Q4. CloudFront와 Global Accelerator의 가장 중요한 차이는?</summary>

CloudFront는 Edge에 콘텐츠를 Cache하여 제공하고, Global Accelerator는 Cache 없이 Traffic을 실제 Application Endpoint로 전달합니다.

</details>

<details>
<summary>Q5. Multi-Region Endpoint의 Health Check와 빠른 Failover가 중요한 경우에는?</summary>

AWS Global Accelerator를 사용할 수 있습니다.

</details>
