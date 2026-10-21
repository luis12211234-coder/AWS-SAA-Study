# 📘 첫 SAA-C03 모의고사 65문항 전체 복기 및 해설 (채점표 기반)

> [!NOTE]
> **First Mock Exam Review** · 2026-10-21 · **37/65 정답, 28/65 오답 (56.9%)**  
> **자료:** 응시자가 제공한 채점 결과 이미지 12장. 문제 원문은 요지로 재서술했으며 정답 선택지/선택한 오답은 제공되지 않았다.  
> **확인 범위:** **Q01~Q65 전체 65문항**의 문제 내용과 정오 결과 확인. 단, **Q41 정오 아이콘은 화면에 가려져 총점과 다른 64개 정오 표시로 추정**했다.  
> **중요:** 본문의 '권장 판단'은 AWS 기술적으로 도출한 **학습용 해법**이지 Udemy 원본 선택지에서 검증한 정답이 아니다. '대표 탈락 논리'는 실제 보기 해설이 아닌 **유사 오답 유형**이다. 특히 '2개/3개 선택' 문제의 완전한 정답 조합은 검증 불가.

## 🇰🇷 0. 이번 모의고사에서 드러난 패턴

전체 채점은 **37 정답 / 28 오답**이다. 모든 65문항의 지문·채점 표시는 업로드 화면에 있으며 **Q41 상태 아이콘만 가려져 있다**. 나머지 오답 수가 이미 28개이므로 Q41은 **정답으로 합계상 추정**된다. 이 추정은 원본 채점 표시를 직접 본 결과가 아니다. **Q48~57은 5정답·5오답**으로 추가 확인했다.

### 우선 재학습할 핵심 묶음

| 우선순위 | 문제 예시 | 더 연습할 판단 기준 |
|---|---|---|
| **A. 네트워크 / 계정 간 연결** | Q04, Q10, Q38, Q39, Q46, Q59, Q62 | Peering의 비전이성, TGW, RAM, IAM 역할·버킷 정책, NAT/Endpoint/SG |
| **A. 배포·운영 자동화** | Q02, Q16, Q23, Q24, Q30, Q34 | Alarm+SNS, Golden AMI, Mixed ASG, 앱 상태 모니터링, User Data |
| **A. 대량 데이터·스트리밍·복제** | Q03, Q27~29, Q35~36, **Q51, Q52** | SQS/Kinesis, DB 엔진, Batch Replication, EFA, 스트림과 서버리스 제약, FIFO 마이그레이션 |
| **B. 보안·스토리지 비용** | Q25, Q32, Q42, Q45, **Q50, Q54, Q56** | GuardDuty 소스, Redis, NLB 해시, 라이선스 BYOL, Neptune, S3 Strong Consistency |

위 분류는 **한 차례 모의고사의 오답 패턴**이지 공식 출제 빈도 통계가 아니다. 특히 Q34는 과거 VPC Wizard UI 의존성이 높아 **낮은 우선순위**로 두는 것이 합리적이다.

### 해설 읽는 법

- **정답/오답:** 사용자의 모의고사 채점 화면에서 보이는 결과. 내가 새로 채점한 것이 아님.
- **확신도:** 원본 **선지 없이도 기술 원리를 추론할 수 있는 정도**. 원본 정답 확인 정도를 뜻하지 않음.
- **권장 판단:** 시험 지문으로부터 요구사항을 추출해 선택할 서비스·구성.
- **대표 탈락 논리:** 실제 선지를 재현하지 않은 개념적인 오답 소거 예시.
- **부록:** 문항 번호 → 핵심 개념 → 기존 `01-Cloud-Basics` 내 실제 GitHub 파일 링크. 마지막에만 모아 중복 링크를 최소화함.

## 🇰🇷 1. 문항별 해설 (문제 요약, 정답 방향, 소거법)
### Q01. 사내 ↔ VPC Site-to-Site IPsec VPN · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** 고객 측 Customer Gateway와 AWS 측 Virtual Private Gateway 또는 Transit Gateway를 사용해 IPsec 터널을 구성한다. 일반적으로 연결은 이중 터널로 제공되며 정적 또는 BGP 라우팅을 설계한다.

**이유:** 인터넷을 통과해도 IPsec으로 통신을 암호화하는 것이 VPN의 본질이다.

**대표 탈락 논리(실제 선지 아님):** Direct Connect는 전용 네트워크 연결이지 IPsec 암호화 자체를 보장하지 않는다. Peering은 사내 네트워크 연결 수단이 아니다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q02. EC2 CPU 임계값 초과 시 이메일 알림 · 오답

> **판단 확신도:** 높음 · **화면:** 오답

**권장 판단:** Amazon CloudWatch에서 CPUUtilization Alarm을 만들고 Amazon SNS Topic의 이메일 구독으로 알림을 발송한다. (2개 구성 요소)

**이유:** CloudWatch는 지표를 관찰하고 조건을 평가하고, SNS는 이메일을 전달한다.

**대표 탈락 논리(실제 선지 아님):** CloudTrail은 AWS API 감사 이력이지 CPU 알림의 중심이 아니다. EventBridge만으로 CloudWatch CPU 임계값을 직접 평가한다고 보지 않는다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q03. 갑작스러운 트래픽 급증 시 버퍼링·조절 · 오답

> **판단 확신도:** 중간 · **화면:** 오답

**권장 판단:** 요청 폭증을 생산자와 소비자 사이에서 완충하려면 Amazon SQS를 사용한다. API 호출 자체의 호출률 제한이 요구되면 API Gateway Throttling을 검토한다.

**이유:** SQS는 메시지를 보관하고 소비자가 가능한 속도로 처리하게 해준다. Throttling은 호출 속도를 제한하는 별개 메커니즘이다.

**대표 탈락 논리(실제 선지 아님):** SNS는 주로 팬아웃·알림이고 보존형 버퍼의 대체가 아니다. Kinesis는 지속적 스트림 재처리·순서와 소비자 관리가 중심이다.

> [!CAUTION]
> 정확히 한 개를 고르는 문제라도 선택지가 없으므로 SQS와 API Gateway 중 어느 구성이 채점 정답인지 단정하지 않는다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q04. VPC 5개 허브·스포크에서 상호 통신 실패 · 오답

> **판단 확신도:** 높음 · **화면:** 오답

**권장 판단:** VPC Peering은 전이적(Transitive) 라우팅이 지원되지 않는다. 모든 VPC 사이의 중앙 연결이 필요하면 AWS Transit Gateway에 각 VPC를 Attachment로 연결하고 TGW/VPC Route Table을 구성한다.

**이유:** VPC A가 중앙 VPC와 피어링되었어도 다른 스포크 VPC에 자동 도달하지 않는다.

**대표 탈락 논리(실제 선지 아님):** Peering 연결을 중앙 VPC에만 계속 추가해도 스포크 사이의 통신은 통과되지 않는다. NAT Gateway는 VPC 간 중앙 라우터가 아니다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q05. 본사 Direct Connect + 지사 Site-to-Site VPN 상호 데이터 교환 · 정답

> **판단 확신도:** 중간 · **화면:** 정답

**권장 판단:** 연결 요구·BGP 및 라우팅 조건에 따라 AWS VPN CloudHub(기존 VGW 기반) 또는 Transit Gateway를 활용해 본사/지사망 사이를 연결할 수 있다.

**이유:** 단순히 각각 VPC에 들어오는 연결을 만드는 것과 사이트 간 상호 통신을 허용하는 라우팅은 다르다.

**대표 탈락 논리(실제 선지 아님):** Direct Connect가 있다고 해서 모든 지사 VPN이 자동으로 서로 통신하지 않는다.

> [!CAUTION]
> CloudHub와 Transit Gateway 중 시험 정답은 숨겨진 선택지와 네트워크 게이트웨이 구성에 따라 다르다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q06. Kinesis 스트리밍 데이터 다수 소비자의 병렬 처리 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** Amazon Kinesis Data Streams의 Enhanced Fan-Out(EFO)을 사용하면 등록된 소비자가 다른 소비자와 분리된 읽기 처리량으로 데이터를 받을 수 있다.

**이유:** 다수의 Consumer가 동일 Shard를 공유 읽기하면 처리량 및 지연 문제가 생길 수 있다.

**대표 탈락 논리(실제 선지 아님):** Shard 수를 무조건 늘리는 것과 EFO로 소비자별 전용 처리량을 제공하는 것은 다른 방법이다. SNS가 KDS 소비자 처리량을 해결하지 않는다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q07. EC2 종료와 함께 비즈니스 EBS 데이터 소실 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** EBS Attachment의 `DeleteOnTermination` 설정을 점검한다. 루트 볼륨은 기본적으로 종료 시 삭제될 수 있고, 추가 볼륨도 설정에 따라 삭제된다. 중요 데이터는 옵션을 false로 하고 Snapshot/Backup을 유지한다.

**이유:** EBS는 영속 스토리지지만 인스턴스 종료 시 자동 삭제 옵션 때문에 데이터가 사라질 수 있다.

**대표 탈락 논리(실제 선지 아님):** Instance Store와 EBS를 혼동해 EBS가 본래 임시라고 결론 내리면 안 된다. Stop과 Terminate도 다르다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q08. ECS 컨테이너 트래픽 90%가 정적 파일 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** 정적 콘텐츠를 S3/CloudFront 등 CDN으로 분리해 배포하고 ALB/ECS는 동적 요청을 담당하도록 한다. 적절한 Cache Behavior를 설정한다.

**이유:** 컨테이너와 로드 밸런서가 정적 파일 요청 대부분을 처리하지 않으므로 네트워크·컴퓨팅 비용과 지연을 줄일 수 있다.

**대표 탈락 논리(실제 선지 아님):** ECS Task 수만 늘리면 정적 파일이 만드는 중복 트래픽 비용은 해결되지 않는다. EFS는 CDN 역할을 하지 않는다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q09. Spread Placement Group에 EC2 15대 분산 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** Rack-level Spread Placement Group은 AZ당 실행 인스턴스 최대 7대이므로 15대를 한 그룹으로 분산하려면 최소 3개 AZ가 필요하다(지원 AZ/용량 전제).

**이유:** 2개 AZ라면 최대 14대이고, 3개 AZ면 최대 21대까지 분산 가능하다.

**대표 탈락 논리(실제 선지 아님):** Partition Placement Group은 별도의 분할 단위 개념이다. Cluster Placement Group은 밀접 배치와 고속 통신에 초점을 둔다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q10. AWS Organizations 여러 계정에서 한 공용 VPC 사용 · 오답

> **판단 확신도:** 높음 · **화면:** 오답

**권장 판단:** VPC 소유 계정에서 AWS RAM(Resource Access Manager)을 사용해 VPC의 Subnet을 같은 조직 내 다른 계정에 공유하는 VPC Sharing을 검토한다.

**이유:** 소유 계정은 네트워크 설정을 관리하고 참여 계정은 공유 서브넷에 자원 배치가 가능하다.

**대표 탈락 논리(실제 선지 아님):** VPC Peering/TGW는 서로 다른 VPC 연결이지 한 VPC에 여러 계정 리소스를 공동 배치하는 VPC Sharing과 다르다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q11. ASG가 오래된 Launch Configuration으로 부적절한 인스턴스 실행 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** Launch Configuration은 생성 후 수정 불가이므로 새 Launch Configuration 또는 Launch Template을 만든 뒤 ASG에 연결한다. 기존 인스턴스 교체에는 Instance Refresh 등을 검토한다.

**이유:** 새로 배포되는 인스턴스에 올바른 유형·AMI·사용자 데이터를 일관되게 적용해야 한다.

**대표 탈락 논리(실제 선지 아님):** 수정한 설정을 ASG에 연결하는 것만으로 이미 실행 중인 모든 인스턴스가 자동 교체되는 것은 아니다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q12. 온프레미스 약 5PB 대량 데이터 이전 · 정답

> **판단 확신도:** 중간 · **화면:** 정답

**권장 판단:** 시험 출제 당시라면 오프라인 물리 데이터 전송을 다루는 AWS Snow Family/Snowmobile을 고려했을 가능성이 높다. 현재 신규 고객은 Snowball Edge를 주문할 수 없으므로 AWS Data Transfer Terminal, DataSync, 네트워크 속도 등을 비교한다.

