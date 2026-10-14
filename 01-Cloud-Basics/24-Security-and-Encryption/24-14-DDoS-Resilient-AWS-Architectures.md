# 📘 24-14. DDoS-Resilient Architecture & Best Practices

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `DDoS Resilience` · `Edge Location` · `CloudFront` · `Global Accelerator` · `Route 53` · `ALB` · `Auto Scaling` · `API Gateway` · `Throttling` · `Attack Surface`

## 🇰🇷 1. 왜 하나의 서비스로 DDoS를 완전히 해결하지 못하는가
DDoS는 DNS, 네트워크 연결, HTTP 요청 등 **다양한 진입점**을 공격할 수 있다. 따라서 AWS 아키텍처는 **엣지에서 완화 → 웹 요청 필터링 → 남은 트래픽 분산 → 백엔드 직접 노출 최소화**를 조합한다. AWS의 BP1/BP2 등의 번호는 공식 모범 사례 문서의 **분류 번호**이지 AWS 서비스나 반드시 암기할 고정 숫자가 아니다.

## 🇰🇷 2. 엣지 계층 완화: 글로벌 진입점
- **CloudFront:** 글로벌 Edge Location에서 HTTP/HTTPS 콘텐츠 제공. 정적 콘텐츠를 캐싱해 원본(ALB/EC2)의 요청 처리량을 줄이며 Shield와 함께 네트워크 DDoS를 완화한다.
- **Global Accelerator:** 글로벌 엣지 네트워크와 **고정 Anycast IP**를 제공. CloudFront처럼 콘텐츠를 캐싱하지 않으며, 웹 캐시가 맞지 않는 TCP/UDP 서비스나 ALB 앞단 진입점에 적합하다.
- **Route 53:** 글로벌 DNS 인프라에서 이름 해석과 라우팅을 담당한다. **실제 모든 웹 요청을 Route 53이 프록시하는 것은 아니다.** DNS 자체에 대한 DDoS 복원력에 기여한다.

## 🇰🇷 3. 인프라 계층 방어: ELB + Auto Scaling
```text
사용자 → Route 53(DNS) → CloudFront → ALB → EC2 여러 대(ASG)
```

**ELB**는 여러 서버에 요청을 분산하고, **Auto Scaling Group**은 부하에 따라 EC2 인스턴스 수를 조정해 처리 용량을 늘린다. ELB는 트래픽을 **분배**하고 ASG는 서버 용량을 **확장**하는 상호 보완 관계다. 공격량에 맞춰 무작정 ASG를 확장하면 비용이 상승하므로 WAF/Shield로 악성 트래픽을 먼저 완화한다.

## 🇰🇷 4. 애플리케이션 계층 방어
- **WAF:** SQLi/XSS, IP Set, Geo Match, Managed Rule Groups, Rate-Based Rule 등으로 HTTP 요청 필터링.
- **CloudFront:** 원본 전에 캐시 응답을 제공해 서버 부하를 낮출 수 있다. Geo Restriction 설정 가능.
- **Shield Advanced:** 설정 시 WAF와 연계해 L7 DDoS 완화 규칙을 자동 적용할 수 있다.
- **API Gateway:** Throttling으로 **Rate Limit(지속 요청 속도)** 및 **Burst Limit(순간 몰림 허용치)**를 제어하고 초과 시 429 응답을 반환할 수 있다. WAF는 지원 엔드포인트에 연결해 HTTP 요청을 필터링한다.

**API Key는 호출자를 식별·계량하는 데 쓸 수 있지만 안전한 사용자 인증의 단독 대체제가 아니다.** 인증·인가는 IAM/Cognito/Authorizer 등과 별개로 설계한다.

## 🇰🇷 5. 공격 표면(Attack Surface)을 줄인다
공격자가 퍼블릭 EC2 IP를 알면 CloudFront나 ALB를 우회해 공격할 수 있으므로 **실제 서버를 Private Subnet에 두고 Security Group/NACL로 허용 경로를 제한**한다. API Gateway, ALB, CloudFront 같은 앞단을 배치하되 그것만으로 서버 접근이 자동으로 완벽히 차단되는 것은 아니다. **접근 제어가 필수**다.

