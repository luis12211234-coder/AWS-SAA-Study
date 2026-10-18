# 📘 25-08. NAT Gateway: High Availability & Console Lab

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `NAT Gateway` · `Elastic IP` · `Public NAT` · `Private NAT` · `Zonal` · `Regional` · `Cross AZ Charges`

## 🇰🇷 1. NAT Gateway의 역할
**NAT Gateway**는 Private Subnet의 EC2 등이 IPv4 인터넷 통신을 시작할 때 사용하는 AWS 관리형 NAT다. 일반적인 Zonal Public NAT Gateway는 Public Subnet에 배포되고 EIP를 이용하며, Private Route Table에서 기본 경로를 해당 NAT Gateway로 보낸다.

```text
Private EC2 → Private Route (0.0.0.0/0 → NAT GW)
           → NAT Gateway(Public Subnet, EIP)
           → Public Route (0.0.0.0/0 → IGW)
           → 인터넷
```

## 🇰🇷 2. NAT Instance와 비교
| 구분 | NAT Instance | NAT Gateway |
|---|---|---|
| 관리 | EC2 직접 운영 | AWS 관리형 |
| Source/Dest Check | OFF 필요 | 직접 조작 없음 |
| Security Group | NAT EC2에 연결 | NAT Gateway에는 직접 연결 불가 |
| SSH Bastion 용도 | 구성 가능 | 사용 불가 |
| 비용 | EC2/EIP/전송 등 | 시간당 + 처리 데이터 + 기타 전송 |
| 확장·HA | 직접 구축 | 관리형 제공 |

EC2와 NAT GW 모두 연결된 SG/NACL/경로가 영향을 준다.

## 🇰🇷 3. AZ별 NAT와 고가용성
기존 강의의 **Zonal NAT Gateway**는 한 AZ에 배치된다. 2개 AZ의 Private Subnet이 각각 **같은 AZ의 NAT GW**를 사용하게 구성하면 AZ 장애와 교차 AZ 전송 비용을 줄일 수 있다.

**현재 변경 사항:** AWS는 AZ별 배치·공용 서브넷 구성이 필요 없는 **Regional NAT Gateway**도 지원한다. 워크로드가 위치한 AZ로 자동 확장하는 유형으로, 강의의 '반드시 AZ마다 NAT를 만든다'는 설명은 Zonal NAT 방식에 해당한다. [공식 문서](https://docs.aws.amazon.com/vpc/latest/userguide/nat-gateways-regional.html).

## 🇰🇷 4. NAT 유형과 실습 포인트
Public NAT Gateway는 IGW를 통한 IPv4 인터넷 송신에 쓰고, **Private NAT Gateway**는 별도 사설 네트워크 연결에서 주소 변환에 사용할 수 있다(인터넷 접속용은 아님).

강의에서는 NAT Gateway 생성 → `Available` 상태 확인 → PrivateRouteTable 경로 추가 → Private EC2에서 `curl`, 패키지 설치 명령으로 확인했다. `ping` 실패만으로 전체 인터넷 불통을 단정할 수 없다(ICMP 규칙). NAT Gateway는 시간당 및 GB 처리 비용이 발생하므로 실습 종료 후 삭제한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Private IPv4 Egress → NAT Gateway, Public Route → IGW.
- NAT Gateway에 직접 SG를 붙이지 않는다.
- Zonal NAT: 같은 AZ별 구성, Regional NAT: 자동 다중 AZ.
- S3/DynamoDB만 필요하면 무료 Gateway Endpoint를 먼저 검토.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 관리형 IPv4 NAT가 필요하다면?

<details>
<summary>정답 및 해설 보기</summary>

**NAT Gateway.**

</details>

### Q2. Zonal Public NAT GW가 위치하는 곳은?

<details>
<summary>정답 및 해설 보기</summary>

**Public Subnet**, 일반적으로 EIP와 IGW 경로 활용.

</details>

### Q3. NAT Gateway에 Security Group을 직접 붙이는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 인스턴스 SG·NACL·라우팅을 설정한다.

</details>

### Q4. 두 AZ의 NAT 고가용성에서 과거 강의의 일반적 방식은?

<details>
<summary>정답 및 해설 보기</summary>

**AZ당 Zonal NAT Gateway 하나씩** 및 동-AZ 경로.

</details>

### Q5. Regional NAT Gateway는 AZ마다 Public Subnet이 필요한가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** AWS가 워크로드 AZ에 따라 자동 확장하는 별도 유형이다.

</details>

## 🇯🇵 日本語 Summary

NAT Gateway は AWS 管理型の NAT。従来の Zonal Public NAT は Public Subnet に置き、Private Subnet からデフォルトルートを向ける。現在は複数 AZ に自動展開する Regional NAT Gateway も提供される。NAT Gateway 自体には Security Group を付けない。

## 🇺🇸 English Summary

A NAT Gateway provides managed IPv4 egress for private instances. Classic zonal public NAT gateways run in public subnets and are deployed per AZ for resilience. AWS now also offers regional NAT gateways that expand across AZs automatically. NAT gateways charge for uptime and processed data.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| NAT Gateway | 관리형 NAT 게이트웨이 | NAT ゲートウェイ |
| Elastic IP | 탄력적 공인 IP | Elastic IP |
| Zonal NAT Gateway | AZ 단위 NAT | ゾーン NAT ゲートウェイ |
| Regional NAT Gateway | 리전 NAT | リージョン NAT ゲートウェイ |
