# 📘 25-26. VPC Final Review & SAA-C03 Scenarios

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `VPC` · `CIDR` · `Route Table` · `NAT` · `Peering` · `Endpoint` · `DX` · `VPN` · `TGW` · `IPv6` · `Firewall`

## 🇰🇷 1. 문제 상황에서 서비스 선택하기
| 요구사항 | 우선 고려 |
|---|---|
| 서브넷의 IPv4 인터넷 양방향 연결 | IGW + 공인 IPv4 + Route |
| Private EC2의 IPv4 인터넷 송신 | NAT Gateway |
| Private EC2의 IPv6 송신, 외부 신규 연결 차단 | Egress-only IGW |
| Private EC2 관리용 SSH 경유 | Bastion Host 또는 Session Manager |
| 특정 IP 명시적으로 차단 | NACL |
| ENI 인바운드 허용·상태 저장 | Security Group |
| 두 VPC의 사설 주소 통신 | VPC Peering |
| 수십·수백 VPC 전이적 연결 | Transit Gateway |
| S3/DynamoDB 사설 접근·비용 최소화 | Gateway Endpoint |
| SNS/SSM 등 AWS 서비스 사설 접근 | Interface Endpoint |
| 회사와 AWS의 빠른 암호화 연결 | Site-to-Site VPN |
| 회사와 AWS 전용 회선 | Direct Connect |
| IP/포트 허용·거부 기록 | Flow Logs |
| 실제 패킷 사본 분석 | Traffic Mirroring |
| VPC 경로상의 트래픽 심층 차단 | Network Firewall |

## 🇰🇷 2. 라우트 결정의 기본 흐름
```text
Private EC2가 목적지 IP 확인
  ├─ 동일 VPC CIDR → local
  ├─ S3 Prefix List → S3 Gateway Endpoint
  ├─ 다른 VPC CIDR → Peering 또는 Transit Gateway
  ├─ 회사 CIDR → VPN VGW 또는 TGW / DX 연결
  ├─ 일반 IPv4 인터넷 → NAT GW(Private) / IGW(Public)
  └─ 일반 IPv6 인터넷 → Egress-only IGW(Private) / IGW(Public)
```

**Longest Prefix Match**는 여러 경로가 일치할 때 일반적으로 더 구체적인 경로를 선택한다. 연결별 특수 우선순위도 있으므로 최종 구성은 AWS 라우팅 문서를 확인한다.

## 🇰🇷 3. 보안 문제를 한눈에 비교
**SG는 Stateful + Allow only**, **NACL은 Stateless + Allow/Deny + Rule Number**다. **NACL 반환 트래픽의 임시 포트**를 놓치지 않는다. Flow Logs는 메타데이터 기록, Mirroring은 패킷 사본, Network Firewall은 실제 패킷 흐름 필터다. AWS WAF는 HTTP L7 악성 요청에 집중한다.

## 🇰🇷 4. 오래된 강의에서 정정할 내용
1. **IPv6-only Subnet**과 Nitro 기반 IPv6-only EC2가 현재 지원된다. 'EC2는 무조건 IPv4 필요'는 오래된 설명이다.
2. 기존 NAT GW 강의는 **Zonal NAT** 기준. 현재 **Regional NAT Gateway**도 지원된다.
3. S3 Gateway Endpoint는 무료이며 AWS PrivateLink가 아닌 **Route Table + Prefix List** 기반이다.
4. RDS 같은 리전 원본·Read Replica 간 복제 전송에는 일반적으로 별도 데이터 전송 요금이 없다.
5. DX는 기본 암호화가 되지 않으며 VPN/MACsec을 추가할 수 있다. 회선 속도·가격은 최신 AWS 문서 확인.
6. **EC2-Classic/ClassicLink는 종료**된 레거시 기능이다.
7. Network Firewall은 생성만으로 모든 트래픽을 검사하지 않으며 **라우팅**이 필요하다.

## 🇰🇷 5. 공부 전략
이 섹션의 공통 주제는 **목적지/경로(Route) → 통신 허용(SG/NACL) → 통신 관찰(Flow Logs/Mirroring) → 하이브리드 연결(VPN/DX/TGW)**이다. 독립 서비스 이름을 외우기보다 문제의 요구사항에 맞춰 필요한 **경로·보안·권한·비용 조건**을 결합하면 된다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- VPC Peering은 비전이적, TGW는 전이적 중앙 허브.
- S3/DynamoDB Gateway Endpoint, 그 외 지원 AWS 서비스는 Interface Endpoint.
- VPN은 인터넷+IPsec, DX는 전용 연결+기본 비암호화.
- SG/NACL/Flow Logs/Mirroring/Network Firewall의 역할 분리.
- IPv4/IPv6 목적지 경로와 듀얼 스택 고갈 문제 구분.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 수백 VPC를 전이적으로 연결하려면?

<details>
<summary>정답 및 해설 보기</summary>

**Transit Gateway.**

</details>

### Q2. Private EC2에서 S3에 NAT 요금 없이 접근하려면?

<details>
<summary>정답 및 해설 보기</summary>

**S3 Gateway Endpoint.**

</details>

### Q3. Inbound HTTP ACCEPT 후 응답 Outbound REJECT. 우선 조사할 장치는?

<details>
<summary>정답 및 해설 보기</summary>

**NACL outbound.** SG는 Stateful이기 때문.

</details>

### Q4. 회사와 AWS 간 전용 네트워크 연결은?

<details>
<summary>정답 및 해설 보기</summary>

**AWS Direct Connect.** 기본 암호화는 별도 고려.

</details>

### Q5. EC2의 패킷 사본을 분석하고 실제 원본 통신은 그대로 두고 싶다면?

<details>
<summary>정답 및 해설 보기</summary>

**VPC Traffic Mirroring.**

</details>

## 🇯🇵 日本語 Summary

VPC の課題は経路選択、アクセス制御、監視、オンプレミス接続に分類できる。Peering は非推移的、TGW は推移的。S3/DynamoDB は Gateway Endpoint、他の対応サービスは Interface Endpoint。VPN は IPsec、DX は専用接続。SG/NACL/Flow Logs/Network Firewall を区別して選ぶ。

## 🇺🇸 English Summary

VPC exam scenarios revolve around routing, connectivity, security boundaries, observability, and hybrid networking. Peering is non-transitive; TGW is transitive. Gateway endpoints fit S3/DynamoDB, interface endpoints fit other supported services. Distinguish VPN from DX and SG/NACL from flow logging and network firewalls.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Architecture Scenario | 아키텍처 시나리오 | アーキテクチャシナリオ |
| Hybrid Connectivity | 하이브리드 연결 | ハイブリッド接続 |
| Routing Policy | 라우팅 정책 | ルーティングポリシー |
| Network Observability | 네트워크 관측성 | ネットワーク可観測性 |
