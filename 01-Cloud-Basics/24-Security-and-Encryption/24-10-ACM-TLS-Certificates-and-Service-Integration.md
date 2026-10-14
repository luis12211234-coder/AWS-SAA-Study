# 📘 24-10. AWS Certificate Manager: TLS, Validation & Integrations

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `AWS Certificate Manager` · `TLS Certificate` · `HTTPS` · `DNS Validation` · `CNAME` · `ALB` · `CloudFront` · `API Gateway` · `us-east-1`

## 🇰🇷 1. ACM은 무엇을 대신 관리하는가
**AWS Certificate Manager(ACM)**는 TLS/SSL 인증서의 발급·배포·갱신을 돕는다. 브라우저가 `https://` 웹사이트에 접속할 때 TLS 인증서는 서버의 도메인 신원을 검증하고 안전한 통신 연결을 성립시키는 데 쓰인다. **ACM이 HTTP 요청을 프록시하거나 실제 데이터를 저장하는 서비스는 아니다.** ALB, CloudFront 등이 ACM 인증서를 사용한다.

```text
사용자 Browser --HTTPS/TLS--> ALB[ACM 인증서 적용]
                                 │
                           EC2 Auto Scaling Group
```

ALB는 80번 HTTP 요청을 443번 HTTPS로 **Redirect**할 수 있다. 브라우저↔ALB가 HTTPS여도 ALB↔EC2 구간은 대상 그룹 설정에 따라 HTTP/HTTPS로 별도 설계해야 한다.

## 🇰🇷 2. 인증서를 발급하기 위한 도메인 검증
인증서에 포함할 도메인을 정한다. `app.example.com`처럼 특정 FQDN 또는 `*.example.com`처럼 하위 도메인을 포괄하는 **Wildcard**를 요청할 수 있다.

- **DNS Validation:** ACM이 제공하는 **CNAME**을 도메인 DNS에 등록해 소유·관리 권한 증명. Route 53과 편리하게 연동하고 자동 갱신에 유리하다.
- **Email Validation:** 도메인 관리 주소로 발송된 검증 이메일 승인.

DNS 검증 CNAME을 유지하고 인증서가 적절히 사용 중이면 ACM의 관리형 자동 갱신이 가능하다.

## 🇰🇷 3. 서비스별 ACM 인증서 리전
| 적용 서비스 | ACM 인증서 위치 |
|---|---|
| ALB/ELB, Regional API Gateway Custom Domain | **해당 서비스와 동일 리전** |
| CloudFront | **us-east-1** |
| API Gateway Edge-Optimized Custom Domain | **us-east-1** (관리형 CloudFront 사용) |

API Gateway 엔드포인트의 의미도 구분한다. **Edge-Optimized**는 CloudFront 기반 글로벌 수신, **Regional**은 리전 엔드포인트, **Private**은 VPC 인터페이스 엔드포인트 경유 접근 구조다. Custom Domain을 설정하고 Route 53 Alias/CNAME으로 도메인을 매핑할 수 있다.

## 🇰🇷 4. 인증서 갱신과 만료 알림
- **ACM 발급 관리형 인증서:** 검증·사용 조건을 충족하면 ACM이 갱신.
- **외부에서 Import한 인증서:** 발급기관에서 갱신 후 새 인증서를 **직접 재가져오기**가 필요하다.
- **EventBridge:** 갱신 실패·만료 임박 등 인증서 이벤트를 SNS/Lambda/SQS로 연동 가능.
- **AWS Config:** `acm-certificate-expiration-check` 규칙으로 만료 임박 상태 평가 가능.

