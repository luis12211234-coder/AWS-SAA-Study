# 📘 25-12. VPC Endpoints: Gateway vs Interface & PrivateLink

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `VPC Endpoint` · `Gateway Endpoint` · `Interface Endpoint` · `ENI` · `Prefix List` · `PrivateLink` · `Endpoint Policy`

## 🇰🇷 1. VPC Endpoint의 필요성
Private EC2가 S3나 SNS 같은 AWS 서비스에 접근할 때 반드시 NAT GW+IGW를 경유할 필요는 없다. **VPC Endpoint**는 지원 서비스까지 퍼블릭 인터넷 연결 없이 **AWS 네트워크 경로**로 접근하도록 한다. 공인 서비스 엔드포인트를 이용한다고 해서 트래픽이 반드시 AWS 네트워크 외부 물리망으로 나간다는 뜻은 아니다.

## 🇰🇷 2. Interface Endpoint: ENI와 PrivateLink
**Interface Endpoint**는 선택한 Subnet에 **사설 IP를 갖는 ENI**를 생성한다. EC2가 그 사설 IP로 요청하면 AWS PrivateLink로 SNS, CloudWatch, SSM 등 **지원되는** 서비스를 이용한다. 엔드포인트 ENI에 **Security Group**을 연결하고 일반적으로 HTTPS TCP 443을 허용한다. Private DNS 옵션을 활성화하면 지원 서비스의 평소 DNS 이름이 엔드포인트 사설 주소를 가리키도록 할 수 있다. **시간당·데이터 GB 등 요금**이 발생한다.

## 🇰🇷 3. Gateway Endpoint: S3와 DynamoDB
**Gateway Endpoint**는 S3/DynamoDB를 대상으로 **Route Table에 경로를 추가**한다. 엔드포인트 자체의 추가 이용 요금은 없다.

```text
PrivateRouteTable
10.0.0.0/16         → local
0.0.0.0/0           → NAT GW (일반 인터넷)
pl-xxxxxxxx (S3)   → vpce-xxxxxxxx (S3 Gateway Endpoint)
```

`pl-...`는 AWS가 관리하는 **Prefix List**(여러 S3 IP CIDR의 묶음), `vpce-...`는 Endpoint ID다. S3 IP로 향하는 트래픽은 구체적인 Prefix List 경로를 선택해 NAT GW 대신 엔드포인트로 이동한다. Gateway Endpoint는 별도 ENI/SG가 없으며 **AWS PrivateLink를 사용하지 않는다**.

## 🇰🇷 4. Endpoint Policy와 S3 선택 기준
Endpoint Policy는 해당 엔드포인트를 통한 API 동작을 제한한다. `Full access`도 모든 IAM/버킷 권한을 자동 부여하지 않는다. 같은 리전 VPC 내부에서 S3/DynamoDB만 접근한다면 **Gateway Endpoint가 대표적인 비용 절감 답**이다. 온프레미스/VPC Peering/Transit Gateway를 통한 사설 접근에는 Interface Endpoint를 검토한다. Gateway Endpoint를 다른 VPC에서 직접 사용할 수 없다.

## 🇰🇷 5. PrivateLink Endpoint Service
AWS 제공 서비스에 접속하는 사례 외에도 서비스 제공자의 **NLB 기반 Endpoint Service**를 고객의 **Interface Endpoint**로 연결할 수 있다. 이때 고객에게 제공자 VPC 전체의 라우팅 접근을 허용하지 않고 특정 서비스만 사설로 노출한다. 일반적인 VPC Peering(네트워크 간 연결)과 목적이 다르다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- S3/DynamoDB만 같은 VPC에서 → Gateway Endpoint(무료).
- Interface = ENI + SG + PrivateLink + 시간/GB 비용.
- Gateway = Route Table + Prefix List, ENI/SG 직접 연결 없음.
- Gateway Endpoint는 AWS PrivateLink와 다른 기술.
- Endpoint Policy Full access도 IAM/S3 Bucket Policy 제한을 우회하지 않음.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. S3에 같은 리전 Private EC2에서 최저 관리 비용으로 접근하려면?

<details>
<summary>정답 및 해설 보기</summary>

**S3 Gateway Endpoint.**

</details>

### Q2. Interface Endpoint가 생성하는 네트워크 리소스는?

<details>
<summary>정답 및 해설 보기</summary>

**사설 IP가 있는 ENI.**

</details>

### Q3. `pl-xxxxxxxx`은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

**Prefix List ID.** 여러 CIDR 주소의 집합이다.

</details>

### Q4. Gateway Endpoint는 Security Group을 직접 연결하는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** Route Table 경로를 사용한다.

</details>

### Q5. PrivateLink Endpoint Service가 VPC Peering과 다른 점은?

<details>
<summary>정답 및 해설 보기</summary>

**특정 서비스만 사설 접근**을 제공하며 VPC 전체 통신을 개방할 필요 없다.

</details>

## 🇯🇵 日本語 Summary

VPC Endpoint は NAT/IGW に依存せず AWS サービスへ接続する方法。Interface Endpoint は PrivateLink と ENI/SG を利用して課金される。Gateway Endpoint は S3/DynamoDB 専用で、Prefix List を Route Table に追加し利用料が無料。Endpoint Policy は IAM 権限を置き換えない。

## 🇺🇸 English Summary

A VPC endpoint provides private network access to supported AWS services. Interface endpoints use PrivateLink, private ENIs, security groups, and hourly/data charges. Gateway endpoints for S3/DynamoDB use route-table prefix-list targets at no endpoint charge. Endpoint policies do not replace IAM permissions.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Interface Endpoint | 인터페이스 엔드포인트 | インターフェイスエンドポイント |
| Gateway Endpoint | 게이트웨이 엔드포인트 | ゲートウェイエンドポイント |
| AWS PrivateLink | AWS 사설 서비스 연결 | AWS PrivateLink |
| Prefix List | CIDR 주소 목록 | プレフィックスリスト |
| Endpoint Policy | 엔드포인트 정책 | エンドポイントポリシー |
