
# 📘 28-07. AWS Batch & Lambda Comparison

> [!NOTE]
> **AWS SAA-C03 Study Note** · `GitHub Section 28` · `Udemy Section 30`
> **핵심 키워드:** Batch Processing, Job Definition, Job Queue, Compute Environment, ECS, EC2 Spot, Fargate, Lambda

## 🇰🇷 1. 배치 처리(Batch Processing)란?

**배치 작업은 시작과 끝이 명확한 처리 작업**이다. 예를 들어 매일 새벽 사진 10만 장을 압축하거나 일주일치 로그를 분석하는 작업이다. 24시간 연결을 유지하는 실시간 스트리밍 서버와 다르다.

**AWS Batch**는 이런 작업을 **대기열에 넣고 적절한 컴퓨팅 자원을 배정하여 실행**하는 완전 관리형 배치 처리 서비스다. 사용자는 주로 작업 코드·컨테이너·필요 자원을 정의하고, Batch가 작업 예약과 컴퓨팅 용량 관리를 지원한다.

## 🇰🇷 2. 가장 중요한 세 가지 구성 요소

이 세 가지를 구분하면 서비스 구조가 거의 이해된다.

| 구성 요소 | 한마디로 | 실제 내용 |
|---|---|---|
| **Job Definition** | **어떻게** 실행할까? | 컨테이너 이미지, vCPU, 메모리, 환경 변수, IAM Role 등 |
| **Job Queue** | **무슨 작업을 기다리게** 할까? | 제출된 작업을 대기시키고 우선순위·스케줄링 기준 적용 |
| **Compute Environment** | **어디서** 실행할까? | EC2, Spot, ECS 기반 Fargate 등 실제 실행 환경 |

```text
개발자: 이미지 압축 컨테이너 준비 (예: ECR)
                   ↓
          Job Definition 작성
   (이미지: compressor, vCPU: 2, 메모리: 4GiB)
                   ↓
       SubmitJob → Job Queue
                   ↓ 작업이 실행 가능해질 때
         AWS Batch Scheduler
                   ↓
       Compute Environment
     (EC2 / Spot / ECS Fargate)
                   ↓
     컨테이너가 이미지를 압축
                   ↓
          결과를 S3에 저장
```

**주의:** Job Queue 자체가 서버를 실행하는 것은 아니다. Queue는 작업을 기다리게 하고, 실제 실행은 연결된 Compute Environment에서 이뤄진다.

## 🇰🇷 3. S3 이미지 처리 아키텍처 예시

예시 요구사항: **매일 S3에 쌓인 이미지 10만 장을 밤에 일괄 변환**해야 한다.

1. 변환 코드와 라이브러리를 Docker 이미지로 묶고 Amazon ECR에 보관한다.
2. 변환할 이미지 위치를 Job 파라미터로 전달하도록 Job Definition을 만든다.
3. 작업 제출을 자동화하려면 **EventBridge, Lambda 또는 Step Functions 등**을 이용해 `SubmitJob`을 호출할 수 있다.
4. 제출된 작업은 Job Queue에 대기한다.
5. AWS Batch가 Compute Environment에 맞춰 작업을 배정한다.
6. 컨테이너가 S3에서 데이터를 읽고 처리 결과를 S3에 기록한다.
7. 종료된 작업은 상태 및 로그를 확인하고, 실패 시 재시도 정책을 적용한다.

```text
S3 입력 이미지
    ↓ (이벤트 / 일정)
EventBridge 또는 Lambda
    ↓ SubmitJob API
AWS Batch Job Queue
    ↓
Compute Environment → 컨테이너 실행
    ↓
S3 처리 결과
```

**S3에 객체를 업로드했다고 Batch가 무조건 자동 시작하는 것은 아니다.** 별도의 트리거·작업 제출 연결이 필요하다.

## 🇰🇷 4. EC2와 Fargate 실행 방식

- **EC2 기반 Compute Environment:** 일반 EC2 또는 Spot 인스턴스를 사용한다. 높은 CPU·메모리 요구, 특정 인스턴스 유형 및 커스텀 환경이 필요한 작업에 고려한다.
- **ECS Fargate 기반 Compute Environment:** 서버 인스턴스를 직접 관리하지 않고 컨테이너 배치 작업을 실행할 수 있다.
- **EKS 기반 AWS Batch:** Kubernetes 기반 배치 작업도 지원하지만, **Batch용 Fargate Compute Environment는 ECS 기반에서 지원**되며 EKS 기반 Batch의 Fargate와 혼동하면 안 된다.

EC2·Fargate는 리소스 한도와 지원 기능이 서로 다르다. EC2와 Fargate Compute Environment를 **같은 Job Queue에 혼합할 수 없다.**

## 🇰🇷 5. AWS Lambda와 비교

| 구분 | Lambda | AWS Batch |
|---|---|---|
| 주요 목적 | 짧은 이벤트 기반 함수 | 대량·장시간 배치 작업 |
| 실행 제한 | **호출당 최대 15분** | Batch 자체의 고정 15분 제한은 없음. 작업 타임아웃과 컴퓨팅 환경 제한은 적용 가능 |
| 패키징 | 함수 코드 또는 Lambda 컨테이너 이미지 | 작업 정의에 따른 컨테이너 중심 |
| 실행 환경 | 서버리스 함수 실행 | EC2/Spot/Fargate 등 선택 |
| 예시 | S3에 파일 1개 업로드 후 썸네일 생성 | 수만 개 파일의 야간 변환·시뮬레이션 |

