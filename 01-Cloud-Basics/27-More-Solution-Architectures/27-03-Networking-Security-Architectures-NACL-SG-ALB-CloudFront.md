# 📘 27-03. Networking and Security Architectures

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `SG와 NACL의 경계`, `Client → EC2`, `Client → ALB → EC2`, `Client → CloudFront → ALB → EC2`

## 🇰🇷 1. SG와 NACL의 경계

Security Group은 ENI에 연결되는 상태 저장 허용 규칙이며 명시적 DENY가 없다. NACL은 서브넷에 연결되는 상태 비저장 ALLOW/DENY 규칙으로 인바운드와 아웃바운드 반환 트래픽을 함께 고려한다. 서브넷 CIDR은 소속 네트워크 범위, NACL 규칙의 Source/Destination CIDR은 패킷의 출발지/목적지 매칭이다.

## 🇰🇷 2. Client → EC2

공개 EC2에 클라이언트가 직접 연결하면 EC2 서브넷 NACL에서 원래 클라이언트 IP를 DENY할 수 있다. SG는 특정 IP만 ALLOW할 수 있으나 명시적으로 거부할 수 없다. 호스트 방화벽도 가능하지만 서버 리소스를 사용한다.

## 🇰🇷 3. Client → ALB → EC2

ALB는 클라이언트 연결을 받아 백엔드 EC2로 새 연결을 만든다. EC2가 보는 네트워크 출발지는 ALB 쪽 주소이며 EC2 SG의 Source로 ALB SG를 참조해 허용한다. HTTP X-Forwarded-For 헤더로 원래 클라이언트 IP를 전달할 수 있다. ALB 앞단에서 WAF로 IP·HTTP 패턴을 차단할 수 있다.

## 🇰🇷 4. Client → CloudFront → ALB → EC2

ALB가 받는 네트워크 패킷 출발지는 CloudFront의 Origin-facing IP이며, EC2 쪽은 ALB가 출발지다. 따라서 ALB 서브넷 NACL에서 원래 클라이언트 IP를 DENY해도 기대한 차단이 안 된다. CloudFront에 AWS WAF를 연결하여 원래 뷰어 IP 기반 차단·Rate-based·Geo Match를 적용한다. CloudFront Geo Restriction도 국가 차단에 사용된다.

## 🇰🇷 5. NLB의 출발지 IP

NLB는 대상 유형 및 설정에 따라 클라이언트 IP를 보존할 수 있다. NLB Security Group은 클라이언트의 연결을 제어할 수 있으며, 대상 EC2 SG에 NLB SG를 Source로 참조할 수 있다. NACL은 해당 서브넷의 실제 패킷 IP와 반환 경로를 기준으로 평가한다. NLB에는 AWS WAF를 직접 연결하지 않는다.

## 🇰🇷 6. 보안 설계 선택

직접 IP 차단이 필요하면 실제 클라이언트 패킷이 도착하는 경계에 NACL을 적용하거나 웹 트래픽은 WAF를 사용한다. SG는 허용만, NACL은 허용/거부, WAF는 L7 웹 요청 필터다. X-Forwarded-For는 HTTP 헤더이지 IP 패킷 Source 주소가 아니며 신뢰 경계를 고려해야 한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **SG와 NACL의 경계:** Security Group은 ENI에 연결되는 상태 저장 허용 규칙이며 명시적 DENY가 없다.
- **Client → EC2:** 공개 EC2에 클라이언트가 직접 연결하면 EC2 서브넷 NACL에서 원래 클라이언트 IP를 DENY할 수 있다.
- **Client → ALB → EC2:** ALB는 클라이언트 연결을 받아 백엔드 EC2로 새 연결을 만든다.
- **Client → CloudFront → ALB → EC2:** ALB가 받는 네트워크 패킷 출발지는 CloudFront의 Origin-facing IP이며, EC2 쪽은 ALB가 출발지다.
- **NLB의 출발지 IP:** NLB는 대상 유형 및 설정에 따라 클라이언트 IP를 보존할 수 있다.
- **보안 설계 선택:** 직접 IP 차단이 필요하면 실제 클라이언트 패킷이 도착하는 경계에 NACL을 적용하거나 웹 트래픽은 WAF를 사용한다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. SG와 NACL 중 명시적 DENY가 가능한 것은?

<details>
<summary>정답 및 해설 보기</summary>

NACL이다. SG는 허용 규칙만 제공한다.

</details>

### Q2. ALB 뒤 EC2 SG에서 권장하는 Source는?

<details>
<summary>정답 및 해설 보기</summary>

ALB의 Security Group을 참조한다.

</details>

### Q3. CloudFront → ALB 구성에서 ALB 서브넷 NACL이 원래 사용자 IP를 못 보는 이유는?

<details>
<summary>정답 및 해설 보기</summary>

ALB에 도착하는 패킷의 네트워크 출발지가 CloudFront이기 때문이다.

</details>

### Q4. 원래 사용자 IP를 HTTP 계층에서 전달하는 대표 헤더는?

<details>
<summary>정답 및 해설 보기</summary>

X-Forwarded-For.

</details>

### Q5. NLB가 클라이언트 IP를 항상 보존하는가?

<details>
<summary>정답 및 해설 보기</summary>

아니다. 대상 유형과 설정에 따라 달라진다.

</details>

## 🇯🇵 日本語 Summary

Networking and Security Architectures：AWS SAA-C03 では、要件に応じてサービスを組み合わせ、可用性・性能・運用自動化を設計する。主要な選択基準と障害時の動作を理解する。

## 🇺🇸 English Summary

Networking and Security Architectures: Understand the architecture, trade-offs, failure handling, and SAA-C03 service selection criteria. Combine managed AWS services based on performance, availability, and operational requirements.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| SG와 NACL의 경계 | SG와 NACL의 경계 | SG와 NACL의 경계 |
| Client → EC2 | Client → EC2 | Client → EC2 |
| Client → ALB → EC2 | Client → ALB → EC2 | Client → ALB → EC2 |
| Client → CloudFront → ALB → EC2 | Client → CloudFront → ALB → EC2 | Client → CloudFront → ALB → EC2 |
| NLB의 출발지 IP | NLB의 출발지 IP | NLB의 출발지 IP |
| 보안 설계 선택 | 보안 설계 선택 | 보안 설계 선택 |
