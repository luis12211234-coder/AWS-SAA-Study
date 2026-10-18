# 📘 25-23. Egress-only Internet Gateway & IPv6 Routing

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Egress-only Internet Gateway` · `IPv6` · `IGW` · `NAT Gateway` · `Private Subnet` · `Route Table`

## 🇰🇷 1. Egress-only IGW의 핵심 동작
**Egress-only Internet Gateway**는 IPv6 주소가 있는 Private EC2가 **인터넷으로 새 연결을 시작하는 것과 그 응답**을 허용하되 **인터넷에서 EC2로 새 연결을 먼저 시작하지 못하게** 하는 관리형 게이트웨이다. IPv6 주소를 숨기거나 변환(NAT)하는 기능은 아니다. IPv6 주소가 알려져 있어도 신규 인바운드 연결은 막힌다.

## 🇰🇷 2. IGW, NAT와 역할 비교
| 연결 방식 | 용도 |
|---|---|
| IGW | 공인 IPv4 또는 글로벌 IPv6로 인터넷 양방향 통신(보안 허용 필요) |
| Public NAT GW | 주로 Private EC2의 IPv4 인터넷 송신 |
| Egress-only IGW | Private EC2의 IPv6 인터넷 송신, 신규 인바운드 차단 |

```text
IPv6 요청: Private EC2 → Egress-only IGW → 인터넷
IPv6 응답: 인터넷 → Egress-only IGW → Private EC2  (허용)
새 인바운드: 인터넷 → Egress-only IGW → Private EC2  (차단)
```

## 🇰🇷 3. Public과 Private Subnet Route Table
```text
PublicRouteTable:
0.0.0.0/0 → IGW
::/0      → IGW

PrivateRouteTable:
0.0.0.0/0 → NAT Gateway  (IPv4)
::/0      → Egress-only IGW (IPv6)
```

기존 IPv4/IPv6 `local` 경로도 함께 존재한다. Egress-only IGW는 **VPC에 연결**하고, Private Route Table에서 `::/0`의 대상으로 지정해야 사용한다.

## 🇰🇷 4. 콘솔 실습
`VPC → Egress-only Internet Gateways → Create`에서 `DemoEIGW`를 만들어 `DemoVPC`에 연결했다. 이후 `PrivateRouteTable → Edit routes`에서 `::/0 → Egress-only IGW`를 추가했다. NAT GW는 IPv4 기본 경로, Egress-only IGW는 IPv6 기본 경로로 **대상 IP 버전에 따라 분기**한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- IPv6 송신과 응답 허용, 외부 신규 연결 차단.
- Egress-only IGW는 IPv6 NAT가 아니다.
- Private Subnet IPv4 기본 경로는 NAT, IPv6 기본 경로는 Egress-only IGW.
- VPC에 만들고 Route Table에 `::/0` 추가해야 동작.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. IPv6 Private EC2의 인터넷 송신만 허용하는 것은?

<details>
<summary>정답 및 해설 보기</summary>

**Egress-only Internet Gateway.**

</details>

### Q2. 외부에서 새 SSH 연결을 먼저 시작할 수 있는가?

<details>
<summary>정답 및 해설 보기</summary>

**Egress-only IGW를 통해서는 불가.**

</details>

### Q3. Egress-only IGW는 IPv6 주소를 변환하는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** NAT 방식이 아니다.

</details>

### Q4. IPv6 전체 인터넷 기본 경로의 Destination은?

<details>
<summary>정답 및 해설 보기</summary>

**`::/0`.**

</details>

### Q5. Private EC2의 IPv4 인터넷 송신 경로는 일반적으로 어디를 가리키는가?

<details>
<summary>정답 및 해설 보기</summary>

**NAT Gateway.**

</details>

## 🇯🇵 日本語 Summary

Egress-only Internet Gateway は IPv6 の送信開始と戻り通信を許可し、インターネットからの新規接続を遮断する。NAT と異なりアドレス変換はしない。Private Subnet の IPv6 デフォルトルート `::/0` を Egress-only IGW に設定する。

## 🇺🇸 English Summary

An egress-only internet gateway enables outbound-initiated IPv6 internet traffic and replies while blocking unsolicited inbound connections. It performs no NAT. In a private subnet, route IPv6 ::/0 to it and IPv4 0.0.0.0/0 to a suitable NAT solution.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Egress-only Internet Gateway | 송신 전용 인터넷 게이트웨이 | Egress-only Internet Gateway |
| Outbound Connection | 송신 연결 | アウトバウンド接続 |
| Unsolicited Inbound | 외부 신규 유입 연결 | 外部からの新規受信接続 |
| IPv6 Route | IPv6 라우트 | IPv6 ルート |
