# Amazon Route 53 - Routing Policies

## Overview

Route 53의 **Routing Policy**는 DNS Query가 들어왔을 때  
**어떤 DNS Record를 응답할지 결정하는 방식**이다.

여기서 `Routing`은 Load Balancer의 Routing과 다르다.

```text
Client
   │
   │ DNS Query
   │ "app.example.com의 주소는?"
   ▼
Route 53
   │
   │ DNS Response
   │ "11.22.33.44"
   ▼
Client
   │
   │ HTTP Request
   ▼
Application
```

Route 53은 실제 HTTP Traffic을 전달하지 않는다.

> **Route 53 Routing Policy = 실제 Traffic 경로가 아니라 DNS 응답을 결정하는 정책**

Route 53은 다음 Routing Policies를 지원한다.

- Simple
- Weighted
- Failover
- Latency-based
- Geolocation
- Multi-Value Answer
- Geoproximity

현재까지 학습한 정책은 **Simple, Weighted, Latency-based**이다.

---

# 1. Simple Routing Policy

가장 기본적인 Routing Policy이다.

일반적으로 하나의 리소스로 연결할 때 사용하며,  
**하나의 Record 안에 여러 Value를 지정하는 것도 가능하다.**

## Single Value

```text
simple.example.com

A Record
└── 11.22.33.44
```

Client가 DNS Query를 보내면 Route 53은 해당 값을 반환한다.

```text
Client
   │
   │ simple.example.com?
   ▼
Route 53
   │
   └── 11.22.33.44
```

---

## Multiple Values

하나의 Simple Record에 여러 IP를 지정할 수도 있다.

```text
simple.example.com

A Record
├── 11.22.33.44
├── 55.66.77.88
└── 99.11.22.33
```

이 경우 Route 53은 여러 값을 DNS Response로 반환할 수 있으며,  
Client가 반환된 값 중 하나를 선택한다.

### Hands-on

실습에서는 처음에 Singapore EC2의 Public IP 하나를 등록했다.

```text
Name: simple.stephanetheteacher.com
Type: A
Routing Policy: Simple
TTL: 20

Value:
Singapore EC2 Public IP
```

이후 같은 Record를 수정하여 Virginia EC2의 IP를 추가했다.

```text
simple.stephanetheteacher.com

A Record
├── Singapore EC2 IP
└── Virginia EC2 IP
```

`dig` 명령으로 확인했을 때 두 IP가 DNS Response에 나타나는 것을 확인했다.

TTL을 짧게 설정한 이유는 기존 DNS Cache가 빠르게 만료되어  
Record 변경 결과를 실습에서 빠르게 확인하기 위해서이다.

---

## Simple + Alias

Simple Routing에서 Alias를 사용하는 경우  
**하나의 지원되는 AWS Resource를 Alias Target으로 지정한다.**

예:

```text
example.com
Type: A
Alias: Yes
Routing Policy: Simple

        │
        ▼
Application Load Balancer
```

여기서 "하나의 AWS Resource"는  
ALB 내부에 IP가 하나만 존재한다는 의미가 아니다.

```text
example.com
    │
 A Alias
    │
    ▼
   ALB
  /   \
IP A  IP B
```

Alias는 ALB뿐만 아니라 Route 53에서 지원하는 여러 AWS Resource를 대상으로 사용할 수 있다.

예:

- Elastic Load Balancer
- CloudFront
- API Gateway
- Elastic Beanstalk
- S3 Website
- VPC Interface Endpoint
- Global Accelerator
- 같은 Hosted Zone의 Route 53 Record

> **Alias = 지원되는 AWS Resource를 DNS Target으로 지정하는 Route 53 기능**

Simple Routing은 **Health Check와 연결할 수 없다.**

---

# 2. Weighted Routing Policy

Weighted Routing Policy는  
**같은 DNS Name과 Record Type을 가진 여러 Record 중 어떤 Record를 응답할지 Weight를 이용해 결정한다.**

Simple과 달리 Record 자체를 여러 개 만든다.

```text
weighted.example.com

A Record ①
├── Value: Singapore IP
└── Weight: 10

A Record ②
├── Value: Frankfurt IP
└── Weight: 20

A Record ③
├── Value: Virginia IP
└── Weight: 70
```

모든 Record는 동일한 Name과 Type을 사용한다.

```text
Name: weighted.example.com
Type: A
```

---

## Relative Weight

Weight는 퍼센트 자체가 아니라 **상대적인 값**이다.

선택 비율은 다음과 같다.

```text
Record Weight
────────────────────
Total Record Weights
```

예:

```text
Singapore = 10
Frankfurt = 20
Virginia  = 70

Total = 100
```

따라서 대략:

```text
Singapore → 10%
Frankfurt → 20%
Virginia  → 70%
```

하지만 Weight의 합이 반드시 100일 필요는 없다.

```text
Singapore = 1
Frankfurt = 2
Virginia  = 7

Total = 10
```

이 경우에도 비율은 동일하다.

```text
Singapore → 1 / 10 = 10%
Frankfurt → 2 / 10 = 20%
Virginia  → 7 / 10 = 70%
```

