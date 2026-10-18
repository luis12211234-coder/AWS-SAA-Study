# 📘 25-22. IPv6, Dual Stack & IPv6 Console Lab

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `IPv6` · `Dual Stack` · `IPv6 CIDR` · `Auto-Assign IPv6` · `IPv6-only` · `Internet Gateway`

## 🇰🇷 1. IPv6와 Dual Stack
IPv4 주소는 32비트(약 43억 개), **IPv6는 128비트**(약 `3.4×10^38`개)다. IPv6는 16진수·콜론 형식이며 `2001:db8::1`처럼 표현한다. **Dual Stack**은 같은 VPC/서브넷/ENI에서 IPv4와 IPv6를 함께 사용하는 방식이다. AWS가 EC2에 부여하는 일반적인 글로벌 IPv6는 전역 라우팅 가능하지만, **인터넷 접근은 Route Table과 SG/NACL 허용이 추가로 필요**하다.

## 🇰🇷 2. VPC → Subnet → ENI 주소 할당
강의에서는 `DemoVPC → Edit CIDRs`에서 Amazon-provided IPv6 CIDR(일반적으로 VPC `/56`)을 붙이고, `PublicSubnetA → Edit IPv6 CIDRs`에서 `/64` 범위를 추가했다. 이후 **Auto-assign IPv6**를 켜고 기존 **BastionHost의 ENI(eth0)**에 IPv6 주소를 할당했다.

```text
VPC IPv4: 10.0.0.0/16     IPv6: 2001:db8:1234:ab00::/56
Subnet A: 10.0.0.0/24     IPv6: 2001:db8:1234:ab00::/64
EC2:      10.0.0.72       IPv6: 2001:db8:1234:ab00::10
```

주소는 모두 설명용 예시다.

## 🇰🇷 3. IPv4와 IPv6의 경로는 별도
| Destination | Target |
|---|---|
| `10.0.0.0/16` | local |
| `2001:db8:1234:ab00::/56` | local |
| `0.0.0.0/0` | IGW |
| `::/0` | IGW |

IPv6 `local` 경로는 VPC 내부 통신, `::/0 → IGW`는 외부 IPv6 인터넷 경로다. SG의 모든 IPv4 `0.0.0.0/0`와 모든 IPv6 `::/0`는 다른 규칙이므로 SSH 허용 시 IPv6 TCP 22도 명시해야 한다. 관리자 주소로 제한하는 것이 안전하다. 접속하는 컴퓨터/ISP도 IPv6를 지원해야 한다.

## 🇰🇷 4. IPv4 고갈과 현대 AWS의 IPv6-only
**Dual Stack Subnet**에서는 EC2 생성 시 IPv4 주소도 필요하므로 IPv4 주소가 고갈되면 IPv6가 남아도 새 인스턴스 생성이 실패할 수 있다. 기존 Subnet 크기를 늘리는 대신 VPC 보조 IPv4 CIDR과 새 Subnet 등으로 해결한다.

**강의 정정:** 현재는 **IPv6-only Subnet**을 지원하며 Nitro 기반 호환 EC2를 IPv4 주소 없이 실행할 수 있다. 따라서 '모든 EC2는 반드시 IPv4를 가진다'는 설명은 더 이상 보편적이지 않다. [AWS 문서](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-vpc.html).

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- IPv6 = 128비트, `::/0` = 모든 IPv6 목적지.
- Dual Stack은 IPv4+IPv6 동시 사용.
- VPC `/56`, Subnet `/64`가 일반적.
- IPv6 주소 할당만으로 인터넷 불가: `::/0 → IGW`, SG/NACL 필요.
- IPv6-only EC2도 현재 지원(강의 구버전 주의).

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 모든 IPv6 목적지를 가리키는 라우트 CIDR는?

<details>
<summary>정답 및 해설 보기</summary>

**`::/0`.**

</details>

### Q2. Dual Stack은 무슨 뜻인가?

<details>
<summary>정답 및 해설 보기</summary>

**IPv4와 IPv6를 같이 사용**하는 네트워크 구성.

</details>

### Q3. IPv6 주소가 있으면 IGW 라우트가 없어도 인터넷 접속 가능한가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 외부로 가는 `::/0` 경로 등이 필요.

</details>

### Q4. 기본 `0.0.0.0/0` SSH 허용이 IPv6에도 적용되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** IPv6 `::/0` 또는 적절한 `/128` 규칙 별도 설정.

</details>

### Q5. 현재 AWS에서 IPv4 없이 EC2 시작 가능한가?

<details>
<summary>정답 및 해설 보기</summary>

**가능.** 지원되는 Nitro 기반 EC2를 IPv6-only Subnet에 배치할 수 있다.

</details>

## 🇯🇵 日本語 Summary

IPv6 は 128bit のアドレス体系。Dual Stack では IPv4 と IPv6 を併用する。VPC に IPv6 CIDR、Subnet に `/64`、ENI に IPv6 を付与し、`::/0 → IGW` と SG を設定する。現行 AWS は Nitro 系 EC2 の IPv6-only Subnet もサポートする。

## 🇺🇸 English Summary

IPv6 offers 128-bit addressing. A dual-stack VPC supports IPv4 and IPv6 simultaneously. Attach IPv6 CIDRs to the VPC and subnets, assign IPv6 addresses to ENIs, and add IPv6 routing and security rules. IPv6-only subnets now support compatible Nitro EC2 instances without IPv4.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| IPv6 | 인터넷 프로토콜 버전 6 | IPv6 |
| Dual Stack | 이중 IP 스택 | デュアルスタック |
| IPv6-only Subnet | IPv6 전용 서브넷 | IPv6 専用サブネット |
| IPv6 Default Route | IPv6 기본 경로 | IPv6 デフォルトルート |
