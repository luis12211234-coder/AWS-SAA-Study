# 📘 25-19. DX Gateway, Connection Types, Resiliency & VPN Backup

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Direct Connect Gateway` · `Dedicated` · `Hosted` · `MACsec` · `IPsec` · `VPN Backup` · `Resiliency`

## 🇰🇷 1. Direct Connect Gateway로 여러 VPC 접근
**Direct Connect Gateway(DXGW)**는 DX 물리 연결을 여러 VPC의 **VGW** 또는 **TGW**와 연결하는 데 쓰는 AWS 네트워킹 리소스다. 다른 리전이나 계정의 VPC에 접근하는 구성에 유용하다.

```text
회사 → DX Location → DX Connection → Private VIF → DXGW
                                                ├── VGW → VPC A
                                                └── VGW → VPC B
```

TGW와 연결하는 경우 **Transit VIF → DXGW → TGW → 다수 VPC**로 이어진다. DXGW 자체가 연결된 VPC들 사이를 자동으로 중계하는 라우터는 아니다.

## 🇰🇷 2. Dedicated vs Hosted Connection
| 종류 | 제공 방식 | 대표 속도 |
|---|---|---|
| **Dedicated** | 고객에게 전용 DX 물리 포트 | 1/10/100/400 Gbps |
| **Hosted** | AWS DX 파트너가 제공하는 연결 | 50 Mbps~25 Gbps의 지원 단계 |

최대 속도·제공 여부는 연결 위치와 파트너 지원에 따라 달라진다. 물리 회선 개통에는 수주 이상이 걸릴 수 있으므로 **긴급 1주 내 구축** 요구에는 VPN이 더 적합할 수 있다. [AWS 문서](https://docs.aws.amazon.com/directconnect/latest/UserGuide/connection_options.html).

## 🇰🇷 3. 보안: 사설 경로와 암호화는 별개
DX는 공용 인터넷을 통하지 않지만 **기본 암호화되지 않는다**. 민감 데이터가 암호화되어야 한다면 **DX 경로 위 IPsec VPN**, 또는 지원되는 전용 연결에서 **MACsec(L2 암호화)**을 사용한다.

**DX 위 VPN**은 암호화 목적, **DX 장애 대비 별도 인터넷 기반 Site-to-Site VPN**은 백업 경로 목적이다. 두 구조는 서로 다르다.

## 🇰🇷 4. 높은 복원력과 Maximum Resiliency
DX 하나만 있으면 물리 링크나 접속 시설 장애에 취약하다. 일반적인 **High Resiliency** 구성은 **서로 다른 두 DX Location에 연결 하나씩** 배치한다. **Maximum Resiliency** 대표 모델은 **두 Location × 각각 독립 연결 두 개 = 총 4개**다. 독립 장치와 회선으로 공통 장애 가능성을 줄이고 Failover 테스트를 수행한다.

## 🇰🇷 5. VPN Backup 설계
```text
회사 ── Direct Connect ── AWS  [Primary]
   └── 인터넷 IPsec VPN ── AWS  [Backup]
```

BGP 등 라우팅 정책으로 평소 DX를 선호하고 DX 장애 시 VPN을 사용하도록 구성한다. 이 VPN은 DX 위에서 암호화하는 터널이 아니라 **독립적인 인터넷 기반 백업 경로**다. 연결과 데이터 전송에는 요금이 발생한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- DXGW는 VIF를 여러 VGW/TGW 연결과 연계.
- DX는 기본 암호화 안 됨, IPsec VPN 또는 지원 환경 MACsec 활용.
- DX + VPN Backup은 장애 복구; DX over IPsec은 암호화 목적.
- 고복원력: 두 Location에서 독립 연결; 최대 복원력 대표 모델은 4개 연결.
- 긴급 개통은 VPN, 장기간 안정 연결·대량 전송은 DX 고려.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 서로 다른 리전의 VPC를 하나의 DX 연결로 접근하려면?

<details>
<summary>정답 및 해설 보기</summary>

**Direct Connect Gateway**와 적절한 VGW/TGW 연결.

</details>

### Q2. DX는 기본적으로 암호화되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 공용 인터넷을 우회하지만 암호화와 별개다.

</details>

### Q3. Direct Connect 연결 유형 두 가지는?

<details>
<summary>정답 및 해설 보기</summary>

**Dedicated Connection, Hosted Connection.**

</details>

### Q4. DX 장애 시 인터넷 IPsec VPN으로 넘기는 설계의 목적은?

<details>
<summary>정답 및 해설 보기</summary>

**장애 복구용 Backup/Failover.**

</details>

### Q5. 최대 복원력 대표 설계의 DX 연결 수는?

<details>
<summary>정답 및 해설 보기</summary>

**두 Location에 각각 2개씩 총 4개.**

</details>

## 🇯🇵 日本語 Summary

DX Gateway は Direct Connect を複数の VPC/VGW/TGW へ関連付ける。Dedicated は専用物理ポート、Hosted はパートナー経由。DX は暗号化を標準提供しないため VPN/MACsec を検討する。DX 障害のバックアップ VPN と DX 上の暗号化 VPN は目的が違う。

## 🇺🇸 English Summary

Direct Connect Gateway extends physical DX connectivity to multiple VPC networks. Dedicated connections allocate a physical port; hosted connections use partners. DX is not encrypted by default. Distinguish IPsec over DX for encryption from independent VPN backup for failover. Resiliency increases with diverse DX locations and links.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Direct Connect Gateway | DX 연결 게이트웨이 | Direct Connect ゲートウェイ |
| Dedicated Connection | 전용 물리 연결 | 専用接続 |
| Hosted Connection | 파트너 제공 연결 | ホスト接続 |
| Failover | 장애 전환 | フェイルオーバー |
| Resiliency | 복원력 | レジリエンシー |
