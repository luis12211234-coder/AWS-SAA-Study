# 📘 24-13. AWS Firewall Manager & Central Security Policies

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `AWS Firewall Manager` · `AWS Organizations` · `WAF Policy` · `Shield Advanced` · `Security Group Policy` · `Network Firewall` · `DNS Firewall`

## 🇰🇷 1. 여러 AWS 계정에서 같은 방화벽 규칙을 적용한다면
회사에 AWS 계정이 100개 있고 계정마다 ALB가 있다고 가정하자. 보안 담당자가 각 계정에 접속해 동일한 WAF 규칙을 반복 설정하는 것은 비효율적이며 누락도 발생할 수 있다.

**AWS Firewall Manager**는 **AWS Organizations와 연동해 여러 계정·리소스의 보안 정책을 중앙에서 관리**하는 서비스다. 적용 대상 계정·OU·리전·리소스를 설정하면 현재 및 **새로 생성되는 정책 대상 리소스**에 보호 정책을 지속적으로 적용하거나 준수 상태를 관리할 수 있다. 모든 리소스에 자동으로 무조건 적용되는 것이 아니라 정의된 정책 범위와 설정에 따른다.

## 🇰🇷 2. Firewall Manager가 중앙 관리하는 대상
| 정책 범주 | 실제로 보호/관리할 대상 |
|---|---|
| **AWS WAF Policy** | ALB, CloudFront, API Gateway 등 WAF Web ACL 중앙 관리 |
| **AWS Shield Advanced Policy** | Organizations 내 지원 리소스의 DDoS 보호 적용 관리 |
| **Security Group Policy** | EC2, ENI, ALB 등의 SG 규칙 감사·표준화 |
| **AWS Network Firewall Policy** | VPC 네트워크 트래픽 검사 방화벽 정책 중앙 관리 |
| **Route 53 Resolver DNS Firewall Policy** | VPC DNS 질의 차단/허용 규칙 중앙 관리 |

**AWS Network Firewall**은 VPC 경계와 네트워크 경로의 통신을 검사/필터링한다. **Route 53 Resolver DNS Firewall**은 도메인 이름에 대한 DNS 질의를 허용/차단한다. 이 둘은 서로 다른 검사 계층이다.

## 🇰🇷 3. 정책이 적용되는 범위
Firewall Manager 정책은 유형에 따라 **리전별 또는 CloudFront 등의 글로벌 범위**로 정의한다. Organizations에서 관리자와 대상 범위를 정해 중앙 관리한다. 예를 들어 아일랜드 리전의 모든 지정 계정 ALB에 공통 WAF 규칙을 적용하고, 향후 생성될 ALB까지 정책 대상으로 포함할 수 있다.

## 🇰🇷 4. WAF / Shield / Firewall Manager 역할 정리
| 서비스 | 담당 업무 |
|---|---|
| **WAF** | HTTP 요청을 L7 규칙으로 검사하고 차단 |
| **Shield** | DDoS 트래픽 탐지·완화(Advanced는 추가 대응) |
| **Firewall Manager** | WAF, Shield Advanced, SG, Network Firewall, DNS Firewall 등의 정책 **조직 단위 중앙 관리** |

Firewall Manager가 요청을 직접 검사하는 WAF 엔진을 대신하는 것이 아니다. **WAF·Shield가 실행하는 보호 기능을 다중 계정에 일관되게 배포/관리**하는 역할이다.

## 🇰🇷 5. 콘솔 실습에서 확인한 것
관리 화면에는 WAF 정책, Shield Advanced 정책, 보안 그룹, Network Firewall 등의 정책 유형이 있다. 강사는 비용 때문에 실제 정책 생성은 생략했으며 **정책 유형과 대상 리전을 선택하면 조직 계정에 정책을 적용하도록 구성할 수 있다**는 것을 보여주었다. 강의에 제시된 기본 Firewall Manager 가격은 정책/리전 기준 요금 예시로, 실제 활성화 전 최신 가격을 확인해야 한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- AWS Organizations 여러 계정의 공통 보안 정책 자동 배포·중앙 관리 = Firewall Manager.
- 기존 리소스뿐 아니라 정책 범위 안의 신규 리소스에도 적용 가능.
- Network Firewall은 VPC 통신 검사, Resolver DNS Firewall은 DNS 질의 제어.
- WAF = 요청 필터링, Shield = DDoS, Firewall Manager = 정책 관리.
- 리전별/글로벌 범위를 정책 유형과 리소스에 맞춰 선택한다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Organizations의 50개 계정에 공통 WAF Web ACL을 운영하고 신규 ALB에도 적용하려 한다. 적절한 서비스는?

<details>
<summary>정답 및 해설 보기</summary>

**AWS Firewall Manager.** 중앙 정책으로 다중 계정과 신규 리소스 보호를 관리한다.

</details>

### Q2. VPC 경계를 통과하는 네트워크 트래픽 자체를 검사하는 서비스는?

<details>
<summary>정답 및 해설 보기</summary>

**AWS Network Firewall.** DNS 도메인 요청만 검사하는 서비스가 아니다.

</details>

### Q3. 악성 도메인에 대한 VPC의 DNS 질의를 제한하려면 무엇을 사용할까?

<details>
<summary>정답 및 해설 보기</summary>

**Route 53 Resolver DNS Firewall.** DNS 요청 수준에서 도메인 기반 필터링을 한다.

</details>

### Q4. Firewall Manager를 쓰면 Shield 없이 DDoS가 자동으로 완화되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** Firewall Manager는 Shield Advanced 등의 보호 정책을 중앙 관리하며 자체가 모든 DDoS 트래픽을 차단하는 엔진은 아니다.

</details>

### Q5. Firewall Manager 정책을 만들었다면 조직의 모든 AWS 리소스에 항상 자동 적용되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 대상 OU/계정, 리전, 리소스 유형과 정책 설정의 범위에 따라 적용된다.

</details>

## 🇯🇵 日本語 Summary

AWS Firewall Manager は AWS Organizations と統合し、複数アカウントの WAF、Shield Advanced、Security Group、Network Firewall、DNS Firewall ポリシーを一元管理する。対象範囲の既存・新規リソースにも継続的に適用できる。WAF は検査、Shield は DDoS 防御、Firewall Manager は組織横断のポリシー管理。

## 🇺🇸 English Summary

Firewall Manager centrally governs security policies across AWS Organizations accounts, including WAF, Shield Advanced, security groups, Network Firewall, and Route 53 Resolver DNS Firewall. Policies can cover newly created matching resources. It orchestrates protections rather than replacing their inspection engines.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Centralized Security Management | 중앙 집중식 보안 관리 | 一元的セキュリティ管理 |
| Firewall Policy | 방화벽 정책 | ファイアウォールポリシー |
| AWS Organizations | AWS 조직 관리 서비스 | AWS 組織管理サービス |
| AWS Network Firewall | AWS 네트워크 방화벽 | AWS ネットワークファイアウォール |
| Resolver DNS Firewall | 리졸버 DNS 방화벽 | リゾルバー DNS ファイアウォール |
| Security Group Policy | 보안 그룹 정책 | セキュリティグループポリシー |
