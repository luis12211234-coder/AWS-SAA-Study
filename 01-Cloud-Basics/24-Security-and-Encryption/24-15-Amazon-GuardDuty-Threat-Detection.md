# 📘 24-15. Amazon GuardDuty: Intelligent Threat Detection

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `GuardDuty` · `Threat Detection` · `Anomaly Detection` · `CloudTrail` · `VPC Flow Logs` · `DNS Logs` · `Finding` · `EventBridge` · `Cryptocurrency`

## 🇰🇷 1. GuardDuty가 찾는 것은 '수상한 활동'이다
**Amazon GuardDuty**는 계정과 AWS 리소스에서 **실제 벌어지는 이상 행동/위협 신호**를 찾아내는 관리형 위협 탐지 서비스다. 머신러닝·이상 탐지·위협 인텔리전스(악성 IP/도메인 정보)를 활용한다. 실행 중인 EC2에 별도 백신 프로그램을 일괄 설치해야만 기본 탐지를 시작하는 것은 아니다.

예: 평소와 다른 AWS API 호출 패턴, 악성 주소와의 통신, DNS를 악용한 데이터 유출, 탈취된 EC2의 **암호화폐 채굴(Cryptocurrency Mining)** 관련 활동 탐지.

## 🇰🇷 2. 기본 데이터 소스
| 원본 | GuardDuty가 살피는 대표 징후 |
|---|---|
| **CloudTrail Management Events** | EC2/서브넷 등 리소스 생성·수정 API의 수상한 사용 |
| **VPC Flow Logs 기반 활동** | 비정상적 외부 IP와의 네트워크 통신 패턴 |
| **Route 53 Resolver DNS Logs** | 악성 도메인 질의, DNS를 통한 데이터 유출 징후 |

기본 GuardDuty 데이터 분석은 **GuardDuty가 관리하는 독립 데이터 스트림**을 사용할 수 있으므로 사용자가 반드시 직접 VPC Flow Logs를 생성·보관해야 GuardDuty를 활용하는 것은 아니다. 다만 별도 계정 로깅/보존은 자체 감사 요구사항에 따라 설정할 수 있다.

## 🇰🇷 3. 선택적으로 추가할 보호 기능
- **S3 Protection:** CloudTrail S3 Data Events (`GetObject`, `DeleteObject` 등)를 분석해 수상한 객체 접근 탐지.
- **EKS Protection:** Kubernetes/EKS Audit Logs 등에서 의심스러운 제어 평면 활동 분석.
- **RDS Protection:** RDS/Aurora DB Login Activity의 이상 징후 파악.
- **Lambda Protection:** Lambda 함수의 네트워크 활동에서 위협 탐지.
- **Malware Protection:** EBS 볼륨 등의 악성코드 검사 기능.
- **Runtime Monitoring:** EC2 및 컨테이너 런타임 동작 분석.

보호 기능에 따라 별도 활성화와 비용이 필요할 수 있다. 추가 기능 목록은 AWS 업데이트로 변할 수 있다.

## 🇰🇷 4. Findings와 EventBridge 자동화
```text
CloudTrail / Network / DNS / Optional Sources
                   ↓
             Amazon GuardDuty
                   ↓
            Threat Finding 생성
                   ↓
              EventBridge
               ├─ SNS → 운영팀 경보
               └─ Lambda → 사전 정의 대응 작업
```

**Finding**은 의심스러운 활동에 대한 구조화된 탐지 결과다. GuardDuty가 발견했다고 공격이 반드시 자동 차단되지는 않는다. 대응은 EventBridge 규칙과 SNS/Lambda/SSM 등의 자동화 또는 운영자의 별도 작업으로 구성한다.

## 🇰🇷 5. Inspector와 다른 점
GuardDuty는 **누군가 이미 수상하게 행동하는지** 분석한다. **Inspector**는 서버나 배포 이미지에 **악용 가능한 알려진 취약점이 존재하는지** 평가한다. 전자는 위협 활동, 후자는 취약점 관리다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- GuardDuty = 계정/리소스에서 의심스러운 실제 활동 탐지.
- 기본 소스: CloudTrail 관리 이벤트, VPC 흐름 관련 데이터, DNS 로그.
- 옵션: S3/EKS/RDS/Lambda/Malware/Runtime Protection.
- 암호화폐 무단 채굴 탐지 키워드는 GuardDuty.
- GuardDuty Findings → EventBridge → SNS/Lambda 대응. 감지 자체가 자동 차단은 아님.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. EC2가 악성 IP와 통신하고 암호화폐 채굴에 악용되는지 확인하려 한다. 가장 적절한 서비스는?

<details>
<summary>정답 및 해설 보기</summary>

**Amazon GuardDuty.** 네트워크/행동 패턴과 위협 정보를 활용해 수상한 활동을 탐지한다.

</details>

### Q2. CloudTrail의 수상한 EC2 생성 API 호출 패턴을 탐지하려면 어떤 서비스가 적합한가?

<details>
<summary>정답 및 해설 보기</summary>

**GuardDuty.** CloudTrail 관리 이벤트 등을 기반으로 위협을 탐지한다.

</details>

### Q3. S3 객체의 비정상적인 GetObject/DeleteObject 활동을 위협으로 탐지하려면 GuardDuty의 어떤 기능인가?

<details>
<summary>정답 및 해설 보기</summary>

**S3 Protection.** 객체 수준 데이터 이벤트 분석을 통한 S3 위협 탐지다.

</details>

### Q4. GuardDuty Finding이 생성되었다. 운영팀에 SNS로 알리려면 중간 연결 서비스는?

<details>
<summary>정답 및 해설 보기</summary>

**EventBridge.** Finding 이벤트를 규칙으로 SNS에 전달한다.

</details>

### Q5. GuardDuty를 켜면 자동으로 악성 IP와의 모든 통신이 차단되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 핵심은 위협 탐지와 Finding 생성이며 차단 대응은 별도 구성한다.

</details>

## 🇯🇵 日本語 Summary

Amazon GuardDuty は CloudTrail 管理イベント、VPC Flow 関連データ、DNS クエリなどを分析し、機械学習と脅威インテリジェンスで不審な挙動を検知する。S3/EKS/RDS/Lambda/Malware/Runtime などの追加保護がある。Finding を EventBridge で SNS/Lambda に接続できる。暗号資産マイニングの検知も試験ポイント。

## 🇺🇸 English Summary

GuardDuty detects suspicious behavior and potential threats using CloudTrail, VPC network activity, DNS data, and optional protection features for S3, EKS, RDS, Lambda, malware, and runtime. Findings can trigger EventBridge notifications or automation. It is threat detection rather than automatic blanket blocking.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Threat Detection | 위협 탐지 | 脅威検出 |
| Anomaly Detection | 이상 탐지 | 異常検知 |
| Threat Intelligence | 위협 인텔리전스 | 脅威インテリジェンス |
| Finding | 탐지 결과 | 検出結果 |
| VPC Flow Logs | VPC 흐름 로그 | VPC フローログ |
| DNS Exfiltration | DNS 기반 데이터 유출 | DNS 経由のデータ流出 |
| Cryptocurrency Mining | 암호화폐 채굴 | 暗号資産マイニング |
