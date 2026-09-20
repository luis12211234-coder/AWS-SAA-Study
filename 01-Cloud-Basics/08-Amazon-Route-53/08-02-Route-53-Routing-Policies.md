# Route 53 Routing Policies

## 1. Routing Policy란?

Route 53의 Routing Policy는 DNS Query에 대해 **어떤 Resource의 주소를 응답할지 결정하는 규칙**이다.

> Route 53이 실제 네트워크 트래픽을 전달하는 것은 아니다.  
> DNS Query에 어떤 값을 반환할지를 결정한다.

---

## 2. Simple Routing

가장 기본적인 Routing Policy.

- 하나의 Record에 하나 이상의 값을 지정할 수 있다.
- 여러 값이 있으면 Route 53은 여러 값을 반환할 수 있다.
- Health Check를 연결할 수 없다.
- 특별한 라우팅 조건이 필요하지 않을 때 사용한다.

```text
example.com
├── 1.2.3.4
├── 5.6.7.8
└── 9.10.11.12
```

### 핵심

> **Simple = 특별한 조건 없이 DNS 응답**

---

## 3. Weighted Routing

각 Resource에 **Weight(가중치)**를 설정하여 트래픽 비율을 조절한다.

실제 비율은 다음과 같이 결정된다.

```text
해당 Record Weight / 모든 Record Weight의 합
```

예:

```text
US        Weight 70
Europe    Weight 20
Singapore Weight 10
```

대략 70 : 20 : 10 비율로 DNS 응답이 선택된다.

- Weight의 합이 반드시 100일 필요는 없다.
- Health Check 연결 가능
- 신규 버전 테스트, 트래픽 분산 등에 활용

### 핵심

> **Weighted = 내가 비율을 정한다**

---

## 4. Latency-based Routing

사용자에게 **가장 낮은 네트워크 Latency를 제공하는 AWS Region의 Resource**를 반환한다.

```text
User
 ↓
Route 53
 ↓
Network Latency 비교
 ↓
가장 낮은 Latency의 Resource
```

- 물리적으로 가장 가까운 Region을 의미하는 것은 아니다.
- AWS Region과 Record를 연결한다.
- Health Check 연결 가능

### 핵심

> **Latency = 네트워크상 어디가 가장 빠른가?**

---

## 5. Failover Routing

**Active-Passive 구조**를 구현한다.

```text
Primary
   │
Health Check
   │
   ├── Healthy   → Primary 반환
   │
   └── Unhealthy → Secondary 반환
```

Route 53 Record를 만들 때 직접 다음 역할을 지정한다.

```text
Failover Record Type
├── Primary
└── Secondary
```

Primary/Secondary는 EC2 자체의 속성이 아니라 **Route 53 Record에 설정하는 역할**이다.

주요 사용 사례:

- Disaster Recovery
- Primary 장애 시 자동 전환
- Active-Passive Architecture

### 핵심

> **Failover = Primary 살아있냐?**

---

## 6. Geolocation Routing

**사용자의 실제 지리적 위치**를 기준으로 DNS 응답을 결정한다.

설정 가능한 위치 예:

- Continent
- Country
- US State

여러 규칙이 동시에 일치하면 **더 구체적인 위치가 우선**된다.

```text
Asia        → Singapore
United States → US
Default     → Europe
```

어떤 위치 규칙에도 해당하지 않는 사용자를 위해 **Default Record**를 설정할 수 있다.

사용 사례:

- Website Localization
- Content Distribution Restriction
- 지역별 서비스 제공
- Load Distribution

Health Check 연결 가능.

### Latency와 차이

```text
Latency
→ 네트워크상 어디가 빠른가?

Geolocation
→ 사용자가 지리적으로 어디에 있는가?
```

### 핵심

> **Geolocation = 사용자 위치**

---

## 7. Geoproximity Routing

사용자와 Resource의 **지리적 위치 및 거리**를 기반으로 Routing한다.

Resource는 Route 53이 사용자를 보낼 대상 Endpoint를 의미한다.

AWS Resource라면 Region을 지정하고, AWS 외부 Resource라면 위치를 판단할 수 있도록 Latitude/Longitude를 지정할 수 있다.

### Bias

Geoproximity의 핵심 기능.

```text
Bias = 0
→ 기본적인 지리적 근접성

Positive Bias
→ 해당 Resource의 영향 영역 확대

Negative Bias
→ 해당 Resource의 영향 영역 축소
```

Bias를 변경한다고 실제 서버 위치가 바뀌는 것은 아니다.

