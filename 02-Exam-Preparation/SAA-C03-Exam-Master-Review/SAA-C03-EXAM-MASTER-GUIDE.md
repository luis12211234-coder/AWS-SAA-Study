# 📘 AWS SAA-C03 시험 전용 마스터 정리본 v1.0

> [!NOTE]
> **학습용 원본 | 2026-10-21 기준 | 시험 영역별 재구성 | 추후 PDF 전환 예정**  
> 이 문서는 AWS 공식 SAA-C03 시험 가이드, 기존 `AWS-SAA-Study/01-Cloud-Basics/01~28` 노트, 강의 대본에서 확인된 시험 강조점, 최근 응시 후기를 대조한 **독창적인 시험 대비 자료**다.  
> 실제 시험에 출제된 비공개 문항이나 정답을 모은 '기출 복원'은 아니다. 문제 유출 자료를 사용하지 않는다.  
> **우선순위 태그:** `[공식]` AWS 시험 도메인에 명시됨 · `[강사]` 제공된 강의 대본/과거 대화에서 출제 가능성 강조 확인 · `[후기]` 공개된 일부 합격자의 체감 · `[보충]` 선택 기준 이해를 위해 추가.

## 🇰🇷 0. 이 책을 보는 방법: 56%에서 점수를 올리는 공부

AWS SAA는 '서비스를 기억하는가'보다 **정해진 제약을 만족하는 가장 적합한 설계를 선택하는가**를 평가한다. 56%라는 첫 모의고사 점수는 출발점이다. 목표는 오답을 읽고 정답을 암기하는 것이 아니라, 그 문제에 들어 있던 **요구사항을 읽어내는 규칙**을 머릿속에 만드는 것이다.

### 공식 시험 도메인

| 도메인 | 채점 문항 비중 | 이 책의 초점 |
|---|---:|---|
| 1. Secure Architectures | **30%** | IAM, SCP, 네트워크 접근, KMS, S3, 인증, 보안 탐지 |
| 2. Resilient Architectures | **26%** | ALB/ASG, Multi-AZ, SQS/SNS, 장애 복구, Route 53, DR |
| 3. High-Performing Architectures | **24%** | EC2/EBS/EFS, RDS/DynamoDB, 캐시, 네트워크, 데이터 분석 |
| 4. Cost-Optimized Architectures | **20%** | On-Demand/Spot/Savings Plans, S3 계층, 네트워크 전송 비용 |

**정답률 72% = 공식 합격점수 720점이라는 뜻이 아니다.** AWS는 100~1000 환산 점수를 사용한다. 총 65문항 가운데 15개는 비채점이며, 어느 문항인지 공개되지 않는다. 영역별 최소 점수를 반드시 통과해야 하는 것도 아니다.

### 6단계 풀이 루틴

1. 마지막 문장을 먼저 읽고 **'무엇을 최적화하라'**는지 표시한다.
2. 요구사항을 범주화한다: `보안 / HA / 성능 / 비용 / 운영 최소화 / 데이터 위치 / 지연 / 지속성`.
3. 반드시 필요한 조건을 체크한다: `Multi-AZ`, `Cross-Region`, `No internet`, `No downtime`, `Millisecond`, `Event ordering`, `SQL`, `KMS`, `No management`.
4. 보기별로 **조건 하나라도 어기면 지운다**. "기술적으로 가능"과 "가장 적합"은 다르다.
5. 남은 두 답에서 운영 부담·비용·서비스 특화 기능을 비교한다.
6. 낯선 문제는 임시 답을 고르고 Flag. 마지막까지 모든 문항에 답한다.

### 지문의 언어를 서비스 선택으로 바꾸기

| 지문 표현 | 번역해야 할 설계 요구 | 후보 / 주의 |
|---|---|---|
| **Least operational overhead** | 서버 관리 최소화 | 관리형 서비스, Fargate, Lambda, S3, RDS/Aurora |
| **Highly available / AZ failure** | 단일 AZ 장애 견딤 | 다중 AZ ALB+ASG, RDS Multi-AZ |
| **Disaster / Region outage** | 리전 장애·지역 재난 복구 | Cross-Region 복제, Route 53, DR 전략 |
| **Read-heavy SQL** | SQL 읽기 부하 분산 | RDS/Aurora Read Replica, 필요 시 캐시 |
| **Automatic DB failover** | DB 장애 시 자동 전환 | RDS Multi-AZ 또는 Aurora HA, Read Replica 단독은 부적합 |
| **Private access to S3 at lowest network cost** | 인터넷·NAT 없이 S3 접근 | S3 Gateway Endpoint (지원 조건) |
| **Private connection to AWS service** | 퍼블릭 경로 없이 서비스 호출 | Interface Endpoint/PrivateLink |
| **Millions of unpredictable key-value requests** | 대규모 비관계형 처리 | DynamoDB On-Demand, 경우에 따라 Global Tables |
| **Ordering / deduplication** | 메시지 순서 보장 및 중복 제거 | SQS FIFO, FIFO 지원 조합; 범위와 제한 확인 |
| **Fan-out** | 한 이벤트를 여러 소비자로 전송 | SNS → 여러 SQS Queue 등 |
| **Near-zero RTO/RPO** | 가장 빠른 DR·엄격한 복구 | Active-Active / Multi-Site, 높은 비용 |
| **Least expensive interruptible compute** | 중단 허용·유휴 EC2 용량 활용 | Spot |
| **Steady 1–3 year usage** | 지속적 컴퓨팅 비용 할인 | Savings Plans / RI (유연성 차이) |

> [!IMPORTANT]
> **학습 우선순위 규칙:** 공식 도메인에 직접 등장하면서 유사 서비스와 혼동될 가능성이 큰 주제가 1순위. 강사가 강조했지만 세부 점수 영향이 작은 서비스 소개는 후순위. 후기에서 반복된 개념은 보조 검증 신호일 뿐 확정 출제 목록이 아니다.

## 🇰🇷 1. Secure Architectures (30%)

### 1.1 IAM 정책 평가의 뼈대 [공식][강사]

**인증(Authentication)** = 누구인가? / **인가(Authorization)** = 무엇을 해도 되는가?

- Identity-based Policy는 IAM 사용자·그룹·역할에 부착한다. Resource-based Policy는 S3 Bucket, SQS Queue 등 대상 리소스에 부착한다.
- 기본 상태는 **Implicit Deny**. 허용이 있어야 접근 가능하며 **Explicit Deny는 Allow보다 우선**한다.
- Permission Boundary는 IAM Identity의 **최대 권한 상한**을 제한한다. 권한을 자체적으로 부여하지 않는다.
- Organizations의 SCP는 조직 내 **멤버 계정의 최대 권한 상한**이다. 리소스 생성 권한을 직접 주지 않으며, 관리 계정에는 적용되지 않는다.
- SCP는 일반적으로 멤버 계정의 루트 사용자에게도 제한 효과가 있지만 서비스 연결 역할(Service-linked Role) 같은 적용 예외를 구분한다.
- Session Policy도 STS 임시 세션 권한을 제한한다. 조건·리소스 정책·세션 정책이 함께 작용할 수 있다.
- IAM Role은 장기 암호·Access Key를 저장하는 대신 **STS 임시 자격 증명**으로 서비스나 다른 계정에 권한을 부여한다.
- Cross-account AssumeRole: **대상 Role의 Trust Policy**와 **호출자 측 권한**을 함께 확인한다. S3 Bucket Policy만으로 해결되는 경우와 Role을 Assume해야 하는 경우를 구분한다.

```text
접근 시도
  ↓
명시적 Deny? ── Yes → 거부
  ↓ No
SCP / Boundary / Session Policy 등 상한 충족?
  ↓ Yes
적합한 Identity / Resource 기반 Allow?
  ↓ Yes
허용 (예외와 서비스별 정책 평가도 고려)
```

이 도식은 **사고를 돕는 단순화**다. AWS 정책 평가에는 같은 계정/교차 계정, 세션 Principal, 리소스 정책 등 예외가 있으므로 무조건 모든 Allow의 교집합이라고 외우지 않는다.

**함정**: `iam:PassRole`은 **기존 Role을 AWS 서비스에 전달**할 권한이다. `iam:CreateRole`은 새로운 Role 생성. CloudFormation Service Role을 EC2 Instance Profile과 혼동하면 안 된다.

### 1.2 S3 ARN·Policy Condition [강사]

| 대상 | ARN 예시 | 관련 Action |
|---|---|---|
| 버킷 자체 | `arn:aws:s3:::my-bucket` | `s3:ListBucket` |
| 버킷의 객체들 | `arn:aws:s3:::my-bucket/*` | `s3:GetObject`, `s3:PutObject` |

- `aws:SourceIp`: 특정 요청 출발 IP 조건(서비스를 거치는 경로에 따라 주의).
- `aws:RequestedRegion`: 요청한 AWS Region 관련 조건. 글로벌 서비스·리전별 엔드포인트 예외 확인.
- `aws:PrincipalOrgID`: AWS Organizations 소속 Principal만 접근하도록 제한.
- `aws:MultiFactorAuthPresent`, Principal/Resource Tag: MFA 또는 ABAC(속성 기반 접근 제어) 요구.
- `aws:SourceVpce` 등은 지정한 VPC Endpoint를 통한 액세스 제한에 사용 가능.

**지문에서 '한 조직 내 계정만 버킷에 접근'** → `aws:PrincipalOrgID` 포함 S3 Bucket Policy 후보. **'클라우드 리소스에서 역할 부여'** → IAM Role 후보.

### 1.3 Organizations / Identity Center / Cognito

