# 🐳 Docker & Amazon ECS

## 1. Docker

Docker는 애플리케이션과 실행 환경을 **Container**로 패키징하여 실행하는 플랫폼이다.

```text
Physical Server
      ↓
     OS
      ↓
   Docker
  ├─ Container A
  ├─ Container B
  └─ Container C
```

### VM vs Container

- **VM**: 각 VM이 독립적인 Guest OS를 가짐
- **Container**: Host OS의 Kernel을 공유하여 더 가볍게 실행
- 하나의 서버에서 여러 Container를 실행할 수 있음

```text
Dockerfile
   ↓ Build
Docker Image
   ↓ Push
Registry
   ↓ Pull
Container
```

- **Image**: Container 실행에 필요한 패키지
- **Container**: Image가 실제로 실행된 상태
- **Registry**: Image 저장소
- AWS에서는 **Amazon ECR**을 사용할 수 있음

---

## 2. Amazon ECS

**Amazon ECS (Elastic Container Service)**는 AWS의 Container 관리 및 오케스트레이션 서비스이다.

```text
ECS Cluster
     │
     └─ Service
          ├─ Task
          │   └─ Container
          └─ Task
              └─ Container
```

### 핵심 구성

- **Cluster**: ECS 리소스를 관리하는 논리적 그룹
- **Task Definition**: Container 실행 방법을 정의하는 설계도
- **Task**: Task Definition을 기반으로 실제 실행되는 단위
- **Service**: 원하는 수의 Task가 계속 실행되도록 관리

```text
Task Definition = 설계도
Task            = 실제 실행 단위
Service         = Task 관리자
```

Task를 한 번 실행하고 종료할 수도 있고, Service를 사용해 일정한 수의 Task를 계속 유지할 수도 있다.

---

## 3. EC2 vs Fargate

### EC2

```text
ECS Cluster
 ├─ EC2
 │   ├─ Task
 │   └─ Task
 └─ EC2
     └─ Task
```

사용자가 EC2 인스턴스를 준비하고 관리하며, **ECS Agent**를 통해 ECS와 통신한다.

### Fargate

```text
ECS
 ↓
CPU / Memory 지정
 ↓
Fargate
 ↓
Task 실행
```

Fargate에서는 EC2 인스턴스를 직접 관리하지 않는다.

사용자는 Task에 필요한 **CPU / Memory**를 지정하고, 기반 서버 인프라는 AWS가 관리한다.

> Fargate = 서버 크기를 선택하는 것이 아니라 Task에 필요한 CPU / Memory를 지정한다.

---

## 4. ECS IAM Roles

### Container Instance Role

EC2 Launch Type에서 **EC2 / ECS Agent**가 사용하는 권한.

### Task Execution Role

Task를 실행하기 위해 필요한 권한.

- ECR Image Pull
- CloudWatch Logs
- Secrets / Parameters 조회

### Task Role

Container 내부 Application이 AWS 서비스에 접근할 때 사용하는 권한.

```text
ECS Task
   ↓ Task Role
Application
 ├─ S3
 └─ DynamoDB
```

---

## 5. ECS + ALB / EFS

### Application Load Balancer

```text
Users
  ↓
 ALB
  ↓
Target Group
  ↓
ECS Tasks
```

ALB를 이용해 여러 ECS Task로 HTTP/HTTPS 요청을 분산할 수 있다.

### Amazon EFS

```text
Task A ─┐
Task B ─┼─→ EFS
Task C ─┘
```

EFS는 여러 ECS Task가 함께 사용할 수 있는 **Persistent Shared File Storage**이다.

EC2와 Fargate 모두에서 사용할 수 있다.

---

## 6. ECS Service Auto Scaling

ECS Service Auto Scaling은 부하에 따라 **Desired Task 수를 자동으로 증가 또는 감소**시킨다.

대표적인 지표:

- Average CPU Utilization
- Average Memory Utilization
- ALB Request Count Per Target

Scaling 방식:

- **Target Tracking**: 목표 지표 값을 유지
- **Step Scaling**: CloudWatch Alarm에 따라 단계적으로 조절
- **Scheduled Scaling**: 지정한 시간에 조절

```text
Traffic 증가
     ↓
CloudWatch Metric
     ↓
ECS Service Auto Scaling
     ↓
Desired Tasks
  2 → 3 → 4
```

> ECS Service Auto Scaling = **Task 수 Scaling**

