# 📘 24-18. Security Service Comparison & SAA-C03 Scenarios

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `KMS` · `Secrets Manager` · `Parameter Store` · `ACM` · `WAF` · `Shield` · `Firewall Manager` · `GuardDuty` · `Inspector` · `Macie` · `SAA-C03`

## 🇰🇷 1. 각 서비스를 해결할 문제로 구분하기
| 문제 상황 | 우선 떠올릴 AWS 서비스 |
|---|---|
| 데이터 암호화 키와 접근 권한 관리 | **AWS KMS** |
| 서버에 보내기 전 앱이 직접 암호화 + 다중 리전 호환 키 | **Client-Side Encryption + KMS MRK** |
| 앱 환경 설정값/일반 비밀번호 보관 | **SSM Parameter Store** |
| 실제 DB 비밀번호의 정기적 자동 교체 | **Secrets Manager** |
| HTTPS TLS 인증서 발급·갱신 | **ACM** |
| SQLi/XSS/악성 HTTP 요청 차단 | **AWS WAF** |
| DDoS 트래픽 완화 | **AWS Shield** |
| Organizations 다중 계정의 공통 보안 정책 | **Firewall Manager** |
| 악성 IP 접속/수상한 API/암호화폐 채굴 위협 탐지 | **GuardDuty** |
| EC2/ECR/Lambda 패키지 CVE 취약점 평가 | **Inspector** |
| S3 객체의 민감정보/PII 탐색 | **Macie** |
| 여러 서비스의 보안 Finding 통합 | **Security Hub** |
| Finding 기반 이벤트 라우팅/자동화 | **EventBridge** |

## 🇰🇷 2. 시험에서 헷갈리기 쉬운 대비
### 2.1 KMS vs Secrets Manager vs Parameter Store
KMS는 **암호화 키**를 관리한다. Parameter Store는 설정값과 SecureString을 **보관**, Secrets Manager는 Secret 보관과 **Rotation**을 지원한다. DB 패스워드를 바꿀 때는 저장소 값뿐 아니라 **실제 DB 자격 증명**도 바뀌어야 한다.

### 2.2 WAF vs Shield vs Firewall Manager
WAF는 **웹 요청을 필터링**, Shield는 **DDoS 완화**, Firewall Manager는 이들 및 기타 보안 정책을 **조직 전체에 일관되게 적용**한다.

### 2.3 GuardDuty vs Inspector vs Macie
GuardDuty는 **이상 행동**, Inspector는 **CVE 기반 취약점**, Macie는 **S3의 PII**. Security Hub는 결과 통합, EventBridge는 이벤트 연계다.

## 🇰🇷 3. 권한을 두 겹으로 생각해야 하는 아키텍처
- **S3 SSE-KMS 복제:** S3 복제 권한 + 소스 KMS `Decrypt` + 대상 `Encrypt` + 키 정책.
- **계정 간 암호화된 AMI:** AMI Launch Permission + CMK Key Policy + 대상 IAM 권한.
- **Lambda에서 SecureString 조회:** `ssm:GetParameters` + 필요한 `kms:Decrypt`.
- **Secrets Manager의 CMK Secret 조회:** `secretsmanager:GetSecretValue` + 필요한 KMS 권한.

서로 다른 서비스의 권한은 자동으로 합쳐지지 않는다. '해당 리소스를 읽을 수 있다'는 사실과 '암호화 키로 복호화할 수 있다'는 사실은 다르다.

## 🇰🇷 4. 리전과 자동화 문제
| 요구 | 핵심 |
|---|---|
| CloudFront·Edge-Optimized API Gateway 인증서 | ACM **us-east-1** |
| Regional API Gateway 인증서 | API와 **동일 리전** |
| 다중 리전 클라이언트 측 데이터 복호화 | **KMS MRK** |
| S3 SSE-KMS 객체 복제 | **옵트인 + 양쪽 키 권한**, MRK 특례 없음 |
| 글로벌 Secret 읽기와 DR | Secrets Manager **Multi-Region Replica + Promote** |
| 신규 계정/ALB에 자동 공통 WAF 정책 | Firewall Manager + Organizations |

