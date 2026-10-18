# 📘 25-16. Site-to-Site VPN: CGW, VGW & VPN CloudHub

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Site-to-Site VPN` · `IPsec` · `Customer Gateway` · `VGW` · `NAT-T` · `BGP` · `CloudHub`

## 🇰🇷 1. Site-to-Site VPN이 해결하는 문제
회사 **온프레미스 데이터센터**와 AWS VPC의 사설 네트워크를 **공용 인터넷을 통한 암호화된 IPsec VPN 터널**로 연결한다. IP 주소 체계가 충돌하지 않도록 설계하고 양쪽 라우팅을 맞춰야 한다. AWS가 제공하는 일반적인 Site-to-Site VPN 연결은 이중화된 **터널 두 개**를 제공한다.

## 🇰🇷 2. CGW와 VGW를 구분하기
```text
회사 네트워크 → [실제 회사 VPN 라우터] ══ IPsec VPN ══ [AWS VGW] → VPC
                  CGW 쪽 종단                         AWS 종단
```

**CGW(Customer Gateway)**는 실제 회사 VPN 장비/고객 측 종단을 가리킨다. 다만 AWS 콘솔의 **CGW 리소스**는 회사에 있는 장비의 공인 IP·BGP ASN 등 연결 정보를 등록한 **설정 객체**다. 콘솔에서 CGW를 만든다고 회사에 물리 장비가 생성되는 것은 아니다.

**VGW(Virtual Private Gateway)**는 AWS 측 VPN 종단 기능으로 VPC에 Attach하여 사용한다.

## 🇰🇷 3. CGW가 NAT 뒤에 있을 때
회사 VPN 라우터에 공인 IP가 직접 있다면 해당 공인 IP를 AWS CGW 리소스에 등록한다. **사설 IP만 있는 VPN 장비가 NAT 라우터 뒤에 있다면** NAT 장치의 **공인 IP**를 CGW에 등록하고 **NAT-T(NAT Traversal)**를 이용해 IPsec 터널이 NAT를 통과하게 한다. NAT-T는 일반적으로 ESP 트래픽을 UDP 4500으로 캡슐화한다. SAA에서는 세부 포트 암기보다 **NAT 뒤 VPN 장비의 연결 지원 기술**로 기억하면 된다.

## 🇰🇷 4. 라우팅, BGP, ICMP 검사
회사 → AWS VPC 경로, AWS Route Table의 회사 CIDR 경로가 모두 필요하다. **Route Propagation**으로 VPN에서 학습한 경로를 VPC Route Table에 전파하거나 정적 경로를 등록할 수 있다. **BGP(Border Gateway Protocol)**는 동적 경로 교환 프로토콜, **ASN**은 자율 시스템 식별 번호다. `ping`으로 EC2를 확인한다면 SG에서 ICMP를 허용하는지도 본다.

## 🇰🇷 5. VPN CloudHub
여러 회사 지점의 **CGW**들을 **하나의 VGW**에 Site-to-Site VPN으로 연결하고, BGP 동적 라우팅을 이용해 사이트 간 통신을 지원하는 **Hub-and-Spoke** 모델이다. 각 사이트의 CIDR가 겹치지 않도록 하고 필요한 경로를 전파한다. VPN은 공용 인터넷 경로이지만 트래픽은 IPsec으로 암호화된다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- CGW = 회사 VPN 장비, AWS CGW 리소스 = 장비의 연결 정보.
- VGW = AWS 측 VPN 게이트웨이.
- IPsec VPN은 공용 인터넷을 이용하지만 암호화된다.
- CGW 뒤에 NAT가 있으면 NAT 공인 IP + NAT-T.
- VGW 라우트 전파 또는 정적 경로, SG ICMP 점검.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Site-to-Site VPN은 공용 인터넷을 이용하는가?

<details>
<summary>정답 및 해설 보기</summary>

**이용한다.** IPsec으로 암호화된 터널을 사용한다.

</details>

### Q2. AWS 콘솔에서 CGW를 생성하면 회사에 물리 라우터가 생기는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 회사 VPN 장비의 IP/ASN을 등록한 리소스다.

</details>

### Q3. VPN의 AWS 측 종단으로 VPC에 연결되는 것은?

<details>
<summary>정답 및 해설 보기</summary>

**VGW(Virtual Private Gateway).**

</details>

### Q4. 회사 VPN 장비가 NAT 뒤에 있으면 CGW에 어떤 IP를 등록하는가?

<details>
<summary>정답 및 해설 보기</summary>

**NAT 라우터의 공인 IP**와 NAT-T 설정을 사용한다.

</details>

### Q5. 여러 회사 지점을 하나의 VGW에 BGP VPN으로 연결하는 모델은?

<details>
<summary>정답 및 해설 보기</summary>

**AWS VPN CloudHub.**

</details>

## 🇯🇵 日本語 Summary

Site-to-Site VPN はオンプレミスと AWS を公衆インターネット上の IPsec で暗号化接続する。CGW は顧客側装置、AWS の CGW リソースはその接続情報、VGW は AWS 側終端。NAT 配下では NAT-T を用い、VPN CloudHub は複数拠点を Hub-and-Spoke で結ぶ。

## 🇺🇸 English Summary

Site-to-Site VPN connects on-premises networks and AWS over IPsec-encrypted internet tunnels. A customer gateway device lives on premises, while its AWS CGW object stores device attributes. VGW is the AWS-side endpoint. NAT-T supports customer devices behind NAT; VPN CloudHub interconnects multiple sites.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Site-to-Site VPN | 사이트 간 VPN | サイト間 VPN |
| Customer Gateway | 고객 게이트웨이 | カスタマーゲートウェイ |
| Virtual Private Gateway | 가상 사설 게이트웨이 | 仮想プライベートゲートウェイ |
| NAT Traversal | NAT 통과 지원 | NAT トラバーサル |
| BGP ASN | BGP 자율 시스템 번호 | BGP AS 番号 |