---

## DNS Response Flow

Weighted Routing도 실제 HTTP Traffic을 Route 53이 전달하는 것은 아니다.

```text
Client
   │
   │ weighted.example.com?
   ▼
Route 53
   │
   │ Weight를 기준으로 Record 선택
   ▼
Virginia Record
   │
   │ Virginia EC2 IP 반환
   ▼
Client
   │
   │ HTTP Request
   ▼
Virginia EC2
```

즉,

> **Weight는 실제 Packet을 분배하는 값이 아니라 Route 53이 DNS Record를 선택하는 비율이다.**

---

## Record ID

Weighted Record들은 동일한 Name과 Type을 사용하기 때문에  
각 Record를 식별하기 위한 **Record ID**를 설정할 수 있다.

실습에서는 다음과 같이 설정했다.

```text
Record ID: SOUTHEAST
Record ID: EU
Record ID: US EAST
```

Record ID는 해당 Weighted Record를 구별하기 위한 식별자이다.

---

## Hands-on

실습에서는 같은 DNS Name에 A Record 세 개를 생성했다.

```text
weighted.stephanetheteacher.com

├── SOUTHEAST
│   ├── Singapore EC2 IP
│   └── Weight: 10
│
├── EU
│   ├── Frankfurt EC2 IP
│   └── Weight: 20
│
└── US EAST
    ├── Virginia EC2 IP
    └── Weight: 70
```

TTL은 결과를 빠르게 확인하기 위해 `3 seconds`로 설정했다.

`dig`를 반복 실행했을 때 대부분 Weight가 가장 높은  
US EAST의 IP가 반환되었지만, 때때로 EU 등의 다른 IP도 반환되는 것을 확인했다.

---

## Weight = 0

특정 Record의 Weight를 `0`으로 설정하면  
해당 리소스로 DNS 응답을 보내는 것을 중단할 수 있다.

```text
Resource A → Weight 70
Resource B → Weight 30
Resource C → Weight 0
```

단, **모든 Record의 Weight가 0이면 모든 Record가 동일하게 반환된다.**

---

## Use Cases

Weighted Routing의 대표적인 사용 사례:

### Multi-Region Traffic Distribution

```text
example.com
├── us-east-1      Weight 70
└── eu-central-1   Weight 30
```

### New Application Testing

```text
example.com
├── Current Version   Weight 90
└── New Version       Weight 10
```

새 버전에 일부 DNS 요청만 보내 테스트하는 방식으로 활용할 수 있다.

Weighted Routing은 **Health Check와 연결할 수 있다.**

---

# 3. Latency-based Routing Policy

Latency-based Routing Policy는  
사용자에게 **가장 낮은 네트워크 Latency를 제공하는 AWS Region의 Record를 응답**한다.

```text
latency.example.com

A Record ①
├── Singapore EC2 IP
└── Region: ap-southeast-1

A Record ②
├── Virginia EC2 IP
└── Region: us-east-1

A Record ③
├── Frankfurt EC2 IP
└── Region: eu-central-1
```

Route 53은 사용자와 AWS Region 사이의 Latency를 기준으로  
적절한 Record를 선택한다.

---

## Lowest Latency, Not Nearest Location

Latency-based Routing은 단순히  
**지리적으로 가장 가까운 Region을 선택하는 정책이 아니다.**

예를 들어 독일 사용자의 경우에도:

```text
Germany User
     │
     ▼
Route 53
     │
     ├── eu-central-1 → Higher Latency
     │
     └── us-east-1    → Lower Latency
                         ▲
                         │
                      Selected
```

네트워크 상황에 따라 미국 Region이 더 낮은 Latency를 제공한다면  
미국 Region의 Record가 반환될 수 있다.

> **Latency-based = Geographic Distance가 아니라 Network Latency 기준**

---

## Why Specify the Region?

실습에서는 A Record의 Value로 EC2 Public IP를 직접 입력했다.

```text
Value: 13.x.x.x
```

IP 주소만으로는 Route 53이 해당 IP가 어느 AWS Region의 리소스인지 알 수 없다.

따라서 각 Record에 Region을 지정했다.

```text
Singapore EC2 IP
→ Region: ap-southeast-1

Virginia EC2 IP
→ Region: us-east-1

Frankfurt EC2 IP
→ Region: eu-central-1
```

Route 53은 이 Region 정보를 이용해  
사용자에게 가장 낮은 Latency를 제공하는 Record를 선택한다.

---

## Hands-on Concept

강사가 유럽에서 접속했을 때:

```text
User in Europe
      │
      ▼
Route 53
      │
      ▼
eu-central-1 Record
      │
      ▼
Frankfurt EC2
```

VPN을 이용해 캐나다에서 접속했을 때:

```text
User in Canada
      │
      ▼
Route 53
      │
      ▼
us-east-1 Record
      │
      ▼
Virginia EC2
```

홍콩에서 접속했을 때:

```text
User in Hong Kong
      │
      ▼
Route 53
      │
      ▼
ap-southeast-1 Record
      │
      ▼
Singapore EC2
```