| 상황 | 우선 고려 | 이유 |
|---|---|---|
| 수십 계정 통합 관리, OU, 정책 제한 | AWS Organizations + SCP | 계정 단위 권한 상한·거버넌스 |
| 계정 생성·랜딩 존·가드레일 | AWS Control Tower | 멀티계정 환경의 설정·가드레일 자동화 |
| 직원이 여러 계정에 SSO로 로그인 | IAM Identity Center | 인력 접근 중앙화와 Permission Set |
| 웹앱 최종 사용자 로그인 | Amazon Cognito **User Pool** | 회원가입·로그인·JWT 사용자 인증 |
| 앱이 사용자의 AWS 리소스 접근 권한 획득 | Cognito **Identity Pool** | AWS 임시 자격 증명 및 IAM 역할 연계 |
| 사내 AD와 AWS 연동 | AWS Directory Service / 신뢰 관계 | 요구 형태에 맞는 디렉터리 통합 |

**Cognito User Pool + Identity Pool**은 연결 가능하지만 역할이 다르다. User Pool이 로그인 토큰을, Identity Pool이 AWS 자격 증명 연결을 맡을 수 있다. **CloudFront OAC + Bucket Policy**는 원본 S3 접근 제한이고 **Cognito Identity Pool + IAM**은 사용자/앱의 AWS 리소스 권한 부여다. 목적 자체가 다르다.

### 1.4 네트워크 보안: SG / NACL / WAF / Shield / Network Firewall [공식]

| 비교 | Security Group | Network ACL |
|---|---|---|
| 범위 | ENI/인스턴스 네트워크 인터페이스 | Subnet 경계 |
| 상태 | **Stateful** | **Stateless** |
| 규칙 | 허용(Allow)만 | 허용 및 거부(Allow/Deny) |
| 반환 트래픽 | 허용된 연결에 대해 자동 허용 | 별도 Inbound/Outbound 규칙 필요 |
| 평가 | 모든 규칙 종합 | 낮은 번호 우선 첫 일치 |
| 특정 악성 IP 명시적 차단 | SG보다 NACL 또는 WAF | NACL 사용 가능 |

- **WAF**: HTTP/HTTPS 레이어 7 웹 요청의 SQL Injection, XSS, IP/Geo/Rate 기반 필터링. CloudFront, ALB, API Gateway 등 지원 리소스에 연결.
- **Shield Standard**: 기본 DDoS 방어. **Shield Advanced**: 고급 DDoS 보호 및 추가 지원/비용.
- **Firewall Manager**: 여러 계정/리소스의 WAF, Shield 등 중앙 정책 관리.
- **Network Firewall**: VPC 트래픽의 상태 기반·심층 패킷 검사 및 방화벽 정책.
- **GuardDuty**: AWS 계정·워크로드의 위협 징후 탐지(로그·이벤트 분석).
- **Inspector**: EC2, 컨테이너 이미지, Lambda 등 지원 대상의 취약점 검사.
- **Macie**: S3 민감 데이터(예: 개인 식별 정보) 발견·분류.
- **Detective**: 보안 탐지 조사·분석 지원. **Security Hub**: 보안 결과 집계·상태 파악.

### 1.5 Encryption / KMS / ACM / Secrets [공식][강사]

- **전송 중** 보호는 TLS/HTTPS, **저장 시**는 S3 SSE, EBS/RDS 암호화 등.
- **KMS**: 키 관리와 Envelope Encryption을 이해한다. 데이터 키를 생성/암호화해 큰 데이터의 암호화에 활용. `kms:Decrypt` 권한만 부여해도 **KMS Key Policy가 허용 구조를 지원해야** 한다.
- **SSE-S3**: S3가 관리하는 키를 통한 서버 측 암호화. **SSE-KMS**: KMS 키·감사·권한 제어가 추가. **SSE-C**: 고객이 각 요청에 키를 제공.
- **Client-Side Encryption**: AWS 전송 전에 애플리케이션에서 암호화. AWS가 평문을 보지 않아야 하는 요구에 대응.
- **S3 Replication + SSE-KMS**: 복제 규칙, 목적지 권한, 관련 KMS Decrypt/Encrypt 권한 및 명시적 설정 확인.
- **ACM**: 지원되는 AWS 서비스에 TLS 인증서 프로비저닝·갱신. CloudFront와 연결할 인증서는 **us-east-1** 요구를 기억.
- **Secrets Manager**: RDS 비밀번호/API Key 등 **자동 Rotation** 필요 시 강력한 후보.
- **SSM Parameter Store**: 설정값·SecretString 유사 보안 파라미터(`SecureString`) 계층 저장. 모든 사용 사례에서 자동 Rotation이 기본 제공되는 것은 아니다.
- **CloudHSM**: 전용 HSM 기반 키 관리와 더 큰 제어 책임이 요구되는 상황.

### 1.6 S3 보안·접근·거버넌스 [공식]

- **Block Public Access**는 의도치 않은 퍼블릭 버킷/객체 정책 노출 방지.
- **Bucket Policy**는 리소스 기반 제어, IAM Policy는 주체 권한 제어. S3 Access Point는 데이터 소비자/애플리케이션별 접근 엔드포인트·권한 관리를 돕는다.
- **CloudFront OAC + S3 Bucket Policy**는 S3 원본을 CloudFront 배포만 읽게 하는 대표 패턴. 하지만 **사용자가 CloudFront URL에 접근할 수 있는가**까지 자동 제한하지 않는다. 사용자 제한은 Signed URL/Cookie, 앱 인증 등을 추가 고려.
- **S3 Presigned URL**: 제한된 시간 동안 특정 객체 작업 허용; 생성 주체의 권한 범위 안에서 유효.
- **Versioning**: 덮어쓰기·삭제에 대비해 객체 버전 보존. 단순 DELETE는 Delete Marker를 추가할 수 있다.
- **Object Lock**: WORM(변경 불가 보관), Governance/Compliance 모드 및 Retention/Legal Hold 차이. Compliance 모드는 유효 보존 기간 중 우회 삭제 불가.
- **Access Logs / CloudTrail Data Events**: 요청 감사 범위가 다름. CloudTrail 기본 관리 이벤트 기록만으로 모든 S3 Object API가 자동 로깅된다고 가정하지 않는다.

### 1.7 Security 빠른 적용 예시

**요구:** 여러 계정의 개발자에게 S3 자료 읽기만 허용, 조직 밖 계정은 금지.  
**선택:** 적절한 IAM/역할 및 S3 Bucket Policy, `aws:PrincipalOrgID` 등 조직 조건. **SCP 하나만으로 외부 계정의 리소스 정책 접근을 차단한다고 생각하면 곤란**하다.

**요구:** 개인 정보가 포함된 S3 파일을 찾고 싶음.  
**선택:** Macie. CloudTrail은 활동 기록, GuardDuty는 위협 탐지, Inspector는 취약점 탐지.

**요구:** 직원은 SSO, 일반 고객은 앱 로그인.  
**선택:** 직원 IAM Identity Center / 고객 Cognito User Pool.

## 🇰🇷 2. Resilient Architectures (26%)

### 2.1 HA의 핵심: 단일 장애점 제거 [공식]

**High Availability(고가용성)**은 일부 구성 요소 장애에도 서비스 제공을 유지하는 능력이다. **Fault Tolerance**는 구성 요소가 실패해도 서비스 품질·작동을 가능한 지속하도록 한 단계 높은 내결함성을 뜻한다. **Durability**는 저장된 데이터가 손실되지 않을 성질로, 가용성과 혼동하지 않는다.

```text
Route 53 / Client
       ↓
ALB (2개 이상 AZ에 배치)
       ↓
ASG: AZ-A EC2 × N  ↔  AZ-B EC2 × N
       ↓
RDS Multi-AZ 또는 Aurora HA
```

- AZ 장애 대응은 **여러 AZ**에 분산 배치해야 한다. EC2 한 대 + EBS Snapshot만으로 자동 무중단 서비스는 불가능하다.
- ALB는 대상(Target) 건강 상태를 확인하고 정상 대상에 요청을 전달한다.
- ASG는 요구한 용량을 유지하며 비정상 인스턴스를 교체하고, 동적 수요에 따라 증감시킬 수 있다.
- **Auto Scaling만으로 DB까지 자동 고가용성 되는 것은 아니다.** DB 계층의 Multi-AZ/복제 구조를 별도 설계.
- EC2 Auto Recovery/ASG Replacement는 단일 인스턴스 복구·교체 용도. 여러 AZ의 동시 Active 트래픽 분산과 목적이 다르다.

### 2.2 ELB와 ASG 선택 [공식]

| 조건 | 후보 | 시험 포인트 |
|---|---|---|
| HTTP(S) Host/Path 라우팅, 마이크로서비스 | **ALB** | L7, Path/Host-based, 대상 그룹, WebSocket 지원 |
| TCP/UDP, 정적 IP, 초저지연 네트워크 | **NLB** | L4, 고성능, AZ당 고정 IP/탄력 IP 구성 가능 |
| 가상 방화벽/보안 어플라이언스 삽입 | **GWLB** | GENEVE 기반 네트워크 어플라이언스 트래픽 전달 |
| 인스턴스 수 자동 조절 | **ASG** | Min/Desired/Max, Target Tracking, Scheduled/Step Scaling |
| 인스턴스 신규 배포 시 요청 처리 준비 | **Lifecycle Hook/Health Check** | 애플리케이션 시작 완료 전 트래픽 수락 주의 |

- **ALB Cross-Zone** 기능과 가격 모델, **NLB Cross-Zone** 기본값·비용은 환경에 따라 구분. 무조건 모든 ELB가 동일하다고 외우지 않는다.
- **Target Tracking**은 목표 CPU 또는 `ALBRequestCountPerTarget` 같은 지표를 일정 수준으로 유지하도록 조절.
- **Health Check Grace Period**는 시작 직후 앱 준비 시간을 확보해 너무 빨리 실패로 판단하는 문제를 줄임.
- **Deregistration Delay(Draining)**은 기존 연결/요청 처리할 시간. **Sticky Session**은 같은 클라이언트를 같은 대상에 고정할 수 있지만, 가능하면 Stateless 설계가 스케일링에 유리.

