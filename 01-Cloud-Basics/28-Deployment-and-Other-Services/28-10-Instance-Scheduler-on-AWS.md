
# 📘 28-10. Instance Scheduler on AWS

> [!NOTE]
> **AWS SAA-C03 Study Note** · `GitHub Section 28` · `Udemy Section 30`
> **핵심 키워드:** Instance Scheduler, CloudFormation, EventBridge, Lambda, DynamoDB, Tag, EC2, RDS, Cost Optimization

## 🇰🇷 1. 왜 Instance Scheduler가 필요한가?

업무용 개발 서버를 월~금 **09:00~18:00**에만 사용하는 회사가 있다고 하자. 개발자가 퇴근해도 EC2가 24시간 실행되면 사용하지 않는 시간에 컴퓨팅 비용이 계속 발생한다.

**Instance Scheduler on AWS**는 **정해진 일정에 따라 EC2·RDS 등 지원되는 리소스를 자동 시작·중지하는 AWS 제공 솔루션**이다.

> [!IMPORTANT]
> **독립된 AWS 관리형 서비스가 아니라, CloudFormation으로 배포하는 AWS Solutions 구현체**다.

## 🇰🇷 2. 전체 아키텍처를 연결해서 이해하기

```text
[1] CloudFormation
    └─ Instance Scheduler 솔루션 구성 요소 배포
                ↓
[2] DynamoDB Configuration Table
    └─ office-hours = 평일 09:00~18:00
                ↑ 조회
[3] EventBridge 정기 트리거 (기본 5분 간격)
                ↓
[4] Orchestration Lambda
    └─ 스케줄 대상 파악, 실행 Lambda 호출
                ↓
[5] Scheduling Lambda
    └─ 태그 및 현재 시간을 확인
                ↓
[6] EC2 / RDS 등에 Start 또는 Stop API 호출
```

각 서비스의 역할을 정확히 구분하자.

| 구성 요소 | 역할 |
|---|---|
| **CloudFormation** | 자동화 솔루션 인프라를 **처음 배포** |
| **DynamoDB** | 스케줄 및 기간 설정 **저장** |
| **EventBridge** | 일정 간격으로 자동화 **실행 트리거** |
| **Lambda** | 현재 시간·대상을 판단해 **시작/중지 API 호출** |
| **Resource Tag** | 어떤 리소스에 어떤 일정을 적용할지 **대상 식별** |

**CloudFormation 자체가 매일 EC2를 끄는 것은 아니다.** CloudFormation은 스케줄러를 만들어 주고, 스케줄러 안의 Lambda가 실제 작업을 실행한다.

## 🇰🇷 3. office-hours 예시

예를 들어 EC2 인스턴스 `dev-server-01`에 다음 태그를 붙였다고 하자.

```text
Tag Key:   Schedule
Tag Value: office-hours
```

DynamoDB의 `office-hours` 스케줄에는 업무일과 시작·종료 시각, 시간대가 설정된다. 실제 키 이름은 솔루션 배포 시 사용자 지정할 수 있다.

```text
월요일 09:00 ── EC2 시작
월요일 18:00 ── EC2 중지
화요일 09:00 ── EC2 시작
...
금요일 18:00 ── EC2 중지
주말         ── EC2 중지 상태 유지
```

이처럼 **동일 태그를 가진 서버 여러 대에 같은 일정을 적용**할 수 있다. 시간대(Time Zone)를 잘못 설정하면 원하는 현지 업무 시간과 실제 실행 시간이 달라질 수 있다.

실행은 정기 확인 방식이므로 **정확히 09:00:00에 실행됨을 보장하는 초 단위 타이머는 아니다.**

## 🇰🇷 4. 비용 절감의 원리와 한계

**학습용 계산 예시:** EC2 실행 시간만 비교하자.

- 매일 24시간: `24시간 × 7일 = 168시간/주`.
- 평일 9시간: `9시간 × 5일 = 45시간/주`.
- 실행 시간 감소: 약 **73%**. 따라서 컴퓨팅 시간에 비례한 비용 감소를 기대할 수 있다.

그러나 **전체 AWS 요금이 73% 줄어든다는 뜻은 아니다.**

- EC2가 중지되어도 **EBS 볼륨, 스냅샷, 일부 IP 및 관련 리소스 비용**이 남을 수 있다.
- Instance Scheduler를 구성하는 **Lambda, DynamoDB, CloudWatch 등에도 사용량에 따른 비용**이 발생할 수 있다.
- 상시 운영 서비스나 중지 불가능한 인스턴스는 이 전략에 부적절할 수 있다.

## 🇰🇷 5. EC2, RDS, Auto Scaling Group의 차이

### EC2

일반적인 개발·테스트 EC2는 일정을 적용해 중지/시작할 수 있다. 단, 인스턴스 스토어처럼 종료·재시작 시 데이터 지속성이 제한되는 요소와 시작 시점의 IP·애플리케이션 초기화 문제를 검토한다.

### RDS

지원되는 RDS 인스턴스와 클러스터에 스케줄을 적용할 수 있다. 다만 RDS에는 **연속 중지 가능 기간의 제한**이 있어 유지보수 등으로 자동 재시작될 수 있다. 따라서 '일주일 이상 무조건 계속 중지'로 가정하면 안 된다.

### EC2 Auto Scaling Group (ASG)

ASG는 단일 EC2와 운영 방식이 다르다. Instance Scheduler는 ASG에 대해 **Scheduled Scaling Actions**를 사용해 용량을 일정에 맞게 조정한다. ASG 인스턴스에 Stop API만 직접 호출하면 ASG가 다시 인스턴스를 생성할 수 있으므로 주의한다.

