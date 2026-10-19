# 📘 27-04. High Performance Computing (HPC)

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `HPC의 목적과 데이터 이전`, `컴퓨팅 및 배치`, `ENI vs ENA vs EFA`, `스토리지 선택`

## 🇰🇷 1. HPC의 목적과 데이터 이전

HPC는 많은 CPU/GPU 계산 자원을 병렬 활용해 유전체 분석, 기상 예측, 금융 위험 모델링, 딥러닝 등 계산 시간을 줄인다. 필요할 때 생성하고 종료하여 사용량에 따라 지불한다. Direct Connect는 전용 연결, DataSync는 파일/객체 데이터 이전, 물리 장치 이전은 과거 Snowball/Snowmobile 강의 맥락이다. Snowmobile은 종료됐고 Snowball Edge는 신규 고객에게 제공되지 않는다.

## 🇰🇷 2. 컴퓨팅 및 배치

CPU/GPU 최적화 EC2, 중단 허용 계산의 Spot/Spot Fleet, 수요에 따른 확장을 활용한다. Cluster Placement Group은 동일 AZ에서 인스턴스를 네트워크 성능에 유리하게 배치하며 반드시 같은 물리 랙을 뜻하지 않는다.

## 🇰🇷 3. ENI vs ENA vs EFA

ENI는 IP/SG/MAC 등 네트워크 속성을 갖는 가상 네트워크 인터페이스다. ENA는 SR-IOV 기반 Enhanced Networking으로 대역폭/PPS 향상과 지연 감소를 제공한다. Intel 82599 VF는 구형 방식이다. EFA는 HPC 노드 간 통신에 적합하며 지원되는 Linux 환경에서 Libfabric과 MPI 애플리케이션을 통한 OS-bypass 통신이 가능하다. MPI는 메시지 교환 표준, Libfabric은 고성능 통신 라이브러리다.

## 🇰🇷 4. 스토리지 선택

EBS io2 Block Express는 고성능 영속 블록 볼륨, Instance Store는 빠르지만 임시 로컬 스토리지다. S3는 대규모 객체 보관, EFS는 공유 파일 시스템이다. FSx for Lustre는 HPC용 병렬 파일 시스템으로 다수 EC2가 빠르게 파일을 공유하며 S3 연동은 선택적이다. EFS의 Provisioned Throughput은 Provisioned IOPS와 다르다.

## 🇰🇷 5. AWS Batch

대량 계산 작업을 제출하면 Job Queue와 스케줄링, 컴퓨팅 환경을 이용해 실행을 관리한다. 여러 독립 작업 및 Multi-node Parallel Job도 지원한다. 작업 정의·권한·컴퓨팅 환경 등의 설정은 필요하다.

## 🇰🇷 6. AWS ParallelCluster

설정 파일을 이용해 EC2 계산 노드, VPC/서브넷, EFA, FSx for Lustre, Slurm 등의 HPC 클러스터를 구축·관리하는 오픈 소스 도구다. Batch는 **작업 중심 자동화**, ParallelCluster는 **클러스터 환경 중심 자동화**다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **HPC의 목적과 데이터 이전:** HPC는 많은 CPU/GPU 계산 자원을 병렬 활용해 유전체 분석, 기상 예측, 금융 위험 모델링, 딥러닝 등 계산 시간을 줄인다.
- **컴퓨팅 및 배치:** CPU/GPU 최적화 EC2, 중단 허용 계산의 Spot/Spot Fleet, 수요에 따른 확장을 활용한다.
- **ENI vs ENA vs EFA:** ENI는 IP/SG/MAC 등 네트워크 속성을 갖는 가상 네트워크 인터페이스다.
- **스토리지 선택:** EBS io2 Block Express는 고성능 영속 블록 볼륨, Instance Store는 빠르지만 임시 로컬 스토리지다.
- **AWS Batch:** 대량 계산 작업을 제출하면 Job Queue와 스케줄링, 컴퓨팅 환경을 이용해 실행을 관리한다.
- **AWS ParallelCluster:** 설정 파일을 이용해 EC2 계산 노드, VPC/서브넷, EFA, FSx for Lustre, Slurm 등의 HPC 클러스터를 구축·관리하는 오픈 소스 도구다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. ENI, ENA, EFA를 구분하면?

<details>
<summary>정답 및 해설 보기</summary>

ENI=가상 인터페이스, ENA=일반 고성능 네트워킹, EFA=HPC 노드 간 저지연 통신.

</details>

### Q2. HPC의 고성능 병렬 파일 시스템은?

<details>
<summary>정답 및 해설 보기</summary>

FSx for Lustre.

</details>

### Q3. 동일 AZ 내 EC2 간 지연을 줄이는 배치 옵션은?

<details>
<summary>정답 및 해설 보기</summary>

Cluster Placement Group.

</details>

### Q4. 대량 계산 작업을 자동으로 스케줄링·실행하려면?

<details>
<summary>정답 및 해설 보기</summary>

AWS Batch.

</details>

### Q5. 설정 파일로 Slurm/EFA 기반 HPC 클러스터를 구축하려면?

<details>
<summary>정답 및 해설 보기</summary>

AWS ParallelCluster.

</details>

## 🇯🇵 日本語 Summary

High Performance Computing (HPC)：AWS SAA-C03 では、要件に応じてサービスを組み合わせ、可用性・性能・運用自動化を設計する。主要な選択基準と障害時の動作を理解する。

## 🇺🇸 English Summary

High Performance Computing (HPC): Understand the architecture, trade-offs, failure handling, and SAA-C03 service selection criteria. Combine managed AWS services based on performance, availability, and operational requirements.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| HPC의 목적과 데이터 이전 | HPC의 목적과 데이터 이전 | HPC의 목적과 데이터 이전 |
| 컴퓨팅 및 배치 | 컴퓨팅 및 배치 | 컴퓨팅 및 배치 |
| ENI vs ENA vs EFA | ENI vs ENA vs EFA | ENI vs ENA vs EFA |
| 스토리지 선택 | 스토리지 선택 | 스토리지 선택 |
| AWS Batch | AWS Batch | AWS Batch |
| AWS ParallelCluster | AWS ParallelCluster | AWS ParallelCluster |