### 2.3 RDS Multi-AZ vs Read Replica vs Aurora [공식][강사]

| 요구 | Multi-AZ | Read Replica | Aurora Cluster |
|---|---|---|---|
| 주목적 | **자동 장애 조치/가용성** | **읽기 확장**, 필요 시 승격 | 다중 AZ 분산 스토리지 기반 HA·복제 |
| 읽기 부하 분산 | 전통적인 RDS Multi-AZ DB Instance Standby는 읽기 불가 | **읽기 가능** | Reader Endpoint를 통해 읽기 가능 |
| 복제 형태 | 설정/엔진에 따른 동기식 관리형 Standby | 일반적으로 비동기(지연 가능) | 클러스터 스토리지와 Replicas의 분리된 아키텍처 |
| 장애 시 | 자동 Failover | 단독 RR은 자동 HA Standby를 뜻하지 않음 | Writer 장애 시 Reader 승격 및 엔드포인트 전환 |
| Cross-Region | 리전 내부 AZ 중심 | Cross-Region RR 가능한 엔진/구성 | Aurora Global Database 등 |

> [!WARNING]
> `RDS Multi-AZ DB Instance`와 **`RDS Multi-AZ DB Cluster`**는 다르다. 후자는 읽기 가능한 인스턴스를 포함하는 별도 배포 형태일 수 있다. 문제 지문에서 어느 옵션인지 확인한다.

- **RDS Proxy**: Lambda/다수 클라이언트의 연결 폭증 때 DB 연결 풀링·연결 관리로 부담 완화.
- **Aurora Global Database**: 전 세계 읽기 및 리전 간 DR, 단일 기본 쓰기 리전과 보조 리전(지원 모드에 따른 차이).
- **Aurora Serverless**: 수요 변동에 맞춰 용량 조절; 일반적인 RDS Read Replica와 개념 차이.
- **ElastiCache**는 자주 조회하는 데이터를 메모리에 저장해 DB 읽기 부하 감소; 근본 SQL 저장/트랜잭션 저장 대체가 아님.

**암기 반사:** `자동 DB Failover` → Multi-AZ, `읽기 요청 너무 많음` → Read Replica/캐시, `Lambda가 DB 연결 과다` → RDS Proxy.

### 2.4 SQS / SNS / EventBridge / Kinesis / Step Functions [공식]

| 서비스 | 메시지 모델 | 보관·전달 특징 | 전형적인 정답 힌트 |
|---|---|---|---|
| **SQS** | 생산자 → Queue → 소비자 Pull | 버퍼링, 비동기, 중복 가능, 소비자가 삭제 | **Decouple**, 백프레셔, 재시도/DLQ |
| **SNS** | Publish → 여러 Subscriber Push | Pub/Sub, Fan-out | 한 이벤트를 이메일·SQS·Lambda 등에 동시 전달 |
| **EventBridge** | 이벤트 버스·규칙·대상 | 이벤트 패턴/스케줄 기반 라우팅 | SaaS 이벤트 통합, 복잡한 이벤트 필터 |
| **Kinesis Data Streams** | 스트리밍 기록·여러 소비자 | 순서/파티션, 리플레이, 샤드/용량 설계 | **실시간 이벤트 스트림**, 로그·클릭스트림 |
| **Step Functions** | 상태 머신 | 순서, 분기, 재시도, 여러 서비스 조율 | 다단계 서버리스 워크플로 |

#### SQS 함정 7개

1. Standard Queue는 At-least-once와 Best-effort ordering: **중복 메시지 처리 가능한 Idempotency**가 중요.
2. FIFO Queue는 MessageGroupId별 **순서**, 중복 제거 기능을 제공. 모든 작업이 무제한 전역 순서를 자동 보장하는 건 아니다.
3. **Visibility Timeout**은 소비 중 메시지를 잠시 안 보이게 하는 시간. 짧으면 처리 중 재수신, 길면 장애 시 재시도 지연.
4. **Retention Period**는 큐 안 메시지 보존 기간. Visibility Timeout과 다르다.
5. **Long Polling**은 빈 요청과 API 호출 낭비를 줄인다. Delay Queue는 메시지를 일정 시간 보이지 않게 지연.
6. **DLQ**는 반복 실패한 메시지를 별도 보관하여 조사·재처리. `maxReceiveCount`와 Redrive Policy 확인.
7. **SQS → Lambda**는 Lambda의 Event Source Mapping이 Queue를 폴링하는 구조. 실패 시 Visibility Timeout과 부분 배치 실패 처리(partial batch response)를 구분.

#### SNS+SQS Fan-out

```text
          SNS Topic
      ┌─────┼─────┐
      ▼     ▼     ▼
   SQS A  SQS B  SQS C
      ↓     ↓     ↓
   결제    배송   분석 서비스
```

**SNS만** 사용하면 독립 소비자가 항상 요청을 받을 때까지 Queue에 메시지를 쌓아 두는 기능과 다르다. 각각의 소비자에게 **버퍼**가 필요하면 SNS → SQS 조합.

### 2.5 Lambda, API Gateway, ECS/Fargate [공식]

- **Lambda**: 이벤트 기반 함수. **호출당 최대 15분**. 동시성/메모리/임시 스토리지/Cold Start 제약을 고려.
- **API Gateway**: 공개/비공개 REST/HTTP/WebSocket API 관리, 인증·요청 제한·사용량 관리 등. REST API와 HTTP API의 기능/비용 차이를 구분한다.
- **ECS**: AWS 관리형 컨테이너 오케스트레이션. **EKS**: Kubernetes 기반. **Fargate**: 서버 인스턴스 관리 없이 ECS/EKS 지원 구성에서 컨테이너 실행.
- **AWS Batch**: Job Definition + Job Queue + Compute Environment. 시작/끝이 있는 대규모 작업. EC2/Spot 또는 ECS Fargate 등의 실행 옵션.
- **Elastic Beanstalk**: 앱 배포 환경 관리 간소화. Amplify는 웹·모바일 프런트엔드와 백엔드 연동·호스팅 지원.
- `S3 Object Created → Lambda`는 이벤트 기반 짧은 처리에 적합. **S3가 자동으로 Batch SubmitJob을 호출하는 것은 아님**. EventBridge/Lambda 등 연결 필요.

### 2.6 Route 53 장애 조치와 글로벌 분산 [공식]

| 라우팅 정책 | 의미 |
|---|---|
| Simple | 단순 DNS 응답 |
| Weighted | 가중치 기준 배분(배포·트래픽 실험) |
| Latency | 사용자에게 상대적으로 지연 낮은 AWS Region 선택 |
| Failover | Primary Health Check 실패 시 Secondary |
| Geolocation | 사용자의 **지역** 규칙에 따른 답변 |
| Geoproximity | 거리/편향(Bias) 기반 트래픽 이동 |
| Multi-Value Answer | 다중 정상 레코드를 반환, 로드 밸런서 완전 대체는 아님 |

- Route 53은 **DNS 응답**을 관리한다. 이미 캐시된 응답·열린 세션의 즉각적인 전환까지 보장하지 않는다.
- **CloudFront**는 HTTP 콘텐츠 CDN/캐시·보안 연계, **Global Accelerator**는 글로벌 Anycast IP와 AWS 글로벌 네트워크 경로로 TCP/UDP 기반 접속 품질/장애 라우팅 향상. 둘 다 모두 '전 세계 서비스'라는 이유만으로 동일하지 않다.

### 2.7 DR: RPO / RTO / 4대 전략 [공식][강사]

- **RPO (Recovery Point Objective)**: 허용 가능한 **데이터 손실 시간** (어디 시점까지 복구해야 하나).
- **RTO (Recovery Time Objective)**: 허용 가능한 **서비스 중단 시간** (얼마나 빨리 복구해야 하나).

| 전략 | 대기 상태 | 비용 경향 | 복구 속도 경향 |
|---|---|---|---|
| Backup & Restore | 주로 백업만 | 낮음 | 느림 |
| Pilot Light | 핵심 데이터/최소 핵심부 준비 | 비교적 낮음 | 백업보다 빠름 |
| Warm Standby | 기능 갖춘 작은 시스템 계속 가동 | 중상 | 빠름 |
| Multi-Site Active-Active | 여러 사이트가 평소에도 서비스 | 높음 | 매우 빠름 |

**함정:** `Multi-AZ`는 **AZ 장애**에 강하지만 `리전 전체 소실` 해결과는 별도. Cross-Region 백업/복제·DNS 장애 조치·인증·의존 리소스도 함께 설계해야 한다.

### 2.8 백업·이전 서비스 비교 [공식]

| 요구 | 적합한 후보 |
|---|---|
| 서비스 간 백업 계획·Vault 중앙화 | AWS Backup |
| 백업을 규정상 삭제 불가하게 보호 | Backup Vault Lock (모드·권한·유예기간 확인) |
| RDS 간/온프레미스 DB 데이터 이전 및 CDC | AWS DMS |
| 서로 다른 DB 엔진으로 스키마 변환 | AWS DMS Schema Conversion / SCT |
| 서버 OS·애플리케이션 통째 Lift-and-Shift | AWS Application Migration Service (MGN) / 현재 AWS Transform 연계 |
| 대량 파일 지속적 이동(서버/NAS ↔ AWS) | AWS DataSync |
| SFTP/FTPS/FTP 클라이언트로 AWS 저장소 접근 | AWS Transfer Family |
| 온프레미스에 AWS 스토리지 연동 게이트웨이 | AWS Storage Gateway |
| 초대용량·네트워크 제약 데이터 이전 | 지원되는 오프라인/하이브리드 전송 선택지의 **현재 이용 가능 여부** 확인 |

**서비스 수명 주기 주의:** 강의의 Snowball Edge, Snowmobile, 일부 Migration 서비스 명칭은 현재 제공 범위가 바뀌었을 수 있다. 실제 배포 솔루션은 최신 AWS 공식 서비스 상태를 우선한다.

