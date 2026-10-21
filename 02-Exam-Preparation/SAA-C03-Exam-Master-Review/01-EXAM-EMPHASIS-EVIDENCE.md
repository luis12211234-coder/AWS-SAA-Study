# 📌 SAA-C03 강사 시험 강조 및 출제 경향 조사 기록

> [!NOTE]
> **2026-10-21 조사 초안**. 강사 발언, 공식 시험 가이드, 후기, 학습 보강 우선순위는 **증거 수준이 다르다**. '시험에 나온다'는 강사의 발언 자체가 개별 출제 빈도나 확정 출제를 보장하지 않는다.

## 🇰🇷 1. 분류 규칙

- **A. [공식]**: AWS SAA-C03 공식 시험 도메인/세부 기술에 명시되어 있는 내용. 우선순위가 가장 높다.
- **B. [강사-대본확인]**: 현재 제공된 대본에서 실제로 강사가 시험 가능성을 언급하는 내용.
- **C. [강사-과거대화검색]**: 과거 AWS 학습 대화 검색에서 강사의 시험 강조 문맥이 보고·요약된 내용. 원문 전체를 재확인하지 못한 일부 사례를 포함한다.
- **D. [응시후기]**: 실명/시험 버전이 확인되지 않을 수 있는 공개 커뮤니티 경험담. 대표 표본이 아니고 본시험의 실제 빈도·문항을 보장하지 않음.

## 🇰🇷 2. A. 공식 시험 가이드: 우선순위의 기준

| 영역 | 공식 비중 | 대표 업무 |
|---|---:|---|
| 보안 설계 | 30% | IAM·역할·SCP·네트워크·KMS·리소스 보안 |
| 복원력 설계 | 26% | 확장·느슨한 결합·장애 조치·DR |
| 고성능 설계 | 24% | 스토리지·컴퓨팅·DB·네트워크·수집/분석 |
| 비용 최적화 | 20% | 스토리지·컴퓨팅·DB·네트워크 비용 설계 |

공식 가이드에는 `DataSync`, `Glue`, `Batch`, `Cognito`, `SQS`, `Fargate`, `CloudFront`, `VPN`, `Direct Connect`, `Cost Explorer`, `Budgets`, `Cost and Usage Report` 등의 개념과 서비스가 각 도메인의 예시로 실려 있다. **목록에 있다는 이유만으로 각각 최소 한 문제가 출제된다는 의미는 아니다.**

## 🇰🇷 3. B. 현재 제공 대본에서 직접 확인한 강사의 강조

| 대상 | 강사의 요지 | 마스터 정리본 반영 |
|---|---|---|
| Cost Explorer | 시험에서 비용 분석 사용 사례가 나올 수 있다고 언급 | 분석·예측 vs Budgets/Anomaly 비교 |
| AWS Outposts | 하이브리드 클라우드/온프레미스 사용 사례 출제 가능 | 온프레미스의 AWS 인프라 |
| AWS Batch | 장시간 대규모 배치와 Lambda 15분 비교를 알면 충분 | Job Queue/Definition/Environment 비교 |
| Amazon AppFlow | Salesforce 사례가 시험에 나올 수 있다고 언급 | SaaS→S3/Redshift 통합 |
| Instance Scheduler | CloudFormation 기반 자동 시작/중지 아이디어 출제 가능 | Cost/Operations 구분 |
| AWS Systems Manager | 운영 기능은 일반적인 목적·사용 사례 수준 중심 | Session Manager vs Run Command vs Patch |
| AWS Well-Architected Framework | 여섯 기둥과 Tool을 알아두어야 한다고 언급 | 설계 리뷰 vs 계정 상태 점검 |
| Trusted Advisor | 지원 범위·계정 점검 카테고리 중요 | Tool과의 선택 구분 |
| 시험 가이드 자체 | 보안 30%, 복원력 26%, 성능 24%, 비용 20%, 모두 답변 | 기본 분량 배분 및 소거법 |

## 🇰🇷 4. C. 과거 대화 검색으로 확인한 강사 강조 문맥