**이유:** PB급 데이터는 회선 대역폭과 마이그레이션 기한을 함께 계산해 온라인·오프라인 비용/시간을 비교해야 한다.

**대표 탈락 논리(실제 선지 아님):** 오래된 문제의 Snowmobile/Snowball 선지를 현재도 무조건 이용 가능한 상품으로 받아들이면 안 된다.

> [!CAUTION]
> 스냅샷에 선택지가 없고 AWS Snow 서비스 제공 상태가 변경되어, 원래 정답 상품은 복원하지 않는다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q13. EC2·RDS·S3가 1년 이상 지속 운영되며 비용 높음 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** 지속 사용되는 EC2에는 Savings Plans/RI, RDS에는 엔진·조건에 맞는 DB Reserved Instance, S3에는 접근 빈도 기반 Storage Class/Lifecycle을 검토한다.

**이유:** 장기간 안정적인 컴퓨팅 수요는 할인 약정에 적합하고, S3 보관 비용은 데이터 액세스 방식에 따라 줄일 수 있다.

**대표 탈락 논리(실제 선지 아님):** EC2 Savings Plans가 S3 저장 요금을 할인해 준다고 생각하면 안 된다. Spot은 중단 허용 워크로드에서 별도 검토한다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q14. S3의 민감 데이터 탐지와 악의적 활동 파악 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** Amazon Macie는 S3의 PII 등 민감 데이터를 식별·분류하고, Amazon GuardDuty(S3 Protection 등)는 악의적인 S3 API 활동 징후를 탐지한다.

**이유:** 민감 데이터 내용 검사와 위협 활동 탐지는 서로 다른 보안 목표다.

**대표 탈락 논리(실제 선지 아님):** CloudTrail은 API 호출 이력 기록이지 민감 데이터 자동 분류가 아니다. Inspector는 취약점 점검이 중심이다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q15. 인터넷을 거치지 않고 Private VPC에서 SQS 접근 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** SQS용 Interface VPC Endpoint(AWS PrivateLink)를 생성하고 VPC Endpoint의 DNS, Security Group, Endpoint Policy 등을 설정한다.

**이유:** SQS에 대한 AWS API를 사설 연결로 사용하고 인터넷 egress가 불필요해진다.

**대표 탈락 논리(실제 선지 아님):** S3/DynamoDB용 Gateway Endpoint와 혼동하면 안 된다. NAT Gateway는 AWS 퍼블릭 엔드포인트로 나가는 경로다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q16. Elastic Beanstalk 인스턴스 생성 45분 → 2분 미만 · 오답

> **판단 확신도:** 중간 · **화면:** 오답

**권장 판단:** 오래 걸리는 공통 OS 패키지·정적 자산·설치 과정을 미리 Custom AMI에 Bake한다. 기동 시 달라지는 설정은 별도 구성 관리/짧은 부팅 스크립트로 처리한다. (정확히 2개 선지는 미확인)

**이유:** 부팅 시 매번 내려받고 설치하던 시간을 이미지 제작 시점으로 이전하는 것이 핵심이다.

**대표 탈락 논리(실제 선지 아님):** Instance Type만 키우거나 Timeout만 늘리는 것은 구조적 해결이 아니다. 정적 파일 모두를 모든 인스턴스에서 매번 다운로드하는 방식도 비효율적이다.

> [!CAUTION]
> 실제 보기 중 두 번째 정답이 어떤 구성인지 선지가 없어 확정할 수 없다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q17. 온프레미스 NFS 영상 파일을 Direct Connect 통해 EFS로 정기 이전 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** AWS DataSync를 사용한다. 온프레미스 NFS Storage Location과 EFS Location을 만들고 Direct Connect 연결을 경유해 파일을 이동/동기화한다.

**이유:** 기존 NAS/NFS 파일 및 메타데이터 복사를 자동화하는 관리형 데이터 전송 도구다.

**대표 탈락 논리(실제 선지 아님):** Storage Gateway는 하이브리드 파일 접근·캐싱 등 목적, Transfer Family는 SFTP/FTP 프로토콜 접속용으로 구분한다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q18. Direct Connect 연결 데이터 웨어하우스의 시각화 응답 최소화 · 정답

> **판단 확신도:** 중간 · **화면:** 정답

**권장 판단:** 분석 및 시각화 처리를 데이터가 있는 AWS 쪽에 두고, 온프레미스 사용자에게는 큰 원시 쿼리 결과(예: 60MB) 대신 완성된 시각화 페이지/결과(예: 600KB)를 보내는 방식을 고려한다. Amazon QuickSight가 대표 예시다.

**이유:** 데이터를 전부 사용자 쪽으로 이동시키는 것보다 결과만 내려보내면 아웃바운드 데이터가 크게 줄어든다.

**대표 탈락 논리(실제 선지 아님):** Direct Connect를 통한다고 전송량 자체가 줄어드는 것은 아니다. S3에 결과를 저장해도 브라우저에 전체 데이터를 내려받으면 절감이 어렵다.

> [!CAUTION]
> 선지가 없으므로 QuickSight가 실제 단일 정답인지 확정하지 않는다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q19. 48시간 내 Blue/Green 배포 및 사용자 테스트 · 오답

> **판단 확신도:** 중간 · **화면:** 오답

**권장 판단:** Route 53 Weighted Routing/단계적 Traffic Shift 또는 ALB Weighted Target Group 등으로 새 버전으로 보내는 트래픽 비율을 제어하고 실제 사용자의 새 버전 테스트 비율을 높인다. DNS 전환은 TTL 및 캐시 지연을 감안한다.

**이유:** 새 버전에 트래픽을 보내는 비율과 검증 시간을 따로 설계해야 하며 DNS 캐시 때문에 변경이 즉시 모든 사용자에게 적용되지는 않는다.

**대표 탈락 논리(실제 선지 아님):** Failover 정책은 건강 상태 장애 전환 중심, Latency 정책은 최단 지연 리전 선택 중심이다. DNS TTL을 극단적으로 길게 설정하면 전환이 느려진다.

> [!CAUTION]
> “가능한 한 많은 사용자”와 DNS TTL 조건의 정확한 뜻은 숨겨진 선지 확인이 필요하다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q20. Redshift의 1년 이상 오래된 데이터를 S3로 옮기고 함께 SQL 분석 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** Amazon Redshift Spectrum을 사용해 S3에 저장된 외부 테이블 데이터를 Redshift 쿼리에서 참조한다. 기존 Redshift 내부 테이블과 결합 분석할 수 있다.

**이유:** 콜드 데이터를 S3에 두면서 분석용 SQL을 유지하면 저장 비용/운영 부담을 줄일 수 있다.

**대표 탈락 논리(실제 선지 아님):** S3에 내린 모든 데이터를 반드시 다시 Redshift 클러스터 스토리지로 적재할 필요는 없다. Athena도 SQL-on-S3이지만 현재 Redshift와 결합하는 맥락을 확인한다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q21. EC2 웹 서버 ACM 인증서 자동 갱신으로 운영 최소화 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** ACM 공개 인증서를 Application Load Balancer의 HTTPS Listener에 연결하고 TLS 종료를 ALB에서 처리한다. ACM 지원 배포에서는 인증서 자동 갱신을 활용한다.

**이유:** ACM 발급 공인 인증서를 일반 EC2 파일 시스템에 직접 설치하는 방식을 기본으로 지원하는 것은 아니다.

**대표 탈락 논리(실제 선지 아님):** EC2에서 수동으로 SSL 파일을 바꾸는 설계는 번거롭고 장애 가능성이 크다. ACM Private CA 및 수출 가능한 인증서는 별도 조건이다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q22. API Gateway에서 사용자 관리 기반 인증 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** Amazon Cognito User Pools를 API Gateway의 Authorizer와 연동한다(REST의 Cognito User Pool Authorizer 또는 HTTP API의 JWT Authorizer 등의 형태).

**이유:** 사용자 회원가입·로그인·JWT 발급을 중앙 관리한다.

**대표 탈락 논리(실제 선지 아님):** IAM SigV4는 AWS 주체 인증, Lambda Authorizer는 커스텀 로직이 필요할 때 사용한다. Cognito Identity Pool은 AWS 임시 자격 증명이다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q23. EC2 On-Demand + Spot 혼합 ASG 비용 최적화 · 오답

> **판단 확신도:** 높음 · **화면:** 오답

**권장 판단:** ASG의 Mixed Instances Policy를 통해 여러 Instance Type, On-Demand Base/Percentage, Spot Allocation Strategy를 설정하고 Launch Template을 사용한다.

**이유:** 중단 가능 용량은 Spot으로 할인받되 필수 용량은 On-Demand로 확보하는 방식이다.

**대표 탈락 논리(실제 선지 아님):** 하나의 특정 Spot Instance Type에만 의존하면 용량 부족·회수 위험이 커진다. 일반 Launch Configuration만으로 최신 Mixed Instances 옵션을 설명하면 부족하다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q24. EC2 애플리케이션이 무응답이 되면 자동 재시작 · 오답

> **판단 확신도:** 중간 · **화면:** 오답

**권장 판단:** CloudWatch에 애플리케이션 Health/커스텀 지표를 만들어 Alarm이 EC2 재부팅 작업을 트리거하게 하거나, ALB Health Check+ASG의 Replace Unhealthy를 구성하는 방식을 비교한다.

**이유:** EC2 Instance Status Check가 정상이라도 앱 프로세스는 멈출 수 있으므로 모니터링 대상이 중요하다.

**대표 탈락 논리(실제 선지 아님):** EC2 Auto Recovery는 하드웨어/시스템 문제를 다루며 모든 애플리케이션 멈춤을 감지하는 건 아니다. ASG를 쓰면 재부팅이 아니라 교체될 수도 있다.

> [!CAUTION]
> 낮은 비용/운영 부담을 강조했지만 ‘자동 재부팅’인지 ‘비정상 인스턴스 교체’인지 보기 비교가 필요하다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q25. GuardDuty가 직접 분석하는 보안 로그·데이터 소스 · 오답

> **판단 확신도:** 높음 · **화면:** 오답

**권장 판단:** 기본 데이터 소스는 CloudTrail 관리 이벤트, VPC Flow Logs, Route 53 Resolver DNS 쿼리 로그다. 추가 보호 기능으로 S3 Data Events, EKS Audit/Runtime, RDS Login Activity 등도 지원한다.

**이유:** 위협 탐지는 CloudTrail API/네트워크/DNS 활동을 결합하며 일부 추가 보호는 기능 활성화가 필요하다.

**대표 탈락 논리(실제 선지 아님):** 애플리케이션의 모든 CloudWatch Logs나 웹 서버 자체 로그를 GuardDuty 기본 입력으로 자동 수집한다고 이해하면 안 된다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q26. 90% 사용자에게 동일하게 읽히는 API 데이터의 캐싱 · 정답

> **판단 확신도:** 중간 · **화면:** 정답

**권장 판단:** API Gateway REST API Cache, DynamoDB DAX(읽기 패턴·지원 API 조건), CloudFront(S3 정적 콘텐츠) 중 캐시 위치를 요구사항에 맞게 선택한다. 동일 REST API 응답 반복이라면 API Gateway Cache가 직접적인 후보다.

**이유:** 원본 DynamoDB 읽기 부담이 줄고 정적 파일은 CloudFront 캐시로 ECS/EC2 부하 없이 제공한다.

**대표 탈락 논리(실제 선지 아님):** DAX를 켜면 S3 객체까지 캐시되는 것은 아니다. CloudFront가 API Gateway 캐시와 무조건 같은 계층도 아니다.

> [!CAUTION]
> 문제의 최선 답이 API Gateway Cache인지 DAX인지 선지를 확인해야 한다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q27. 외래키·저장 프로시저·보조 인덱스가 필요한 관계형 데이터베이스 · 오답

> **판단 확신도:** 중간 · **화면:** 오답

**권장 판단:** Amazon RDS의 적합한 관계형 엔진 또는 MySQL/PostgreSQL 호환 Aurora를 검토한다. SQL·FK·Stored Procedure 같은 기능은 선택한 DB 엔진/버전의 지원 범위를 확인한다.

**이유:** 복잡한 관계형 제약조건은 관리형 관계형 데이터베이스의 요구사항이다.

