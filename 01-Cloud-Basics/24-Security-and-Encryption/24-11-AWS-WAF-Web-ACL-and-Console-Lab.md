# 📘 24-11. AWS WAF: Web ACL, Rules & Console Lab

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `AWS WAF` · `Layer 7` · `Web ACL` · `Rule Group` · `Rate-Based Rule` · `IP Set` · `WCU` · `Bot Control` · `Managed Rules`

## 🇰🇷 1. WAF가 해결하는 문제
웹 애플리케이션은 정상적인 HTTP/HTTPS 요청도 받고 공격자의 악성 요청도 받는다. **AWS WAF**는 OSI **L7(Application Layer)**의 HTTP 요청을 검사해 SQL Injection, XSS, 악성 IP, 비정상적으로 많은 요청 등을 차단하거나 허용한다. L4의 TCP/UDP 연결 자체를 검사하는 NLB의 기능과는 다르다.

**연결 가능한 대표 대상:** ALB, CloudFront, API Gateway **REST API** Stage, AppSync GraphQL API, Cognito User Pool 등. **NLB나 일반 EC2에 WAF Web ACL을 직접 연결하지 않는다.** 지원 대상은 AWS 기능 확대에 따라 추가될 수 있다.

## 🇰🇷 2. Web ACL을 만드는 이유와 실제 순서
보호할 쇼핑몰 ALB가 있다고 가정하자. 웹 공격을 막으려면 '어떤 요청을 막을지'를 정해야 한다. **Web ACL은 그 규칙을 모아 AWS 리소스에 적용하는 보안 정책**이다.

1. **Scope 설정:** ALB/API Gateway는 Regional + 대상 리전; CloudFront는 Global Scope(관리 API 위치는 us-east-1).
2. **Rule 추가:** AWS Managed Rule Group 또는 사용자 정의 규칙 추가. 예: 악성 IP/SQLi/XSS 차단.
3. **Priority 설정:** 어떤 규칙을 먼저 검사할지 지정. Block/Allow처럼 평가 종료 동작은 후속 규칙 평가에 영향을 준다.
4. **Default Action:** 어떤 종료 규칙에도 걸리지 않은 요청을 Allow 또는 Block.
5. **Associate Resource:** 완성된 Web ACL을 실제 ALB/API Gateway/CloudFront 등에 연결.

```text
사용자 요청 → [ALB에 연결된 WAF Web ACL]
                   ├─ Rule 1: 악성 IP? → Block
                   ├─ Rule 2: SQLi? → Block
                   └─ 일치 없음 → Default Allow
                                     ↓
                              ALB → EC2
```

**Web ACL을 생성만 하고 리소스에 연결하지 않으면** 해당 리소스에는 보호가 적용되지 않는다.

## 🇰🇷 3. Rule Group과 자주 쓰는 조건
- **IP Set:** IP 또는 CIDR 집합. 집합 하나의 최대 주소 항목은 일반적으로 **10,000**개.
- **Geo Match:** 요청 출발 국가 기준 차단/허용.
- **Header, URI, Body, Size 조건:** 특정 경로나 헤더 패턴, 요청 본문, 검사 가능한 크기 조건.
- **SQLi / XSS:** 흔한 웹 취약점 악용 패턴 탐지.
- **Rate-Based Rule:** 특정 평가 기간 동안 요청이 많은 IP 등 집계 키에 대응. 단순한 **초당 정확한 11개** 제한과 다르다.
- **Managed Rule Groups:** AWS/보안 파트너가 만든 재사용 규칙. **Bot Control**, **Amazon IP Reputation List**, **Anonymous IP List**(VPN·Proxy 등 식별), **Known Bad Inputs** 등이 예시.

**Rule Group**은 여러 Rule의 재사용 묶음이고, **Web ACL**은 이를 포함해 실제 리소스에 연결하는 정책이다. IP 평판 규칙은 알려진 위험 IP 기반 탐지이지 '평판 좋은 IP만 무조건 허용'하는 단순 목록이 아니다.