**2026 최신 주의:** 신규 ACM 퍼블릭 인증서의 유효기간은 **198일**이고 DNS 검증 자동 갱신 검사는 보통 **만료 45일 전**부터 진행한다(기존 395일 인증서는 전환 일정 상이). [AWS 문서](https://docs.aws.amazon.com/acm/latest/userguide/dns-renewal-validation.html).

## 🇰🇷 5. EC2에 ACM 인증서를 직접 쓸 수 없는가?
예전 강의에서는 ACM 퍼블릭 인증서를 EC2에 설치할 수 없다고 했으나, **2025년 이후 Exportable Public Certificate**를 제공한다. **내보낼 수 있는 유료 인증서**라면 EC2·온프레미스에도 배포할 수 있다. 다만 갱신된 인증서의 **재배포는 직접 관리**한다. 일반적인 내보내기 불가 ACM 퍼블릭 인증서는 ALB/CloudFront 같은 지원 서비스에 사용하며 기본 무료 발급 대상이다. [AWS 문서](https://docs.aws.amazon.com/acm/latest/userguide/acm-exportable-certificates.html).

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- CloudFront와 Edge-Optimized API Gateway는 us-east-1 ACM 인증서를 사용한다.
- Regional API Gateway/ALB 인증서는 리소스와 같은 리전.
- DNS Validation CNAME + ACM 자동 갱신 흐름.
- 가져온 인증서는 ACM이 자동으로 외부 CA에서 재발급하지 않는다.
- EC2 직접 배포에 대한 오래된 강의 설명은 Exportable Public Certificate 출시로 달라졌다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. CloudFront 배포에 사용자 지정 HTTPS 도메인을 연결할 때 ACM 인증서를 생성할 리전은?

<details>
<summary>정답 및 해설 보기</summary>

**us-east-1.** CloudFront 사용자 지정 인증서의 ACM 리전 규칙이다.

</details>

### Q2. ap-southeast-2 Regional API Gateway에 Custom Domain을 연결한다. 적합한 ACM 리전은?

<details>
<summary>정답 및 해설 보기</summary>

**ap-southeast-2.** Regional 엔드포인트는 같은 리전 인증서를 사용한다.

</details>

### Q3. DNS 검증 방식 인증서 발급을 위해 도메인 DNS에 추가하는 대표 레코드는?

<details>
<summary>정답 및 해설 보기</summary>

**CNAME.** ACM이 요구하는 검증 레코드로 도메인 관리 권한을 증명한다.

</details>

### Q4. 외부 CA에서 발급받아 ACM에 Import한 인증서는 자동 갱신되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 외부에서 갱신한 인증서를 다시 가져와야 한다.

</details>

### Q5. 2026년 현재 ACM 발급 퍼블릭 인증서를 EC2에 배포하는 것은 항상 불가능한가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** Exportable Public Certificate를 선택해 내보내면 가능하며 추가 비용과 배포 관리가 필요하다.

</details>

## 🇯🇵 日本語 Summary

ACM は TLS/SSL 証明書の発行・検証・更新を支援する。DNS 検証では CNAME を利用し、自動更新と相性が良い。CloudFront と API Gateway Edge-Optimized には us-east-1 の証明書が必要。Regional API Gateway と ALB は同一リージョンの証明書を使う。Exportable Public Certificate は EC2 にも配置できる。

## 🇺🇸 English Summary

ACM provisions and manages TLS certificates. DNS validation uses CNAME records and enables convenient managed renewal. CloudFront and edge-optimized API Gateway require ACM certificates in us-east-1; regional API Gateway and ALB use certificates in their own Region. Exportable public certificates can be deployed to EC2 with extra management.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| TLS Certificate | TLS 인증서 | TLS 証明書 |
| DNS Validation | DNS 도메인 검증 | DNS 検証 |
| CNAME Record | CNAME 레코드 | CNAME レコード |
| Wildcard Certificate | 와일드카드 인증서 | ワイルドカード証明書 |
| Edge-Optimized API | 엣지 최적화 API | エッジ最適化 API |
| Certificate Renewal | 인증서 갱신 | 証明書更新 |
| Exportable Certificate | 내보내기 가능 인증서 | エクスポート可能な証明書 |