**대표 탈락 논리(실제 선지 아님):** DynamoDB는 기본적으로 외래키/SQL 저장 프로시저를 제공하는 RDBMS가 아니다. ElastiCache는 영구 트랜잭션 DB 대체품이 아니다.

> [!CAUTION]
> 두 개를 고르는 문항이라 엔진별 실제 정답은 선택지를 봐야 확정된다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q28. S3 us-west-1에 이미 복사한 1PB를 us-east-1에도 복제 · 오답

> **판단 확신도:** 높음 · **화면:** 오답

**권장 판단:** S3 Cross-Region Replication(CRR) 규칙을 설정하고, **설정 이전부터 존재한 객체**까지 옮겨야 한다면 S3 Batch Replication을 추가한다. 새로 업로드되거나 수정되는 객체는 Live Replication으로 처리한다.

**이유:** CRR은 일반적으로 규칙 설정 후 새 객체·업데이트를 복제하고, 기존 객체 초기 Backfill은 별도의 Batch Replication이 필요하다.

**대표 탈락 논리(실제 선지 아님):** 이미 S3에 있는 데이터를 다시 온프레미스로 다운로드해서 업로드하는 건 비효율적이다. ‘CRR만 켜면 기존 1PB가 자동 복제’라는 오해도 피한다.

> [!CAUTION]
> 두 선택지의 정확한 조합은 미확인이지만 기존/신규 객체 복제 차이는 중요하다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q29. 날씨 관측 데이터 분당 입력, 고가용성 수집·처리·저장 · 오답

> **판단 확신도:** 중간 · **화면:** 오답

**권장 판단:** Kinesis Data Streams로 이벤트를 수집하고 Data Firehose를 통해 S3에 장기 저장하거나, 실시간 분석이 필요하면 Managed Service for Apache Flink 등을 추가한다.

**이유:** 스트리밍 인입, 실시간 계산, 최종 저장은 서로 다른 계층이다.

**대표 탈락 논리(실제 선지 아님):** SQS 단독은 스트림의 순서·재처리·데이터 분석까지 자동 제공하지 않는다. S3 단독 업로드만으로 실시간 파이프라인은 완성되지 않는다.

> [!CAUTION]
> 2개 선택지 중 요구되는 처리/저장 조합은 보기 없이는 확정 불가.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q30. EC2 User Data 설정 두 가지 옳은 설명 · 오답

> **판단 확신도:** 중간 · **화면:** 오답

**권장 판단:** User Data는 인스턴스 시작 시 초기화 스크립트(Cloud-init 등)로 사용할 수 있고, 일반적인 Linux 초기화 스크립트는 기본적으로 첫 부팅에 실행된다. User Data는 부팅 구성 전달이지 비밀정보 저장소가 아니다.

**이유:** 인스턴스를 Stop/Start한다고 기본적으로 모든 User Data 스크립트가 다시 실행되지는 않는다.

**대표 탈락 논리(실제 선지 아님):** 매 부팅 실행을 기본값으로 기억하거나 User Data에 평문 비밀번호를 넣는 것은 위험하다.

> [!CAUTION]
> 두 개 선택 문항인데 보기가 없어 핵심 사실만 검증한다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q31. 개발 계정 사용자가 운영 계정 AWS 리소스 접근 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** 교차 계정 IAM Role을 운영 계정에 만들고 Trust Policy에 개발 계정 주체를 허용하며, 호출자에게 `sts:AssumeRole` 권한을 부여한다.

**이유:** 영구 액세스 키 공유 없이 STS 임시 자격 증명으로 접근하도록 하는 대표 패턴이다.

**대표 탈락 논리(실제 선지 아님):** 운영 계정 IAM User의 비밀번호/액세스 키를 개발자들에게 나눠주는 방식은 부적절하다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q32. HIPAA 의료 데이터의 고가용성 SQL 결과 캐시 · 오답

> **판단 확신도:** 중간 · **화면:** 오답

**권장 판단:** Amazon ElastiCache for Redis/Valkey 계열의 HIPAA 적격 구성, TLS/암호화와 Multi-AZ Automatic Failover를 검토한다. 애플리케이션이 SQL 쿼리 **결과**를 캐시에 저장하는 것이지 Redis가 SQL을 직접 실행하는 것은 아니다.

**이유:** RDS/Oracle/PostgreSQL 등에서 SQL을 실행하고 자주 쓰는 결과를 인메모리로 캐싱해 응답 시간을 줄인다.

**대표 탈락 논리(실제 선지 아님):** DynamoDB/ElastiCache를 아무 SQL 쿼리나 실행하는 RDBMS로 오해하면 안 된다. HIPAA는 서비스 적격성 외에 BAA·구성 준수도 필요하다.

> [!CAUTION]
> 정확한 서비스/엔진 조합은 원래 선택지의 지원 조건에 따라 달라진다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q33. ECS EC2 Launch Type과 Fargate Launch Type 비용 차이 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** EC2 시작 유형은 인스턴스 용량에 대해 비용을 지불하고, Fargate는 Task/Pod에 할당한 vCPU·메모리 등 리소스와 실행 시간에 따라 과금한다.

**이유:** Fargate는 서버 인스턴스를 직접 관리하지 않으므로 운영 부담이 낮을 수 있다.

**대표 탈락 논리(실제 선지 아님):** Fargate가 언제나 절대적으로 더 저렴한 것은 아니고 일정한 대규모 부하는 EC2가 유리할 수 있다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q34. VPC Console Wizard의 지원하지 않는 네트워크 구성 · 오답

> **판단 확신도:** 낮음 · **화면:** 오답

**권장 판단:** 과거 VPC Wizard의 지원 템플릿 종류와 현재 VPC Console Create VPC 경험은 차이가 있다. 문제에 나온 기능이 콘솔 마법사의 기본 지원 옵션인지, 나중에 수동으로 구성해야 하는 기능인지 구별한다.

**이유:** 웹/DB 2계층이라면 퍼블릭 웹 서브넷과 프라이빗 DB 서브넷, 라우팅, NAT 여부를 설계한다.

**대표 탈락 논리(실제 선지 아님):** 이 문제는 과거 콘솔 UI 기능 명칭에 크게 의존하므로 선택지 없이 ‘정답’을 만들어내면 오히려 오학습할 위험이 높다.

> [!CAUTION]
> **원래 선택지 필요.** 구버전 UI 지식이 최신 서비스와 다를 수 있다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q35. 기존·신규 S3 객체 이벤트를 Kinesis Data Streams로 보냄 · 정답

> **판단 확신도:** 중간 · **화면:** 정답

**권장 판단:** S3 ObjectCreated 이벤트를 EventBridge/Lambda 등으로 전달하고 Lambda가 Kinesis PutRecords 등을 호출하도록 구성할 수 있다. **기존 S3 객체**는 Batch Operations/별도 백필 작업으로 다시 읽어 Kinesis로 적재한다.

**이유:** S3 이벤트는 앞으로 발생하는 이벤트 중심이므로 예전에 있던 객체를 전부 스트리밍하려면 초기 데이터 적재 전략이 추가로 필요하다.

**대표 탈락 논리(실제 선지 아님):** S3 Event Notification을 활성화하면 과거 모든 파일이 자동으로 Kinesis에 흘러간다고 생각하면 안 된다.

> [!CAUTION]
> 문항이 매우 오래된 방법을 가정했을 수 있어 선지와 비교해야 한다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q36. 분산 처리 노드 간 네트워크 성능 극대화 · 오답

> **판단 확신도:** 중간 · **화면:** 오답

**권장 판단:** EC2 Cluster Placement Group과 Enhanced Networking(ENA), 필요 시 EFA를 함께 검토한다. HPC 노드 간 최저 지연 통신이 요구되면 EFA가 강력한 후보가 된다.

**이유:** 같은 AZ에서 인스턴스를 가까이 배치하고 지원 네트워크 인터페이스/통신 라이브러리를 써서 통신 지연을 줄일 수 있다.

**대표 탈락 논리(실제 선지 아님):** Spread Placement는 장애 분산이 목표라 Cluster와 다르다. EFA는 모든 워크로드에 필수인 일반 네트워크 카드가 아니다.

> [!CAUTION]
> 문항의 ‘배포 방법’이 Cluster Placement Group 단독을 묻는지, EFA까지 묻는지 보기가 필요하다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q37. IoT 실시간 분석 결과 모바일 사용자에게 푸시 알림 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** Amazon SNS Mobile Push를 통해 APNs/FCM 등 모바일 푸시 서비스로 알림을 전송한다.

**이유:** Kinesis/Flink 등의 분석 결과 이벤트를 사용자 디바이스로 전달하는 배포 계층이다.

**대표 탈락 논리(실제 선지 아님):** SES는 이메일, SQS는 작업 대기열이므로 모바일 푸시 알림의 직접 대체 수단이 아니다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q38. 전 세계 지사의 대형 영상 S3 업로드 속도 개선 (2개) · 오답

> **판단 확신도:** 높음 · **화면:** 오답

**권장 판단:** S3 Transfer Acceleration으로 AWS Edge Location을 활용하고, 대형 단일 객체는 Multipart Upload를 적용하는 조합을 우선 검토한다.

**이유:** 국제 장거리 네트워크 왕복 지연을 줄이고 여러 파트 병렬 업로드로 큰 파일 전송을 개선할 수 있다.

**대표 탈락 논리(실제 선지 아님):** CloudFront는 주로 콘텐츠 다운로드 캐시, Global Accelerator는 일반적인 S3 Upload Accelerator의 직접 대체물이 아니다. Transfer Acceleration은 추가 요금이 있으므로 비용 요건도 본다.

> [!CAUTION]
> 두 개를 고르는 문항의 실제 선지는 확인되지 않았지만 가장 대표적인 조합이다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q39. EC2 애플리케이션이 S3·DynamoDB에 안전하게 접근 · 오답

> **판단 확신도:** 높음 · **화면:** 오답

**권장 판단:** EC2에 필요한 최소 권한 IAM Role을 Instance Profile로 부착하고, 필요 시 S3/DynamoDB Gateway Endpoint를 구성해 사설 경로로 API 접근한다.

**이유:** 애플리케이션에 정적 액세스 키를 하드코딩하지 않고 임시 자격 증명으로 서비스별 권한을 부여한다.

**대표 탈락 논리(실제 선지 아님):** S3 Bucket Policy만으로 DynamoDB 접근 권한까지 얻지 않는다. EC2 Security Group만 열면 AWS API 인가가 해결되는 것도 아니다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q40. AWS Shield Advanced 예상 외 고비용 · 정답

> **판단 확신도:** 중간 · **화면:** 정답

**권장 판단:** Shield Advanced는 상위 보호 수준의 구독 요금과 보호 리소스/데이터 전송 등 관련 요금 구조가 있어 비용을 확인해야 한다. AWS WAF·지원 플랜·DRT 관련 조건도 주의한다.

**이유:** Shield Standard가 기본 보호를 제공하는 것과 달리 Advanced는 유료 고급 보호 서비스다.

**대표 탈락 논리(실제 선지 아님):** ‘Shield는 모두 무료’ 또는 ‘DDoS 공격이 없어도 아무 비용이 없다’라는 가정이 틀리다.

> [!CAUTION]
> 실제 어떤 과금 요소를 묻는지 선택지를 봐야 확정할 수 있다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q41. 글로벌 UDP 라이브 스포츠 애플리케이션 지연 최소화 · 정답(합계상 추정)

> **판단 확신도:** 중간 · **화면:** 정답(총점 합산으로 추정)

**권장 판단:** UDP 대규모 네트워크 서비스에는 Network Load Balancer가 대표 후보이며, 글로벌 사용자 지연 최적화가 요구되면 AWS Global Accelerator와 NLB 조합을 검토한다.

**이유:** CloudFront CDN의 객체 캐시와 낮은 지연의 UDP 네트워크 트래픽 가속은 목적이 다르다.

**대표 탈락 논리(실제 선지 아님):** ALB는 HTTP/HTTPS L7 용도라 임의의 UDP 트래픽을 전달하지 않는다.

> [!CAUTION]
> 문항 상태 아이콘이 화면 오버레이로 가려졌다. 총점과 다른 표시에서 정답으로 추정되지만 독립 확인은 불가능하다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q42. NLB 인스턴스 ID 대상으로 전달하는 로드 밸런싱 원리 · 오답