---

## 7. Capacity Provider

Capacity Provider는 **ECS Task를 실행할 Compute Capacity를 어디에서 공급받을지 연결하는 개념**이다.

```text
Capacity Provider
├─ FARGATE
├─ FARGATE_SPOT
└─ ASG Capacity Provider
```

EC2 방식에서 Task를 늘렸는데 Capacity가 부족하다면:

```text
Desired Tasks 증가
       ↓
EC2 Capacity 부족
       ↓
ASG Capacity Provider
       ↓
Auto Scaling Group
       ↓
EC2 추가
       ↓
Task 실행
```

따라서 두 Scaling은 구분해야 한다.

```text
ECS Service Auto Scaling
= Task 수 조절

EC2 Auto Scaling
= EC2 Instance 수 조절
```

Fargate에서는 기반 Compute Capacity를 AWS가 관리한다.

---

## 8. ECS Architecture Patterns

### EventBridge → ECS Task

S3 객체 업로드 같은 이벤트를 기반으로 새로운 ECS Task를 실행할 수 있다.

```text
S3
 ↓ Event
EventBridge
 ↓
ECS Task
 ↓
S3 객체 처리
 ↓
DynamoDB
```

S3와 DynamoDB 접근 권한은 **Task Role**을 통해 부여할 수 있다.

### EventBridge Schedule → ECS Task

```text
EventBridge Schedule
        ↓
     ECS Task
        ↓
 Batch Processing
        ↓
       종료
```

주기적인 Batch Processing 작업에 활용할 수 있다.

### SQS → ECS Service

```text
SQS Queue
    ↓ Poll
ECS Service
 ├─ Task
 ├─ Task
 └─ Task
```

SQS에 처리할 메시지가 증가하면 ECS Service Auto Scaling을 이용해 Task 수를 늘릴 수 있다.

### ECS → EventBridge → SNS

```text
ECS Task
   ↓ STOPPED
EventBridge
   ↓
  SNS
   ↓
Administrator
```

ECS Task의 상태 변화를 EventBridge로 감지하고 SNS를 통해 관리자에게 알림을 보낼 수 있다.

---

## 🎯 Exam Notes

- **Task Definition** = Container 실행 설계도
- **Task** = 실제 Container 실행 단위
- **Service** = 원하는 수의 Task를 유지
- **EC2** = 사용자가 기반 서버 관리
- **Fargate** = AWS가 기반 서버 관리
- **Task Execution Role** = Task 실행에 필요한 권한
- **Task Role** = Container Application의 AWS 접근 권한
- **Service Auto Scaling** = Task 수 조절
- **ASG Capacity Provider** = ECS와 ASG를 연결하여 EC2 Capacity 확장
- **EventBridge** = Event / Schedule 기반 ECS Task 실행 가능
- **SQS + ECS Service** = Queue의 작업량에 따라 Worker Task 확장 가능

---

## 💡 Practical Example

이미지 처리 시스템을 예로 들 수 있다.

```text
User
 ↓
S3 Upload
 ↓
EventBridge
 ↓
Fargate Task
 ↓
Image Processing
 ↓
DynamoDB
```

이미지가 업로드될 때만 Fargate Task를 실행하고, 처리가 끝나면 Task도 종료한다.

반대로 지속적으로 SQS 메시지를 처리해야 하는 Worker라면 **ECS Service**를 사용하여 Task를 계속 유지할 수 있다.

---

## 🇯🇵 日本語 Summary

Amazon ECSは、AWS上でコンテナを管理・実行するためのサービスです。

**Task Definition**でコンテナの実行方法を定義し、それを基に**Task**が実際に実行されます。長時間稼働するアプリケーションでは、**ECS Service**を利用して必要な数のTaskを維持できます。

ECSではEC2またはFargateを利用できます。EC2ではユーザーがインスタンスを管理しますが、Fargateでは基盤となるサーバーをAWSが管理します。

ECS Service Auto Scalingを利用すると、CPUやメモリなどの負荷に応じてTask数を自動調整できます。また、EventBridge、SQS、SNSなどのAWSサービスと組み合わせることで、イベント処理やバッチ処理などのアーキテクチャを構築できます。

---

## 🇺🇸 English Summary

Amazon ECS is a container orchestration service for running and managing containers on AWS.

