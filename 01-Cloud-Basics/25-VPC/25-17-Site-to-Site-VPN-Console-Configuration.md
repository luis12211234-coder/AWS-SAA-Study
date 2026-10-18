# 📘 25-17. Site-to-Site VPN: Console Configuration Steps

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `CGW Console` · `VGW Console` · `VPN Connection` · `BGP ASN` · `Static Routing` · `Tunnel`

## 🇰🇷 1. 실습의 제한: 실제 장비 없음
강사는 실제 온프레미스 네트워크 장비가 없어 **설정 화면과 생성 절차**만 확인했다. 실제 VPN 터널 연결이 완료된 것으로 오해하면 안 된다.

## 🇰🇷 2. 1단계: AWS Customer Gateway 리소스
VPC Console → **Customer Gateways** → Create. 회사 VPN 장치의 외부 공인 IP(또는 NAT 장치 공인 IP), 필요한 **BGP ASN** 등을 지정한다. 인증서 기반 인증을 사용할 때 **Certificate ARN**을 설정할 수 있다. ARN은 모든 VPN에 반드시 필요한 입력은 아니다.

## 🇰🇷 3. 2단계: Virtual Private Gateway
VPC Console → **Virtual Private Gateways**에서 VGW를 생성하고 대상 VPC에 Attach한다. VGW는 AWS 측 IPsec VPN 종단이며, AWS 측 ASN 값이 설정될 수 있다.

## 🇰🇷 4. 3단계: VPN Connection
**Site-to-Site VPN Connections → Create VPN Connection**에서 Target Gateway Type=VGW, 생성한 VGW와 CGW를 선택하고 **BGP(동적)** 또는 **Static** 라우팅 방식과 터널 옵션을 설정한다. AWS가 제공하는 장비별 구성 파일을 기반으로 회사 방화벽/VPN 라우터의 터널과 암호화 옵션을 설정해야 한다.

## 🇰🇷 5. 4단계: 양쪽 Route 및 보안 확인
AWS 라우팅 테이블과 온프레미스 라우터에 상대 CIDR 경로를 등록하거나 경로 전파를 사용한다. **VPN 연결만 만든다고 EC2가 보이는 게 아니다.** ICMP 테스트라면 SG/NACL과 회사 방화벽에서 ICMP 허용 여부를 확인한다. 일반적인 Site-to-Site VPN에는 두 IPsec 터널이 제공되며 실제 운영에서는 이중화를 구성한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- AWS CGW Resource 설정 ≠ 고객 물리 VPN 장비 생성.
- VGW를 VPC에 연결하고 VPN Connection에서 CGW/VGW 선택.
- 회사 VPN 장치 쪽 구성 파일 반영 및 라우팅이 추가로 필요.
- BGP 또는 정적 라우팅을 선택하고 터널 2개 이중화 검토.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 회사 VPN 장치의 외부 IP를 등록하는 AWS 리소스는?

<details>
<summary>정답 및 해설 보기</summary>

**Customer Gateway(CGW).**

</details>

### Q2. VGW를 만든 후 반드시 연결해야 하는 대상은?

<details>
<summary>정답 및 해설 보기</summary>

**VPN으로 연결하려는 VPC.**

</details>

### Q3. VPN Connection을 만든 직후 실제 통신이 자동 완성되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 회사 장비 터널 및 양쪽 경로를 설정해야 한다.

</details>

### Q4. BGP는 VPN에서 무엇을 도와주는가?

<details>
<summary>정답 및 해설 보기</summary>

**동적 경로 정보 교환**과 장애 전환 운용.

</details>

### Q5. 실습에서 실제 VPN 연결을 생성하지 않은 이유는?

<details>
<summary>정답 및 해설 보기</summary>

**온프레미스 실물 VPN 인프라가 없었기 때문.**

</details>

## 🇯🇵 日本語 Summary

実習は CGW リソース作成、VGW 作成・VPC 接続、Site-to-Site VPN Connection の関連付けをコンソールで確認した。実際の接続にはオンプレミス機器設定、ルーティングと IPsec トンネル設定が必要。

## 🇺🇸 English Summary

The console workflow creates a CGW configuration object, a VGW attached to a VPC, and a VPN connection referencing both. Actual connectivity still requires on-premises device configuration, IPsec tunnel setup, and two-way routing.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| VPN Connection | VPN 연결 | VPN 接続 |
| Customer Gateway Resource | 고객 게이트웨이 설정 리소스 | カスタマーゲートウェイリソース |
| Tunnel | VPN 터널 | VPN トンネル |
| Static Routing | 정적 라우팅 | 静的ルーティング |
