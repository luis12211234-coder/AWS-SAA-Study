# 📘 25-02. CIDR, Private IP Ranges & Longest Prefix Match

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `CIDR` · `IPv4` · `RFC 1918` · `Subnet Mask` · `Longest Prefix Match` · `Overlapping CIDR`

## 🇰🇷 1. CIDR 계산의 출발점
**CIDR(Classless Inter-Domain Routing)**은 `IP/접두사 길이`로 주소 범위를 표현한다. IPv4는 32비트이며 `/24`는 상위 24비트가 네트워크를 나타낸다는 뜻이다. 총 주소 수는 `2^(32-prefix)`다.

| CIDR | 총 IPv4 주소 수 |
|---|---:|
| `/32` | 1 |
| `/28` | 16 |
| `/27` | 32 |
| `/26` | 64 |
| `/24` | 256 |
| `/20` | 4,096 |
| `/16` | 65,536 |
| `/0` | 모든 IPv4 |

## 🇰🇷 2. RFC1918 사설 IP
일반적인 사설 IPv4 주소 범위는 **`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`**이다. 인터넷에서 직접 라우팅되지 않는 내부 네트워크 주소 범위다. AWS VPC는 조건에 따라 이 외 주소 범위도 사용할 수 있다.

격리된 두 VPC는 같은 CIDR를 재사용할 수 있지만 **VPC Peering으로 직접 연결하려면 주소가 중복되지 않아야 한다**. 나중의 VPN·DX·TGW 연결도 충돌하지 않게 설계하는 편이 좋다.

## 🇰🇷 3. Longest Prefix Match의 정확한 의미
패킷의 **목적지 IP**가 라우트 테이블에서 여러 경로에 일치하면 **접두사가 더 긴(구체적인) 경로**를 선택한다.

```text
목적지 10.1.2.35
10.0.0.0/8   → target-A
10.1.0.0/16  → target-B
10.1.2.0/24  → target-C    ← 선택
```

중요: 라우터가 목적지 호스트의 실제 서브넷 마스크를 알아내는 기술이 아니다. **현재 라우팅 테이블에 등록된 여러 경로 중 가장 구체적인 것을 선택**하는 규칙이다.

## 🇰🇷 4. AWS 설계에서의 주소 계산
VPC IPv4 CIDR로 통상 `/16`부터 `/28` 범위를 선택할 수 있다. 서브넷은 AZ 하나에 속하고 서로 겹치면 안 된다. 각 IPv4 서브넷에서 **AWS 예약 IP 5개**가 빠진다. 예: `/27`은 총 32개 중 27개 사용 가능. IP가 최소 29개 필요하다면 `/27`은 부족하므로 `/26`을 택한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- `/0`은 모든 IPv4, `/32`는 IPv4 주소 하나.
- Longest Prefix Match는 목적지 IP와 **라우트 테이블 항목** 비교.
- 일반 사설 범위 10/8, 172.16/12, 192.168/16.
- 연결할 VPC CIDR 중복은 라우팅 충돌의 핵심 원인.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. `/24` CIDR에 IPv4 주소는 모두 몇 개?

<details>
<summary>정답 및 해설 보기</summary>

**256개.** 실제 AWS 서브넷에서 5개가 예약된다.

</details>

### Q2. `/27` 서브넷의 사용 가능 IPv4는?

<details>
<summary>정답 및 해설 보기</summary>

**27개.** 32개 전체 중 5개 예약.

</details>

### Q3. 10.1.2.30의 `/8`과 `/24` 경로가 둘 다 있으면?

<details>
<summary>정답 및 해설 보기</summary>

**더 긴 `/24` 경로.** Longest Prefix Match 때문이다.

</details>

### Q4. 라우터가 목적지 서버의 실제 마스크를 자동 추론하는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 자신의 라우트 테이블에 등록된 경로를 비교한다.

</details>

### Q5. VPC A와 B가 CIDR가 겹치면 직접 VPC Peering 가능한가?

<details>
<summary>정답 및 해설 보기</summary>

**불가능.** 연결 전에 중복 IP 설계를 해결해야 한다.

</details>

## 🇯🇵 日本語 Summary

CIDR は IP アドレス範囲をプレフィックス長で表す。IPv4 アドレス数は 2^(32-prefix)。AWS Subnet では IPv4 の予約アドレスが 5 個ある。Longest Prefix Match は宛先 IP に一致するルートから最も具体的なプレフィックスを選ぶ。

## 🇺🇸 English Summary

CIDR expresses an IP address range with a prefix length. An AWS IPv4 subnet reserves five addresses. Longest prefix match selects the most specific route matching the destination IP. Avoid overlapping CIDRs when interconnecting VPCs.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| CIDR | IP 범위 표기법 | CIDR 表記 |
| Private IPv4 | 사설 IPv4 | プライベート IPv4 |
| Longest Prefix Match | 최장 접두사 일치 | 最長プレフィックス一致 |
| Overlapping CIDR | 중복 CIDR | 重複 CIDR |