## 🇰🇷 3. High-Performing Architectures (24%)

### 3.1 EC2 성능과 스토리지 선택 [공식]

| 요구 | 우선 선택 | 실전 함정 |
|---|---|---|
| 범용 서버 | EC2 General Purpose (`M`, `T` 등) | T Burst Credit와 무제한 모드 요금 이해 |
| CPU 계산 집중 | Compute Optimized (`C`) | 단순 메모리 증가 요구와 다름 |
| 인메모리/대용량 캐시 | Memory Optimized (`R`, `X`) | CPU 중심 C계열과 구분 |
| GPU / ML / 그래픽 | Accelerated Computing (`P`, `G`, `Inf` 등) | 실제 워크로드에 맞는 유형 |
| 임시 초고속 로컬 디스크 | EC2 Instance Store | 종료·기반 호스트 장애 시 데이터 보존 보장 안 됨 |
| 인스턴스에 연결하는 지속적 블록 디스크 | EBS | 기본적으로 같은 AZ의 EC2와 연결 |
| 여러 인스턴스에서 공유하는 Linux 파일 시스템 | EFS | NFS, Regional AZ 이중화 구성 지원 |
| 고성능 Windows SMB 공유 파일 시스템 | FSx for Windows File Server | EFS가 SMB/Windows ACL 대체가 아님 |
| HPC 병렬 파일 작업·S3 연계 | FSx for Lustre | ML/HPC 대량 병렬 처리 |
| 수많은 정적 이미지·백업·로그 객체 | S3 | POSIX 파일 시스템이나 EBS 블록 장치 아님 |

#### EBS 유형과 IOPS/Throughput

- `gp3`: 범용 SSD, **용량과 IOPS/Throughput을 독립 구성 가능**(제한 내). 다양한 일반 워크로드에 적합.
- `gp2`: 이전 세대 범용 SSD, 성능이 주로 볼륨 크기에 연동.
- `io2`: 높은 IOPS 및 내구성을 요구하는 데이터베이스; 고성능 및 높은 프로비저닝이 필요한 경우.
- `st1`: 순차적 Throughput 중심 HDD, 지속적 고속 스캔/로그 처리를 위한 용도(부팅 볼륨 아님).
- `sc1`: 덜 자주 액세스하는 Throughput 중심 HDD, 낮은 비용.
- EBS Snapshot은 **리전 범위 관리형 스냅샷**을 통해 같은 리전 다른 AZ의 새 EBS Volume으로 복구할 수 있다. 기존 볼륨을 다른 AZ로 즉시 이동시키는 기능이 아니다.
- EBS Multi-Attach는 지원되는 `io1`/`io2` 및 Nitro 조건에서 **같은 AZ 여러 EC2가 하나의 볼륨에 접근** 가능한 특별한 기능. **일반 EBS가 전부 여러 EC2에 공유 가능한 것은 아니다.** 동시에 쓰는 앱은 클러스터 파일시스템/동시성 제어가 필요.

#### EFS / FSx 특징

- EFS는 여러 Linux EC2에서 NFS로 공유 가능. Regional EFS(여러 AZ) vs One Zone EFS(단일 AZ, 비용 절감)를 구분.
- FSx for Windows는 SMB/Active Directory, FSx for Lustre는 HPC/대규모 병렬 IO. FSx for NetApp ONTAP / OpenZFS는 기존 파일 워크로드 프로토콜·스냅샷 등을 요구할 때 검토.
- EC2 Instance Store는 휘발성 **호스트에 묶인** 스토리지로, 애플리케이션이 다른 곳에 복제·저장하도록 설계해야 한다.

### 3.2 S3 스토리지 클래스 핵심표 [공식][강사]

| 클래스 | 특징 | 고르는 힌트 |
|---|---|---|
| **S3 Standard** | 빈번 액세스, 여러 AZ 기반 | 일반 웹 자산/업로드 |
| **S3 Intelligent-Tiering** | 접근 패턴 불확실, 자동 계층화 | 사용 패턴을 예측하기 어려움 |
| **S3 Standard-IA** | 자주 읽지 않지만 빠른 접근 필요, 여러 AZ | DR 데이터/백업에서 빠른 복구 |
| **S3 One Zone-IA** | 단일 AZ, 복원 가능한 데이터 | AZ 손실 허용, 비용 절감 |
| **S3 Glacier Instant Retrieval** | 드물게 접근하지만 즉시 검색 | 밀리초 검색이 필요한 아카이브 |
| **S3 Glacier Flexible Retrieval** | 분~시간 단위 복원 | 빠른 직접 액세스가 불필요한 아카이브 |
| **S3 Glacier Deep Archive** | 장기 최저 보관 비용 중심 | 매우 드문 복원, 시간 단위 복구 허용 |
| **S3 Express One Zone** | 한 AZ의 매우 낮은 지연과 높은 요청 성능 | 단일 AZ 허용, 초고성능 지연 최적화 |

- 저장 용량 비용만 보지 말고 **검색 요청 비용/복원 비용/최소 보관 기간/최소 청구 객체 크기/데이터 전송비**를 함께 비교.
- S3 Lifecycle은 시간이 지나면 객체를 다른 계층으로 전환/만료 가능. 전환 규칙과 작은 객체 제약을 주의.
- S3 Versioning은 이전 버전 보존, CRR/SRR은 버전 관리 등이 요구될 수 있다. **기존 객체 복제는 별도 Batch Replication 같은 절차가 필요할 수 있다.**
- S3 Transfer Acceleration은 먼 거리의 **업로드 가속**. CloudFront는 전 세계 **다운로드/콘텐츠 전송 캐시**에 주로 활용.
- S3 Multipart Upload: 큰 파일을 분할 병렬 전송, 실패한 부분만 재전송. Range GET은 병렬 다운로드와 부분 객체 조회.
- S3 Byte-Range Fetch / Prefix 분산 설계는 구형 권장사항과 최신 S3 자동 파티셔닝 동작을 구분한다.
- S3의 기본 읽기 일관성은 신규 Put·Overwrite·Delete에 대해 **강한 읽기 일관성**을 지원한다. '항상 최종적 일관성'은 오래된 설명.

### 3.3 DynamoDB vs RDS vs Aurora [공식][강사]

**관계형 SQL + JOIN + 트랜잭션/정해진 스키마** → RDS/Aurora.  
**대규모 키 기반 접근 + 엄청난 요청 처리 + 서버리스 NoSQL** → DynamoDB.

| 기능 | RDS/Aurora | DynamoDB |
|---|---|---|
| 데이터 모델 | 관계형 SQL | NoSQL Key-Value / Document |
| 주요 접근 | SQL 쿼리, JOIN | Partition Key (+ Sort Key), Key 기반 조회 |
| 용량 운영 | 인스턴스/서버리스 옵션 | Provisioned / On-Demand 용량 모드 |
| 읽기 캐시 | ElastiCache / Aurora cache 등 | DynamoDB Accelerator **DAX** |
| 단일 Item 크기 | 엔진/타입 별도 | **400 KB** 제한 |
| 변경 스트림 | DB 엔진 특화 | **DynamoDB Streams** |
| 전역 다중 활성 쓰기 | 별도 멀티리전 구조 | **Global Tables** (지원 모드/버전) |

#### DynamoDB 열 번 헷갈리는 포인트

1. **Partition Key**는 데이터 분산의 중심. 잘못된 Hot Key는 특정 Partition 병목을 초래.
2. **Sort Key**가 있으면 같은 Partition Key 내 정렬·범위 조건 쿼리에 적합.
3. **Query**는 Key 조건으로 제한된 항목을 효율적으로 조회. **Scan**은 테이블/인덱스 전체를 읽어 비싸고 느릴 수 있다.
4. **GSI(Global Secondary Index)**: 다른 Partition/Sort Key로 조회 패턴 확장. **GSI는 최종적 일관성 읽기**, 별도의 처리량/스토리지 영향.
5. **LSI(Local Secondary Index)**: 동일 Partition Key, 다른 Sort Key. 테이블 생성 때 정의 필요, 크기/제약에 주의.
6. **On-Demand**: 예측 어려운 요청량, 운영 단순성. **Provisioned + Auto Scaling**: 비교적 예측 가능한 처리량의 비용/성능 조절.
7. **TTL**: 만료 예정 항목을 자동 제거하지만 **설정 시각에 정확히 즉시 삭제되는 SLA는 아님**.
8. **PITR**: 특정 시점 복구용. S3 Export에서 읽기 용량을 소모하지 않는 옵션과 연계.
9. **Global Tables**: 여러 리전 활성 쓰기와 지역 액세스 지연 감소. 충돌 해결·복제 특성/실제 데이터 일관성 범위 이해.
10. **Import from S3**는 새로운 DynamoDB 테이블을 만들며 해당 테이블의 쓰기 용량을 소비하지 않는다. **Export to S3**는 PITR 기반 내보내기로 DynamoDB 읽기 용량을 소비하지 않고 Athena 등의 분석 입력으로 활용 가능.

### 3.4 캐싱 서비스 선택 [공식][강사]

| 어디에서 캐시? | 서비스 | 대표 힌트 |
|---|---|---|
| 사용자에 가까운 글로벌 엣지 | CloudFront | 정적/동적 콘텐츠 가속, CDN |
| API 응답 계층 | API Gateway Cache (지원 API 종류) | 반복 API 요청 감소 |
| DB 앞의 공유 인메모리 캐시 | ElastiCache (Valkey/Redis OSS/Memcached) | 초고속 읽기, 세션, 빈번 데이터 |
| DynamoDB 읽기 요청 | DAX | DynamoDB 전용 마이크로초 단위 캐시 |
| 애플리케이션 내부 | 앱 로컬 캐시 | 관리·일관성 책임이 앱에 있음 |

