# 📘 25-05. Internet Gateway & Public/Private Routing

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Internet Gateway` · `IGW` · `Route Table` · `Public IP` · `Private Subnet` · `Local Route`

## 🇰🇷 1. Internet Gateway의 역할
**IGW(Internet Gateway)**는 VPC와 인터넷 사이에 연결되는 AWS 관리형 게이트웨이다. IPv4/IPv6 인터넷 라우팅을 지원하며 가용성과 확장을 AWS가 관리한다. 일반적인 IPv4 퍼블릭 EC2가 IGW를 이용하려면 **EC2 공인 IP**, **Subnet Route Table의 `0.0.0.0/0 → IGW`**, **SG/NACL 허용**이 맞아야 한다. IGW는 VPC에 연결한다.

## 🇰🇷 2. 라우트 테이블을 보고 Public / Private 결정
```text
PublicRouteTable
10.0.0.0/16 → local
0.0.0.0/0   → igw-...

PrivateRouteTable
10.0.0.0/16 → local
(인터넷 기본 경로 없음)
```

PublicSubnetA/B를 PublicRouteTable에 **명시적으로 연결(Associate)**하고 PrivateSubnetA/B는 PrivateRouteTable에 연결한다.

**Public Subnet**은 IGW 경로가 있는 서브넷을 뜻한다. 하지만 공인 IP가 없는 EC2는 IGW만으로 IPv4 인터넷에 직접 연결할 수 없다.

## 🇰🇷 3. 같은 VPC 안의 Private EC2와 통신
Public EC2에서 Private EC2의 사설 IP로 SSH를 시도하면 **`local` 라우트**를 사용한다. 같은 VPC 내부 트래픽이기 때문에 IGW를 우회한다. 접속 가능 여부는 SG/NACL과 OS 서비스 설정에 달려 있다.

## 🇰🇷 4. 인터넷 접속 문제를 디버깅하는 순서
1. **ENI 주소:** IPv4 인터넷 연결이면 공인 IP가 있는지 확인.
2. **IGW:** VPC에 정상 연결되어 있는지 확인.
3. **Subnet Route Table:** 기본 경로가 IGW인지 확인.
4. **SG/NACL:** 필요한 방향·포트를 허용하는지 확인.
5. **OS 서비스:** SSH(22), HTTP(80) 등이 실제 실행 중인지 확인.

콘솔의 EC2 Instance Connect도 네트워크 경로와 접근 권한 조건을 충족해야 한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- IGW 자체만으로 인터넷 통신이 완성되지 않는다.
- Public/Private는 Subnet에 연결된 Route Table 기준.
- VPC 내부 통신에는 `local` 경로 사용.
- IPv4 인터넷 통신엔 일반적으로 인스턴스 공인 IPv4가 필요.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Custom VPC에 IGW를 붙였는데 인터넷이 안 된다. 가장 먼저 볼 경로는?

<details>
<summary>정답 및 해설 보기</summary>

**`0.0.0.0/0 → IGW`**가 해당 서브넷 Route Table에 있는지 확인.

</details>

### Q2. 공인 IP 없는 EC2가 Public Subnet에 있다면 IGW로 IPv4 인터넷 접속되는가?

<details>
<summary>정답 및 해설 보기</summary>

**직접 접속 불가.** 공인 IPv4도 필요하다.

</details>

### Q3. 서로 다른 두 Subnet이 같은 VPC 안에 있으면 기본 경로는?

<details>
<summary>정답 및 해설 보기</summary>

**`local` 경로.** SG/NACL이 허용하면 사설 IP 통신 가능.

</details>

### Q4. Public Subnet과 Private Subnet의 핵심 구분 근거는?

<details>
<summary>정답 및 해설 보기</summary>

**Route Table의 IGW 경로 여부.**

</details>

### Q5. 새 VPC에는 인터넷 게이트웨이가 자동으로 생성되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 따로 생성·연결한다.

</details>

## 🇯🇵 日本語 Summary

Internet Gateway は VPC とインターネットを結ぶ管理型ゲートウェイ。IPv4 のインターネット接続には Public IP、IGW ルート、適切なセキュリティ設定が必要。VPC 内部通信は `local` ルートを使用する。

## 🇺🇸 English Summary

An internet gateway connects a VPC to the internet. A public IPv4 instance needs an IPv4 public address, a default route to IGW, and permitting security rules. Route tables, not subnet names, determine whether a subnet is public. Internal traffic uses local routes.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Internet Gateway | 인터넷 게이트웨이 | インターネットゲートウェイ |
| Route Association | 라우팅 테이블 연결 | ルートテーブル関連付け |
| Local Route | 로컬 경로 | ローカルルート |
| Public Subnet | 퍼블릭 서브넷 | パブリックサブネット |