**해당 Resource로 Routing되는 지리적 영역의 크기를 조절하여 Resource 사이의 경계를 이동시키는 것**으로 이해하면 된다.

```text
West Resource      East Resource
      \               /
       \             /
        ----경계----

East Bias 증가
→ 경계가 West 방향으로 이동
→ East Resource가 더 넓은 지역의 사용자를 담당
```

### 핵심

> **Geoproximity = 지리적으로 가까운 Resource + Bias로 영향 영역 조절**

---

## 8. IP-based Routing

**Client IP Address가 속한 CIDR 범위**를 기준으로 Routing한다.

먼저 Route 53에 알고 있는 Client IP 범위를 정의한다.

```text
CIDR A → Endpoint A
CIDR B → Endpoint B
```

예:

```text
203.0.113.0/24
→ 1.2.3.4

200.5.4.0/24
→ 5.6.7.8
```

Client의 IP가 첫 번째 CIDR에 속하면 `1.2.3.4`, 두 번째 CIDR에 속하면 `5.6.7.8`을 DNS 응답으로 반환한다.

특정 ISP나 네트워크의 IP 범위를 이미 알고 있을 때 유용하다.

사용 사례:

- 특정 ISP별 Routing
- 성능 최적화
- 네트워크 비용 최적화

### Geolocation과 차이

```text
Geolocation
→ 사용자의 지리적 위치

IP-based
→ Client IP가 어느 CIDR에 포함되는가?
```

### 핵심

> **IP-based = Client IP → CIDR Matching → 지정 Endpoint**

---

## 9. Multi-Value Answer Routing

하나의 DNS Query에 대해 **여러 Resource의 값을 반환**한다.

Health Check와 연결하면 **Healthy Resource만 DNS 응답에 포함**할 수 있다.

```text
US     → Healthy
Asia   → Healthy
Europe → Unhealthy

DNS Response
→ US
→ Asia
```

한 Multi-Value Query에서 **최대 8개의 Healthy Record**를 반환할 수 있다.

### Simple과 차이

Simple Routing도 여러 값을 반환할 수 있지만 Health Check를 연결하지 않는다.

```text
Simple
→ 여러 값 반환 가능
→ 비정상 Resource가 포함될 가능성

Multi-Value
→ 여러 값 반환
→ Health Check 사용 가능
→ Healthy Resource만 반환 가능
```

### Multi-Value ≠ ELB

Multi-Value는 Load Balancer 자체가 아니다.

```text
Multi-Value

Client
  ↓ DNS Query
Route 53
  ↓
[IP A, IP B, IP C]
  ↓
Client가 반환된 값 중 하나 사용
```

반면 ELB는 실제 요청을 받아 Backend Resource로 전달한다.

> Multi-Value는 DNS 기반의 Client-side Load Balancing과 비슷하지만 **ELB를 대체하지 않는다.**

### 핵심

> **Multi-Value = 여러 Healthy Resource를 DNS 응답으로 반환**

---

# Routing Policy 비교

| Policy | 판단 기준 | Health Check | 핵심 사용 사례 |
|---|---|---|---|
| Simple | 특별한 조건 없음 | ❌ | 기본 DNS |
| Weighted | Weight / 비율 | ✅ | 트래픽 비율 조절 |
| Latency | Network Latency | ✅ | 낮은 지연시간 |
| Failover | Primary 상태 | ✅ | Active-Passive / DR |
| Geolocation | 사용자 지리적 위치 | ✅ | 지역별 서비스 |
| Geoproximity | 지리적 거리 + Bias | 가능 | 지리적 영향 영역 조절 |
| IP-based | Client IP / CIDR | 가능 | 특정 네트워크별 Routing |
| Multi-Value | 여러 Healthy Resource | ✅ | 여러 정상 Resource 반환 |

## 시험용 암기

```text
Simple       = 그냥
Weighted     = 비율
Latency      = 속도
Failover     = 생존 여부
Geolocation  = 사용자 위치
Geoproximity = 거리 + Bias
IP-based     = Client CIDR
Multi-Value  = 여러 Healthy Resource
```

---

# 日本語まとめ

## Route 53 Routing Policies

- **Simple**: 特別な条件なしでDNS応答を返す
- **Weighted**: Weightに基づいてトラフィックの割合を調整
- **Latency**: ネットワークレイテンシーが最も低いリージョンへルーティング
- **Failover**: Primaryが異常な場合、Secondaryへ切り替える
- **Geolocation**: ユーザーの地理的位置に基づいてルーティング
- **Geoproximity**: ユーザーとリソースの地理的距離を基準とし、Biasで対象地域を調整
- **IP-based**: クライアントIPが属するCIDRに基づいてルーティング
- **Multi-Value**: 複数の正常なリソースをDNS応答として返す