- Redis OSS/Valkey 계열은 풍부한 자료구조(예: **Sorted Sets 기반 Leaderboard**), 복제·지속성 등 지원 기능을 활용 가능. Memcached는 간결한 분산 메모리 캐시.
- **Cache Aside / Lazy Loading**: 캐시 미스 시 DB 조회·캐시 적재, 오래된 값 일관성 관리 필요.
- **Write Through**: 쓰기 시 캐시 갱신을 연계. **TTL**: 캐시 만료 기간.
- 자주 변하는 개인정보가 포함된 응답을 무작정 CloudFront에 캐싱하지 않는다. Cache Key, Header/Cookie 정책이 중요.

### 3.5 VPC 라우팅·연결 [공식][후기]

```text
Private Subnet 인스턴스가 접근할 목적지
  ├─ 같은 VPC 주소 → local route
  ├─ 인터넷 IPv4 → NAT Gateway (아웃바운드)
  ├─ 인터넷 IPv6 → Egress-only Internet Gateway
  ├─ S3 / DynamoDB → Gateway Endpoint (지원 조건)
  ├─ SSM / SNS / Secrets Manager 등 → Interface Endpoint (지원 조건)
  ├─ 다른 VPC (간단 1:1) → VPC Peering
  ├─ 여러 VPC/Hubs → Transit Gateway
  ├─ 온프레미스 암호화 VPN → Site-to-Site VPN
  └─ 온프레미스 전용 물리 연결 → Direct Connect
```

#### VPC 기본 함정

- **Public Subnet**은 Route Table에서 IGW로 나갈 경로가 있다는 의미이지, EC2에 자동으로 퍼블릭 IPv4가 항상 있다는 뜻은 아니다.
- **Private Subnet + NAT GW**는 IPv4 외부로 먼저 나가는 트래픽을 처리. 인터넷에서 NAT GW를 경유해 임의 Private EC2에 신규 연결을 수립하는 일반적인 방식이 아니다.
- **NAT Gateway**는 적절한 Public Subnet·EIP 등의 조건이 필요하며 AZ 장애/비용을 고려한다.
- **VPC Peering은 Non-transitive**. `VPC A↔B, B↔C`라고 해서 A↔C 자동 통신 X. 상대 VPC 간 CIDR 중복 제한도 확인.
- **Transit Gateway**: 멀티 VPC/온프레미스 Hub-and-Spoke와 중앙 라우팅, Route Table/Attachment 정책을 이해. TGW도 무조건 모든 접속이 자동 연결되는 건 아니다.
- **VPN**: 퍼블릭 인터넷을 통한 암호화된 연결, 비교적 빠른 설정. **Direct Connect**: 전용 회선, 보다 예측 가능한 네트워크 특성. **DX가 자동 암호화되는 것은 아님**. DX + VPN 등 결합 가능.
- **Gateway Endpoint**: S3/DynamoDB와 VPC Route Table 기반, 지원 조건에서 시간당 Endpoint 요금 없음. **Interface Endpoint**: ENI/Private IP/Private DNS, 시간 및 데이터 처리 요금 발생 가능.
- **VPC Flow Logs**: 네트워크 흐름 메타데이터(허용/거부)를 기록. **Traffic Mirroring**은 실제 패킷 사본 기반 분석. **CloudTrail**은 AWS API 호출 감사.
- **Longest Prefix Match**는 일반적인 라우팅 원칙이나 AWS의 특정 경로 타입 간 추가 우선순위 규칙도 존재한다.

### 3.6 ALB vs CloudFront vs Global Accelerator

| 요구 | 적합한 서비스 |
|---|---|
| Host/Path 기반 HTTP 백엔드 라우팅 | ALB |
| 전 세계 사용자 정적·동적 웹 콘텐츠 가속, 캐시 | CloudFront |
| 여러 리전의 TCP/UDP 앱, 고정 Anycast IP 및 글로벌 경로 개선 | Global Accelerator |
| DNS 정책/Health Check 기반 리전 라우팅 | Route 53 |

- **CloudFront OAC**는 오리진 S3 보호용. **Signed URL/Cookie**는 최종 시청자 접근을 제한. CloudFront는 Edge에서 사용자에게 응답하며, Cache Miss면 Origin으로 요청한다.
- **ALB**는 X-Forwarded-For 등 헤더로 원본 클라이언트 IP 정보를 전달할 수 있다. **NLB**는 설정·대상 타입에 따라 소스 IP 보존 동작이 다를 수 있다. Source IP 문제는 '항상 동일'이라고 외우지 않는다.

### 3.7 Data & Analytics 서비스 비교 [공식][강사]

| 목적 | 서비스 |
|---|---|
| S3에 있는 데이터를 SQL로 직접 분석 | **Athena** |
| 대규모 Data Warehouse / 복잡한 분석 SQL | **Redshift** |
| ETL, CSV→Parquet, 메타데이터 Crawler | **AWS Glue** |
| S3 데이터 레이크 중앙 권한·행/열 통제 | **Lake Formation** |
| 실시간 데이터 스트림 수집·처리 | **Kinesis Data Streams/Firehose(현재 Amazon Data Firehose)** |
| 대규모 Hadoop/Spark 분석 | **EMR** |
| 대시보드·BI 시각화 | **Amazon Quick / QuickSight 계열** (현행 명칭 확인) |
| Salesforce 등 SaaS 데이터 통합 | **Amazon AppFlow** |
| 검색 및 로그 검색 인덱싱 | **OpenSearch Service** |

- **Glue Crawler**는 데이터 스키마 발견→**Glue Data Catalog** 메타데이터 생성/업데이트.
- **Glue Job Bookmarks**는 처리한 데이터의 상태를 기록해 중복 ETL 작업량을 줄일 수 있다.
- **CSV→Parquet**는 열 지향 압축·필요 열만 스캔하는 분석에 유리. Athena 비용도 일반적으로 읽은 데이터량 영향을 받는다.
- **Kinesis Data Streams**는 스트림 소비/리플레이 필요, **Data Firehose**는 S3/Redshift/OpenSearch 등에 관리형 전달·변환이 주요 목적.
- **Redshift vs RDS**: OLAP 데이터웨어하우스 분석 vs OLTP 트랜잭션 애플리케이션 DB.

### 3.8 High-Performance Computing(HPC) [공식]

- **Cluster Placement Group**: 동일 AZ의 인스턴스 간 네트워크 성능/낮은 지연에 유리.
- **ENA**: EC2 향상된 네트워킹. **EFA**: 분산 HPC/MPI 노드 간 초고성능 통신, Libfabric 등의 통신 라이브러리 지원.
- **FSx for Lustre**: 병렬 파일 시스템, HPC/ML 처리와 S3 연계.
- **AWS Batch**: 개별·다수 배치 Job 예약·컴퓨팅 환경 관리. **ParallelCluster**: HPC 클러스터 전체 배포·관리.
- **EC2 Spot**: 중단 허용 HPC 작업을 비용 효율적으로 실행할 수 있으나 회수·체크포인트 전략 필요.

## 🇰🇷 4. Cost-Optimized Architectures (20%)

### 4.1 EC2 구매 모델 [공식][강사]

| 구매 모델 | 적합한 요구사항 | 조심할 함정 |
|---|---|---|
| **On-Demand** | 단기/불규칙, 중단되면 안 되는 작업 | 장기 고정 사용에는 더 비쌀 수 있음 |
| **Savings Plans** | 1년/3년 일정 컴퓨팅 사용량 약정 | 약정 지출 의무·유형별 유연성 차이 |
| **Reserved Instances (RI)** | 안정적 사용량, 특히 특정 EC2/RDS 등 지원 서비스 할인 | EC2 RI의 할인과 Capacity Reservation 개념 구분 |
| **Spot** | 체크포인트 가능한 배치·스팟 중단 허용 작업 | 회수 가능성, **중단 처리 로직** 필요 |
| **On-Demand Capacity Reservation** | 특정 AZ의 **EC2 용량 확보**가 핵심 | 가격 할인 상품이 아니다(할인 혜택 별도 조합 가능) |
| **Dedicated Instance / Dedicated Host** | 물리적 격리/규정, 코어·소켓 기반 라이선스(Host) | Dedicated Host와 Dedicated Instance는 다른 상품 |

#### Savings Plans를 더 정확히

- **Compute Savings Plans**: 사용량 약정의 적용 범위 유연성이 상대적으로 높음(지원 컴퓨팅 서비스/리전/인스턴스 계열 등).
- **EC2 Instance Savings Plans**: 특정 리전+인스턴스 패밀리에 대한 약정, 더 큰 할인 가능성이 있으나 범위 제한.
- **RI**는 서비스 및 옵션에 따른 기간/지역/가용 영역/구매 방식 영향이 있다. **예약 인스턴스 = 반드시 전용 인스턴스를 실제로 만들어 두는 것**이라는 해석은 틀림.
- **Spot Fleet/EC2 Fleet**은 여러 유형·AZ에서 용량을 분산 확보하고 중단 위험/비용을 조절할 수 있다.
- **Persistent Spot Request**는 인스턴스 중지/회수 뒤 다시 요청할 수 있는 유형. EC2만 종료했다고 요청이 끝났다고 가정하지 않는다.

### 4.2 S3 보관 비용 설계 [공식]

**수명 주기와 액세스 빈도**가 비용 최적화의 핵심이다.

| 데이터 성격 | 후보 |
|---|---|
| 매일 읽고 쓰는 웹 자산 | S3 Standard |
| 접근 빈도 예측 불가 | S3 Intelligent-Tiering |
| 거의 읽지 않지만 빠르게 복구해야 하는 백업 | Standard-IA / Glacier Instant Retrieval(접근 패턴·요금 조건 비교) |
| 장기 보존, 몇 시간 걸려도 됨 | Glacier Flexible Retrieval / Deep Archive |
| AZ 손실 시 다시 생성 가능한 데이터 | One Zone-IA, Express One Zone(각 용도·성능 요구 다름) |

