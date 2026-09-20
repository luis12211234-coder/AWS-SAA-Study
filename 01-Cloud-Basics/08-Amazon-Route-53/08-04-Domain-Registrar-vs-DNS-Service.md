# Domain Registrar vs DNS Service

## 1. Domain Registrar와 DNS Service

Domain Registrar와 DNS Service는 서로 다른 역할을 한다.

```text
Domain Registrar
= Domain Name을 등록하고 구매하는 곳

DNS Service
= DNS Record를 관리하고 DNS Query에 응답하는 서비스
```

즉,

> **도메인을 구매한 곳과 DNS를 관리하는 곳은 같을 필요가 없다.**

---

## 2. Route 53의 역할

Amazon Route 53은 다음 기능을 모두 제공할 수 있다.

```text
Route 53
├── Domain Registration
└── DNS Service
```

따라서 AWS에서 Domain을 등록하고 Route 53에서 DNS까지 관리할 수도 있다.

하지만 반드시 Route 53에서 Domain을 구매해야 Route 53 DNS를 사용할 수 있는 것은 아니다.

---

## 3. Third-Party Registrar + Route 53

예를 들어 Domain을 외부 Registrar에서 구매하고 DNS는 Route 53으로 관리할 수 있다.

```text
Third-Party Registrar
      │
      │ example.com 구매
      ↓
Domain Registration

Route 53
      │
      ↓
Public Hosted Zone
      │
      ├── A
      ├── AAAA
      ├── CNAME
      └── ...
```

핵심은 **Name Server(NS)를 Route 53으로 위임하는 것**이다.

---

## 4. 설정 과정

### Step 1. Route 53에서 Public Hosted Zone 생성

```text
Route 53
→ Hosted Zones
→ Create Hosted Zone
→ example.com
```

Public Hosted Zone을 생성하면 Route 53이 해당 Hosted Zone의 **Name Servers**를 제공한다.

예:

```text
ns-xxx.awsdns-xx.com
ns-xxx.awsdns-xx.net
ns-xxx.awsdns-xx.org
ns-xxx.awsdns-xx.co.uk
```

### Step 2. Registrar의 Name Server 변경

Domain을 구매한 Registrar의 설정에서 기존 Name Server를 Route 53이 제공한 Name Server로 변경한다.

```text
Third-Party Registrar

example.com
     │
     │ NS 설정
     ↓
Route 53 Name Servers
```

이후 해당 Domain의 DNS Query는 Route 53의 Name Server로 위임된다.

### Step 3. Route 53에서 DNS Records 관리

```text
Client
   ↓
example.com DNS Query
   ↓
Route 53 Name Server
   ↓
Public Hosted Zone
   ↓
A / AAAA / CNAME / Alias ...
   ↓
DNS Response
```

이제 실제 DNS Records는 Route 53에서 관리한다.

---

## 5. 핵심 구조

```text
Domain 구매
   ↓
Third-Party Registrar
   ↓
NS를 Route 53 Name Server로 변경
   ↓
Route 53 Public Hosted Zone
   ↓
DNS Records 관리
```

### 시험 포인트

```text
Domain Registrar ≠ DNS Service

외부 Registrar에서 Domain 구매
+
Route 53을 DNS Service로 사용
        ↓
Route 53 Public Hosted Zone 생성
        ↓
Registrar의 NS를
Route 53 Name Servers로 변경
```

> **Registrar는 도메인 등록, Route 53은 DNS 관리라는 식으로 분리할 수 있다.**

---

# 日本語まとめ

## Domain Registrar と DNS Service

- **Domain Registrar**: ドメイン名を登録・購入するサービス
- **DNS Service**: DNSレコードを管理し、DNSクエリに応答するサービス
- ドメインの登録先とDNSサービスは同じである必要はない
- 外部Registrarで購入したドメインでもRoute 53をDNSサービスとして使用できる

### Third-Party Registrar + Route 53

```text
外部RegistrarでDomainを購入
        ↓
Route 53でPublic Hosted Zoneを作成
        ↓
Route 53のName Serverを確認
        ↓
Registrar側のNS設定を変更
        ↓
Route 53でDNS Recordを管理
```

---

# English Summary

## Domain Registrar vs DNS Service

A **Domain Registrar** is used to register and purchase a domain name, while a **DNS Service** manages DNS records and answers DNS queries.

The registrar and DNS provider do not need to be the same company.

A domain registered with a third-party registrar can use Route 53 as its DNS service:

```text
Register Domain
      ↓
Third-Party Registrar
      ↓
Create Route 53 Public Hosted Zone
      ↓
Update Registrar Name Servers
      ↓
Route 53 DNS
```

The key step is updating the registrar's **Name Server settings** to point to the Route 53 Name Servers.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Domain Name | ドメイン名 | 도메인 이름 |
| Domain Registrar | ドメインレジストラ | 도메인 등록 대행자 |
| DNS Service | DNSサービス | DNS 서비스 |
| Hosted Zone | ホストゾーン | 호스팅 영역 |
| Public Hosted Zone | パブリックホストゾーン | 퍼블릭 호스팅 영역 |
| Name Server | ネームサーバー | 네임 서버 |
| NS Record | NSレコード | NS 레코드 |
| DNS Record | DNSレコード | DNS 레코드 |
| Domain Registration | ドメイン登録 | 도메인 등록 |
| DNS Query | DNSクエリ | DNS 질의 |
| Delegate | 委任する | 위임하다 |

---

# Review Questions

### 1. Domain Registrar와 DNS Service는 반드시 같은 업체를 사용해야 하는가?

<details>
<summary>정답 보기</summary>

**정답: 아니다.**

Domain Registrar는 Domain을 등록하는 역할이고 DNS Service는 DNS Record를 관리하는 역할이므로 서로 다른 서비스를 사용할 수 있다.

</details>

### 2. 외부 Registrar에서 구매한 Domain을 Route 53에서 관리할 수 있는가?

<details>
<summary>정답 보기</summary>

**정답: 가능하다.**

Route 53에서 해당 Domain의 Public Hosted Zone을 생성한 뒤 Registrar의 Name Server 설정을 Route 53 Name Servers로 변경한다.

</details>

### 3. Third-Party Registrar에서 Route 53 DNS를 사용하기 위한 핵심 설정은?

<details>
<summary>정답 보기</summary>

**정답: Registrar의 Name Server(NS)를 Route 53 Name Servers로 변경한다.**

이를 통해 해당 Domain의 DNS Resolution이 Route 53의 Hosted Zone으로 위임된다.

</details>

### 4. Route 53에서 Public Hosted Zone을 생성하면 무엇을 확인해야 하는가?

<details>
<summary>정답 보기</summary>

**정답: Route 53이 할당한 Name Servers**

이 Name Server 정보를 Domain Registrar의 NS 설정에 등록해야 한다.

</details>

### 5. Route 53은 Domain Registrar와 DNS Service 중 어느 역할을 제공하는가?

<details>
<summary>정답 보기</summary>

**정답: 둘 다 제공할 수 있다.**

Route 53에서 Domain을 등록할 수도 있고 DNS Hosted Zone과 Records를 관리할 수도 있다.

</details>