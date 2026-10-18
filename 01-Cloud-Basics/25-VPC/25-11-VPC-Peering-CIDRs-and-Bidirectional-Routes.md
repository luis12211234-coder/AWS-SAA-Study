# 📘 25-11. VPC Peering, CIDRs & Bidirectional Routes

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `VPC Peering` · `Non-Transitive` · `Requester` · `Accepter` · `Route Table` · `Cross Region`

## 🇰🇷 1. 왜 VPC Peering이 필요한가
각 VPC는 기본적으로 격리된다. **VPC Peering**은 서로 다른 두 VPC가 사설 IP로 직접 통신할 수 있도록 AWS 네트워크 내 연결을 제공한다. 같은 계정/다른 계정, 같은 리전/다른 리전 조합을 지원한다. 단 **두 VPC의 CIDR가 중복되면 피어링 불가**하다.

## 🇰🇷 2. 비전이성의 의미
```text
VPC A ← Peering → VPC B ← Peering → VPC C

A ↔ B 가능, B ↔ C 가능
A ↔ C 불가능 (직접 A-C Peering 필요)
```

B는 A의 트래픽을 C로 중계해 주지 않는다. 대규모 VPC 네트워크에서 전이적 라우팅이 필요하면 **Transit Gateway**를 고려한다.

## 🇰🇷 3. 실습: DemoVPC와 DefaultVPC 연결
강사는 `DemoVPC(10.0.0.0/16)`의 BastionHost `10.0.0.72`에서 HTTP 서비스를 실행하고, `DefaultVPC(172.31.0.0/16)`의 EC2에서 `curl http://10.0.0.72:80/`을 시도했다. 초기에는 연결되지 않았다.

**Peering Connections**에서 Requester=DemoVPC, Accepter=DefaultVPC로 생성하고 **Pending Acceptance**를 Accept하여 Active로 만들었다. 하지만 아직 `curl`은 실패했다. 연결 생성만으로 라우트가 추가되는 것은 아니기 때문이다.

## 🇰🇷 4. 양방향 라우팅 및 보안
```text
DemoVPC PublicRouteTable:
  172.31.0.0/16 → pcx-...

DefaultVPC MainRouteTable:
  10.0.0.0/16 → pcx-...
```

각 인스턴스가 속한 Subnet의 Route Table에 **상대 VPC CIDR로 향하는 피어링 경로**가 필요하다. SG/NACL도 해당 포트를 허용해야 한다. 같은 리전의 피어링에서는 상대 VPC SG를 보안 그룹 규칙에서 참조할 수 있으며 **리전 간 피어링에서는 상대 SG 직접 참조가 불가능**하다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Peering = 두 VPC 간 사설 IP 직접 통신, CIDR 중복 불가.
- 비전이성: A-B, B-C여도 A-C 직접 통신 불가.
- Requester 생성, Accepter 승인 후 **양쪽 Route Table**에 경로 필요.
- SG/NACL 허용도 필수. 같은 리전의 피어 SG 참조 가능.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. A-B, B-C Peering이 있으면 A-C 연결도 자동 가능한가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 비전이성으로 별도 A-C 피어링이 필요하다.

</details>

### Q2. Peering Connection이 Active인데 curl 실패. 우선 확인할 것은?

<details>
<summary>정답 및 해설 보기</summary>

**양쪽 Subnet Route Table의 상대 CIDR 경로** 및 SG/NACL.

</details>

### Q3. CIDR가 겹치는 두 VPC는 피어링할 수 있는가?

<details>
<summary>정답 및 해설 보기</summary>

**불가능.**

</details>

### Q4. 다른 AWS 계정의 VPC와 Peering 가능한가?

<details>
<summary>정답 및 해설 보기</summary>

**가능.** 요청과 승인 및 경로 구성이 필요하다.

</details>

### Q5. 교차 리전 Peering에서 다른 VPC SG ID를 직접 참조할 수 있는가?

<details>
<summary>정답 및 해설 보기</summary>

**일반적으로 불가능.** CIDR 기반 규칙 사용.

</details>

## 🇯🇵 日本語 Summary

VPC Peering は異なる VPC を AWS ネットワーク経由で接続する。CIDR の重複は不可、接続は非推移的。Requester/Accepter で接続を有効化し、両側 Subnet の Route Table と SG/NACL を設定する。

## 🇺🇸 English Summary

VPC peering privately connects two non-overlapping VPCs across accounts or Regions. It is non-transitive. After request acceptance, both sides need routing entries toward the peer CIDR plus permitting security rules.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| VPC Peering | VPC 피어링 | VPC ピアリング |
| Non-Transitive | 비전이성 | 非推移性 |
| Requester | 요청자 | リクエスター |
| Accepter | 수락자 | アクセプター |
| Peering Connection | 피어링 연결 | ピアリング接続 |
