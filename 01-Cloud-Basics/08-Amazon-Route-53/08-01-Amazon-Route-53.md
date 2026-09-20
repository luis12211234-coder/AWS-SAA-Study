# Amazon Route 53

## 1. DNS Basics

DNS(Domain Name System)는 사람이 읽기 쉬운 **도메인 이름을 IP 주소로 변환**하는 시스템이다.

```text
example.com
    ↓ DNS
9.10.11.12
```

사용자가 `example.com`에 접속하면 DNS를 통해 서버의 IP 주소를 알아낸 후 실제 서버에 연결한다.

### DNS Resolution

DNS는 계층적인 구조로 동작한다.

```text
Client
  ↓
Local DNS Resolver
  ↓
Root DNS Server
  ↓
TLD DNS Server (.com)
  ↓
Authoritative DNS Server (example.com)
  ↓
IP Address
  ↓
Web Server
```

- **Root DNS Server**: `.com`, `.org` 등 다음 DNS 서버를 안내
- **TLD DNS Server**: `example.com`을 담당하는 Name Server를 안내
- **Authoritative DNS Server**: 실제 DNS Record를 가지고 최종 응답
- **DNS Resolver**: DNS 조회를 수행하고 결과를 캐싱

---

## 2. Amazon Route 53

Amazon Route 53은 AWS의 **고가용성, 확장 가능한 Fully Managed Authoritative DNS 서비스**이다.

주요 기능:

- DNS 관리
- Domain Registration
- DNS Routing
- Health Checks

Route 53이라는 이름의 `53`은 DNS가 사용하는 기본 포트 번호에서 유래한다.

### Domain Registrar vs DNS Service

두 개념은 서로 다르다.

```text
Domain Registrar
→ 도메인을 등록하는 서비스

DNS Service
→ 도메인의 DNS Records를 관리하는 서비스
```

Route 53은 두 기능을 모두 제공한다.

다른 Registrar에서 구입한 Domain도 Route 53을 DNS 서비스로 사용할 수 있다.

```text
외부 Registrar에서 Domain 구매
        ↓
Route 53 Hosted Zone 생성
        ↓
Registrar의 NS Records를
Route 53 Name Servers로 변경
```

---

## 3. Hosted Zone

Hosted Zone은 특정 Domain과 Subdomain의 **DNS Records를 관리하는 영역(Container)**이다.

예:

```text
Hosted Zone: hangbin.com

hangbin.com           ← Zone Apex
www.hangbin.com       ← Subdomain
api.hangbin.com       ← Subdomain
blog.hangbin.com      ← Subdomain
```

### Zone Apex

Zone Apex는 **Hosted Zone의 최상위 DNS 이름 자체**를 의미한다.

```text
Hosted Zone = DNS 관리 영역 전체

Zone Apex = 그 영역의 최상위 이름
```

예:

```text
Hosted Zone: hangbin.com

hangbin.com
↑
Zone Apex
```

### Public Hosted Zone

인터넷에서 조회 가능한 Public DNS Records를 관리한다.

```text
www.example.com
        ↓
Public IP / Public AWS Resource
```

### Private Hosted Zone

연결된 VPC 내부에서 사용하는 Private DNS Records를 관리한다.

```text
VPC

web.example.internal → 10.0.0.10
api.example.internal → 10.0.0.20
db.example.internal  → 10.0.0.30
```

핵심:

```text
Public Hosted Zone
→ Internet

Private Hosted Zone
→ VPC 내부
```

---

## 4. Route 53 DNS Records

Route 53의 DNS Record에는 다음과 같은 정보가 포함된다.

- Name
- Record Type
- Value
- Routing Policy
- TTL

SAA에서 반드시 알아야 할 주요 Record Type:

| Type | 역할 |
|---|---|
| A | Hostname → IPv4 |
| AAAA | Hostname → IPv6 |
| CNAME | Hostname → 다른 Hostname |
| NS | Hosted Zone의 Name Server |

---

## 5. A / AAAA Records

### A Record

Hostname을 IPv4 주소에 매핑한다.

```text
app.example.com
       ↓ A
11.22.33.44
```

### AAAA Record

Hostname을 IPv6 주소에 매핑한다.

```text
app.example.com
       ↓ AAAA
IPv6 Address
```

핵심:

```text
A    → IPv4
AAAA → IPv6
```

---

## 6. CNAME Record

CNAME은 Hostname을 **다른 Hostname에 매핑**한다.