- S3 Lifecycle **객체 전환 비용, 최소 보관 기간**을 같이 고려. 자주 작은 파일을 읽는 워크로드를 무조건 Archive로 보내면 역효과.
- **S3 Intelligent-Tiering**은 접근 모니터링/자동 계층화가 장점이나 일부 계층에 추가 보관·복원 조건과 모니터링 비용이 있을 수 있다.
- **CloudFront Cache Hit**를 늘리면 Origin 요청과 일부 데이터 전송을 줄일 수 있지만 CDN 자체 요금도 비교한다.
- 대용량 분석 데이터는 압축·Parquet·Partitioning을 이용해 Athena 스캔량 및 조회 비용 감소를 노릴 수 있다.

### 4.3 서버리스 / 컨테이너 / DB 비용

- 간헐적·예측 어려운 이벤트 요청이라면 **Lambda**가 유리할 수 있다. 지속적인 고부하 앱은 EC2/ECS/Fargate 등의 총비용을 비교.
- **Fargate**는 노드 운영 부담 절감, 그러나 동일한 장시간 높은 사용률의 컨테이너가 항상 EC2보다 싸지는 않음.
- **EC2 ASG**: 유휴 시간에 줄이고 피크에 자동 확장. 적절한 인스턴스 크기/혼합 인스턴스/Spot을 고려.
- **DynamoDB On-Demand**: 요청량 변화 큰 서비스. **Provisioned + Auto Scaling**: 예측 가능한 부하의 비용 제어.
- **Aurora Serverless / RDS 적정 크기**: DB 변동 수요와 기능·비용 조건 비교.
- **ElastiCache/DAX**는 DB 접근량을 줄일 수 있지만 캐시 자체 비용도 발생하므로 총비용 비교.

### 4.4 네트워크 비용: 숨은 정답 [공식][후기]

- AWS **인바운드 데이터 전송**은 여러 일반 상황에서 무료지만 서비스/경로 예외 확인. **인터넷 Outbound**는 비용이 중요한 경우가 많다.
- **AZ 간 트래픽**에는 데이터 전송 요금이 발생할 수 있다. 같은 AZ로 구성해 비용을 줄이는 전략은 HA 요구와 충돌할 수 있다.
- **NAT Gateway**는 시간당 요금 + 처리 데이터량 기반 요금. AWS 서비스(S3 등)만 접근하는 Private EC2에 NAT를 무조건 거치게 하면 불필요한 비용.
- **S3 Gateway Endpoint**는 지원되는 VPC 내 접근에 대해 Interface Endpoint에 비해 비용상 유리한 경우가 많다.
- **VPC Peering vs Transit Gateway**는 연결 개수, 비용·라우팅 복잡성·전이성 요구를 비교.
- **CloudFront**는 엣지 캐시로 지연·오리진 전송을 최적화할 수 있지만 요금 모델 검토 필요.
- **Direct Connect**는 안정적인 전용 회선이 필요할 때 선택하며 단순히 'VPN보다 싸다'라는 답은 아니다.

### 4.5 비용 분석 도구 구분

| 질문 | 서비스 |
|---|---|
| 지난달 서비스/계정/태그별 요금 원인과 향후 예측 | **Cost Explorer** |
| 설정한 월 예산/예상 비용 초과 알림 | **AWS Budgets** |
| 평소 패턴과 다른 비정상 비용(ML 기반) 탐지 | **Cost Anomaly Detection** |
| 매우 상세한 비용·사용량 원본 데이터 분석 | **AWS Cost and Usage Report / Data Exports** |
| 활용률이 낮거나 비효율적인 리소스 권고 | **Trusted Advisor / Compute Optimizer** (목적·권한 차이) |
| 개발용 EC2/RDS를 업무 시간에만 실행 | **Instance Scheduler on AWS** |

- **Instance Scheduler는 CloudFormation으로 배포하는 AWS Solutions 구현체**다. 단순 AWS 관리형 서비스 하나가 아니다. DynamoDB+Lambda+EventBridge 및 태그 기반으로 관리.
- 개발 인스턴스가 중지돼도 EBS, 일부 IP, 스냅샷 등 비용이 남을 수 있다.
- Cost Explorer의 예측은 **최대 18개월 범위**(월 단위 등 공식 지원 조건)로 소개되지만 미래 청구액 보장은 아니다.

## 🇰🇷 5. 최우선 결정표: 두 선택지가 헷갈릴 때

다음 표는 모의고사에서 **정답 후보 두 개가 모두 그럴듯할 때** 사용하는 최종 구분표다.

| 헷갈리는 두 후보 | 정답을 가르는 조건 |
|---|---|
| **Multi-AZ vs Read Replica** | 자동 장애 조치 필요? Multi-AZ / SQL 읽기 성능 분산? Read Replica |
| **RDS vs DynamoDB** | SQL·JOIN·관계형 트랜잭션 vs 키 중심 대규모 NoSQL |
| **ElastiCache vs DAX** | 범용 앱·관계형 DB 캐시 vs DynamoDB 전용 읽기 캐시 |
| **SQS vs SNS** | 메시지 버퍼링·Pull vs 여러 구독자에게 Push Fan-out |
| **SNS vs EventBridge** | 단순 Pub/Sub 전달 vs 이벤트 규칙 기반 라우팅·SaaS 통합 |
| **SQS FIFO vs Standard** | 순서·중복 제거 요구 vs 최대 처리량 중심, 순서 제약 완화 |
| **Kinesis vs SQS** | 스트리밍 기록·리플레이·다중 실시간 소비 vs 작업 대기열 |
| **Step Functions vs SQS** | 작업 분기·순서·오케스트레이션 vs 독립 작업 버퍼링 |
| **CloudFront vs Global Accelerator** | CDN·HTTP 캐싱 vs 글로벌 고정 IP·TCP/UDP 경로 최적화 |
| **ALB vs NLB** | L7 URL/Host 라우팅 vs L4 TCP/UDP·정적 IP |
| **Security Group vs NACL** | Stateful ENI 허용 vs Stateless Subnet Allow/Deny |
| **WAF vs Shield** | L7 웹 공격 규칙 vs DDoS 보호 |
| **GuardDuty vs Inspector vs Macie** | 위협 감지 vs 취약점 검사 vs S3 민감 데이터 발견 |
| **KMS vs Secrets Manager vs ACM** | 암호화 키 vs Secret Rotation vs TLS 인증서 |
| **Identity Center vs Cognito User Pool** | 직원 멀티계정 SSO vs 서비스 최종 사용자 로그인 |
| **User Pool vs Identity Pool** | 로그인 토큰 vs 임시 AWS 자격 증명 |
| **OAC vs Signed URL** | CloudFront의 S3 원본 접근 제한 vs 최종 사용자의 배포 콘텐츠 접근 제한 |
| **IAM Permission Boundary vs SCP** | IAM Identity 상한 vs 조직 멤버 계정 상한 |
| **NAT Gateway vs Gateway Endpoint** | 일반 인터넷 IPv4 송신 vs S3/DynamoDB 사설 경로 |
| **Interface Endpoint vs Gateway Endpoint** | PrivateLink 지원 서비스 ENI 접근 vs S3/DynamoDB Route 기반 접근 |
| **VPC Peering vs TGW** | 소수 VPC의 비전이적 직접 연결 vs 여러 VPC 중앙 허브 연결 |
| **VPN vs Direct Connect** | 인터넷 기반 암호화 터널 vs 전용 연결 및 운영/개통 고려 |
| **EBS vs EFS vs S3** | 블록 볼륨 vs 공유 파일 시스템 vs 객체 저장소 |
| **FSx Windows vs FSx Lustre** | SMB/AD 파일 공유 vs HPC 병렬 파일 IO |
| **Instance Store vs EBS** | 임시 로컬 초고속 디스크 vs 지속 블록 볼륨 |
| **S3 Standard-IA vs One Zone-IA** | 여러 AZ 내구성/가용성 vs 재생 가능한 데이터의 단일 AZ 비용 절감 |
| **Glacier Instant vs Flexible vs Deep** | 즉시 액세스 vs 분~시간 복구 vs 장기·매우 드문 복구 |
| **Athena vs Redshift** | S3 직접 Serverless SQL 분석 vs 웨어하우스 OLAP |
| **Glue vs DataSync** | 데이터 정제·ETL·형식 변환 vs 파일/객체 전송 |
| **MGN vs DMS** | 서버 전체 Lift-and-Shift vs DB 데이터·CDC 마이그레이션 |
| **DataSync vs Transfer Family** | 자동 대량 이동·동기화 vs 기존 SFTP/FTPS/FTP 이용 |
| **Backup Restore vs Pilot Light vs Warm Standby vs Active-Active** | 낮은 비용·느린 복구에서 높은 비용·빠른 복구까지 RTO/RPO 요구로 결정 |
| **Spot vs Savings Plans vs Capacity Reservation** | 회수 허용 최저 컴퓨팅 비용 vs 사용량 할인 약정 vs 물리 용량 확보 |
| **Lambda vs Batch** | 단시간 이벤트 함수 vs 장시간·대규모 컨테이너 작업 |
| **ECS vs EKS vs Fargate** | 컨테이너 오케스트레이션(AWS) vs Kubernetes vs 서버 관리 없는 실행 기반 |
| **CloudFormation vs Systems Manager** | 인프라 선언적 배포 vs 실행 중 운영·관리 |
| **Session Manager vs Run Command** | 대화형 셸 접속 vs 다수 노드 원격 명령 |
| **Patch Manager vs Maintenance Windows** | 패치 관리 vs 작업 예약 시간 |
| **Cost Explorer vs Anomaly Detection vs Budgets** | 분석·예측 vs 비정상 지출 탐지 vs 설정한 예산 통제 |
| **Well-Architected Tool vs Trusted Advisor** | 설계 리뷰용 질문지·위험 평가 vs 계정·리소스 구성 점검 |

## 🇰🇷 6. 모의고사에 자주 섞이는 오답 표현 30개