이를 통해 사용자의 위치에 따라 네트워크 Latency가 달라지고  
Route 53이 서로 다른 Region의 Record를 반환하는 것을 확인했다.

Latency-based Routing은 **Health Check와 연결할 수 있다.**

---

# 4. Simple vs Weighted vs Latency-based

| Routing Policy | Record 구조 | 선택 기준 | Health Check |
|---|---|---|---|
| Simple | 하나의 Record에 여러 Value 가능 | 기본 DNS 응답 | ❌ |
| Weighted | 같은 Name/Type의 Record 여러 개 | Relative Weight | ⭕ |
| Latency-based | 여러 Region의 Record | Lowest Network Latency | ⭕ |

### Mental Model

```text
Simple
→ 그냥 답한다

Weighted
→ Weight를 보고 답을 고른다

Latency-based
→ 어느 Region이 더 빠른지 보고 답을 고른다
```

---

# 5. Exam Notes

### Simple

```text
Basic DNS Routing
→ Simple

Multiple Values in one Record
→ Simple 가능

Health Check
→ Simple 불가
```

### Weighted

```text
Traffic percentage / ratio
→ Weighted

90% old version + 10% new version
→ Weighted

Traffic distribution between Regions
→ Weighted
```

### Latency-based

```text
Lowest latency for users
→ Latency-based

Latency-sensitive application
→ Latency-based
```

주의:

```text
Latency-based
≠ geographically nearest Region

Latency-based
= lowest network latency Region
```

---

# 日本語まとめ

## Route 53 Routing Policies

Route 53 の Routing Policy は、  
DNS Query に対して **どの DNS Record を返すか**を決定する。

Route 53 自体が HTTP Traffic を転送するわけではない。

### Simple Routing

最も基本的な Routing Policy。

- 1つの Record に複数の Value を設定可能
- 複数の値が返された場合、Client がその中から選択
- Alias を使用する場合は1つの対応 AWS Resource を Target に指定
- Health Check との関連付けは不可

### Weighted Routing

同じ Name と Type の複数 Record に Weight を設定する。

```text
Record A → Weight 70
Record B → Weight 20
Record C → Weight 10
```

Weight は割合そのものではなく相対値であり、  
合計が100である必要はない。

主な用途:

- Region 間のトラフィック分散
- 新しい Application Version のテスト
- Health Check と関連付け可能

### Latency-based Routing

ユーザーに対して最も低い Network Latency を提供する  
AWS Region の Record を返す。

地理的に最も近い Region とは限らない。

- Latency-sensitive Application に有効
- Region 情報を基準に Record を選択
- Health Check と関連付け可能

---

# English Summary

## Route 53 Routing Policies

Route 53 Routing Policies determine **which DNS record is returned for a DNS query**.

Route 53 does not route the actual HTTP traffic.

### Simple Routing

- Basic DNS routing
- Multiple values can exist in one record
- The client selects a value when multiple values are returned
- With Alias enabled, one supported AWS resource is specified as the target
- Cannot be associated with Health Checks

### Weighted Routing

Multiple records with the same name and type can have different relative weights.

```text
Record A → Weight 70
Record B → Weight 20
Record C → Weight 10
```

Weights do not need to add up to 100.

Common use cases include:

- Traffic distribution across Regions
- Testing new application versions
- Health Check integration

### Latency-based Routing

Returns the record associated with the AWS Region that provides the lowest network latency for the user.

The selected Region is not necessarily the geographically closest Region.

Useful for latency-sensitive applications and can be associated with Health Checks.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Routing Policy | ルーティングポリシー | 라우팅 정책 |
| DNS Query | DNSクエリ | DNS 쿼리 |
| DNS Response | DNSレスポンス | DNS 응답 |
| Simple Routing | シンプルルーティング | 단순 라우팅 |
| Weighted Routing | 加重ルーティング | 가중치 기반 라우팅 |
| Latency-based Routing | レイテンシーベースルーティング | 지연 시간 기반 라우팅 |
| Weight | 重み | 가중치 |
| Relative Weight | 相対的な重み | 상대적 가중치 |
| Record ID | レコードID | 레코드 ID |
| Latency | レイテンシー | 지연 시간 |
| Region | リージョン | 리전 |
| Alias Target | エイリアスターゲット | 별칭 대상 |
| Health Check | ヘルスチェック | 상태 확인 |
| Endpoint | エンドポイント | 엔드포인트 |
| Traffic Distribution | トラフィック分散 | 트래픽 분산 |

---

# Review Questions

1. Route 53의 Routing Policy가 결정하는 것은 실제 HTTP Traffic의 경로인가, DNS Response인가?
2. Simple Routing에서 하나의 A Record에 여러 IP Value를 지정할 수 있는가?
3. Simple Routing은 Health Check와 연결할 수 있는가?
4. Weighted Routing에서 Weight의 합은 반드시 100이어야 하는가?
5. Weighted Routing에서 여러 Record가 가져야 하는 공통 조건은 무엇인가?
6. Latency-based Routing은 지리적으로 가장 가까운 Region을 선택하는가?
7. 새로운 Application Version에 일부 사용자만 보내 테스트하려면 어떤 Routing Policy가 적합한가?