### 覚え方

```text
Weighted     → 割合
Latency      → 速度
Failover     → 障害対応
Geolocation  → ユーザーの場所
Geoproximity → 距離 + Bias
IP-based     → CIDR
Multi-Value  → 複数の正常なリソース
```

---

# English Summary

## Route 53 Routing Policies

- **Simple**: Basic DNS routing without special conditions.
- **Weighted**: Distributes DNS responses according to assigned weights.
- **Latency**: Routes users to the resource associated with the lowest network latency.
- **Failover**: Uses a Primary resource and switches to Secondary when the Primary becomes unhealthy.
- **Geolocation**: Routes based on the user's geographic location.
- **Geoproximity**: Routes based on geographic proximity and adjusts resource influence using Bias.
- **IP-based**: Routes based on the CIDR range of the client IP address.
- **Multi-Value**: Returns multiple healthy resources in a DNS response.

Multi-Value Answer Routing can return up to **8 healthy records per query** and is **not a replacement for an ELB**.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Routing Policy | ルーティングポリシー | 라우팅 정책 |
| Weighted Routing | 加重ルーティング | 가중치 기반 라우팅 |
| Latency | レイテンシー | 지연 시간 |
| Failover | フェイルオーバー | 장애 조치 |
| Primary | プライマリ | 주 리소스 |
| Secondary | セカンダリ | 보조 리소스 |
| Geolocation | 地理的位置 | 지리적 위치 |
| Geoproximity | 地理的近接性 | 지리적 근접성 |
| Bias | バイアス | 편향 / 영향 영역 조정값 |
| Client IP | クライアントIP | 클라이언트 IP |
| CIDR | CIDR | CIDR 주소 범위 |
| Multi-Value Answer | 複数値回答 | 다중 값 응답 |
| Health Check | ヘルスチェック | 상태 확인 |
| Resource | リソース | 리소스 |
| Endpoint | エンドポイント | 엔드포인트 |

---

# Review Questions

### 1. 사용자의 네트워크 지연시간을 기준으로 Resource를 선택하려면 어떤 정책을 사용하는가?

<details>
<summary>정답 보기</summary>

**정답: Latency-based Routing**

사용자의 지리적 위치 자체가 아니라 AWS가 판단하는 **Network Latency**를 기준으로 Resource를 선택한다.

</details>

### 2. Primary가 장애 상태일 때 Secondary로 자동 전환하려면?

<details>
<summary>정답 보기</summary>

**정답: Failover Routing**

Primary Record에 Health Check를 연결하여 장애를 감지하고 Secondary Record로 DNS 응답을 전환한다.

</details>

### 3. 특정 국가의 사용자에게 특정 Resource를 제공하려면?

<details>
<summary>정답 보기</summary>

**정답: Geolocation Routing**

사용자의 실제 지리적 위치를 기준으로 Routing한다.

</details>

### 4. Bias라는 키워드가 등장하면 어떤 Routing Policy를 떠올려야 하는가?

<details>
<summary>정답 보기</summary>

**정답: Geoproximity Routing**

Bias를 사용하여 특정 Resource가 담당하는 지리적 영향 영역을 확대하거나 축소할 수 있다.

</details>

### 5. 특정 ISP의 Client IP 범위를 이미 알고 있으며 해당 사용자들을 특정 Endpoint로 보내고 싶다면?

<details>
<summary>정답 보기</summary>

**정답: IP-based Routing**

Client IP가 어느 CIDR 범위에 포함되는지 확인하여 지정된 Endpoint의 DNS 값을 반환한다.

</details>

### 6. 여러 정상 Resource를 한 DNS Query에서 반환하고 싶다면?

<details>
<summary>정답 보기</summary>

**정답: Multi-Value Answer Routing**

Health Check와 결합하여 정상 Resource만 반환할 수 있으며, 한 Query에서 최대 8개의 Healthy Record를 반환할 수 있다.

</details>

### 7. Multi-Value Answer Routing은 ELB를 대체할 수 있는가?

<details>
<summary>정답 보기</summary>

**정답: 아니다.**

Multi-Value는 DNS 응답으로 여러 Resource 주소를 제공하는 방식이다. ELB처럼 실제 Client Request를 받아 Backend로 분배하는 Load Balancer가 아니다.

</details>