아래는 **공식 문항을 복제한 문장들이 아니라**, 틀린 선택지에서 흔히 나타날 수 있는 논리 오류를 예시화한 것이다.

1. **"RDS Read Replica를 배포하면 자동 Failover가 제공된다"** → 읽기 확장과 Multi-AZ 장애 조치 목적 다름.
2. **"RDS Multi-AZ Standby가 모든 SELECT를 대신 처리한다"** → 기존 Multi-AZ DB Instance Standby는 읽기 불가. DB Cluster 변형을 별도로 판단.
3. **"S3는 한 AZ에 저장되므로 AZ 장애에 취약하다"** → Standard는 여러 AZ에 분산. One Zone 클래스는 다름.
4. **"S3의 모든 클래스는 같은 복원 지연"** → Glacier 계층별 접근/복원 시간 다름.
5. **"DynamoDB Scan이 Query보다 Key 기반 조회에 효율적이다"** → Query가 Key 조건을 활용하는 효율적 접근.
6. **"DynamoDB GSI에서 Strongly Consistent Read"** → GSI는 Eventually Consistent.
7. **"DynamoDB TTL이 정해진 시각에 즉시 레코드 삭제 보장"** → 만료 후 비동기 삭제.
8. **"DynamoDB Global Tables의 여러 리전 쓰기가 데이터 충돌 가능성을 완전히 제거"** → 복제/충돌 해결 동작을 고려.
9. **"SQS Standard는 Exactly-once Delivery를 보장"** → 중복 메시지 처리 설계 필요.
10. **"SQS가 SNS처럼 구독자에게 직접 Push"** → SQS는 소비자가 Poll.
11. **"SNS Topic에 이벤트를 Publish하면 언제든 Replay 가능"** → Replay가 필요한 스트리밍은 Kinesis 등 고려.
12. **"모든 Lambda는 2시간 동안 실행 가능"** → Lambda 호출은 최대 15분.
13. **"AWS Batch는 EC2만 사용할 수 있다"** → Fargate 등 지원 옵션 존재.
14. **"Private EC2는 반드시 NAT Gateway를 거쳐 S3에 접근"** → S3 Gateway Endpoint 가능.
15. **"Interface Endpoint는 어떤 서비스든 0원"** → 시간당 및 데이터 처리 과금 가능.
16. **"VPC Peering A-B, B-C면 A-C 자동 통신"** → VPC Peering은 비전이적.
17. **"NACL은 Stateful이므로 응답 포트 불필요"** → Stateless, 복귀 트래픽 규칙 고려.
18. **"Security Group에서 IP를 Deny하는 규칙을 만든다"** → SG는 Allow 기반. 명시적 Deny 필요 시 NACL/WAF 등.
19. **"Direct Connect는 기본적으로 IPsec으로 암호화"** → DX 자체가 VPN 같은 자동 암호화를 보장하지 않음.
20. **"EC2 Instance Connect는 모든 경우에 SSH Port 22 불필요"** → EC2 Instance Connect는 SSH 기반.
21. **"CloudTrail 관리 이벤트 기록은 모든 S3 Object Read 기록"** → S3 데이터 이벤트 등 별도 설정 개념.
22. **"GuardDuty는 EC2 OS 패치 설치 도구"** → GuardDuty는 위협 탐지; Patch Manager/Inspector와 구별.
23. **"CloudFront OAC가 최종 사용자 로그인까지 처리"** → Origin 보호와 사용자 접근 제어는 다른 계층.
24. **"KMS Key Policy 없이 IAM의 Decrypt Allow만 있으면 모든 경우에 됨"** → KMS 권한 모델 고려.
25. **"SCP는 계정 사용자에게 서비스 사용 Allow를 직접 부여"** → SCP는 권한 상한/제한.
26. **"Spot은 원하면 중단되지 않는 무기한 고정 용량"** → 용량 회수 위험.
27. **"Savings Plans 약정을 가입하면 리소스는 자동 생성"** → 할인 모델이지 인프라 자동 생성이 아님.
28. **"백업만 설정하면 RTO=0"** → Restore 절차·시간 필요.
29. **"CloudFormation Change Set은 무중단 업데이트 보장"** → 일부 리소스 Replacement 가능.
30. **"Well-Architected Tool이 계정 전체 리소스를 자동 수정"** → 질문 기반 아키텍처 평가·개선 계획 도구.

## 🇰🇷 7. 실전 사고방식: 12개 마이크로 시나리오

> 원본 문제를 재생산하지 않은 **독창적인 이해 훈련**이다. 최종 65문항×10세트 모의고사와는 별개의 짧은 예시다.

### 7-01. DB 읽기 부하 + 장애 요구

**상황:** SQL 데이터베이스의 읽기 요청 급증. DB 장애에도 자동 서비스 유지 필요.  
**해석:** 읽기 확장과 HA 두 요구는 서로 독립.  
**설계:** RDS Multi-AZ로 HA, Read Replica로 읽기 분산(또는 요건에 맞는 Aurora Cluster).  
**탈락:** Read Replica *하나만* 만들어 Multi-AZ 장애 조치 문제까지 해결한다고 주장.

### 7-02. Private Subnet에서 S3 접근

**상황:** EC2의 퍼블릭 인터넷 통신 차단. 대량 S3 접근, 최소 운영/네트워크 비용.  
**설계:** S3 Gateway Endpoint + Route Table + Bucket/Endpoint Policy.  
**탈락:** 요구에 없는 NAT Gateway로 S3를 경유하는 불필요한 통신 경로.

### 7-03. 고객별 로그인과 파일 업로드

**상황:** 웹앱 사용자가 로그인해 파일 업로드, AWS 임시 자격 증명이 필요한 설계.  
**설계:** Cognito User Pool 인증 + 필요한 경우 Identity Pool을 통한 임시 IAM Credential + S3 정책.  
**탈락:** CloudFront OAC만으로 사용자가 인증되고 개인별 S3 권한이 자동 생성된다는 주장.

### 7-04. 주문 처리 성공 여부와 관계없이 배송 이벤트 전달

**상황:** 주문 이벤트를 결제와 배송 서비스가 독립적으로 처리. 한 서비스 장애 시 이벤트 보관 필요.  
**설계:** SNS Topic → 별도 SQS Queue → 소비자.  
**탈락:** SNS 직접 Push만 사용하고 소비자 장애 시 장기 버퍼링을 당연시하는 설명.

### 7-05. 이벤트 정확한 처리 순서

**상황:** 같은 고객의 금융 이벤트를 순서대로 처리하고 중복 요청도 다뤄야 함.  
**설계:** SQS FIFO(MessageGroupId 전략) + Idempotent Consumer.  
**탈락:** SQS Standard를 순서 보장이라고 설명.

### 7-06. Lambda에서 RDS 연결이 급증

**상황:** API Gateway + Lambda 트래픽 급증으로 RDS 연결 수 폭발.  
**설계:** RDS Proxy를 통한 풀링, 동시성·DB 부하도 같이 관리.  
**탈락:** 단순 Read Replica만 늘리면 쓰기 연결 수 문제가 자동 해결된다고 주장.

### 7-07. 조직의 민감 정보 점검

**상황:** 여러 S3 Bucket에 주민등록번호나 이메일 주소 등 민감 정보가 있는지 찾기.  
**선택:** Amazon Macie.  
**탈락:** Inspector(취약점) 또는 GuardDuty(위협)로 PII 파일 내용을 직접 분류한다고 판단.

### 7-08. 두 리전 DR

**상황:** Region 장애 후 수 분 이내 서비스 재개, 데이터 손실 극소화.  
**선택:** Warm Standby 또는 더욱 엄격한 경우 Multi-Site + Cross-Region 복제/Route 53 구성.  
**탈락:** 해당 Region 내부 Multi-AZ 배포만으로 리전 장애 대비 완료.

### 7-09. 예측할 수 없는 트래픽과 NoSQL

**상황:** 키로 접근하는 쇼핑몰 장바구니, 수많은 요청, 서비스 운영 최소화.  
**선택:** DynamoDB On-Demand + 올바른 Key 설계/TTL.  
**탈락:** SQL JOIN이 필요하다는 조건이 없는데 과도한 관리형 관계형 DB 서버 구축.

### 7-10. 글로벌 웹사이트 정적 콘텐츠

**상황:** 여러 국가의 사용자가 이미지/JS를 지연 적게 받도록 개선.  
**선택:** S3 Origin + CloudFront 캐시 + OAC로 Origin 접근 제어.  
**탈락:** Global Accelerator가 S3 정적 파일을 CDN 방식으로 캐싱한다고 설명.

### 7-11. 장시간 배치 이미지 변환

**상황:** 한 번 실행되면 90분 걸리는 컨테이너 작업 수천 개, Spot 활용 가능.  
**선택:** AWS Batch Job Queue + EC2 Spot Compute Environment, 회수 시 재시도 설계.  
**탈락:** 하나의 Lambda Invocations로 90분 작업 수행.

### 7-12. 직장 PC에서 계정 여러 개 관리

**상황:** 기업 직원들이 하나의 SSO 포털로 여러 AWS 계정의 Role 기반 접근.  
**선택:** IAM Identity Center + Permission Sets / Organizations.  
**탈락:** 수만 명 직원에게 각 계정의 루트 사용자 비밀번호를 공유하거나 Cognito User Pool만으로 AWS 계정 관리자 SSO 구현.

## 🇰🇷 8. 약점 보강: 기존 강의에서 질문했던 부분을 시험으로 연결