## 🇰🇷 6. 관련 AWS 서비스와 비교

| 요구사항 | 적합한 선택 |
|---|---|
| 서버를 **업무 시간에만 자동 시작/중지** | **Instance Scheduler on AWS** |
| 인프라를 **코드로 배포** | **AWS CloudFormation** |
| 개별 AWS API 작업을 **예약 트리거** | **EventBridge Scheduler + Lambda 등** |
| OS 패치 작업을 **정해진 시간에 실행** | **Systems Manager Maintenance Windows** |
| AWS 비용 지출을 **분석·예측** | **Cost Explorer** |

Instance Scheduler가 항상 유일한 구현 방법은 아니다. 간단한 단일 계정 자동화라면 EventBridge와 Lambda로 별도 개발할 수 있다. 하지만 **여러 리소스·계정·리전에 걸친 시작/중지 스케줄을 바로 배포**하는 문제에서는 이 솔루션이 적합하다.

## 🎯 Exam Notes

> [!IMPORTANT]
> **CloudFormation 배포 솔루션 + 일정표 DynamoDB + EventBridge/Lambda + Tag 기반 EC2/RDS 시작·중지 → Instance Scheduler on AWS.**

- **CloudFormation은 자동화 인프라 배포**, Lambda는 **실제 시작/중지 실행**.
- 태그로 관리 대상을 찾고 DynamoDB의 일정과 비교한다.
- **ASG는 Scheduled Scaling Actions** 등 용량 관리 방식으로 다룬다.
- 비용 절감은 **실행 시간 감소**이지 전체 요금 0원 보장이 아니다.
- 강의의 '최대 70%'는 특정 사용 패턴에 따른 **예시 수치**다.

## 📝 Review Questions

> [!TIP]
> 답을 먼저 떠올리고 펼쳐 확인한다.

### Q1. Instance Scheduler on AWS는 독립적인 AWS 관리형 서비스인가?

<details>
<summary>정답 및 해설 보기</summary>

아니다. CloudFormation 템플릿 등으로 배포하는 AWS 제공 솔루션이다.

</details>

### Q2. CloudFormation, DynamoDB, EventBridge, Lambda의 역할은 각각 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

CloudFormation은 구성 요소 배포, DynamoDB는 일정 저장, EventBridge는 주기적 트리거, Lambda는 일정 확인과 리소스 시작/중지를 담당한다.

</details>

### Q3. `Schedule=office-hours` 같은 태그는 왜 필요한가?

<details>
<summary>정답 및 해설 보기</summary>

스케줄러가 어떤 리소스를 어떤 일정으로 관리할지 구분하기 위해서다. 태그 키는 구성에 따라 바뀔 수 있다.

</details>

### Q4. EC2가 중지되면 비용이 완전히 0원이 되는가?

<details>
<summary>정답 및 해설 보기</summary>

아니다. 컴퓨팅 실행 비용은 줄지만 EBS, 스냅샷, IP, 스케줄러 자체 비용 등은 남을 수 있다.

</details>

### Q5. Auto Scaling Group을 구성하는 EC2를 그냥 Stop API로 중지하면 왜 문제가 될 수 있는가?

<details>
<summary>정답 및 해설 보기</summary>

ASG가 설정된 희망 용량을 유지하려고 인스턴스를 다시 생성할 수 있기 때문이다. ASG는 Scheduled Scaling Actions와 같은 용량 기반 일정 관리가 적절하다.

</details>

## 🇯🇵 日本語 Summary

**Instance Scheduler on AWS** は **CloudFormation でデプロイする AWS Solutions の実装**であり、EC2 や RDS などをスケジュールに基づいて起動・停止する。DynamoDB がスケジュールを保持し、EventBridge が定期起動し、Lambda がタグ付きリソースを操作する。ASG は Scheduled Scaling Actions を使う。インスタンス停止後も EBS 等の料金は残る可能性がある。

## 🇺🇸 English Summary

Instance Scheduler on AWS is a CloudFormation-deployed solution for automatically starting and stopping supported resources. DynamoDB stores schedules, EventBridge triggers orchestration, Lambda evaluates tagged resources and performs the actions. Auto Scaling Groups require scheduled scaling actions. Stopping compute resources reduces runtime charges but does not eliminate every AWS cost.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Instance Scheduler | 인스턴스 시작·중지 스케줄러 | インスタンススケジューラー |
| Scheduling | 일정 실행·관리 | スケジューリング |
| EventBridge Rule | 정기 실행 규칙 | EventBridge ルール |
| Orchestration | 작업 흐름 제어 | オー케ストレーション |
| Resource Tag | 리소스 태그 | リソースタグ |
| Start / Stop | 시작 / 중지 | 起動 / 停止 |
| Scheduled Scaling | 예약된 용량 조절 | スケジュールされたスケーリング |
| Cost Optimization | 비용 최적화 | コスト最適化 |

## 📖 References

- [Instance Scheduler on AWS overview](https://docs.aws.amazon.com/solutions/latest/instance-scheduler-on-aws/solution-overview.html)
- [Architecture](https://docs.aws.amazon.com/solutions/latest/instance-scheduler-on-aws/architecture.html)
- [Auto Scaling Group scheduling](https://docs.aws.amazon.com/solutions/latest/instance-scheduler-on-aws/ec2-auto-scaling-group-scheduling.html)
- [RDS shutdown behavior](https://docs.aws.amazon.com/solutions/latest/instance-scheduler-on-aws/instance-shutdown-behavior.html)