> **판단 확신도:** 중간 · **화면:** 오답

**권장 판단:** NLB Target Group에 Instance ID를 등록하면 인스턴스의 Primary Private IP로 트래픽을 전달한다. 연결/흐름 별 라우팅은 프로토콜과 네트워크 플로우 정보에 기반하며, 실제 Health Check·등록 상태가 영향을 준다.

**이유:** 대량 TCP/UDP 네트워크 연결 분산은 NLB L4 특성으로 이해한다.

**대표 탈락 논리(실제 선지 아님):** ALB의 URL Path/Host 기반 라우팅이나 쿠키 기반 세션 스티키 방식을 NLB의 일반적인 핵심 분배 원리로 혼동하지 않는다.

> [!CAUTION]
> 선지가 없어 알고리즘의 정확한 이름 및 보기 표현을 검증할 수 없다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q43. 새 개발자가 DynamoDB 테이블 및 데이터 실수 삭제 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** IAM Least Privilege로 필요한 DynamoDB 액션만 허용하고 `DeleteTable`·불필요한 `DeleteItem` 등에 대한 거부 제어를 설계한다. 추가로 PITR/Backup과 CloudTrail 감사를 활용한다.

**이유:** 권한 범위를 최소화해야 사고 예방이 가능하고, 이미 손상된 데이터는 PITR/백업으로 복구한다.

**대표 탈락 논리(실제 선지 아님):** CloudTrail 로그만으로 잘못된 삭제 호출을 차단하는 것은 아니다. DynamoDB 암호화도 삭제 권한 통제가 아니다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q44. API Gateway·Lambda·ElastiCache Redis 사용자 인증 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** Redis/Valkey ACL 사용자·User Group 또는 지원 환경의 AUTH Token, TLS in transit 등을 사용한다. Lambda에서 인증 정보는 Secrets Manager/암호화된 환경설정 등을 통해 안전하게 주입한다.

**이유:** API 사용자 로그인과 Redis 자체 접근 인증은 별도 계층이다.

**대표 탈락 논리(실제 선지 아님):** API Gateway Authorizer를 붙여도 Redis 엔드포인트의 인증이 자동 해결되지 않는다. 비밀번호를 소스 코드에 하드코딩하면 안 된다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q45. S3 업로드 첫 주만 많이 읽고 이후 간헐적 접근하며 즉시 조회 필수 · 오답

> **판단 확신도:** 중간 · **화면:** 오답

**권장 판단:** S3 Intelligent-Tiering은 접근 패턴이 불확실할 때 자동 계층화하는 주요 선택지다. Standard-IA는 즉시 조회가 가능하지만 최소 보관 기간·검색 요금·전환 정책을 비교해야 한다.

**이유:** Intelligent-Tiering은 비접근 30일 경과 후 IA 계층, 90일 후 Archive Instant Access 계층으로 이동할 수 있다.

**대표 탈락 논리(실제 선지 아님):** S3 Glacier Flexible Retrieval/Deep Archive는 즉시 조회 조건과 맞지 않는다. 7일 만에 IA 계층으로 자동 이동한다는 뜻은 아니다.

> [!CAUTION]
> 접근 패턴이 명확히 고정이라면 Standard-IA+Lifecycle도 후보이며, 정확한 비용 우열은 선택지 필요.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q46. NAT Instance vs NAT Gateway 설정 차이 (3개) · 오답

> **판단 확신도:** 중간 · **화면:** 오답

**권장 판단:** NAT Instance는 퍼블릭 서브넷, 올바른 Route, Source/Destination Check 비활성화, SG·관리·확장 설계가 필요하다. 관리형 Public NAT Gateway는 NAT 인스턴스와 달리 SG가 직접 부착되지 않으며 라우팅·EIP 조건을 확인한다.

**이유:** 둘 다 Private Subnet의 아웃바운드 IPv4 인터넷 접속을 제공할 수 있지만 운영·가용성·기능 제약이 다르다.

**대표 탈락 논리(실제 선지 아님):** NAT Gateway에 SG를 직접 추가한다거나 NAT Instance의 Source/Destination Check를 켜둬야 한다고 생각하면 틀리다.

> [!CAUTION]
> 3개 선택형인데 옵션이 없으므로 가능한 3개 정답을 확정해 적지는 않는다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q47. 수시로 변경되는 S3 버킷 설정의 감사·준수성 모니터링 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** AWS Config Recorder/Rules를 활용해 리소스 설정 변경 이력과 규정 준수 상태를 점검한다. CloudTrail로 API 호출 주체도 조사할 수 있다.

**이유:** Config는 설정 상태·컴플라이언스 관점, CloudTrail은 누가 어떤 API를 호출했는지 감사 관점이다.

**대표 탈락 논리(실제 선지 아님):** CloudWatch Metrics만으로 버킷 정책이 어떤 값으로 변했는지 일관되게 추적하기 어렵다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q48. RDS for MySQL 읽기 병목·글로벌 지사, 스키마 변경 없이 성능 개선 · 정답

> **판단 확신도:** 중간 · **화면:** 정답

**권장 판단:** 기존 MySQL 엔진과 테이블 구조를 유지한다면 **RDS Read Replica(필요시 Cross-Region Read Replica)**를 구성하고, 읽기 전용 쿼리를 Replica Endpoint로 보내는 방식을 우선 검토한다. 가용성만 필요한 것은 아니므로 읽기 분산 여부가 핵심이다.

**이유:** 읽기 복제본은 원본 데이터베이스의 읽기 트래픽을 분산한다. 다른 리전의 사용자에게 로컬 읽기 서비스를 제공할 수도 있다. 기존 관계형 스키마의 복잡한 변경이 불필요하다.

**대표 탈락 논리(실제 선지 아님):** 전통적인 RDS Multi-AZ Standby는 읽기 성능 확장을 위한 복제본이 아니다. ElastiCache는 캐시 가능한 읽기를 빠르게 할 수 있어도 모든 쿼리와 스키마 요구를 대체하지 않는다.

> [!CAUTION]
> Aurora MySQL로 엔진을 마이그레이션해 글로벌 읽기를 제공하는 선택지도 있을 수 있다. 원본 보기 없이 RDS Read Replica와 Aurora Global Database 중 **시험 채점 정답**은 결정할 수 없다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q49. Private EC2에서 서비스에 대한 사설 접근을 위한 Gateway Endpoint · 정답

> **판단 확신도:** 중간 · **화면:** 정답

**권장 판단:** 퍼블릭 인터넷/NAT를 거치지 않고 **Amazon S3 또는 DynamoDB**에 VPC에서 접근해야 할 때 **Gateway VPC Endpoint**를 사용할 수 있다. Route Table에 대상 Prefix List 경로가 연동된다.

**이유:** S3와 DynamoDB는 대표적인 Gateway Endpoint 지원 서비스이며, 별도의 인스턴스별 Public IP 없이 접근 가능하다.

**대표 탈락 논리(실제 선지 아님):** 모든 AWS 서비스를 Gateway Endpoint로 연결할 수 있다는 설명은 틀리다. SSM·SNS 등 다수 서비스는 **Interface Endpoint** 방식이 필요하다.

> [!CAUTION]
> 원본 문항은 「게이트웨이 엔드포인트를 지원하는 서비스 두 가지」를 물었다. 예상 조합은 S3와 DynamoDB이나, 보기 자체는 없으므로 선택지 일치 여부는 확인 불가.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q50. 기존 특수 OS·Oracle 라이선스 유지한 AWS 마이그레이션 · 오답

> **판단 확신도:** 중간 · **화면:** 오답

**권장 판단:** 기존 라이선스가 **물리 호스트·코어·소켓 단위**로 묶여 있다면 **EC2 Dedicated Hosts + BYOL(Bring Your Own License)**을 우선 검토한다. 관리 측면에서는 AWS License Manager로 사용량·규칙을 추적할 수 있다. Oracle 및 OS 라이선스 계약의 AWS 사용 가능 조건을 확인해야 한다.

**이유:** Dedicated Host는 물리 호스트에 대한 가시성 및 배치 제어를 제공해 일부 코어·소켓 종속 라이선스의 활용 조건을 충족시키는 데 도움이 된다.

**대표 탈락 논리(실제 선지 아님):** Dedicated Instance는 물리 호스트 수준의 식별·소켓/코어 배치 통제가 요구되는 모든 BYOL 사례에 적합한 대체재가 아니다. AWS가 제공하는 일반 라이선스 포함 AMI를 구매하면 기존 라이선스를 재사용하는 것과 다르다.

> [!CAUTION]
> AWS 기술은 Dedicated Hosts/BYOL이 유력하나, 업체별 재사용 권한과 경제성은 계약 조건에 좌우된다. 원본 보기 없이는 채점 정답 번호를 특정할 수 없다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q51. SQS Standard → FIFO 마이그레이션 체크리스트 (3개 선택) · 오답

> **판단 확신도:** 높음 · **화면:** 오답

**권장 판단:** **기존 Standard Queue를 FIFO로 직접 변환할 수 없으므로** `.fifo` 접미사를 가진 **새 FIFO Queue**를 만들고 생산자·소비자 연결을 전환한다. 생산자는 **`MessageGroupId`**를 지정해야 하며, **`MessageDeduplicationId` 또는 `ContentBasedDeduplication`**을 적절히 구성한다. 기존에 메시지별 DelaySeconds를 사용했다면 FIFO의 대기열 단위 지연 제한도 검토한다.

**이유:** FIFO는 그룹별 순서를 유지하고 중복 전송을 5분 중복 제거 구간에서 방지한다. 따라서 표준 대기열과 달리 메시지 그룹·중복 제거를 명시적으로 설계해야 한다.

**대표 탈락 논리(실제 선지 아님):** 기존 Standard Queue 속성만 변경하면 FIFO가 된다는 것은 틀리다. FIFO가 전송 단계에서 중복을 줄이더라도 소비자 처리까지 항상 정확히 한 번 실행되는 것은 아니므로 멱등성은 여전히 중요하다.

> [!CAUTION]
> **3개 선택형**이지만 선지가 없으므로 「새 FIFO 생성 / MessageGroupId / Deduplication 처리」가 실제 정답 세 가지였는지는 확인 불가. AWS의 공식 전환 체크리스트에는 지연 설정 조정 등도 포함된다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q52. Lambda 서버리스 아키텍처의 필수 특성 (3개 선택) · 오답

> **판단 확신도:** 중간 · **화면:** 오답

**권장 판단:** AWS Lambda는 함수 코드를 이벤트 또는 호출에 따라 실행하고 실행 인프라·확장을 AWS가 관리한다. **일반 Lambda 함수 호출은 최대 15분**, 함수는 **상태 비보존(stateless)**을 전제로 설계하며 필요시 S3·DynamoDB·EFS 등 외부 스토리지를 사용한다. 동시성 한도, VPC 접근 방식, 재시도와 멱등성도 고려한다.

**이유:** 짧은 실행 작업에 적합하고 요청 증가에 따라 실행 환경을 확장한다. 실행 환경이 재사용될 수는 있으나 다음 호출에서도 로컬 상태가 유지된다고 가정하면 안 된다.

**대표 탈락 논리(실제 선지 아님):** Lambda를 실행하려면 EC2 인스턴스를 직접 관리해야 한다는 설명은 틀리다. 모든 함수가 무조건 VPC 내부에서 실행된다거나 인바운드 SSH 접속이 필요한 것도 아니다.

> [!CAUTION]
> 원본은 「3개를 고르라」지만 선지가 없으므로 세 답을 특정하지 못한다. **2026년 현재 Lambda Managed Instances** 등 일부 실행 모델은 일반 함수의 15분 설명과 예외가 있으므로 시험의 문맥을 확인한다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q53. 오래된 서버·기존 라이선스를 AWS 마이그레이션 후에도 계속 사용 · 정답

> **판단 확신도:** 중간 · **화면:** 정답

**권장 판단:** 기존 라이선스를 유지하는 **BYOL** 가능성을 먼저 확인하고, 필요시 **EC2 Dedicated Hosts**로 물리 서버·코어·소켓 관련 라이선스 요구사항을 충족한다. AWS License Manager로 라이선스 관리 자동화를 검토한다.

**이유:** 라이선스 포함 신규 소프트웨어를 무조건 구매하는 것보다, 계약에서 허용하는 기존 권한을 활용하는 게 비용 면에서 유리할 수 있다.

