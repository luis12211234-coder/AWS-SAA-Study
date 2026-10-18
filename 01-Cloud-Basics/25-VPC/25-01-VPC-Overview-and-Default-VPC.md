# 📘 25-01. VPC Overview & Default VPC

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `VPC` · `Region` · `Availability Zone` · `Default VPC` · `Main Route Table` · `DHCP Options`

## 🇰🇷 1. VPC의 의미와 네트워크 격리
**Amazon VPC(Virtual Private Cloud)**는 AWS 리전 안에서 논리적으로 격리된 가상 네트워크이다. VPC의 범위는 **리전**이고, 서브넷의 범위는 **AZ(Availability Zone)**다. 여러 AZ의 서브넷을 동일한 VPC에 둘 수 있다.

VPC를 만든다는 것은 AWS 계정에 독립적인 IP 주소 공간과 라우팅·보안 경계를 정의하는 일이다. 같은 계정의 서로 다른 VPC도 기본적으로 통신할 수 없다. 연결하려면 VPC Peering 또는 Transit Gateway 등과 양쪽 라우팅 설정이 필요하다.

## 🇰🇷 2. Default VPC가 미리 준비하는 구성
AWS 계정의 리전에는 **Default VPC**가 존재할 수 있다. 일반적인 IPv4 CIDR 예는 `172.31.0.0/16`이며 AZ별 기본 서브넷, 연결된 **Internet Gateway(IGW)**, 기본 라우팅과 자동 공인 IPv4 설정 등이 제공된다. 기본 서브넷은 일반적으로 `/20` 규모지만 실제 환경의 CIDR와 서브넷은 콘솔에서 확인한다.

Default VPC의 기본 라우트에는 `local`과 `0.0.0.0/0 → IGW`가 있으므로 인터넷 통신이 쉽게 구성된다. 단 **보안 그룹/NACL**이 필요한 트래픽을 허용해야 한다.

## 🇰🇷 3. 사용자 정의 VPC 생성 직후 상태
새 VPC를 생성하면 Main Route Table, 기본 Security Group, 기본 NACL, DHCP Option Set 연결 등의 기반 리소스가 제공된다. **서브넷과 IGW는 자동 생성되지 않는다.** 직접 생성·연결해야 한다.

**Main Route Table**은 별도 연결 테이블을 지정하지 않은 서브넷에 적용된다. 보통 초기에는 `10.0.0.0/16 → local`처럼 VPC 내부 통신 경로만 있다. 인터넷 연결은 IGW 연결, 공인 IP 및 보안 설정, 기본 경로가 함께 있어야 가능하다.

## 🇰🇷 4. VPC와 서브넷 경계 도식
```text
AWS Region
└── VPC 10.0.0.0/16
    ├── AZ-a : PublicSubnetA  10.0.0.0/24
    ├── AZ-a : PrivateSubnetA 10.0.16.0/20
    ├── AZ-b : PublicSubnetB  10.0.1.0/24
    └── AZ-b : PrivateSubnetB 10.0.32.0/20
```

**Public/Private는 서브넷 이름이나 IP가 아니라 연결된 Route Table의 인터넷 경로로 판단**한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- VPC는 Regional, Subnet은 AZ 단위.
- 새 Custom VPC에는 Subnet/IGW 자동 생성이 아니다.
- Main Route Table은 명시적 연결이 없는 서브넷에 적용.
- VPC 간 통신은 기본 차단, 별도 네트워크 연결과 라우팅 필요.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. VPC와 Subnet 중 AZ 단위 리소스는?

<details>
<summary>정답 및 해설 보기</summary>

**Subnet.** VPC는 리전 전체 범위다.

</details>

### Q2. VPC를 새로 만들면 Internet Gateway도 자동으로 연결되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 별도로 만들고 VPC에 붙여야 한다.

</details>

### Q3. Main Route Table은 언제 적용되는가?

<details>
<summary>정답 및 해설 보기</summary>

**서브넷의 라우트 테이블이 명시적으로 연결되지 않았을 때** 적용된다.

</details>

### Q4. 같은 계정의 두 VPC는 사설 IP만으로 자동 통신하는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** Peering/TGW 등과 경로 설정이 필요하다.

</details>

### Q5. Public Subnet 여부는 이름으로 결정되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 인터넷 게이트웨이로 향하는 라우팅 경로를 본다.

</details>

## 🇯🇵 日本語 Summary

VPC はリージョン単位の仮想ネットワーク、Subnet は AZ 単位。Default VPC には既定の Subnet・IGW・ルートが用意される場合がある。一方、新規 VPC では Subnet と IGW を自分で設定する。Main Route Table は明示的な関連付けのない Subnet に使われる。

## 🇺🇸 English Summary

An Amazon VPC spans a Region, while each subnet resides in one AZ. A default VPC may have preconfigured subnets and internet access. Creating a custom VPC does not automatically create subnets or an internet gateway. The main route table governs subnets without an explicit association.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Virtual Private Cloud | 가상 사설 클라우드 | 仮想プライベートクラウド |
| Default VPC | 기본 VPC | デフォルト VPC |
| Main Route Table | 기본 라우팅 테이블 | メインルートテーブル |
| Availability Zone | 가용 영역 | アベイラビリティゾーン |