```text
app.hangbin.com
       ↓ CNAME
myapp.service.com
       ↓ A / AAAA
11.22.33.44
```

CNAME 자체가 최종 IP를 지정하는 것이 아니다.

대상 Hostname의 DNS Record를 다시 조회하여 최종 IP를 찾는다.

### CNAME을 사용하는 이유

외부 서비스가 실제 서버 IP를 관리하는 경우 유용하다.

```text
우리 DNS

app.hangbin.com
       ↓ CNAME
myapp.service.com


외부 서비스 DNS

myapp.service.com
       ↓
실제 IP
```

외부 서비스가 서버 IP를 변경해도:

```text
Before
myapp.service.com → 11.22.33.44

After
myapp.service.com → 55.66.77.88
```

우리의 CNAME은 수정할 필요가 없다.

```text
app.hangbin.com
       ↓
myapp.service.com
```

즉:

> IP 주소 자체를 관리하는 대신 대상 Hostname을 따라간다.

### CNAME과 Zone Apex

CNAME은 **Zone Apex에는 생성할 수 없다.**

```text
Hosted Zone: hangbin.com

hangbin.com
→ CNAME ❌

www.hangbin.com
→ CNAME ⭕

api.hangbin.com
→ CNAME ⭕
```

---

## 7. Route 53 Alias Record

Alias Record는 Route 53에서 제공하는 기능으로, Hostname을 **지원되는 AWS Resource에 매핑**할 수 있다.

예:

```text
hangbin.com
     ↓
A Alias
     ↓
Application Load Balancer
```

Alias의 가장 중요한 특징은 **Zone Apex에서도 사용할 수 있다는 것**이다.

```text
CNAME
hangbin.com → ALB
❌

Alias
hangbin.com → ALB
⭕
```

### Alias 특징

- Route 53 기능
- 지원되는 AWS Resource를 Target으로 지정
- Zone Apex 사용 가능
- Subdomain 사용 가능
- DNS Query 비용 없음
- Native Health Check 지원
- TTL을 직접 설정하지 않음
- AWS Resource의 IP 변경을 자동으로 인식

Alias Record의 Type은 AWS Resource에 대해 **A 또는 AAAA**를 사용한다.

### Alias Targets

대표적인 지원 대상:

- Elastic Load Balancer
- CloudFront Distribution
- API Gateway
- Elastic Beanstalk
- S3 Website
- VPC Interface Endpoint
- Global Accelerator
- 동일 Hosted Zone의 Route 53 Record

주의:

```text
S3 Website
→ Alias Target ⭕

EC2 DNS Name
→ Alias Target ❌
```

---

## 8. CNAME vs Alias

| 특징 | CNAME | Alias |
|---|---|---|
| 대상 | 다른 Hostname | 지원되는 AWS Resource |
| Subdomain | O | O |
| Zone Apex | X | O |
| Route 53 전용 | X | O |
| TTL 직접 설정 | O | X |
| AWS Resource 연동 | 일반 DNS 방식 | Native |
| Query 비용 | 일반 DNS Query | Alias Query 무료 |

### Exam Pattern

```text
app.hangbin.com → ALB
→ CNAME 가능
→ Alias 가능

hangbin.com → ALB
→ CNAME 불가능
→ Alias 가능
```

**Zone Apex + AWS Resource → Alias**를 우선적으로 떠올린다.

---

## 9. TTL (Time To Live)

TTL은 DNS Resolver가 **DNS 응답을 캐시에 유지하는 시간**이다.

예:

```text
A Record

myapp.example.com
→ 11.22.33.44

TTL = 300 seconds
```

첫 번째 DNS Query:

```text
Client
   ↓
DNS Resolver
   ↓
Route 53
   ↓
11.22.33.44
TTL = 300
```

Resolver는 결과를 캐싱한다.

따라서 TTL이 만료되기 전에는 같은 Domain을 다시 조회해도 Route 53에 다시 Query할 필요가 없다.

```text
첫 요청
→ Route 53 Query
→ 결과 Cache

다음 요청
→ Cached DNS Record 사용
```

### High TTL

예:

```text
TTL = 24 hours
```

장점:

- DNS Query 감소
- Route 53 트래픽 및 비용 감소

단점:

- DNS Record 변경 반영이 느림
- Client가 오래된 값을 계속 사용할 수 있음

### Low TTL

예:

```text
TTL = 60 seconds
```

장점:

- DNS Record 변경이 빠르게 반영
- 오래된 Record가 짧게 유지됨

단점:

- DNS Query 증가
- Route 53 트래픽 및 비용 증가

핵심:

```text
High TTL
→ Cache 오래 유지
→ DNS Query ↓
→ 변경 반영 느림

Low TTL
→ Cache 짧게 유지
→ DNS Query ↑
→ 변경 반영 빠름
```

Alias Record는 TTL을 사용자가 직접 설정하지 않는다.

---

## 10. TTL Hands-on

다음 A Record를 생성했다.

```text
demo.stephanetheteacher.com
        ↓ A
Frankfurt EC2 IP

TTL = 120 seconds
```

`dig`를 실행하면 남은 TTL을 확인할 수 있다.

```text
120
↓
115
↓
98
↓
...
↓
0
```

이 상태에서 Route 53의 A Record를 Singapore EC2 IP로 변경해도 기존 TTL이 남아 있다면:

```text
Route 53

demo.example.com
→ Singapore EC2

하지만 Client Cache
→ Frankfurt EC2
```

기존 IP가 계속 사용된다.

TTL이 만료된 후 다시 DNS Query가 발생하면:

```text
TTL Expired
     ↓
Route 53 재조회
     ↓
Singapore EC2 IP
     ↓
새 값 Cache
```

새로운 Record가 반영된다.

---

## 11. Route 53 Hands-on Architecture

Route 53의 Routing 기능을 테스트하기 위해 서로 다른 세 Region에 EC2를 생성했다.

```text
Frankfurt
eu-central-1
→ EC2 #1

N. Virginia
us-east-1
→ EC2 #2

Singapore
ap-southeast-1
→ EC2 #3
```

각 EC2는 User Data를 통해 간단한 Web Server를 실행하며 Instance/AZ 정보를 출력한다.

Frankfurt에는 별도로 ALB를 생성했다.

```text
Internet
   ↓
Frankfurt ALB
   ↓
Target Group
   ↓
Frankfurt EC2 #1
```

ALB Target Group에는 **Frankfurt EC2 한 대만 등록**하였다.

세 Region의 EC2는 이후 Route 53 Routing Policies를 실습하기 위한 테스트 환경으로 사용된다.

### DNS Record Hands-on

A Record 생성:

```text
test.stephanetheteacher.com
        ↓ A
11.22.33.44
```

`nslookup` 또는 `dig`를 사용하여 DNS Resolution을 확인할 수 있다.

```text
dig test.stephanetheteacher.com

ANSWER:
test.stephanetheteacher.com
→ A
→ 11.22.33.44
```

DNS가 정상적으로 IP를 반환하더라도 해당 IP에서 Web Server가 실행되고 있지 않다면 웹페이지 접속은 실패할 수 있다.

즉:

```text
DNS Resolution 성공
≠
Application 접속 성공
```

---

# 日本語まとめ

## Amazon Route 53

Amazon Route 53 は、高可用性・スケーラビリティを持つ AWS のマネージド DNS サービスです。

### Hosted Zone

Hosted Zone は、Domain と Subdomain の DNS Records を管理する領域です。

```text
Hosted Zone: example.com

example.com       ← Zone Apex
www.example.com   ← Subdomain
api.example.com   ← Subdomain
```

- Public Hosted Zone: Internet 向け
- Private Hosted Zone: VPC 内部向け

### DNS Records

```text
A     → Hostname → IPv4
AAAA  → Hostname → IPv6
CNAME → Hostname → Another Hostname
NS    → Name Server
```

CNAME は Zone Apex では使用できません。

### Alias

Alias は Route 53 の機能で、AWS Resource を Target にできます。

```text
CNAME
Zone Apex → ❌

Alias
Zone Apex → ⭕
```

ALB、CloudFront、API Gateway、S3 Website などを Target にできます。

### TTL

TTL は DNS Record を Resolver が Cache する時間です。

```text
High TTL
→ DNS Query ↓
→ 更新反映が遅い

Low TTL
→ DNS Query ↑
→ 更新反映が速い
```

---

# English Summary

## Amazon Route 53

Amazon Route 53 is a highly available and scalable managed authoritative DNS service.

### Hosted Zone

A Hosted Zone is a container for DNS Records associated with a domain and its subdomains.

```text
Hosted Zone: example.com

example.com       ← Zone Apex
www.example.com   ← Subdomain
api.example.com   ← Subdomain
```

### Record Types

```text
A     → Hostname to IPv4
AAAA  → Hostname to IPv6
CNAME → Hostname to another Hostname
NS    → Name Servers
```

A CNAME cannot be created at the Zone Apex.