## 🇰🇷 5. 오래된 강의·퀴즈 정보 정정
1. **KMS 자동 교체:** 지원되는 대칭 CMK는 **90~2,560일**. '3개월은 반드시 수동 교체'는 일반적으로 구버전 정보다.
2. **ACM:** 2026년 신규 퍼블릭 인증서는 **198일** 유효하며 45일 전 갱신 절차. 오래된 60일/13개월 설명과 구분.
3. **ACM을 EC2에서 못 쓴다:** Exportable Public Certificate는 EC2 등 외부 배포가 가능하다(유료, 인증서 배포 관리 필요).
4. **WAF 1,500 WCU:** 기본 요금 포함 기준이며 **최대치는 보통 5,000 WCU**다.
5. **Secrets Manager는 Rotation에 Lambda가 필수:** 일부 관리형 RDS/Aurora 자격 증명은 Lambda 없는 Managed Rotation을 지원.

자세한 근거는 각 주제 노트와 [AWS KMS 문서](https://docs.aws.amazon.com/kms/latest/developerguide/rotating-keys-enable.html), [ACM 문서](https://docs.aws.amazon.com/acm/latest/userguide/dochistory.html), [WAF 문서](https://docs.aws.amazon.com/waf/latest/developerguide/aws-waf-capacity-units.html)를 참조한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- 암호화 키 → KMS, 비밀번호 교체 → Secrets Manager, 설정값 → Parameter Store.
- HTTP 공격 → WAF, DDoS → Shield, 조직 정책 → Firewall Manager.
- 이상 행동 → GuardDuty, CVE → Inspector, S3 PII → Macie.
- CloudFront 인증서 us-east-1, S3 SSE-KMS 복제 키 권한 분리.
- Security Hub는 보안 결과 통합, EventBridge는 이벤트 연결 및 자동화.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 글로벌 Aurora DB에서 민감 속성을 앱이 저장 전에 암호화하고 지역별로 복호화해야 한다. 대표 해법은?

<details>
<summary>정답 및 해설 보기</summary>

**클라이언트 측 암호화 + KMS Multi-Region Key.** Aurora 복제와 키 호환성을 분리해 구성한다.

</details>

### Q2. 회사가 80개 AWS 계정의 신규 ALB에 공통 WAF 정책을 자동 적용하려 한다. 무엇을 선택할까?

<details>
<summary>정답 및 해설 보기</summary>

**AWS Firewall Manager + Organizations.** 계정별 반복 설정 없이 중앙 정책을 적용한다.

</details>

### Q3. EC2에서 알려진 악성 채굴 명령 서버와 통신하는 듯한 이상 징후를 발견하려 한다. 무엇을 쓸까?

<details>
<summary>정답 및 해설 보기</summary>

**Amazon GuardDuty.** 네트워크·행동 기반 위협 탐지다.

</details>

### Q4. Lambda 패키지에 설치된 취약한 라이브러리 CVE를 찾아야 한다. 무엇을 선택할까?

<details>
<summary>정답 및 해설 보기</summary>

**Amazon Inspector.** Lambda 의존성 취약점을 검사한다.

</details>

### Q5. S3 고객 CSV에서 이름·주민등록번호 등 PII가 포함된 파일을 찾아야 한다. 무엇을 선택할까?

<details>
<summary>정답 및 해설 보기</summary>

**Amazon Macie.** S3 저장 데이터의 민감정보를 탐지한다.

</details>

## 🇯🇵 日本語 Summary

セキュリティサービスは課題で区別する。暗号化キーは KMS、設定値は Parameter Store、認証情報の自動更新は Secrets Manager、TLS 証明書は ACM。HTTP 攻撃は WAF、DDoS は Shield、組織横断のポリシーは Firewall Manager。不審な挙動は GuardDuty、CVE 脆弱性は Inspector、S3 の PII は Macie。

## 🇺🇸 English Summary

Choose services by problem: KMS for keys, Parameter Store for configuration, Secrets Manager for credential rotation, ACM for TLS certificates, WAF for HTTP threats, Shield for DDoS, Firewall Manager for organization-wide policies, GuardDuty for threat behavior, Inspector for vulnerabilities, and Macie for S3 PII. Security Hub aggregates findings; EventBridge routes events.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Service Selection | 서비스 선택 기준 | サービス選択基準 |
| Multi-Region Key | 다중 리전 키 | マルチリージョンキー |
| Security Finding | 보안 탐지 결과 | セキュリティ検出結果 |
| Credential Rotation | 자격 증명 교체 | 認証情報ローテーション |
| Threat Detection | 위협 탐지 | 脅威検出 |
| Vulnerability Assessment | 취약점 평가 | 脆弱性評価 |
| Sensitive Data Discovery | 민감정보 탐색 | 機密データ検出 |
| Multi-Account Governance | 다중 계정 거버넌스 | マルチアカウントガバナンス |
