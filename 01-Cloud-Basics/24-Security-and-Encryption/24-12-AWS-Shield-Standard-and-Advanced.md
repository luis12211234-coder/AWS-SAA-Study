# 📘 24-12. AWS Shield: DDoS Protection Standard & Advanced

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `AWS Shield` · `DDoS` · `SYN Flood` · `UDP Flood` · `Reflection Attack` · `Shield Standard` · `Shield Advanced` · `SRT` · `Cost Protection`

## 🇰🇷 1. DDoS는 왜 정상 사용자를 막는가
DDoS(Distributed Denial of Service)는 여러 출발지에서 대량의 트래픽/요청을 보내 **서비스 처리 능력을 고갈**시키는 공격이다. 목표는 데이터를 꼭 탈취하는 것이 아니라 **정상 사용자의 접속을 불가능하게 만드는 것**이다.

대표 공격의 기본 원리:
- **SYN Flood:** TCP 연결 절차의 SYN 요청 등을 대량으로 보내 연결 관련 자원을 소모.
- **UDP Flood:** 많은 UDP 패킷으로 네트워크·서버 처리량을 압박.
- **Reflection/Amplification:** 제삼자 서버를 악용해 피해자에게 응답 트래픽을 반사·증폭 전송.

## 🇰🇷 2. AWS Shield Standard
**모든 AWS 고객에게 기본 제공되는 무료 DDoS 보호**다. AWS 인프라에서 L3/L4의 일반적 SYN·UDP·반사 공격 등 네트워크 계층 DDoS 공격 완화에 도움을 준다. 사용자가 별도 월 구독을 시작할 필요 없다. AWS WAF의 웹 요청 검사나 GuardDuty의 위협 탐지와 목적이 다르다.

## 🇰🇷 3. AWS Shield Advanced
더 엄격한 DDoS 보호가 필요한 환경에서 선택하는 **유료 서비스**다. 강의 기준 기본 구독 가격은 **조직당 월 $3,000**으로 설명되며, 실제 적용 요금은 최신 조건을 확인한다.

대표적인 보호 리소스는 **EC2/EIP, ELB(ALB/CLB/NLB), CloudFront, Global Accelerator, Route 53 Hosted Zone** 등이다.

중요한 기능:
- **AWS Shield Response Team(SRT):** DDoS 공격 상황에 대한 전문 대응 지원.
- **Advanced Reporting/Visibility:** 추가 공격 가시성과 이벤트 분석.
- **DDoS Cost Protection:** 충족 조건에 따라 공격 때문에 증가한 일부 지원 리소스 사용 요금에 대한 크레딧 요청 가능. 무조건 모든 요금 면제가 아니다.
- **Automatic Application Layer DDoS Mitigation:** 적절한 설정과 연결된 WAF를 통해 **L7 악성 요청을 완화하는 규칙 자동 생성·평가·배포**를 지원.

## 🇰🇷 4. AWS WAF와 어떻게 다른가
**WAF**는 HTTP 내용(SQLi, XSS, IP/Geo/Rate 규칙) 중심, **Shield**는 대량 트래픽으로 서비스를 마비시키는 DDoS 중심이다. L7 HTTP Flood에서는 둘이 함께 동작할 수 있다. **Shield Advanced가 WAF의 웹 공격 방어 규칙을 전부 대체하는 것은 아니다.**

## 🇰🇷 5. 서비스 선택 기준
- 일반 AWS 워크로드의 기본 DDoS 보호 → Standard.
- DDoS 전문가 지원 + 강화된 보호 + 조건부 비용 보호 → Advanced.
- SQLi/XSS 및 맞춤 HTTP 필터링 → WAF.
- 여러 계정에 보호 정책을 통합 적용 → Firewall Manager.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- DDoS 목표는 서비스 가용성 저해다. SYN/UDP/Reflection 공격의 핵심 원리 구분.
- Shield Standard는 AWS 고객 기본 무료 보호.
- Shield Advanced는 EC2/EIP·ELB·CloudFront·Global Accelerator·Route 53 등을 보호할 수 있다.
- 전문 대응 팀, 조건부 비용 보호, L7 WAF 자동 완화가 Advanced 주요 차별점.
- Shield와 WAF는 대체재가 아니라 보호 계층이 다른 보완재다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 기본 AWS 계정에서 별도 구독 없이 L3/L4 DDoS 보호를 제공하는 것은?

<details>
<summary>정답 및 해설 보기</summary>

**AWS Shield Standard.** 모든 AWS 고객에게 기본 제공된다.

</details>

### Q2. 공격자가 TCP 연결 요청을 대량으로 발생시켜 서버 자원을 소모한다. 대표 공격 명칭은?

<details>
<summary>정답 및 해설 보기</summary>

**SYN Flood.** TCP 연결 과정의 요청을 남용하는 DDoS 패턴이다.

</details>

### Q3. 제삼자 서버의 응답을 피해자로 몰아 트래픽을 증폭시키는 공격 유형은?

<details>
<summary>정답 및 해설 보기</summary>

**Reflection/Amplification Attack.** 서버를 반사 경유지로 악용한다.

</details>

### Q4. DDoS 공격에서 전문가 대응 지원과 조건부 비용 보호가 필요하다. 무엇을 선택할까?

<details>
<summary>정답 및 해설 보기</summary>

**Shield Advanced.** SRT 지원 및 DDoS Cost Protection 기능을 제공한다.

</details>

### Q5. L7 DDoS 요청을 자동 WAF 규칙으로 완화하려 한다. 관련 핵심 기능은?

<details>
<summary>정답 및 해설 보기</summary>

**Shield Advanced의 Automatic Application Layer DDoS Mitigation.** 조건에 맞는 WAF 연동이 필요하다.

</details>

## 🇯🇵 日本語 Summary

AWS Shield Standard は全顧客に無料で提供される L3/L4 DDoS 防御。SYN Flood、UDP Flood、反射攻撃などを緩和する。Shield Advanced は有料で、専門対応チーム、追加分析、条件付きコスト保護、WAF を利用した L7 DDoS 自動緩和を提供する。WAF と Shield は補完関係。

## 🇺🇸 English Summary

Shield Standard provides baseline DDoS protection at no additional subscription cost. Shield Advanced adds specialized response support, enhanced protections, conditional DDoS cost protection, and automated Layer 7 DDoS mitigation through WAF. DDoS flooding differs from SQL injection and XSS.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Distributed Denial of Service | 분산 서비스 거부 공격 | 分散型サービス拒否攻撃 |
| SYN Flood | SYN 요청 범람 공격 | SYN フラッド |
| UDP Flood | UDP 패킷 범람 공격 | UDP フラッド |
| Reflection Attack | 반사 공격 | リフレクション攻撃 |
| Shield Response Team | 쉴드 대응 팀 | Shield 対応チーム |
| DDoS Cost Protection | DDoS 비용 보호 | DDoS コスト保護 |