A **Task Definition** defines how containers should run, while a **Task** is the actual running unit. An **ECS Service** maintains the desired number of Tasks for long-running applications.

ECS can run workloads on EC2 or AWS Fargate. With EC2, users manage the underlying instances, while Fargate allows Tasks to run without managing the underlying servers.

ECS Service Auto Scaling automatically adjusts the number of Tasks based on workload. ECS can also integrate with services such as EventBridge, SQS, and SNS to build event-driven and asynchronous architectures.

---

## 📚 Vocabulary

| Term | Meaning |
|---|---|
| Container | 애플리케이션과 실행 환경을 격리하여 실행하는 단위 |
| Docker Image | Container 실행에 필요한 패키지 |
| Registry | Container Image 저장소 |
| Cluster | ECS 리소스를 관리하는 논리적 그룹 |
| Task Definition | ECS Task 실행 방법을 정의하는 설계도 |
| Task | ECS에서 Container가 실제로 실행되는 단위 |
| Service | 원하는 수의 Task를 유지하고 관리하는 기능 |
| Desired Count | Service가 유지하려는 Task 수 |
| Fargate | 서버를 직접 관리하지 않고 Container를 실행하는 방식 |
| Task Execution Role | Task 실행 준비에 필요한 IAM Role |
| Task Role | Container Application이 사용하는 IAM Role |
| Capacity Provider | ECS Task에 Compute Capacity를 공급하는 방식을 연결하는 기능 |
| Auto Scaling | 부하에 따라 리소스 수를 자동 조절하는 기능 |
| Batch Processing | 데이터를 일정 단위로 모아 처리하는 방식 |
| Polling | 서비스에 새로운 데이터가 있는지 주기적으로 요청하는 방식 |

---

## 📝 Review Questions

### 1. Task Definition과 Task의 차이는 무엇인가?

<details>
<summary>정답 보기</summary>

**Task Definition**은 Container를 어떻게 실행할지 정의하는 설계도이고,  
**Task**는 Task Definition을 기반으로 실제 실행되는 단위이다.

</details>

### 2. Task와 Service의 관계는 무엇인가?

<details>
<summary>정답 보기</summary>

**Task**가 실제 Container 실행 단위이고,  
**Service**는 원하는 수의 Task가 계속 실행되도록 관리한다.

</details>

### 3. EC2와 Fargate의 가장 큰 차이는 무엇인가?

<details>
<summary>정답 보기</summary>

- **EC2**: 사용자가 기반 EC2 Instance를 관리
- **Fargate**: AWS가 기반 서버 인프라를 관리

Fargate에서도 Task에 필요한 CPU와 Memory는 사용자가 지정한다.

</details>

### 4. Task Execution Role과 Task Role의 차이는 무엇인가?

<details>
<summary>정답 보기</summary>

- **Task Execution Role**: ECR Image Pull, Logs 등 Task 실행 준비에 필요한 권한
- **Task Role**: Container 내부 Application이 S3, DynamoDB 등 AWS 서비스에 접근하기 위한 권한

</details>

### 5. ECS Service Auto Scaling과 EC2 Auto Scaling의 차이는 무엇인가?

<details>
<summary>정답 보기</summary>

**ECS Service Auto Scaling**
→ Task 수를 증가/감소

**EC2 Auto Scaling**
→ 기반 EC2 Instance 수를 증가/감소

</details>

### 6. Capacity Provider의 역할은 무엇인가?

<details>
<summary>정답 보기</summary>

ECS Task를 실행할 **Compute Capacity를 어디에서 공급받을지 연결하는 역할**을 한다.

예:

- FARGATE
- FARGATE_SPOT
- ASG Capacity Provider

</details>

### 7. EventBridge로 ECS Task를 실행하는 대표적인 두 가지 방식은?

<details>
<summary>정답 보기</summary>

**Event 기반 실행**

```text
S3 Event
   ↓
EventBridge
   ↓
ECS Task
```

**Schedule 기반 실행**

```text
EventBridge Schedule
        ↓
     ECS Task
```

</details>

### 8. SQS와 ECS Service를 함께 사용하는 이유는 무엇인가?

<details>
<summary>정답 보기</summary>

SQS에 작업을 Queue로 저장하고 ECS Task들이 이를 Polling하여 처리할 수 있다.

메시지가 많이 쌓이면 **ECS Service Auto Scaling**을 통해 Task 수를 늘려 병렬 처리할 수 있다.

</details>