**대표 탈락 논리(실제 선지 아님):** 기존 라이선스가 AWS에 항상 자동 양도된다는 생각은 틀리다. 라이선스 대상과 설치 위치·가상화 조건을 계약별로 확인해야 한다.

> [!CAUTION]
> 문항은 특정 기존 라이선스 보유를 강조한다. Dedicated Host/BYOL이 대표 방향이나 옵션이 없으므로 정확한 정답 문장과 동일한지 여부는 미검증.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q54. 친구의 친구, 좋아요·댓글 등의 복잡한 소셜 연결 탐색 · 오답

> **판단 확신도:** 높음 · **화면:** 오답

**권장 판단:** **Amazon Neptune(그래프 데이터베이스)**가 대표 정답 후보다. 사용자·게시물·댓글을 Node(정점), 친구 관계·좋아요 등을 Edge(간선)로 모델링하고 다단계 관계를 Traversal Query로 찾는다.

**이유:** "A의 친구의 친구가 좋아요를 누른 게시물"은 테이블 한 행을 읽는 문제가 아니라 **관계 자체를 여러 단계 따라가는 질의**다. Neptune이 그래프 관계 탐색에 특화됐다.

**대표 탈락 논리(실제 선지 아님):** DynamoDB는 키 기반 빠른 접근, RDS는 관계형 SQL/JOIN에 적합하지만 매우 복잡한 다중 홉 그래프 순회가 중심인 상황에서는 Neptune을 우선 고려한다. OpenSearch는 텍스트 검색/검색 분석 중심이다.

> [!CAUTION]
> 기존 문서에서는 DynamoDB의 관계 표현과 Neptune의 그래프 조회를 구분한다. 실제 데이터량·액세스 패턴에 따라 다른 DB도 기술적으로 가능하지만, 이 질문의 핵심 키워드는 그래프 관계 탐색이다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q55. 암호화된 EBS 볼륨의 속성 (3개 선택) · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** **EBS 암호화는 저장 데이터(At Rest), EC2 ↔ EBS 간 전송 중 데이터(In Transit), EBS 스냅샷을 보호**한다. 암호화 볼륨의 스냅샷과 그 스냅샷에서 생성한 볼륨에도 암호화가 이어진다. AWS KMS의 **대칭형 키**를 사용하며 EC2에서 일반적인 읽기·쓰기 시 암복호화가 투명하게 처리된다.

**이유:** 디스크 I/O 보호와 스냅샷 보호가 EBS 암호화 기능에 포함되어 애플리케이션이 직접 암호화 코드를 작성할 필요는 없다.

**대표 탈락 논리(실제 선지 아님):** 암호화된 EBS 스냅샷을 누구에게나 공개할 수 있다는 것은 틀리다. EBS 암호화가 OS 접근 권한·DB 사용자 인증이나 HIPAA 준수를 저절로 해결하지는 않는다.

> [!CAUTION]
> 3개 선택형의 원본 선지는 없지만 공식 EBS 문서에서 이 세 보호 범위를 직접 명시한다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q56. S3 로그 파일 덮어쓰기 직후 읽기에서 데이터 불일치 · 오답

> **판단 확신도:** 높음 · **화면:** 오답

**권장 판단:** **현재 Amazon S3는 새로운 PUT뿐 아니라 기존 객체의 덮어쓰기 PUT, DELETE 및 LIST에도 강력한 읽기 후 쓰기 일관성(Strong Read-after-Write Consistency)**을 제공한다. **성공한 PUT 응답 후에 시작된 GET**이라면 최신 객체를 반환한다. 동시성 경합이라면 옛값 또는 새값 중 하나를 받을 수 있으므로 조건을 구분한다.

**이유:** S3의 과거 Eventually Consistent 덮어쓰기 설명은 2020년 변경으로 더 이상 일반적인 정답이 아니다. 덮어쓰기가 완료되기 전에 읽었는지, CDN/애플리케이션 캐시가 중간에 있는지 등을 확인해야 한다.

**대표 탈락 논리(실제 선지 아님):** "새로운 객체만 Strong Consistency, 덮어쓰기·삭제는 Eventual Consistency"라는 설명은 현재 틀리다. S3 객체가 원자적으로 부분 파일을 반환한다는 설명도 틀리다.

> [!CAUTION]
> 문제 지문 자체가 「불일치가 발생할 수 있다」고 서술하지만 현재 S3만으로 그런 지연이 발생한다고 추론하면 안 된다. 최신 S3 일관성 규정과 문제의 작성 시점을 반드시 구분한다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q57. ALB + Multi-AZ EC2 Auto Scaling + Aurora에서 트래픽 급증에 대한 복원력 (2개 선택) · 정답

> **판단 확신도:** 중간 · **화면:** 정답

**권장 판단:** **EC2 Auto Scaling의 다중 AZ와 적절한 Target Tracking / Dynamic Scaling**, ALB 대상 그룹의 Health Check를 검토한다. 데이터베이스 읽기 병목이 있다면 **Aurora Replicas + Reader Endpoint**, 필요시 **Aurora Auto Scaling**으로 읽기 복제본 수를 자동 조정한다.

**이유:** 웹 계층은 ASG의 인스턴스 증감과 ALB 분산으로 급증을 감당하고, DB 읽기 계층은 읽기 트래픽 분산과 Reader 확장으로 병목을 줄일 수 있다.

**대표 탈락 논리(실제 선지 아님):** EC2를 한 AZ에만 크게 늘리는 방식은 AZ 장애 복원력을 해결하지 못한다. Aurora에 Writer 하나만 두고 모든 읽기를 집중시키면 DB 병목이 남는다.

> [!CAUTION]
> 2개 선택형에서 정확한 조합은 선택지·트래픽 종류(읽기/쓰기) 확인이 필요하다. Aurora Replica Auto Scaling은 **읽기 Replica 개수**의 확장이지 Writer 자체의 수평 확장이 아니다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q58. Aurora 읽기 작업이 많고 쓰기 지연 증가 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** Aurora Read Replica를 여러 AZ에 구성하고 Reader Endpoint로 읽기 요청을 분산한다. Writer Endpoint에는 쓰기 트래픽을 전송한다.

**이유:** 읽기 부하를 Writer에서 분리해 쓰기 처리 여유를 확보하는 것이 핵심이다.

**대표 탈락 논리(실제 선지 아님):** RDS Multi-AZ Standby를 읽기 트래픽 전용 Replica로 단정하면 안 된다. 기존 쿼리 자체의 비효율도 별도 점검 대상이다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q59. EC2 웹 계층 → Private RDS PostgreSQL 보안 연결 · 오답

> **판단 확신도:** 높음 · **화면:** 오답

**권장 판단:** RDS Security Group 인바운드에 웹 EC2의 Security Group을 Source로 지정하고 PostgreSQL 포트 5432만 허용한다. 데이터베이스는 Private Subnet에 두고 KMS 저장 시 암호화, DB 인증을 별개로 검토한다.

**이유:** SG 참조는 EC2 IP가 바뀌어도 필요한 웹 계층에서만 접근하도록 하며 DB가 인터넷에 노출되지 않는다.

**대표 탈락 논리(실제 선지 아님):** 웹 서버가 접근해야 한다는 이유만으로 RDS Publicly Accessible를 활성화하거나 0.0.0.0/0에 5432를 열면 안 된다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q60. ALB Health Check에서는 대상 전체 Unhealthy, IP로 직접 접속은 가능 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** ALB Target Group의 Health Check Path/Port/Protocol/Success Code, Target SG가 ALB SG를 허용하는지, 응답의 Host/HTTP Code를 점검한다.

**이유:** 사용자 브라우저가 직접 IP로 접속되는 것과 ELB가 특정 Endpoint를 검사할 때 반환 코드가 정상인지는 다를 수 있다.

**대표 탈락 논리(실제 선지 아님):** 인스턴스가 반드시 죽었다고 단정하거나 자동으로 리스너를 제거하면 안 된다.

> [!CAUTION]
> 2개 선택형에서 어떤 두 항목인지는 선지 필요.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q61. RDS Oracle + PostgreSQL → Redshift 분석, 개발 최소화 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** 현재 AWS는 Amazon RDS for Oracle과 RDS for PostgreSQL을 포함하는 Amazon Redshift Zero-ETL Integrations를 지원한다(지원 리전·엔진 버전 확인). 각각 소스와 Redshift 타깃을 연결해 초기 동기화 후 지속적 복제를 구성한다.

**이유:** 사용자가 ETL 파이프라인 코드를 대량 작성하지 않고 관리형 방식으로 트랜잭션 데이터를 분석용으로 전달한다.

**대표 탈락 논리(실제 선지 아님):** ‘Oracle과 PostgreSQL은 Redshift Zero-ETL에서 무조건 지원되지 않는다’는 과거 설명은 최신 공식 문서와 다를 수 있다.

> [!CAUTION]
> 2026-10 기준 공개 공식 문서의 지원 소스와 호환성에 의존한다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q62. 같은 AWS 계정 내 여러 직원에게 S3 프로젝트별 접근권한 반복 부여 · 오답

> **판단 확신도:** 중간 · **화면:** 오답

**권장 판단:** 공통 권한은 IAM Group/Identity-based Policy로 사용자 집합에 부여하고, 계정·리소스 전체의 조건은 S3 Bucket Policy/Access Point Policy 등 Resource-based Policy로 통제한다.

**이유:** IAM Policy는 주체별 작업 허용을, Bucket Policy는 해당 버킷에 허용되는 주체·조건을 표현해 관리할 수 있다.

**대표 탈락 논리(실제 선지 아님):** SCP는 같은 계정 안에서 각 사용자별 세부 S3 객체 권한을 직접 부여하는 도구가 아니다. 그룹 권한을 부여했다고 모든 객체가 외부에 공개되는 것도 아니다.

> [!CAUTION]
> 문항이 ‘사용자별 접근’을 가장 중요하게 묻는지, 계정과 사용자 두 수준을 모두 묻는지 선지 비교 필요.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q63. 계정 A의 Lambda가 계정 B S3 Bucket에 접근 · 정답

> **판단 확신도:** 높음 · **화면:** 정답

**권장 판단:** 계정 A Lambda Execution Role의 IAM Policy에서 대상 S3 ARN을 허용하고, 계정 B의 S3 Bucket Policy에서 그 Role을 Principal로 허용한다. SSE-KMS 객체라면 Key Policy도 확인한다.

**이유:** 교차 계정에서는 요청 주체의 IAM Allow와 대상 리소스의 Resource Policy를 함께 검토하는 것이 기본이다.

**대표 탈락 논리(실제 선지 아님):** 계정 A Role에 S3 권한만 줬다고 계정 B의 버킷 정책 제한을 무시할 수 있는 것은 아니다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q64. EDA 핫 스트리밍 데이터와 콜드 장기 데이터의 구분 · 정답

> **판단 확신도:** 중간 · **화면:** 정답

**권장 판단:** Hot Data는 Kinesis Data Streams/실시간 처리 서비스로 빠르게 수집·처리하고 Cold Data는 S3에 저장하며 Athena/Glue 등의 배치 분석 도구로 처리하는 아키텍처를 고려한다.

**이유:** 실시간 데이터와 반복 읽기/저장 목적의 과거 데이터는 처리 지연·비용·저장 구조가 다르다.

**대표 탈락 논리(실제 선지 아님):** 무조건 모든 원시 데이터를 초저지연 데이터베이스에 보관할 필요는 없다.

> [!CAUTION]
> 문제가 단일 서비스 선택을 물었으므로 실제 정답은 원래 보기 확인이 필요하다.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

### Q65. S3 파일의 사고 삭제 방지와 규제 준수 (2개) · 정답

> **판단 확신도:** 중간 · **화면:** 정답

**권장 판단:** S3 Versioning으로 이전 버전을 보존하고 S3 Object Lock(필요시 Compliance Mode/Legal Hold)으로 보존 기간 중 변경·삭제를 제한하는 구성을 검토한다. MFA Delete도 버전 영구 삭제 등에 대한 별도 방어 수단이다.

**이유:** Versioning은 삭제/덮어쓰기 복구에, Object Lock은 WORM 기반 보존 의무에 직접 대응한다.