## 🇰🇷 4. WCU와 모니터링
WCU(Web ACL Capacity Unit)는 규칙 처리에 필요한 상대적 **용량 비용**이다. 웹 ACL의 기본 요금 포함 범위는 **1,500 WCU**, 일반적인 최대 허용치는 **5,000 WCU**이며 1,500 초과 시 추가 요금이 발생할 수 있다. Web ACL에서 CloudWatch 지표 및 로깅을 구성해 규칙별 차단/허용 현황을 확인할 수 있다. [AWS 문서](https://docs.aws.amazon.com/waf/latest/developerguide/aws-waf-capacity-units.html).

## 🇰🇷 5. 고정 IP + WAF 설계
ALB는 고정 퍼블릭 IP를 직접 할당하지 않지만 WAF를 연결할 수 있다. NLB는 고정 IP를 제공할 수 있어도 WAF를 직접 연결하지 못한다. 두 조건이 동시에 필요하면 다음을 고려한다.

```text
사용자 → Global Accelerator (고정 IP) → ALB + WAF → EC2
```

**Global Accelerator는 고정 IP·글로벌 진입점, WAF는 ALB의 웹 공격 방어**를 담당한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- WAF는 L7, NLB는 L4이므로 WAF를 NLB에 직접 연결하지 못한다.
- Web ACL = 리소스에 적용할 Rule/Rule Group 모음, Rule Group은 재사용 묶음.
- CloudFront Web ACL은 Global 범위, 일반 ALB/API Gateway는 Regional 범위.
- Rate-Based Rule은 설정된 평가 기간의 요청량을 분석한다.
- 고정 IP + L7 WAF = Global Accelerator + ALB + WAF 아키텍처.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 쇼핑몰 ALB에 SQL Injection 차단을 적용하려 한다. WAF에서 리소스에 연결할 구성요소는?

<details>
<summary>정답 및 해설 보기</summary>

**Web ACL.** 해당 규칙을 담은 Web ACL을 ALB에 연결한다.

</details>

### Q2. NLB에 웹 요청용 WAF Web ACL을 직접 붙일 수 있는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** L7 WAF는 ALB 등 지원 리소스에 연결한다.

</details>

### Q3. HTTP 요청이 어떤 차단 규칙과도 일치하지 않는다. Default Action이 Allow라면 결과는?

<details>
<summary>정답 및 해설 보기</summary>

**허용.** 종료 규칙에 해당하지 않는 요청은 기본 동작을 따른다.

</details>

### Q4. AWS가 만든 악성 IP/익명 IP 탐지 규칙을 직접 작성 없이 사용하려면?

<details>
<summary>정답 및 해설 보기</summary>

**AWS Managed Rule Group.** 미리 제공되는 규칙 모음을 Web ACL에 추가한다.

</details>

### Q5. 회사 웹사이트에서 고정 IP와 WAF를 동시에 요구한다. 어떤 조합이 대표적인가?

<details>
<summary>정답 및 해설 보기</summary>

**Global Accelerator + ALB + WAF.** 진입점 고정 IP와 L7 검사 기능을 분리해 충족한다.

</details>

## 🇯🇵 日本語 Summary

AWS WAF は L7 の HTTP リクエストを検査し、SQLi・XSS・悪性 IP・レート超過を緩和する。Web ACL に Rule/Rule Group を追加し、優先順位・デフォルト動作を決め、ALB などに関連付ける。NLB には直接接続できない。固定 IP と WAF が必要なら Global Accelerator + ALB + WAF が有力。

## 🇺🇸 English Summary

AWS WAF filters Layer 7 HTTP traffic using Web ACLs, rules, managed rule groups, IP sets, geo matching, and rate-based rules. Associate a Web ACL with supported resources such as ALB or CloudFront, not NLB. WCU measures rule capacity. Global Accelerator + ALB + WAF combines static IPs with L7 protection.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Web Application Firewall | 웹 애플리케이션 방화벽 | ウェブアプリケーションファイアウォール |
| Web ACL | 웹 접근 제어 목록 | ウェブ ACL |
| Managed Rule Group | 관리형 규칙 그룹 | マネージドルールグループ |
| IP Set | IP 주소 집합 | IP セット |
| Rate-Based Rule | 요청 속도 기반 규칙 | レートベースルール |
| Web ACL Capacity Unit | 웹 ACL 용량 단위 | ウェブ ACL キャパシティユニット |
| Geo Match | 국가 기반 일치 | ジオマッチ |
| Bot Control | 봇 제어 | ボット制御 |