| 사용자가 질문했던 개념 | 시험에서는 이렇게 묻는다 | 복습 힌트 |
|---|---|---|
| DynamoDB Global Tables | Multi-Region Active-Active 데이터 쓰기와 복제 충돌/지연 | DynamoDB와 Aurora Global의 차이 |
| DynamoDB Export/Import S3 | 분석용 Athena 연결, 읽기/쓰기 용량 영향 | PITR, 새 테이블 Import |
| OAC + Bucket Policy vs Cognito Identity Pool | Origin 접근 보호 vs AWS 임시 자격증명 | 인증 주체가 CloudFront인지 사용자/앱인지 |
| CloudFormation Service Role | `iam:PassRole`과 리소스 생성 Role 분리 | Caller/CFN Role/EC2 Role 세 개 구분 |
| On-Demand | EC2 종량제 / DynamoDB 요청 기반 용량 모델 | 서비스별 On-Demand 뜻을 별개로 기억 |
| Query와 Scan | DynamoDB Key 기반 조회 vs 전체 데이터 스캔 | Partition/Sort Key, GSI/LSI |
| HTTPS REST API | API Gateway, TLS 전송 중 보호 | REST는 API 설계 방식, HTTPS는 전송 보호 |
| Well-Architected vs Trusted Advisor | 설계 기준·리뷰 vs 계정 상태 권고 | 프레임워크 6 Pillars vs Trusted Advisor 6 Categories |

## 🇰🇷 9. 첫 56% 이후 권장 학습 방법

### 하루마다 달성할 작업

- 오전 또는 첫 세션: 이 정리본 중 **한 도메인**을 표시하면서 읽는다. 모르는 용어를 원본 GitHub 노트로 연결.
- 두 번째 세션: **정답을 이미 아는 문제보다 처음 보는 15~25문항**을 풀고 근거를 설명한다.
- 오답에 표시: `A 개념 미학습`, `B 두 서비스 혼동`, `C 조건 읽기 실패`, `D 알고 있었지만 기억 실패`, `E 시간·추론 실수`.
- 틀린 문제마다 한 문장: **"X 조건이 있어서 A가 맞고 B는 Y 조건을 만족하지 못한다."**
- 다음 날 복습: 전날 틀린 개념을 **문항 원문 없이** 설명해 본다.

### 권장 평가 루프

```text
현재 첫 모의고사 56%
     ↓
도메인별 판단표 + 오답 분석 (3~5일)
     ↓
새 문제로 65문항 Timed Mock #2
     ↓
오답 유형별 보강 (2~4일)
     ↓
새 문제로 65문항 Timed Mock #3
     ↓
새 문제 2~3회 연속 약 80% 목표 (권고치, 공식 합격률 아님)
     ↓
시험 예약 여부 결정
```

**같은 모의고사를 재응시해 90%가 된 것과 처음 본 모의고사에서 80%를 받은 것은 같은 의미가 아니다.** 기억 효과를 분리해야 한다. 실제 시험의 환산 720점은 백분율 정답률 기준이 아니다.

## 🎯 Exam Notes

> [!IMPORTANT]
> **문제의 요구사항과 탈락 이유를 한 문장으로 말할 수 있으면 그 개념을 안다고 판단한다.** 이름만 외웠거나 설명을 읽고 끄덕이는 단계에서는 새로운 문제를 풀어 검증한다.

- 보안: IAM 정책 평가, 역할/계정 경계, KMS, S3 접근 제어, SG/NACL/WAF/GuardDuty/Inspector/Macie.
- 복원력: ALB/ASG 다중 AZ, RDS Multi-AZ vs Read Replica, SQS 재시도/DLQ, Route 53, RPO/RTO.
- 성능: S3 클래스, EBS/EFS/FSx, Aurora/DynamoDB, 캐시 계층, 네트워크·연결 서비스, ETL.
- 비용: On-Demand/Spot/RI/Savings Plans, S3 Lifecycle, NAT·데이터 전송 비용, 적정 사이징.
- 출제 가능성을 높게 보겠다고 **과거 응시 후기에서 언급된 문항 수를 실제 시험의 확정 배분으로 사용하지 않는다.**

## 📝 Review Questions

> [!TIP]
> 이 정리본을 처음부터 읽은 뒤 전체 개념을 종합하는 5문제다. 정답은 접혀 있다.

### Q1. '계정의 리소스를 읽을 권한을 부여하는 SCP'라는 설명은 왜 틀렸을까?

<details>
<summary>정답 및 해설 보기</summary>

SCP는 멤버 계정의 최대 권한을 제한하는 정책이지 직접 허용 권한을 주는 정책이 아니다. 실제 Allow는 IAM 또는 지원되는 Resource Policy 등에서 필요하다.

</details>

### Q2. RDS Multi-AZ와 Read Replica를 모두 고려해야 하는 대표 상황은?

<details>
<summary>정답 및 해설 보기</summary>

SQL 데이터베이스가 AZ 장애 시 자동으로 복구되어야 하면서 읽기 트래픽도 많을 때다. 전통적인 Multi-AZ DB Instance의 Standby는 읽기용이 아니며, Read Replica는 읽기 확장 목적으로 별도 사용할 수 있다.

</details>

### Q3. S3 객체에 대한 Private Subnet 접근을 가장 낮은 네트워크 운영 비용으로 구성하려는 경우 우선 검토할 것은?

<details>
<summary>정답 및 해설 보기</summary>

S3 Gateway Endpoint. 지원 VPC 조건에서 NAT/인터넷 경로 없이 Route Table과 Endpoint Policy 기반으로 접근할 수 있고 Interface Endpoint/NAT와 비용 구조가 다르다.

</details>

### Q4. DynamoDB Global Tables와 RDS Read Replica가 동일한 기능이 아닌 이유는?

<details>
<summary>정답 및 해설 보기</summary>

Global Tables는 여러 리전의 활성 쓰기를 지원하는 DynamoDB 다중 리전 복제 솔루션이고, RDS Read Replica는 관계형 DB의 읽기 확장 및 복제본 용도다. 데이터 모델·쓰기 동작·일관성·장애 시 조치 모두 다르다.

</details>

### Q5. 일시 중단될 수 있는 작업 10만 개를 2시간씩 처리하면서 비용을 줄이고 싶다면 어떤 서비스를 고려할까?

<details>
<summary>정답 및 해설 보기</summary>

AWS Batch + 지원되는 EC2 Spot 컴퓨팅 환경을 고려한다. Batch가 작업 큐와 스케줄링을 관리하고 Spot은 중단 가능한 계산 비용을 낮춘다. 재시도·체크포인트 설계가 필요하며 Lambda 한 호출 15분 한도와 다르다.

</details>

## 🇯🇵 日本語 Summary

本資料は AWS Certified Solutions Architect – Associate (SAA-C03) の試験対策用に、既存の講義ノートを **セキュリティ(30%)・回復性(26%)・性能(24%)・コスト最適化(20%)** の4領域に再構成したものである。サービス名の暗記ではなく、設計要件、選択基準、他の選択肢を除外する理由を重点的に学習する。非公開の試験問題や流出問題を含まない。

## 🇺🇸 English Summary

This original SAA-C03 exam-review guide consolidates prior AWS study notes into the four official domains: Security (30%), Resilience (26%), Performance (24%), and Cost Optimization (20%). Focus on interpreting requirements, selecting the best architecture, and explaining why plausible distractors fail. This is not a collection of real or leaked exam questions.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Requirement | 요구사항 | 要件 |
| Distractor | 오답 선택지 | 誤答選択肢 |
| Trade-off | 절충·선택 조건 | トレードオフ |
| Least Privilege | 최소 권한 | 最小権限 |
| High Availability | 고가용성 | 高可用性 |
| Fault Tolerance | 내결함성 | 耐障害性 |
| Durability | 데이터 내구성 | 耐久性 |
| Eventual Consistency | 최종적 일관성 | 結果整合性 |
| Read Replica | 읽기 전용 복제본 | リードレプリカ |
| Failover | 장애 조치 | フェイルオーバー |
| Data Transfer | 데이터 전송 | データ転送 |
| Operational Overhead | 운영 부담 | 運用負荷 |
| Cost Optimization | 비용 최적화 | コスト最適化 |
| Workload | 업무 시스템·워크로드 | ワークロード |
| Spot Interruption | 스팟 인스턴스 중단 | スポット中断 |

## 🔗 Official References & Research Boundaries

**2026-10-21 기준 공식 시험 범위의 우선 근거**

1. [SAA-C03 공식 시험 가이드 (한국어)](https://docs.aws.amazon.com/ko_kr/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03.html)
2. [도메인 1: 보안](https://docs.aws.amazon.com/ko_kr/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03-domain1.html)
3. [도메인 2: 복원력](https://docs.aws.amazon.com/ko_kr/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03-domain2.html)
4. [도메인 3: 고성능](https://docs.aws.amazon.com/ko_kr/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03-domain3.html)
5. [도메인 4: 비용](https://docs.aws.amazon.com/ko_kr/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03-domain4.html)
6. [AWS 공식 시험 소개/Official Practice Question Set 안내](https://aws.amazon.com/ko/certification/certified-solutions-architect-associate/)
7. [AWS SAA-C03 Study Repository](https://github.com/luis12211234-coder/AWS-SAA-Study/tree/main/01-Cloud-Basics)

**후기**는 공식 비중을 교체하지 않는다. 2026년 공개 응시 후기에서 반복된 VPC·보안·스토리지·DB·가용성 및 미세한 서비스 차이의 어려움을 보조 신호로 활용했다. 예: [2026-02 후기](https://www.reddit.com/r/AWSCertifications/comments/1r73smm/), [2026-06 토론](https://www.reddit.com/r/AWSCertifications/comments/1ufhk3s/), [2026-08 후기](https://www.reddit.com/r/AWSCertifications/comments/1vqnid7/), [2026-10 후기](https://www.reddit.com/r/AWSCertifications/comments/1wvzteq/).

**주의:** 실제 AWS 기능 및 이용 가능성은 리전과 서비스 업데이트에 따라 달라질 수 있다. 시험 가이드는 출제 가능한 개념의 **비포괄적 목록**이며, 어떤 서비스가 정확히 몇 문제 나올지는 AWS가 공개하지 않는다. v1.0 검토 시 오답 기록과 다른 공식 문서를 대조해 개선한다.