**대표 탈락 논리(실제 선지 아님):** 기본 SSE 암호화는 삭제 방지 기능이 아니다. Lifecycle Expiration은 오히려 삭제 정책이다.

> [!CAUTION]
> 2개 선택 중 Object Lock/Versioning/MFA Delete 어떤 조합이 채점 정답인지는 보기 확인 필요.

**관련 기존 노트:** 문서 마지막의 [문항별 연결 부록](#-2-문항별-기존-github-노트-링크-부록) 참조.

## 🔗 2. 문항별 기존 GitHub 노트 링크 부록

> [!TIP]
> **번호를 먼저 찾고**, 연결된 파일을 열어 해당 개념만 재학습한다. 아래 링크는 이 파일이 위치하는 `02-Exam-Preparation/SAA-C03-Exam-Master-Review/`를 기준으로 `../../01-Cloud-Basics/`를 가리키는 상대 경로이다. GitHub에서 새 폴더를 만들고 전체 파일을 그대로 올리면 작동하도록 작성했다.

| 문항 | 핵심 개념 | 기존 노트 바로가기 |
|---|---|---|
| Q01 | 사내 ↔ VPC Site-to-Site IPsec VPN | [25-16-Site-to-Site-VPN-CGW-VGW-and-CloudHub](../../01-Cloud-Basics/25-VPC/25-16-Site-to-Site-VPN-CGW-VGW-and-CloudHub.md) · [25-18-Direct-Connect-Location-and-Virtual-Interfaces](../../01-Cloud-Basics/25-VPC/25-18-Direct-Connect-Location-and-Virtual-Interfaces.md) |
| Q02 | EC2 CPU 임계값 초과 시 이메일 알림 | [22-04-CloudWatch-Alarms](../../01-Cloud-Basics/22-Monitoring-Audit-Performance/22-04-CloudWatch-Alarms.md) · [22-01-CloudWatch-Metrics-and-Agent](../../01-Cloud-Basics/22-Monitoring-Audit-Performance/22-01-CloudWatch-Metrics-and-Agent.md) · [15-03-Amazon-SNS](../../01-Cloud-Basics/15-AWS-Integration-and-Messaging/15-03-Amazon-SNS.md) |
| Q03 | 갑작스러운 트래픽 급증 시 버퍼링·조절 | [15-02-Amazon-SQS](../../01-Cloud-Basics/15-AWS-Integration-and-Messaging/15-02-Amazon-SQS.md) · [17-05-API-Gateway](../../01-Cloud-Basics/17-Serverless/17-05-API-Gateway.md) · [15-07-SQS-SNS-Kinesis-Comparison](../../01-Cloud-Basics/15-AWS-Integration-and-Messaging/15-07-SQS-SNS-Kinesis-Comparison.md) |
| Q04 | VPC 5개 허브·스포크에서 상호 통신 실패 | [25-11-VPC-Peering-CIDRs-and-Bidirectional-Routes](../../01-Cloud-Basics/25-VPC/25-11-VPC-Peering-CIDRs-and-Bidirectional-Routes.md) · [25-20-Transit-Gateway-Attachments-Routing-RAM-ECMP](../../01-Cloud-Basics/25-VPC/25-20-Transit-Gateway-Attachments-Routing-RAM-ECMP.md) |
| Q05 | 본사 Direct Connect + 지사 Site-to-Site VPN 상호 데이터 교환 | [25-16-Site-to-Site-VPN-CGW-VGW-and-CloudHub](../../01-Cloud-Basics/25-VPC/25-16-Site-to-Site-VPN-CGW-VGW-and-CloudHub.md) · [25-19-Direct-Connect-Gateway-Types-Resiliency-and-VPN-Backup](../../01-Cloud-Basics/25-VPC/25-19-Direct-Connect-Gateway-Types-Resiliency-and-VPN-Backup.md) · [25-20-Transit-Gateway-Attachments-Routing-RAM-ECMP](../../01-Cloud-Basics/25-VPC/25-20-Transit-Gateway-Attachments-Routing-RAM-ECMP.md) |
| Q06 | Kinesis 스트리밍 데이터 다수 소비자의 병렬 처리 | [15-05-Kinesis-Data-Streams](../../01-Cloud-Basics/15-AWS-Integration-and-Messaging/15-05-Kinesis-Data-Streams.md) · [20-08-Apache-Flink-Streaming-Analytics](../../01-Cloud-Basics/20-Data-and-Analytics/20-08-Apache-Flink-Streaming-Analytics.md) |
| Q07 | EC2 종료와 함께 비즈니스 EBS 데이터 소실 | [05-01-EBS-Volumes](../../01-Cloud-Basics/05-EC2-Storage/05-01-EBS-Volumes.md) · [05-02-EBS-Snapshots](../../01-Cloud-Basics/05-EC2-Storage/05-02-EBS-Snapshots.md) |
| Q08 | ECS 컨테이너 트래픽 90%가 정적 파일 | [13-01-CloudFront-Overview](../../01-Cloud-Basics/13-CloudFront-and-Global-Accelerator/13-01-CloudFront-Overview.md) · [16-01-Docker-and-Amazon-ECS](../../01-Cloud-Basics/16-Containers/16-01-Docker-and-Amazon-ECS.md) · [06-02-Application-Load-Balancer](../../01-Cloud-Basics/06-High-Availability-and-Scalability/06-02-Application-Load-Balancer.md) |
| Q09 | Spread Placement Group에 EC2 15대 분산 | [04-09-EC2-Placement-Groups](../../01-Cloud-Basics/04-Amazon-EC2/04-09-EC2-Placement-Groups.md) |
| Q10 | AWS Organizations 여러 계정에서 한 공용 VPC 사용 | [23-01-AWS-Organizations-and-Account-Structure](../../01-Cloud-Basics/23-Advanced-IAM-and-Organizations/23-01-AWS-Organizations-and-Account-Structure.md) · [25-20-Transit-Gateway-Attachments-Routing-RAM-ECMP](../../01-Cloud-Basics/25-VPC/25-20-Transit-Gateway-Attachments-Routing-RAM-ECMP.md) · [25-05-Internet-Gateway-and-Public-Private-Routing](../../01-Cloud-Basics/25-VPC/25-05-Internet-Gateway-and-Public-Private-Routing.md) |
| Q11 | ASG가 오래된 Launch Configuration으로 부적절한 인스턴스 실행 | [06-06-Auto-Scaling-Groups](../../01-Cloud-Basics/06-High-Availability-and-Scalability/06-06-Auto-Scaling-Groups.md) · [04-02-Launching-an-EC2-Instance](../../01-Cloud-Basics/04-Amazon-EC2/04-02-Launching-an-EC2-Instance.md) |
| Q12 | 온프레미스 약 5PB 대량 데이터 이전 | [14-01-AWS-Snow-Family](../../01-Cloud-Basics/14-AWS-Storage-Extra-Features/14-01-AWS-Snow-Family.md) · [26-10-Large-Data-Transfers-VPN-DX-Snowball-DataSync](../../01-Cloud-Basics/26-Disaster-Recovery-and-Migrations/26-10-Large-Data-Transfers-VPN-DX-Snowball-DataSync.md) · [14-05-AWS-DataSync](../../01-Cloud-Basics/14-AWS-Storage-Extra-Features/14-05-AWS-DataSync.md) |
| Q13 | EC2·RDS·S3가 1년 이상 지속 운영되며 비용 높음 | [04-06-EC2-Purchasing-Options](../../01-Cloud-Basics/04-Amazon-EC2/04-06-EC2-Purchasing-Options.md) · [10-06-S3-Storage-Classes](../../01-Cloud-Basics/10-Amazon-S3-Introduction/10-06-S3-Storage-Classes.md) · [11-01-S3-Lifecycle-Rules](../../01-Cloud-Basics/11-Amazon-S3-Advanced/11-01-S3-Lifecycle-Rules.md) |
| Q14 | S3의 민감 데이터 탐지와 악의적 활동 파악 | [24-17-Amazon-Macie-Sensitive-Data-Discovery](../../01-Cloud-Basics/24-Security-and-Encryption/24-17-Amazon-Macie-Sensitive-Data-Discovery.md) · [24-15-Amazon-GuardDuty-Threat-Detection](../../01-Cloud-Basics/24-Security-and-Encryption/24-15-Amazon-GuardDuty-Threat-Detection.md) · [22-07-AWS-CloudTrail](../../01-Cloud-Basics/22-Monitoring-Audit-Performance/22-07-AWS-CloudTrail.md) |
| Q15 | 인터넷을 거치지 않고 Private VPC에서 SQS 접근 | [25-12-VPC-Endpoints-Gateway-Interface-and-PrivateLink](../../01-Cloud-Basics/25-VPC/25-12-VPC-Endpoints-Gateway-Interface-and-PrivateLink.md) · [15-02-Amazon-SQS](../../01-Cloud-Basics/15-AWS-Integration-and-Messaging/15-02-Amazon-SQS.md) |
| Q16 | Elastic Beanstalk 인스턴스 생성 45분 → 2분 미만 | [09-02-Elastic-Beanstalk](../../01-Cloud-Basics/09-Classic-Solutions-Architecture/09-02-Elastic-Beanstalk.md) · [05-03-AMI](../../01-Cloud-Basics/05-EC2-Storage/05-03-AMI.md) · [06-06-Auto-Scaling-Groups](../../01-Cloud-Basics/06-High-Availability-and-Scalability/06-06-Auto-Scaling-Groups.md) |
| Q17 | 온프레미스 NFS 영상 파일을 Direct Connect 통해 EFS로 정기 이전 | [14-05-AWS-DataSync](../../01-Cloud-Basics/14-AWS-Storage-Extra-Features/14-05-AWS-DataSync.md) · [05-08-Amazon-EFS](../../01-Cloud-Basics/05-EC2-Storage/05-08-Amazon-EFS.md) · [25-18-Direct-Connect-Location-and-Virtual-Interfaces](../../01-Cloud-Basics/25-VPC/25-18-Direct-Connect-Location-and-Virtual-Interfaces.md) |
| Q18 | Direct Connect 연결 데이터 웨어하우스의 시각화 응답 최소화 | [20-05-Amazon-QuickSight](../../01-Cloud-Basics/20-Data-and-Analytics/20-05-Amazon-QuickSight.md) · [20-02-Amazon-Redshift](../../01-Cloud-Basics/20-Data-and-Analytics/20-02-Amazon-Redshift.md) · [25-24-AWS-Network-Data-Transfer-Cost-Optimization](../../01-Cloud-Basics/25-VPC/25-24-AWS-Network-Data-Transfer-Cost-Optimization.md) |
| Q19 | 48시간 내 Blue/Green 배포 및 사용자 테스트 | [08-02-Route-53-Routing-Policies](../../01-Cloud-Basics/08-Amazon-Route-53/08-02-Route-53-Routing-Policies.md) · [06-02-Application-Load-Balancer](../../01-Cloud-Basics/06-High-Availability-and-Scalability/06-02-Application-Load-Balancer.md) · [06-05-Elastic-Load-Balancer-Advanced-Features](../../01-Cloud-Basics/06-High-Availability-and-Scalability/06-05-Elastic-Load-Balancer-Advanced-Features.md) |
| Q20 | Redshift의 1년 이상 오래된 데이터를 S3로 옮기고 함께 SQL 분석 | [20-02-Amazon-Redshift](../../01-Cloud-Basics/20-Data-and-Analytics/20-02-Amazon-Redshift.md) · [10-06-S3-Storage-Classes](../../01-Cloud-Basics/10-Amazon-S3-Introduction/10-06-S3-Storage-Classes.md) |
| Q21 | EC2 웹 서버 ACM 인증서 자동 갱신으로 운영 최소화 | [24-10-ACM-TLS-Certificates-and-Service-Integration](../../01-Cloud-Basics/24-Security-and-Encryption/24-10-ACM-TLS-Certificates-and-Service-Integration.md) · [06-02-Application-Load-Balancer](../../01-Cloud-Basics/06-High-Availability-and-Scalability/06-02-Application-Load-Balancer.md) |
| Q22 | API Gateway에서 사용자 관리 기반 인증 | [17-05-API-Gateway](../../01-Cloud-Basics/17-Serverless/17-05-API-Gateway.md) · [17-07-Amazon-Cognito](../../01-Cloud-Basics/17-Serverless/17-07-Amazon-Cognito.md) |
| Q23 | EC2 On-Demand + Spot 혼합 ASG 비용 최적화 | [06-06-Auto-Scaling-Groups](../../01-Cloud-Basics/06-High-Availability-and-Scalability/06-06-Auto-Scaling-Groups.md) · [04-07-EC2-Spot-Instances-and-Spot-Fleet](../../01-Cloud-Basics/04-Amazon-EC2/04-07-EC2-Spot-Instances-and-Spot-Fleet.md) · [04-06-EC2-Purchasing-Options](../../01-Cloud-Basics/04-Amazon-EC2/04-06-EC2-Purchasing-Options.md) |
| Q24 | EC2 애플리케이션이 무응답이 되면 자동 재시작 | [22-04-CloudWatch-Alarms](../../01-Cloud-Basics/22-Monitoring-Audit-Performance/22-04-CloudWatch-Alarms.md) · [22-01-CloudWatch-Metrics-and-Agent](../../01-Cloud-Basics/22-Monitoring-Audit-Performance/22-01-CloudWatch-Metrics-and-Agent.md) · [06-06-Auto-Scaling-Groups](../../01-Cloud-Basics/06-High-Availability-and-Scalability/06-06-Auto-Scaling-Groups.md) |
| Q25 | GuardDuty가 직접 분석하는 보안 로그·데이터 소스 | [24-15-Amazon-GuardDuty-Threat-Detection](../../01-Cloud-Basics/24-Security-and-Encryption/24-15-Amazon-GuardDuty-Threat-Detection.md) · [22-07-AWS-CloudTrail](../../01-Cloud-Basics/22-Monitoring-Audit-Performance/22-07-AWS-CloudTrail.md) · [25-05-Internet-Gateway-and-Public-Private-Routing](../../01-Cloud-Basics/25-VPC/25-05-Internet-Gateway-and-Public-Private-Routing.md) |
| Q26 | 90% 사용자에게 동일하게 읽히는 API 데이터의 캐싱 | [17-05-API-Gateway](../../01-Cloud-Basics/17-Serverless/17-05-API-Gateway.md) · [17-03-DynamoDB](../../01-Cloud-Basics/17-Serverless/17-03-DynamoDB.md) · [13-01-CloudFront-Overview](../../01-Cloud-Basics/13-CloudFront-and-Global-Accelerator/13-01-CloudFront-Overview.md) |
| Q27 | 외래키·저장 프로시저·보조 인덱스가 필요한 관계형 데이터베이스 | [07-01-Amazon-RDS](../../01-Cloud-Basics/07-Databases/07-01-Amazon-RDS.md) · [07-02-Amazon-Aurora](../../01-Cloud-Basics/07-Databases/07-02-Amazon-Aurora.md) · [19-03-Database-Selection-Guide](../../01-Cloud-Basics/19-Databases-in-AWS/19-03-Database-Selection-Guide.md) |
| Q28 | S3 us-west-1에 이미 복사한 1PB를 us-east-1에도 복제 | [10-05-S3-Replication](../../01-Cloud-Basics/10-Amazon-S3-Introduction/10-05-S3-Replication.md) · [11-05-S3-Batch-Operations](../../01-Cloud-Basics/11-Amazon-S3-Advanced/11-05-S3-Batch-Operations.md) |
| Q29 | 날씨 관측 데이터 분당 입력, 고가용성 수집·처리·저장 | [15-05-Kinesis-Data-Streams](../../01-Cloud-Basics/15-AWS-Integration-and-Messaging/15-05-Kinesis-Data-Streams.md) · [15-06-Amazon-Data-Firehose](../../01-Cloud-Basics/15-AWS-Integration-and-Messaging/15-06-Amazon-Data-Firehose.md) · [20-08-Apache-Flink-Streaming-Analytics](../../01-Cloud-Basics/20-Data-and-Analytics/20-08-Apache-Flink-Streaming-Analytics.md) |
| Q30 | EC2 User Data 설정 두 가지 옳은 설명 | [04-02-Launching-an-EC2-Instance](../../01-Cloud-Basics/04-Amazon-EC2/04-02-Launching-an-EC2-Instance.md) · [04-05-EC2-IAM-Roles](../../01-Cloud-Basics/04-Amazon-EC2/04-05-EC2-IAM-Roles.md) |
| Q31 | 개발 계정 사용자가 운영 계정 AWS 리소스 접근 | [23-04-AssumeRole-and-Resource-Based-Policies](../../01-Cloud-Basics/23-Advanced-IAM-and-Organizations/23-04-AssumeRole-and-Resource-Based-Policies.md) · [02-06-IAM-Roles](../../01-Cloud-Basics/02-IAM/02-06-IAM-Roles.md) |
| Q32 | HIPAA 의료 데이터의 고가용성 SQL 결과 캐시 | [07-06-Amazon-ElastiCache](../../01-Cloud-Basics/07-Databases/07-06-Amazon-ElastiCache.md) · [07-01-Amazon-RDS](../../01-Cloud-Basics/07-Databases/07-01-Amazon-RDS.md) |
| Q33 | ECS EC2 Launch Type과 Fargate Launch Type 비용 차이 | [16-01-Docker-and-Amazon-ECS](../../01-Cloud-Basics/16-Containers/16-01-Docker-and-Amazon-ECS.md) · [04-06-EC2-Purchasing-Options](../../01-Cloud-Basics/04-Amazon-EC2/04-06-EC2-Purchasing-Options.md) |
| Q34 | VPC Console Wizard의 지원하지 않는 네트워크 구성 | [25-05-Internet-Gateway-and-Public-Private-Routing](../../01-Cloud-Basics/25-VPC/25-05-Internet-Gateway-and-Public-Private-Routing.md) · [25-08-NAT-Gateway-High-Availability-and-Console-Lab](../../01-Cloud-Basics/25-VPC/25-08-NAT-Gateway-High-Availability-and-Console-Lab.md) |
| Q35 | 기존·신규 S3 객체 이벤트를 Kinesis Data Streams로 보냄 | [11-03-S3-Event-Notifications](../../01-Cloud-Basics/11-Amazon-S3-Advanced/11-03-S3-Event-Notifications.md) · [11-05-S3-Batch-Operations](../../01-Cloud-Basics/11-Amazon-S3-Advanced/11-05-S3-Batch-Operations.md) · [15-05-Kinesis-Data-Streams](../../01-Cloud-Basics/15-AWS-Integration-and-Messaging/15-05-Kinesis-Data-Streams.md) |
| Q36 | 분산 처리 노드 간 네트워크 성능 극대화 | [04-09-EC2-Placement-Groups](../../01-Cloud-Basics/04-Amazon-EC2/04-09-EC2-Placement-Groups.md) · [16-01-Docker-and-Amazon-ECS](../../01-Cloud-Basics/16-Containers/16-01-Docker-and-Amazon-ECS.md) |
| Q37 | IoT 실시간 분석 결과 모바일 사용자에게 푸시 알림 | [15-03-Amazon-SNS](../../01-Cloud-Basics/15-AWS-Integration-and-Messaging/15-03-Amazon-SNS.md) · [15-05-Kinesis-Data-Streams](../../01-Cloud-Basics/15-AWS-Integration-and-Messaging/15-05-Kinesis-Data-Streams.md) |
| Q38 | 전 세계 지사의 대형 영상 S3 업로드 속도 개선 (2개) | [11-04-S3-Performance](../../01-Cloud-Basics/11-Amazon-S3-Advanced/11-04-S3-Performance.md) · [13-08-CloudFront-vs-Global-Accelerator](../../01-Cloud-Basics/13-CloudFront-and-Global-Accelerator/13-08-CloudFront-vs-Global-Accelerator.md) |
| Q39 | EC2 애플리케이션이 S3·DynamoDB에 안전하게 접근 | [04-05-EC2-IAM-Roles](../../01-Cloud-Basics/04-Amazon-EC2/04-05-EC2-IAM-Roles.md) · [25-12-VPC-Endpoints-Gateway-Interface-and-PrivateLink](../../01-Cloud-Basics/25-VPC/25-12-VPC-Endpoints-Gateway-Interface-and-PrivateLink.md) · [10-02-S3-Security](../../01-Cloud-Basics/10-Amazon-S3-Introduction/10-02-S3-Security.md) |
| Q40 | AWS Shield Advanced 예상 외 고비용 | [24-12-AWS-Shield-Standard-and-Advanced](../../01-Cloud-Basics/24-Security-and-Encryption/24-12-AWS-Shield-Standard-and-Advanced.md) |
| Q41 | 글로벌 UDP 라이브 스포츠 애플리케이션 지연 최소화 | [06-03-Network-Load-Balancer](../../01-Cloud-Basics/06-High-Availability-and-Scalability/06-03-Network-Load-Balancer.md) · [13-07-AWS-Global-Accelerator](../../01-Cloud-Basics/13-CloudFront-and-Global-Accelerator/13-07-AWS-Global-Accelerator.md) |
| Q42 | NLB 인스턴스 ID 대상으로 전달하는 로드 밸런싱 원리 | [06-03-Network-Load-Balancer](../../01-Cloud-Basics/06-High-Availability-and-Scalability/06-03-Network-Load-Balancer.md) · [06-05-Elastic-Load-Balancer-Advanced-Features](../../01-Cloud-Basics/06-High-Availability-and-Scalability/06-05-Elastic-Load-Balancer-Advanced-Features.md) |
| Q43 | 새 개발자가 DynamoDB 테이블 및 데이터 실수 삭제 | [02-03-IAM-Policies](../../01-Cloud-Basics/02-IAM/02-03-IAM-Policies.md) · [17-03-DynamoDB](../../01-Cloud-Basics/17-Serverless/17-03-DynamoDB.md) · [22-07-AWS-CloudTrail](../../01-Cloud-Basics/22-Monitoring-Audit-Performance/22-07-AWS-CloudTrail.md) |
| Q44 | API Gateway·Lambda·ElastiCache Redis 사용자 인증 | [07-06-Amazon-ElastiCache](../../01-Cloud-Basics/07-Databases/07-06-Amazon-ElastiCache.md) · [17-07-Amazon-Cognito](../../01-Cloud-Basics/17-Serverless/17-07-Amazon-Cognito.md) · [24-02-AWS-KMS-Keys-Policies-Rotation-and-CLI](../../01-Cloud-Basics/24-Security-and-Encryption/24-02-AWS-KMS-Keys-Policies-Rotation-and-CLI.md) |
| Q45 | S3 업로드 첫 주만 많이 읽고 이후 간헐적 접근하며 즉시 조회 필수 | [10-06-S3-Storage-Classes](../../01-Cloud-Basics/10-Amazon-S3-Introduction/10-06-S3-Storage-Classes.md) · [11-01-S3-Lifecycle-Rules](../../01-Cloud-Basics/11-Amazon-S3-Advanced/11-01-S3-Lifecycle-Rules.md) |
| Q46 | NAT Instance vs NAT Gateway 설정 차이 (3개) | [25-07-NAT-Instance-and-Source-Destination-Check](../../01-Cloud-Basics/25-VPC/25-07-NAT-Instance-and-Source-Destination-Check.md) · [25-08-NAT-Gateway-High-Availability-and-Console-Lab](../../01-Cloud-Basics/25-VPC/25-08-NAT-Gateway-High-Availability-and-Console-Lab.md) · [25-09-Security-Groups-NACLs-and-Ephemeral-Ports](../../01-Cloud-Basics/25-VPC/25-09-Security-Groups-NACLs-and-Ephemeral-Ports.md) |
| Q47 | 수시로 변경되는 S3 버킷 설정의 감사·준수성 모니터링 | [22-08-AWS-Config](../../01-Cloud-Basics/22-Monitoring-Audit-Performance/22-08-AWS-Config.md) · [22-07-AWS-CloudTrail](../../01-Cloud-Basics/22-Monitoring-Audit-Performance/22-07-AWS-CloudTrail.md) · [10-02-S3-Security](../../01-Cloud-Basics/10-Amazon-S3-Introduction/10-02-S3-Security.md) |
| Q48 | RDS MySQL 읽기 확장 / Cross-Region | [07-01-Amazon-RDS](../../01-Cloud-Basics/07-Databases/07-01-Amazon-RDS.md) · [07-02-Amazon-Aurora](../../01-Cloud-Basics/07-Databases/07-02-Amazon-Aurora.md) |
| Q49 | S3·DynamoDB Gateway VPC Endpoint | [25-12-VPC-Endpoints-Gateway-Interface-and-PrivateLink](../../01-Cloud-Basics/25-VPC/25-12-VPC-Endpoints-Gateway-Interface-and-PrivateLink.md) · [25-13-S3-Gateway-Endpoint-Console-Lab](../../01-Cloud-Basics/25-VPC/25-13-S3-Gateway-Endpoint-Console-Lab.md) |
| Q50 | EC2 Dedicated Hosts·BYOL·Oracle 라이선스 | [04-06-EC2-Purchasing-Options](../../01-Cloud-Basics/04-Amazon-EC2/04-06-EC2-Purchasing-Options.md) · [04-03-EC2-Instance-Types](../../01-Cloud-Basics/04-Amazon-EC2/04-03-EC2-Instance-Types.md) |
| Q51 | SQS Standard → FIFO 마이그레이션 | [15-02-Amazon-SQS](../../01-Cloud-Basics/15-AWS-Integration-and-Messaging/15-02-Amazon-SQS.md) · [15-07-SQS-SNS-Kinesis-Comparison](../../01-Cloud-Basics/15-AWS-Integration-and-Messaging/15-07-SQS-SNS-Kinesis-Comparison.md) |
| Q52 | Lambda 서버리스 특성·제한 | [17-01-Serverless-and-Lambda](../../01-Cloud-Basics/17-Serverless/17-01-Serverless-and-Lambda.md) · [17-02-Lambda-Advanced](../../01-Cloud-Basics/17-Serverless/17-02-Lambda-Advanced.md) |
| Q53 | BYOL / Dedicated Hosts와 기존 라이선스 | [04-06-EC2-Purchasing-Options](../../01-Cloud-Basics/04-Amazon-EC2/04-06-EC2-Purchasing-Options.md) |
| Q54 | Amazon Neptune·소셜 그래프 관계 | [19-02-New-Database-Services](../../01-Cloud-Basics/19-Databases-in-AWS/19-02-New-Database-Services.md) · [19-03-Database-Selection-Guide](../../01-Cloud-Basics/19-Databases-in-AWS/19-03-Database-Selection-Guide.md) |
| Q55 | EBS 암호화·KMS·스냅샷 | [05-07-EBS-Encryption](../../01-Cloud-Basics/05-EC2-Storage/05-07-EBS-Encryption.md) · [24-02-AWS-KMS-Keys-Policies-Rotation-and-CLI](../../01-Cloud-Basics/24-Security-and-Encryption/24-02-AWS-KMS-Keys-Policies-Rotation-and-CLI.md) |
| Q56 | S3 Strong Consistency·객체 덮어쓰기 | [10-01-S3-Basics](../../01-Cloud-Basics/10-Amazon-S3-Introduction/10-01-S3-Basics.md) · [11-04-S3-Performance](../../01-Cloud-Basics/11-Amazon-S3-Advanced/11-04-S3-Performance.md) |
| Q57 | ALB·ASG·Aurora Read Replica Auto Scaling | [06-06-Auto-Scaling-Groups](../../01-Cloud-Basics/06-High-Availability-and-Scalability/06-06-Auto-Scaling-Groups.md) · [07-02-Amazon-Aurora](../../01-Cloud-Basics/07-Databases/07-02-Amazon-Aurora.md) · [06-02-Application-Load-Balancer](../../01-Cloud-Basics/06-High-Availability-and-Scalability/06-02-Application-Load-Balancer.md) |
| Q58 | Aurora 읽기 작업이 많고 쓰기 지연 증가 | [07-02-Amazon-Aurora](../../01-Cloud-Basics/07-Databases/07-02-Amazon-Aurora.md) · [07-01-Amazon-RDS](../../01-Cloud-Basics/07-Databases/07-01-Amazon-RDS.md) |
| Q59 | EC2 웹 계층 → Private RDS PostgreSQL 보안 연결 | [25-09-Security-Groups-NACLs-and-Ephemeral-Ports](../../01-Cloud-Basics/25-VPC/25-09-Security-Groups-NACLs-and-Ephemeral-Ports.md) · [07-01-Amazon-RDS](../../01-Cloud-Basics/07-Databases/07-01-Amazon-RDS.md) · [24-02-AWS-KMS-Keys-Policies-Rotation-and-CLI](../../01-Cloud-Basics/24-Security-and-Encryption/24-02-AWS-KMS-Keys-Policies-Rotation-and-CLI.md) |
| Q60 | ALB Health Check에서는 대상 전체 Unhealthy, IP로 직접 접속은 가능 | [06-02-Application-Load-Balancer](../../01-Cloud-Basics/06-High-Availability-and-Scalability/06-02-Application-Load-Balancer.md) · [06-05-Elastic-Load-Balancer-Advanced-Features](../../01-Cloud-Basics/06-High-Availability-and-Scalability/06-05-Elastic-Load-Balancer-Advanced-Features.md) · [25-09-Security-Groups-NACLs-and-Ephemeral-Ports](../../01-Cloud-Basics/25-VPC/25-09-Security-Groups-NACLs-and-Ephemeral-Ports.md) |
| Q61 | RDS Oracle + PostgreSQL → Redshift 분석, 개발 최소화 | [20-02-Amazon-Redshift](../../01-Cloud-Basics/20-Data-and-Analytics/20-02-Amazon-Redshift.md) · [07-01-Amazon-RDS](../../01-Cloud-Basics/07-Databases/07-01-Amazon-RDS.md) |
| Q62 | 같은 AWS 계정 내 여러 직원에게 S3 프로젝트별 접근권한 반복 부여 | [02-03-IAM-Policies](../../01-Cloud-Basics/02-IAM/02-03-IAM-Policies.md) · [10-02-S3-Security](../../01-Cloud-Basics/10-Amazon-S3-Introduction/10-02-S3-Security.md) · [12-07-S3-Access-Points](../../01-Cloud-Basics/12-Amazon-S3-Security/12-07-S3-Access-Points.md) |
| Q63 | 계정 A의 Lambda가 계정 B S3 Bucket에 접근 | [23-04-AssumeRole-and-Resource-Based-Policies](../../01-Cloud-Basics/23-Advanced-IAM-and-Organizations/23-04-AssumeRole-and-Resource-Based-Policies.md) · [10-02-S3-Security](../../01-Cloud-Basics/10-Amazon-S3-Introduction/10-02-S3-Security.md) · [24-02-AWS-KMS-Keys-Policies-Rotation-and-CLI](../../01-Cloud-Basics/24-Security-and-Encryption/24-02-AWS-KMS-Keys-Policies-Rotation-and-CLI.md) |
| Q64 | EDA 핫 스트리밍 데이터와 콜드 장기 데이터의 구분 | [15-05-Kinesis-Data-Streams](../../01-Cloud-Basics/15-AWS-Integration-and-Messaging/15-05-Kinesis-Data-Streams.md) · [15-06-Amazon-Data-Firehose](../../01-Cloud-Basics/15-AWS-Integration-and-Messaging/15-06-Amazon-Data-Firehose.md) · [20-06-AWS-Glue](../../01-Cloud-Basics/20-Data-and-Analytics/20-06-AWS-Glue.md) |
| Q65 | S3 파일의 사고 삭제 방지와 규제 준수 (2개) | [10-04-S3-Versioning](../../01-Cloud-Basics/10-Amazon-S3-Introduction/10-04-S3-Versioning.md) · [12-06-S3-Object-Lock-and-Glacier-Vault-Lock](../../01-Cloud-Basics/12-Amazon-S3-Security/12-06-S3-Object-Lock-and-Glacier-Vault-Lock.md) · [12-03-MFA-Delete](../../01-Cloud-Basics/12-Amazon-S3-Security/12-03-MFA-Delete.md) |


## 🇰🇷 3. 오답 복습 규칙

1. **28개 오답 모두** 정답의 설계 원리와 다른 서비스와의 차이를 자신의 말로 설명한다. 우선 Q50·Q51·Q52·Q54·Q56 같은 신규 확인 오답을 포함한다.
2. 익숙한 서비스 단어가 보여도 반드시 **요구사항 → 서비스 → 왜 다른 서비스가 아닌가** 순서로 복기한다.
3. 각 문항을 최소 두 번 복습하되, 두 번째는 링크를 열지 않고 설명한다.
4. 원본 선지를 확인할 수 있게 되면 **원래 선택한 오답, 실제 정답, 모든 선택지의 판단**을 이 파일에 추가한다.
5. 새 모의고사는 다른 문제로 구성해 반복 학습에 의한 정답 암기 효과를 배제한다.

## 🇰🇷 4. 강의 당시와 달라진 AWS 문서 (중요)

- **S3 CRR**: 설정 이전의 기존 객체는 기본 실시간 복제만으로 채워지지 않는다. **S3 Batch Replication**을 사용한다.
- **RDS Oracle / PostgreSQL → Redshift**: 현재 **Zero-ETL Integrations** 지원 소스에 포함된다. 실제 Region·Engine Version을 확인한다.
- **AWS Snowball Edge**: 현재 **신규 고객에게 제공하지 않는다**. 수 PB 이전 문제에 등장하는 Snow Family 지문은 강의·문제의 출제 연식과 현재 서비스를 구분한다.
- **S3 Intelligent-Tiering**: 7일 후 자동 저비용 계층 이동이 아니다. **연속 미접근 30일 후 Infrequent Access, 90일 후 Archive Instant Access**가 핵심이다.
- **Spread Placement Group**: Rack-level에서는 AZ당 **최대 7개 실행 인스턴스**가 공식 규칙이다.
- **GuardDuty**: 기본 데이터 소스와 선택적으로 사용하는 S3/EKS/RDS 보호 기능을 구분한다.
- **S3 Strong Consistency (Q56)**: 현재 신규·덮어쓰기 PUT, DELETE, LIST에서 강력한 일관성을 제공한다. 성공한 PUT 이후의 GET에서 최신값을 확인할 수 있다.
- **SQS FIFO (Q51)**: Standard Queue를 FIFO로 속성 변경하는 것은 불가능하다. 새 큐 생성과 그룹 ID·중복 제거·생산자 변경을 함께 검토한다.
- **Lambda (Q52)**: 일반 함수의 15분 한도는 핵심이지만 2026년 추가된 실행 모델의 특수 한도와 혼동하지 않는다.

## 📖 검증한 공식 자료

- [S3 strong read-after-write consistency](https://aws.amazon.com/s3/consistency/)
- [SQS Standard to FIFO migration checklist](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/FIFO-queues-moving.html)
- [Amazon Neptune graph and relationships](https://docs.aws.amazon.com/neptune/latest/userguide/graph-get-started.html)
- [EC2 Dedicated Hosts and BYOL](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-hosts-overview.html)
- [EBS encryption details](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-encryption.html)
- [S3 Batch Replication for existing objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-batch-replication-batch.html)
- [Redshift Zero-ETL integrations and supported sources](https://docs.aws.amazon.com/redshift/latest/mgmt/zero-etl-using.html)
- [Snowball Edge availability change](https://docs.aws.amazon.com/snowball/latest/developer-guide/snowball-edge-availability-change.html)
- [S3 Intelligent-Tiering access tiers](https://docs.aws.amazon.com/AmazonS3/latest/userguide/intelligent-tiering-overview.html)
- [EC2 placement group rules](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-strategies.html)
- [GuardDuty data sources](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_data-sources.html)
- [AWS architecture and SAA study source](../../01-Cloud-Basics/README.md)

## 🇯🇵 日本語 Summary

初回模擬試験（37/65、56.9%）の復習ノート。画面で確認できた全65問について、選択肢がないことを明示したうえで、推奨設計・理由・典型的な誤答の見分け方を整理した。Q48〜57も追加で確認済み。各問の概念は付録から既存の GitHub 学習ノートへ移動できる。

## 🇺🇸 English Summary

A post-mortem of the first 65-question SAA-C03 mock exam (37/65, 56.9%). It covers all 65 visible prompts, provides reasoned architectural recommendations, explains typical distractors without fabricating the unavailable answer options, and links each question to existing GitHub notes. Questions 48–57 were added after the missing screenshots arrived; the original answer options remain unavailable.
