# 📘 25-20. Transit Gateway: Attachments, Routing, RAM & ECMP

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Transit Gateway` · `Attachment` · `Association` · `Propagation` · `RAM` · `ECMP` · `Multicast`

## 🇰🇷 1. 왜 TGW가 필요한가
VPC Peering은 **비전이성**이므로 많은 VPC를 모두 직접 연결하면 토폴로지가 복잡해진다. **Transit Gateway(TGW)**는 수많은 VPC, Site-to-Site VPN, Direct Connect Gateway를 **중앙 허브(Hub-and-Spoke)**에 연결해 전이적 라우팅을 제공한다. TGW는 **리전 리소스**이고, 서로 다른 리전의 TGW끼리 Peering할 수 있다.

## 🇰🇷 2. Attachment는 TGW의 연결 지점
**Attachment(어태치먼트)**는 VPC, VPN, DXGW 등의 네트워크가 TGW에 접속하는 연결 객체다.

```text
VPC A --[Attachment A]-- TGW --[Attachment B]-- VPC B
                               ├─ VPN Attachment → 회사
                               └─ DXGW Attachment → Direct Connect
```

패킷을 **VPC B Attachment로 전달한다**는 말은 TGW가 목적지인 VPC B로 향하는 연결을 선택했다는 뜻이다.

## 🇰🇷 3. VPC Route Table과 TGW Route Table의 관계
VPC A(10.1.0.0/16)에서 VPC B(10.2.0.0/16)로 보낼 때 VPC A Route Table에 `10.2.0.0/16 → TGW`, TGW Route Table에 `10.2.0.0/16 → Attachment B`가 있어야 한다. VPC B의 응답에도 반대 방향 경로가 필요하다. TGW에 연결만 했다고 모든 VPC 간 통신이 자동 허용되지는 않는다.

## 🇰🇷 4. Association vs Propagation: 핵심 구분
**Association(어소시에이션):** 특정 Attachment를 통해 **들어온 패킷이 어느 TGW Route Table을 조회하는지** 결정. 한 Attachment는 한 번에 한 TGW Route Table에 연관될 수 있다.

**Propagation(프로퍼게이션):** 특정 Attachment의 도달 가능 CIDR 경로를 **어느 TGW Route Table에 자동 등록**할지 결정. 필요하면 정적 경로도 생성 가능.

```text
A 패킷 유입 → Attachment A Association → Table 1 조회
B의 경로    → Attachment B Propagation → Table 1에 10.2.0.0/16 등록
결과       → Table 1이 B Attachment로 포워딩
```

TGW Route Table을 여러 개 두면 개발/운영망처럼 **상호 접근 가능 대상을 나눌 수 있다**.

## 🇰🇷 5. RAM, Peering, ECMP, Multicast
**AWS RAM**은 다른 계정과 TGW를 공유한다. **Inter-Region TGW Peering**은 다른 리전 TGW를 연결하며 정적 경로 구성이 필요하다.

**ECMP(Equal-Cost Multi-Path)**는 동등한 BGP 경로의 VPN 터널 여러 개로 통신 흐름을 분산해 전체 처리량을 늘린다. 표준 터널의 강의상 최대 **1.25Gbps/터널**, VPN 연결에는 터널 두 개가 있다. TGW에 여러 VPN을 연결해 확장할 수 있지만 **단일 TCP 스트림 속도가 모든 터널 대역폭의 합이 되는 것은 아니다**. 고대역폭 터널 옵션 등 현재 기능이 추가되어 정적 숫자만 암기하지 않는다.

**TGW Multicast**는 지원되는 VPC Attachment 사이 IP 멀티캐스트를 제공하지만 VPN/DX Attachment에 멀티캐스트를 그대로 전달하지는 못한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- TGW는 전이적 라우팅이 가능한 Hub-and-Spoke 중앙 라우터.
- Attachment = TGW와 네트워크 연결.
- Association = 유입 패킷이 볼 TGW Route Table 결정.
- Propagation = 경로를 TGW Route Table에 자동 등록.
- BGP ECMP로 복수 VPN 터널 처리량 확대, IP Multicast는 TGW 기능.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. VPC Peering 100개를 전이적으로 연결할 중앙 서비스는?

<details>
<summary>정답 및 해설 보기</summary>

**Transit Gateway.**

</details>

### Q2. VPC B Attachment는 무엇을 의미하는가?

<details>
<summary>정답 및 해설 보기</summary>

**TGW와 VPC B를 잇는 연결 객체.**

</details>

### Q3. Association과 Propagation 중 Table 선택은?

<details>
<summary>정답 및 해설 보기</summary>

**Association.** 유입 Attachment가 참고할 Table 지정.

</details>

### Q4. Propagation은 어떤 작업인가?

<details>
<summary>정답 및 해설 보기</summary>

**Attachment의 CIDR 경로를 지정 TGW Route Table에 자동 등록.**

</details>

### Q5. 여러 VPN 터널을 동등한 경로로 동시에 활용하는 것은?

<details>
<summary>정답 및 해설 보기</summary>

**ECMP**, 일반적으로 BGP 기반 TGW VPN에서 활용.

</details>

## 🇯🇵 日本語 Summary

TGW は VPC、VPN、DXGW を Hub-and-Spoke で接続するリージョン型ルータ。Attachment は接続口、Association は着信トラフィックが参照する TGW Route Table を指定し、Propagation は経路をその Table に登録する。RAM でアカウント共有でき、ECMP で VPN 経路を分散可能。

## 🇺🇸 English Summary

Transit Gateway is a regional transitive routing hub connecting VPCs, VPNs, and Direct Connect gateways. Attachments represent connections. Association selects the routing table for incoming traffic; propagation installs attachment routes into tables. RAM enables sharing, and ECMP balances VPN flows across equal-cost paths.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Transit Gateway | 중앙 전이형 게이트웨이 | トランジットゲートウェイ |
| Attachment | 연결 객체 | アタッチメント |
| Association | 라우트 테이블 연관 | アソシエーション |
| Propagation | 경로 전파 | ルート伝播 |
| ECMP | 등가 다중 경로 | 等コストマルチパス |
