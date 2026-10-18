# 📘 25-25. AWS Network Firewall & Deep Packet Inspection

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `AWS Network Firewall` · `L3-L7` · `Stateful` · `Stateless` · `Firewall Endpoint` · `Firewall Manager` · `Logging`

## 🇰🇷 1. Network Firewall이 해결하는 문제
**AWS Network Firewall**은 VPC의 네트워크 트래픽을 **Layer 3~7** 수준에서 심층적으로 검사하고 허용·차단하는 AWS 관리형 방화벽 서비스다. 단순 IP/포트뿐 아니라 프로토콜, 도메인, 패턴 등에 따라 네트워크 보안 정책을 구현할 수 있다. **IDS/IPS(침입 탐지·방지)** 성격의 Stateful Inspection을 지원한다.

## 🇰🇷 2. SG, NACL, WAF와 비교
| 서비스 | 주 역할 |
|---|---|
| Security Group | ENI 수준 Stateful Allow 제어 |
| NACL | Subnet 경계 Stateless Allow/Deny |
| AWS WAF | HTTP/HTTPS 애플리케이션 공격 방어 |
| Shield | DDoS 대응 |
| Network Firewall | VPC 흐름의 심층 패킷 필터링 및 차단 |
| Firewall Manager | 여러 계정/VPC에 공통 보안 정책 적용 |

## 🇰🇷 3. 배포 및 라우팅이 필수인 이유
**VPC에 Network Firewall을 생성했다고 모든 트래픽이 자동으로 방화벽을 통과하는 것은 아니다.** Firewall Endpoint를 배치하고 Subnet Route Table을 수정해 **검사할 트래픽이 반드시 해당 엔드포인트를 지나게** 해야 한다.

```text
Internet / Peered VPC / VPN / DX
         ↓ (적절한 Route Table)
    Network Firewall Endpoint
         ↓ (검사: PASS/DROP/ALERT)
      보호 대상 Subnet
```

인터넷 인바운드/아웃바운드 및 VPC 간 트래픽, 온프레미스 연결 트래픽을 검사하도록 **설계**할 수 있지만 이를 위해 명시적 라우팅과 대칭 경로 설계가 필요하다.

## 🇰🇷 4. 규칙과 로깅
**Stateless Rule**은 연결 상태 없이 패킷 기준으로 처리하고, **Stateful Rule**은 세션/연결 흐름을 검사한다. IP, 포트, 프로토콜, 도메인, Suricata 호환 Stateful Rules 등을 활용할 수 있다. 로그는 **CloudWatch Logs, S3, Data Firehose** 등으로 내보내 분석한다. **Firewall Manager**로 Organizations 다중 계정에 정책을 중앙 적용할 수 있다.

## 🇰🇷 5. Traffic Mirroring과의 차이
Traffic Mirroring은 **원본 패킷의 사본을 보내 분석**한다. Network Firewall은 **원본 패킷의 실제 경로에 배치하여 통신을 차단할 수 있다**. 강의는 내부에서 Gateway Load Balancer와 비슷한 관리형 원리를 언급하지만 실무·시험 핵심은 Firewall Endpoint와 라우팅을 통한 트래픽 검사다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Network Firewall은 VPC 수준 L3~L7 관리형 방화벽, IDS/IPS 검사.
- Firewall Endpoint에 패킷이 지나도록 Route Table이 필요.
- SG/NACL은 기본 접근 제어, WAF는 HTTP, Network Firewall은 네트워크 심층 검사.
- 다중 계정 공통 정책은 Firewall Manager와 조합.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. VPC의 통신을 실제 경로에서 L3~7 검사·차단하려면?

<details>
<summary>정답 및 해설 보기</summary>

**AWS Network Firewall.**

</details>

### Q2. Network Firewall만 만들면 모든 패킷이 자동 검사되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** Firewall Endpoint를 통과하는 라우팅 설계 필요.

</details>

### Q3. HTTP SQLi 공격의 웹 ACL 전용 방어는 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

**AWS WAF.**

</details>

### Q4. 복사된 패킷을 관찰만 하는 기능은?

<details>
<summary>정답 및 해설 보기</summary>

**VPC Traffic Mirroring.**

</details>

### Q5. 여러 AWS 계정에 Network Firewall 정책을 중앙 배포하려면?

<details>
<summary>정답 및 해설 보기</summary>

**AWS Firewall Manager.**

</details>

## 🇯🇵 日本語 Summary

AWS Network Firewall は VPC の Layer 3〜7 トラフィックを検査し、Stateful/Stateless ルールで許可・拒否できる。Firewall Endpoint へ実トラフィックを通す Route Table 設計が必要。Firewall Manager で複数アカウントのポリシーを管理可能。

## 🇺🇸 English Summary

AWS Network Firewall performs managed Layer 3–7 packet inspection and blocking in a VPC. You must route relevant traffic through firewall endpoints. It supports stateless and stateful rules, logging, and organization-wide policy management through AWS Firewall Manager.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Network Firewall | 관리형 네트워크 방화벽 | ネットワークファイアウォール |
| Deep Packet Inspection | 심층 패킷 검사 | ディープパケットインスペクション |
| Stateful Inspection | 상태 기반 검사 | ステートフル検査 |
| Firewall Endpoint | 방화벽 엔드포인트 | ファイアウォールエンドポイント |