| 주제 | 검색으로 확인된 정리/학습 문맥 | 마스터에 반영한 포인트 |
|---|---|---|
| IAM Condition과 S3 ARN | 버킷 ARN과 객체 ARN(`/*`) 구분이 시험 포인트로 다뤄짐 | `ListBucket` vs `GetObject` 리소스 식별 |
| EC2 Spot | Persistent Spot Request의 종료 절차·Spot Fleet 전략이 출제 가능으로 요약됨 | Spot 회수와 Request 처리 |
| DynamoDB | Item 400KB, Partition/Sort Key, Provisioned vs On-Demand 핵심 | Query/Scan, GSI/LSI와 연결 |
| Glue/Lake Formation | ETL→Parquet, Job Bookmarks, Crawler/Catalog, 중앙 거버넌스의 시험 강조 문맥 | 분석 서비스 비교 |
| ElastiCache | Redis Sorted Sets 기반 리더보드, 캐시 패턴의 시험 관련 설명 | ElastiCache vs DAX, Lazy Loading/Write Through |
| Security 섹션 | KMS·암호화·Secrets Manager·WAF·GuardDuty·Inspector·Macie 등이 중요 | 보안 영역 최우선 독립 설명 |
| OAC와 Bucket Policy | CloudFront 원본 접근과 최종 사용자 접근 구분을 질의·응답 | OAC vs Signed URL vs Cognito |

> **검색 한계:** 과거 모든 강의 대본 원문이 동일한 검색 범위로 반환되지는 않았다. 위 C항목은 검색에 나타난 학습 정리 및 대화 문맥을 바탕으로 하며, 강사의 문장을 정확히 인용하거나 '모든 강사 강조사항을 완전 수집했다'고 주장하지 않는다. 나중에 원본 대본을 더 찾으면 이 표를 계속 확장한다.

## 🇰🇷 5. D. 2026년 공개 응시 후기에서 반복된 인상

2026년 Reddit r/AWSCertifications 등에서 자발적으로 공유된 후기에서는 다음 어려움이 반복됐다.

- VPC: Peering/Transit Gateway, NAT/Endpoints, VPN/Direct Connect, 네트워크 비용.
- Database: RDS Multi-AZ vs Read Replica, DynamoDB/SQL 선택, 캐시.
- Storage: S3 클래스, EBS/EFS/FSx, 내구성·비용·접근 패턴.
- HA/Event: Multi-AZ + ALB/ASG, SNS/SQS/Lambda의 장애와 재시도.
- 그 밖에 ECS/EKS, AWS Batch, DataSync/ETL, Organizations를 언급한 후기 존재.

**통계적 주의:** 자기선택 편향이 있는 후기다. 어떤 응시자는 VPC 문제가 많았고, 다른 응시자는 컨테이너가 많았다고 한다. 이를 'VPC 몇 문제, ECS 몇 문제 확정'이라고 해석하지 않는다.

## 🎯 6. 우선순위 결론

1. **Tier 1:** IAM/S3/KMS + VPC + ALB/ASG + RDS/Aurora/DynamoDB + S3 클래스·스토리지 + SQS/SNS/Lambda + DR + 비용.
2. **Tier 2:** 컨테이너·API Gateway·CloudFront·Route 53·DataSync/Glue·DMS·캐시·Kinesis 등 공식 가이드에 등장하는 응용.
3. **Tier 3:** Well-Architected Tool, Trusted Advisor, Outposts, AppFlow, Instance Scheduler 등 대표 사용 사례.

Tier는 **학습 효율 기준**이지 AWS가 공개한 세부 출제 확률이 아니다.

## 🔗 자료

- [AWS 공식 SAA-C03 시험 가이드](https://docs.aws.amazon.com/ko_kr/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03.html)
- [2026-02 응시 후기](https://www.reddit.com/r/AWSCertifications/comments/1r73smm/)
- [2026-06 최근 난이도 토론](https://www.reddit.com/r/AWSCertifications/comments/1ufhk3s/)
- [2026-08 합격 후기](https://www.reddit.com/r/AWSCertifications/comments/1vqnid7/)
- [2026-10 합격 후기](https://www.reddit.com/r/AWSCertifications/comments/1wvzteq/)