Security Group은 ENI 단위의 상태 저장 접근 제어, NACL은 서브넷 단위의 상태 비저장 제어다. Elastic IP는 Shield Advanced 보호 대상에 포함할 수 있다.

## 🇰🇷 6. API Gateway Edge-Optimized와 Regional
- **Edge-Optimized:** AWS 관리 CloudFront 배포를 이용해 전 세계 클라이언트에 API 제공.
- **Regional:** 해당 리전으로 API 요청 전달. 원하는 경우 **자체 CloudFront 배포**로 캐싱·분배를 보다 세밀하게 제어 가능.
- **Private:** 인터페이스 VPC 엔드포인트와 API 리소스 정책 등으로 사설 접근을 구성.

결론은 단일 'DDoS 버튼'보다 **트래픽 위치·프로토콜·요청 양·백엔드 접근 경로**별로 대응을 계층화하는 것이다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Edge protection: CloudFront, Global Accelerator, Route 53의 역할을 구분한다.
- ELB는 분산, Auto Scaling은 용량 확장.
- WAF는 애플리케이션 HTTP 요청 필터링, Shield는 DDoS 완화.
- 백엔드 보호에는 Private Subnet과 SG/NACL로 우회 접근 차단이 필요.
- API Gateway Throttling은 Rate/Burst로 부하 보호하지만 인증 기능 자체를 대체하지 않는다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 상품 이미지 요청이 대량 발생한다. 원본 EC2 부담을 줄일 대표 캐싱 서비스는?

<details>
<summary>정답 및 해설 보기</summary>

**CloudFront.** 엣지 캐시에서 제공되는 요청은 원본을 거치지 않을 수 있다.

</details>

### Q2. ALB 앞에 글로벌 고정 IP가 필요하지만 콘텐츠 캐싱은 필요하지 않다. 어떤 서비스를 고려할까?

<details>
<summary>정답 및 해설 보기</summary>

**AWS Global Accelerator.** 고정 Anycast IP와 글로벌 트래픽 진입점을 제공한다.

</details>

### Q3. 트래픽 증가 상황에서 ELB와 ASG의 역할이 어떻게 다른가?

<details>
<summary>정답 및 해설 보기</summary>

**ELB는 서버 간 분산, ASG는 서버 수 조절.** 결합해 처리 능력을 높인다.

</details>

### Q4. EC2를 ALB 뒤에만 두면 원본 IP를 통한 직접 공격이 자동으로 불가능해지는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** Private Subnet/SG/NACL로 원본 경로에 대한 직접 접근을 제한해야 한다.

</details>

### Q5. API 요청 순간 폭증을 제한하면서 초과 시 429 응답을 제공하려면 API Gateway의 어떤 기능이 핵심인가?

<details>
<summary>정답 및 해설 보기</summary>

**Throttling.** Rate/Burst 제한으로 백엔드의 과도한 요청 유입을 조절한다.

</details>

## 🇯🇵 日本語 Summary

DDoS 耐性は Edge での緩和、HTTP リクエストのフィルタリング、ELB の負荷分散、Auto Scaling、バックエンドの非公開化を組み合わせる。CloudFront はキャッシュし、Global Accelerator は固定 Anycast IP を提供する。Route 53 は DNS を担当する。API Gateway の Throttling と WAF は API の負荷軽減に役立つ。

## 🇺🇸 English Summary

DDoS-resilient architectures combine edge mitigation (CloudFront, Global Accelerator, Route 53), application filtering (WAF), distribution (ELB), scaling (ASG), and reduced attack surface through private backends and network access controls. API Gateway throttling limits rate and burst traffic.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Defense in Depth | 다계층 방어 | 多層防御 |
| Edge Location | 엣지 로케이션 | エッジロケーション |
| Anycast IP | 애니캐스트 IP | エニーキャスト IP |
| Auto Scaling Group | 오토 스케일링 그룹 | オートスケーリンググループ |
| Throttling | 요청량 제한 | スロットリング |
| Burst Limit | 순간 요청 한도 | バースト制限 |
| Attack Surface | 공격 표면 | 攻撃対象領域 |
