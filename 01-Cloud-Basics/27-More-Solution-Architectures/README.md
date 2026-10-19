
# 📚 27. More Solution Architectures

> [!NOTE]
> **AWS SAA-C03 Section Guide** · `GitHub Section 27` · `Udemy Section 29: More Solution Architectures`

## 📌 Overview

이번 섹션에서는 지금까지 학습한 AWS 서비스들을 조합하여 실제 아키텍처를 설계하는 방법을 학습한다.

이벤트 기반 아키텍처, 다계층 캐싱, 네트워크 보안, 고성능 컴퓨팅(HPC), EC2 고가용성 및 장애 복구를 다룬다.

단순히 개별 서비스를 암기하는 것이 아니라 **요청·데이터·장애가 어떤 경로로 흐르는지**, 그리고 **어떤 요구사항에서 어떤 서비스를 선택해야 하는지**를 중심으로 정리한다.

## 🎯 Learning Objectives

1. SQS, SNS, Lambda, EventBridge를 활용한 이벤트 기반 아키텍처와 DLQ·재시도 동작을 이해한다.
2. CloudFront, API Gateway, ElastiCache, DAX의 캐싱 계층과 역할을 구분한다.
3. NACL, Security Group, WAF의 차이와 ALB/NLB/CloudFront를 통과하는 트래픽의 Source IP 변화를 설명한다.
4. HPC에서 EC2, Cluster Placement Group, ENA, EFA, FSx for Lustre, AWS Batch, ParallelCluster의 역할을 이해한다.
5. Elastic IP, ASG, User Data, Lifecycle Hook, EBS Snapshot을 조합한 EC2 장애 복구 아키텍처를 설계한다.

## 📂 Contents

| File | Topic |
|---|---|
| [27-01](./27-01-Event-Driven-Architectures-SQS-SNS-S3-EventBridge.md) | Event-Driven Architectures |
| [27-02](./27-02-Caching-Strategies-CloudFront-API-Gateway-ElastiCache-DAX.md) | Caching Strategies |
| [27-03](./27-03-Networking-Security-Architectures-NACL-SG-ALB-CloudFront.md) | Networking and Security Architectures |
| [27-04](./27-04-High-Performance-Computing-ENA-EFA-Batch-ParallelCluster.md) | High Performance Computing (HPC) |
| [27-05](./27-05-Highly-Available-EC2-EIP-ASG-Lifecycle-Hooks-EBS.md) | Highly Available EC2 Architectures |

## 🧠 Key Concepts

### Event-Driven Architecture
- SQS는 메시지를 보관하며 소비자와 생산자를 분리한다.
- SNS는 하나의 이벤트를 여러 구독자에게 전달할 수 있다.
- EventBridge는 이벤트 패턴에 따라 대상 서비스를 연결한다.
- DLQ와 재시도 정책은 이벤트 처리 구조에 따라 달라진다.

### Caching Strategies
- CloudFront는 사용자와 가까운 Edge Location에서 콘텐츠를 캐싱한다.
- API Gateway REST API는 API 응답 캐싱을 지원한다.
- ElastiCache는 애플리케이션의 데이터베이스 조회 부담을 줄인다.
- DAX는 DynamoDB 전용 인메모리 캐시다.

### Networking & Security
- NACL은 Subnet 수준에서 Allow/Deny 규칙을 적용한다.
- Security Group은 ENI 수준에서 Allow 규칙을 적용한다.
- ALB는 클라이언트 연결을 종료하고 백엔드에 새 연결을 생성한다.
- CloudFront, ALB, NLB 구성에 따라 실제 Source IP가 달라진다.
- AWS WAF는 HTTP/HTTPS 요청을 분석하고 차단한다.

### High Performance Computing
- Cluster Placement Group은 인스턴스 간 네트워크 지연을 줄이는 데 활용된다.
- ENA는 일반적인 고성능 네트워킹을 제공한다.
- EFA는 HPC의 노드 간 고성능 통신을 지원한다.
- FSx for Lustre는 HPC용 병렬 파일 시스템이다.
- AWS Batch는 계산 작업 실행을 관리한다.
- AWS ParallelCluster는 HPC 클러스터 구축과 관리를 자동화한다.

### Highly Available EC2
- ASG는 비정상 EC2 인스턴스를 자동으로 교체할 수 있다.
- EC2 User Data는 인스턴스 시작 시 초기화 작업을 수행한다.
- Lifecycle Hook은 ASG의 시작·종료 과정에서 추가 작업을 수행할 시간을 제공한다.
- EBS Snapshot을 이용해 다른 AZ에 새 EBS 볼륨을 복원할 수 있다.
- Elastic IP 재연결과 데이터 복구는 별도로 고려해야 한다.

## 🎯 SAA-C03 Exam Focus

| Requirement | AWS Service / Architecture |
|---|---|
| 메시지 기반 비동기 처리 | SQS + Lambda |
| 여러 소비자에게 이벤트 전달 | SNS Fan-out |
| 이벤트 패턴 기반 라우팅 | EventBridge |
| 글로벌 콘텐츠 캐싱 | CloudFront |
| DynamoDB 조회 가속 | DAX |
| 특정 IP 차단 | NACL / AWS WAF |
| 웹 애플리케이션 공격 차단 | AWS WAF |
| HPC 노드 간 고성능 통신 | EFA |
| HPC 병렬 파일 시스템 | FSx for Lustre |
| 대량 계산 작업 실행 | AWS Batch |
| HPC 클러스터 구축 | AWS ParallelCluster |
| EC2 장애 시 자동 교체 | Auto Scaling Group |
| EC2 시작·종료 과정의 추가 작업 | Lifecycle Hook |
| EBS 데이터의 다른 AZ 복구 | EBS Snapshot |

## 🛠️ Study Notes

- 총 5개 학습 노트로 구성한다.
- 각 문서에는 접기형 복습 문제 5개가 포함된다.
- 강의 대본을 기반으로 AWS 서비스의 동작 흐름을 재구성한다.
- 개별 서비스보다는 서비스 간 연결과 아키텍처 선택 기준을 강조한다.
- 오래된 강의 설명과 현재 AWS 기능 사이의 차이를 구분한다.

## 🇯🇵 日本語 Summary

このセクションでは、既存の AWS サービスを組み合わせたソリューションアーキテクチャを学ぶ。

イベント駆動アーキテクチャ、キャッシュ戦略、ネットワークセキュリティ、HPC、EC2 の高可用性とフェイルオーバーを扱う。

AWS Batch と AWS ParallelCluster の役割、ENA と EFA の違い、Auto Scaling Group と Lifecycle Hook を活用した復旧方法を理解する。

## 🇺🇸 English Summary

This section focuses on designing solution architectures by combining existing AWS services.

Topics include event-driven architectures, caching strategies, network security, high-performance computing, and highly available EC2 architectures.

Key objectives are understanding service interactions, choosing appropriate architectures, and automating failover and recovery.
