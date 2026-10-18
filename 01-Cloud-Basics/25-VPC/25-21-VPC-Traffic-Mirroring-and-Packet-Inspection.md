# 📘 25-21. VPC Traffic Mirroring & Packet Inspection

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Traffic Mirroring` · `Packet Capture` · `ENI` · `NLB` · `Filter` · `VXLAN` · `IDS`

## 🇰🇷 1. Traffic Mirroring의 목적
**VPC Traffic Mirroring**은 지원 EC2의 **ENI에서 실제 IP 패킷의 복사본**을 만들어 별도의 보안 분석 시스템으로 전달한다. 원래 패킷의 통신 경로를 끊거나 검사 결과를 기다리게 하는 **Inline Inspection**과 달리, **대역 외(Out-of-Band) 관찰**에 적합하다. 물리 네트워크의 SPAN/포트 미러링과 유사하다.

## 🇰🇷 2. Source, Target, Filter, Session
| 요소 | 역할 |
|---|---|
| Source ENI | 복사할 트래픽이 있는 EC2 인터페이스 |
| Target | 사본을 수신할 분석 ENI/NLB/Gateway Load Balancer Endpoint |
| Filter | 복사할 패킷의 방향, 프로토콜, CIDR/Port 등을 선택 |
| Session | Source, Target, Filter를 결합하여 미러링 실행 |

복사본은 VXLAN으로 캡슐화되어 대상까지 전송된다.

## 🇰🇷 3. NLB와 Auto Scaling 분석 구조
```text
EC2 A (ENI A) ── 복사 ─┐
                        ├── NLB → 분석 EC2 Auto Scaling Group
EC2 B (ENI B) ── 복사 ─┘
```

NLB는 분석 자체가 아니라 **트래픽 분산**, 보안 소프트웨어가 실제 패킷 분석을 담당한다. 원본 EC2는 정상 서비스를 지속한다. 단 미러링 송신에 네트워크 대역폭이 필요하므로 비용과 자원 영향이 완전히 0은 아니다.

## 🇰🇷 4. Flow Logs 및 Inline Network Firewall과 비교
| 서비스 | 취급 데이터 | 역할 |
|---|---|---|
| Flow Logs | IP/포트/Action 등 메타데이터 | 통신 기록 분석 |
| Traffic Mirroring | 실제 패킷 복사본 | 침입 탐지·심층 분석 |
| Network Firewall | 원본 통신 경로상의 트래픽 | 실시간 허용·차단 |

암호화된 HTTPS 패킷을 미러링해도 그 데이터가 자동으로 복호화되는 것은 아니다. 타깃은 같은 VPC 또는 네트워크가 연결된 다른 VPC에도 구성할 수 있다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Mirroring은 실제 패킷 **사본**을 분석 시스템으로 전달.
- Source=ENI, Target=ENI/NLB 등, Filter/Session 구성.
- NLB는 분석용 EC2에 복사본을 분산.
- Flow Logs는 메타데이터, Mirroring은 패킷 복사.
- Mirroring 자체는 실제 악성 패킷을 차단하는 Inline 방화벽이 아니다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. HTTP 본문을 포함하는 실제 패킷 복사본을 얻으려면?

<details>
<summary>정답 및 해설 보기</summary>

**VPC Traffic Mirroring**(단 암호화 내용은 별도 문제).

</details>

### Q2. 미러링 소스는 어떤 AWS 네트워크 구성 요소인가?

<details>
<summary>정답 및 해설 보기</summary>

**ENI.**

</details>

### Q3. NLB 뒤 분석 EC2 여러 대를 두는 이유는?

<details>
<summary>정답 및 해설 보기</summary>

**미러링 트래픽 분석 부하 분산 및 확장.**

</details>

### Q4. Mirroring이 침입 트래픽을 직접 차단하는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 사본 분석으로 탐지가 목적.

</details>

### Q5. Flow Logs와 Traffic Mirroring의 핵심 데이터 차이는?

<details>
<summary>정답 및 해설 보기</summary>

**메타데이터 vs 패킷 복사본.**

</details>

## 🇯🇵 日本語 Summary

VPC Traffic Mirroring は ENI の実パケットをコピーし、分析用 ENI/NLB 等に配送する。元の通信経路をブロックせずに脅威監視・パケット解析を行う。Flow Logs はメタデータ、Network Firewall はインラインの制御機能。

## 🇺🇸 English Summary

VPC Traffic Mirroring duplicates packet traffic from source ENIs to monitoring targets such as ENIs or NLBs. It supports packet inspection and intrusion detection without inserting a blocking appliance into the original flow. Unlike Flow Logs, it carries packet copies, not just metadata.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Traffic Mirroring | 트래픽 복사 | トラフィックミラーリング |
| Packet Inspection | 패킷 검사 | パケット検査 |
| Mirror Target | 미러링 대상 | ミラーターゲット |
| Out-of-Band | 비인라인 관찰 | アウトオブバンド |