> [!IMPORTANT]
> **'15분 초과' 하나만으로 무조건 Batch라는 뜻은 아니다.** Step Functions로 여러 Lambda 호출을 오케스트레이션할 수도 있다. 하지만 **장시간 실행하는 하나의 컨테이너 작업이나 대규모 작업 큐**라는 문맥이면 Batch가 매우 강한 후보가 된다.

## 🇰🇷 6. 운영 시 알아둘 포인트

- **IAM:** Job이 S3를 읽고 쓰려면 적절한 IAM 권한이 필요하다.
- **로그:** 컨테이너 출력과 상태를 모니터링하고 실패 원인을 확인한다.
- **Retry / Timeout:** 일시 장애에 대비한 재시도 횟수와 타임아웃을 설정할 수 있다.
- **비용:** 필요할 때만 작업을 실행하고 Spot을 사용할 수 있으면 비용 최적화에 유리하다. Spot 중단과 재처리 가능성도 고려한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> **대규모·장시간 컨테이너 작업을 큐에 쌓아 실행 → AWS Batch.**

- Job Definition = **실행 방법**, Job Queue = **대기**, Compute Environment = **실행 자원**.
- Lambda **최대 15분**과 대비하여 장시간 작업에 적합.
- 강의의 'Batch = EC2만 사용'은 오래된 설명. **ECS Fargate** 지원.
- Batch가 S3 업로드를 알아서 감지해 작업을 제출하는 것은 아님. **SubmitJob 트리거**를 연결해야 한다.
- Batch는 배치 처리용이며 지속 실행 웹 서버나 실시간 스트리밍 서비스와 구분한다.

## 📝 Review Questions

> [!TIP]
> 답을 먼저 떠올리고 펼쳐 확인한다.

### Q1. Job Definition, Job Queue, Compute Environment는 각각 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Definition은 실행 이미지·리소스 등의 명세, Queue는 대기열, Compute Environment는 실제 작업이 실행되는 인프라다.

</details>

### Q2. S3에 이미지가 들어오면 AWS Batch가 자동으로 무조건 실행되는가?

<details>
<summary>정답 및 해설 보기</summary>

아니다. EventBridge, Lambda, Step Functions 등으로 이벤트를 연결하고 `SubmitJob`을 호출하도록 설계해야 한다.

</details>

### Q3. 하나의 작업이 2시간 동안 컨테이너로 수행된다. Lambda와 비교했을 때 무엇을 선택할까?

<details>
<summary>정답 및 해설 보기</summary>

AWS Batch가 유력하다. Lambda 한 번의 최대 실행 시간은 15분이고 Batch는 장시간 배치 컨테이너 작업에 적합하다.

</details>

### Q4. AWS Batch에서는 반드시 EC2 인스턴스를 직접 관리해야 하는가?

<details>
<summary>정답 및 해설 보기</summary>

아니다. 관리형 EC2 Compute Environment도 있고 ECS 기반 Fargate를 통한 서버리스 컨테이너 실행도 가능하다.

</details>

### Q5. 실행 비용을 줄이려면 어떤 선택과 위험을 함께 고려할까?

<details>
<summary>정답 및 해설 보기</summary>

중단을 견딜 수 있는 배치 작업에 EC2 Spot을 고려할 수 있다. 다만 회수로 인한 중단과 재시도·체크포인트 설계가 필요하다.

</details>

## 🇯🇵 日本語 Summary

**AWS Batch** は大量のバッチジョブをキューに登録し、必要な計算リソースへスケジュールするマネージドサービスである。**Job Definition** は実行方法、**Job Queue** は待機場所、**Compute Environment** は実行基盤を示す。EC2 や ECS Fargate を利用でき、15 分の実行制限がある Lambda より長時間のコンテナ処理に向いている。

## 🇺🇸 English Summary

AWS Batch queues and schedules finite container workloads across managed compute environments. A job definition specifies how to run a job, a job queue holds submissions, and a compute environment supplies execution resources. Batch supports EC2 and ECS Fargate and is suitable for large, long-running workloads, unlike Lambda's 15-minute invocation limit.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Batch Processing | 배치 처리 | バッチ処理 |
| Job Definition | 작업 실행 명세 | ジョブ定義 |
| Job Queue | 작업 대기열 | ジョブキュー |
| Compute Environment | 실행 컴퓨팅 환경 | コンピューティング環境 |
| Job Scheduler | 작업 스케줄러 | ジョブスケジューラー |
| Spot Instance | 스팟 인스턴스 | スポットインスタンス |
| Container Image | 컨테이너 이미지 | コンテナイメージ |
| Retry | 재시도 | リトライ |

## 📖 References

- [AWS Batch components](https://docs.aws.amazon.com/batch/latest/userguide/batch_components.html)
- [AWS Batch Fargate environments](https://docs.aws.amazon.com/batch/latest/userguide/fargate.html)
- [AWS Batch compute environments](https://docs.aws.amazon.com/batch/latest/userguide/compute_environments.html)
- [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html)