### Alias Record

Route 53 Alias Records can route traffic to supported AWS resources and can be used at the Zone Apex.

Common targets include ELB, CloudFront, API Gateway, S3 Website, and Global Accelerator.

### TTL

TTL determines how long DNS Resolvers cache a DNS response.

```text
High TTL
→ Fewer DNS queries
→ Slower DNS changes

Low TTL
→ More DNS queries
→ Faster DNS changes
```

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Domain Name System (DNS) | ドメインネームシステム | 도메인 네임 시스템 |
| Domain Registrar | ドメインレジストラ | 도메인 등록 기관 |
| DNS Record | DNS レコード | DNS 레코드 |
| Hosted Zone | ホストゾーン | 호스팅 존 |
| Zone Apex | ゾーンエイペックス | DNS 영역의 최상위 이름 |
| Name Server | ネームサーバー | 네임 서버 |
| Authoritative DNS | 権威 DNS | 권한 있는 DNS |
| Resolver | リゾルバー | DNS 리졸버 |
| Subdomain | サブドメイン | 서브도메인 |
| A Record | A レコード | IPv4 매핑 레코드 |
| AAAA Record | AAAA レコード | IPv6 매핑 레코드 |
| CNAME Record | CNAME レコード | 다른 호스트 이름 매핑 레코드 |
| Alias Record | エイリアスレコード | 별칭 레코드 |
| Time To Live (TTL) | TTL / 生存時間 | 캐시 유지 시간 |
| Routing Policy | ルーティングポリシー | 라우팅 정책 |
| Public Hosted Zone | パブリックホストゾーン | 퍼블릭 호스팅 존 |
| Private Hosted Zone | プライベートホストゾーン | 프라이빗 호스팅 존 |
| DNS Cache | DNS キャッシュ | DNS 캐시 |

---

# Review Questions

### Q1. Hosted Zone과 Zone Apex의 차이는?

<details>
<summary>Answer</summary>

Hosted Zone은 Domain과 Subdomain의 DNS Records를 관리하는 **영역 전체**이다.

Zone Apex는 그 Hosted Zone의 **최상위 DNS 이름 자체**이다.

```text
Hosted Zone: example.com

example.com
↑
Zone Apex
```

</details>

### Q2. A Record와 CNAME Record의 차이는?

<details>
<summary>Answer</summary>

```text
A
Hostname → IPv4

CNAME
Hostname → Another Hostname
```

CNAME 대상 Hostname은 다시 DNS Resolution을 통해 최종 IP를 찾는다.

</details>

### Q3. `example.com` 자체를 ALB에 연결해야 한다. CNAME과 Alias 중 무엇을 사용하는가?

<details>
<summary>Answer</summary>

**Alias Record**

`example.com`은 Zone Apex이므로 CNAME을 사용할 수 없다.

Route 53 Alias는 Zone Apex에서 사용할 수 있으며 ALB를 Target으로 지정할 수 있다.

</details>

### Q4. High TTL과 Low TTL의 차이는?

<details>
<summary>Answer</summary>

```text
High TTL
→ Cache 오래 유지
→ DNS Query 감소
→ Record 변경 반영 느림

Low TTL
→ Cache 짧게 유지
→ DNS Query 증가
→ Record 변경 반영 빠름
```

</details>

### Q5. Public Hosted Zone과 Private Hosted Zone의 차이는?

<details>
<summary>Answer</summary>

Public Hosted Zone은 Internet에서 조회 가능한 DNS Records를 관리한다.

Private Hosted Zone은 연결된 VPC 내부에서 사용하는 Private DNS Records를 관리한다.

</details>

### Q6. Route 53 Alias Record의 주요 특징은?

<details>
<summary>Answer</summary>

- 지원되는 AWS Resource를 Target으로 지정
- Zone Apex에서 사용 가능
- Query 비용 없음
- Native Health Check 지원
- TTL을 직접 설정하지 않음
- AWS Resource의 IP 변경을 자동으로 반영

</details>

### Q7. Route 53에서 A Record의 IP를 변경했는데 사용자가 잠시 이전 서버에 계속 접속한다. 가장 먼저 확인할 것은?

<details>
<summary>Answer</summary>

**TTL과 DNS Cache**

기존 DNS 응답의 TTL이 아직 만료되지 않았다면 Resolver가 이전 IP를 Cache하고 있을 수 있다.

TTL이 만료된 후 Route 53을 다시 조회하면 새로운 Record 값이 반영된다.

</details>