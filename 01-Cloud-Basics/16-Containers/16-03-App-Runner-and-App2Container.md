# AWS App Runner & AWS App2Container

## 1. AWS App Runner

AWS App Runner는 **웹 애플리케이션과 API를 빠르게 배포하고 운영하기 위한 완전관리형 서비스**이다.

개발자가 인프라를 세세하게 구성하는 대신, **소스 코드 또는 Container Image**를 제공하면 App Runner가 애플리케이션 배포와 운영에 필요한 많은 작업을 자동으로 처리한다.

```text
Source Code / Container Image
            ↓
        App Runner
            ↓
   Web Application / API
            ↓
        Public URL
```

### App Runner의 특징

- Source Code 또는 Container Image를 이용해 배포
- Container Image는 Amazon ECR 등을 활용
- vCPU / Memory 설정
- Auto Scaling 지원
- Health Check 지원
- Load Balancing
- HTTPS Endpoint 제공
- Custom Domain 지원
- Logs / Metrics / AWS X-Ray 연동
- VPC 리소스와 연결 가능

즉, ECS보다 더 많은 인프라 구성을 AWS에 맡길 수 있다.

```text
관리 자유도 ↑

EC2
 ↓
ECS + EC2
 ↓
ECS + Fargate
 ↓
App Runner

관리 편의성 ↑
```

### App Runner vs ECS

App Runner는 단순히 ECS의 상위 버전이 아니다.

App Runner는 **웹 애플리케이션/API를 간단하게 배포하는 것**에 초점을 두며, ECS는 **Container 운영 구조를 더 세밀하게 설계**할 수 있다.

```text
간단한 Web App / API
빠른 배포 + 적은 인프라 관리
        ↓
    App Runner


여러 종류의 Task
복잡한 Scaling
SQS Worker
Batch Processing
세밀한 Container 운영
        ↓
       ECS
```

---

## 2. App Runner Deployment Flow

Container Image를 사용하는 경우의 기본 흐름은 다음과 같다.

```text
Amazon ECR / ECR Public
          ↓
   Container Image
          ↓
      App Runner
          ↓
CPU / Memory / Port 설정
          ↓
Auto Scaling
Health Check
Networking
          ↓
       Deploy
          ↓
   Application URL
```

예를 들어 HTTP Server Container가 Port 80을 사용한다면 App Runner에 해당 Port를 지정한다.

### Concurrency

Concurrency는 **하나의 App Runner Instance가 동시에 처리하는 요청 수를 기준으로 Auto Scaling을 판단하는 설정**이다.

요청량이 증가하면 App Runner가 애플리케이션 실행 용량을 자동으로 확장할 수 있다.

### Health Check

App Runner는 Health Check를 통해 애플리케이션이 정상적으로 동작하고 있는지 확인할 수 있다.

---

## 3. AWS App2Container (A2C)

AWS App2Container는 기존 **Java 및 .NET 애플리케이션을 Container 기반 환경으로 마이그레이션하고 현대화하기 위한 CLI 도구**이다.

핵심 목적은 기존 애플리케이션의 코드 변경을 최소화하면서 Container화하는 것이다.

```text
On-Premises / VM

Legacy Java / .NET Application
              ↓
       App2Container
              ↓
       Analyze Application
              ↓
       Containerize
              ↓
        Docker Image
              ↓
         Amazon ECR
              ↓
     ┌────────┼────────┐
     ↓        ↓        ↓
    ECS      EKS    App Runner
```

### App2Container가 하는 일

App2Container는 기존 애플리케이션을 분석하여 Container화에 필요한 정보를 추출하고 배포 준비를 자동화한다.

주요 기능:

- Java / .NET 애플리케이션 검색 및 분석
- 애플리케이션 Dependency 분석
- Docker Container Image 생성
- Amazon ECR에 Image 저장
- CloudFormation Template 생성
- ECS Task 관련 배포 Artifact 생성
- EKS Pod 관련 배포 Artifact 생성
- CI/CD Pipeline 구성 지원
- ECS / EKS / App Runner로 배포 가능

즉,

```text
"기존 앱을 Docker Image로 만들어준다"
                 +
"AWS에 배포하기 위한 준비도 도와준다"
```

라고 이해하면 된다.

---

## 4. App2Container Use Case

대표적인 상황은 오래된 Java 또는 .NET 웹 애플리케이션을 AWS로 이전하는 경우이다.

```text
기존 회사 서버

VM
└── Tomcat
    └── Legacy Java Web App

        ↓

"코드를 대규모로 수정하지 않고
 Container 기반 AWS 환경으로 옮기고 싶다"

        ↓

AWS App2Container
```

App2Container는 모든 종류의 프로그램을 자동으로 Container화하는 범용 도구가 아니다.

**지원되는 Java/.NET 애플리케이션의 Container Migration 및 Modernization**이 핵심 사용 사례이다.

---

# 🎯 Exam Notes

### App Runner

> **Web Application / API를 최소한의 인프라 관리로 빠르게 배포**

시험 키워드:

- Fully Managed
- Web Application / API
- Source Code 또는 Container Image
- Auto Scaling
- Load Balancing
- 빠른 배포
- 인프라 관리 최소화

```text
"웹/API를 빠르게 배포하고 싶다"
+
"인프라를 최대한 관리하고 싶지 않다"

→ AWS App Runner
```

### App2Container

> **기존 Java/.NET 애플리케이션을 Container화하여 AWS로 Migration**

시험 키워드:

- Legacy Application
- Java / .NET
- On-Premises / VM
- Containerize
- Minimal Code Changes
- Migration / Modernization
- ECS / EKS / App Runner

```text
Legacy Java / .NET
        +
Container Migration
        +
코드 변경 최소화

→ AWS App2Container
```

---

# 💡 Practical Example

## App Runner

개발자가 Spring 기반 API를 Container Image로 만들었다.

요구사항:

- 빠르게 인터넷에 배포
- 트래픽 증가 시 자동 확장
- Load Balancer 직접 관리하고 싶지 않음
- 서버 관리 최소화

```text
Developer
   ↓
Docker Image
   ↓
Amazon ECR
   ↓
App Runner
   ↓
Public HTTPS Endpoint
```

→ **App Runner가 적합**

---

## App2Container

회사가 On-Premises VM에서 오래된 Java 웹 애플리케이션을 운영하고 있다.

```text
On-Premises VM
      ↓
Legacy Java Application
      ↓
App2Container
      ↓
Docker Image
      ↓
Amazon ECR
      ↓
ECS / EKS / App Runner
```

기존 코드를 대규모로 다시 작성하기보다 Container 기반 AWS 환경으로 Migration하려는 경우 사용할 수 있다.

---

# 🇯🇵 日本語 Summary

AWS App Runnerは、WebアプリケーションやAPIを簡単にデプロイ・運用するためのフルマネージドサービスです。ソースコードまたはコンテナイメージを利用してアプリケーションをデプロイでき、オートスケーリング、ヘルスチェック、ロードバランシングなどのインフラ管理を簡素化できます。

AWS App2Container（A2C）は、既存のJavaや.NETアプリケーションをコンテナ化し、AWSへ移行・モダナイズするためのCLIツールです。アプリケーションを分析してDockerイメージやデプロイ用のアーティファクトを生成し、Amazon ECS、Amazon EKS、AWS App Runnerなどへの移行を支援します。

---

# 🇺🇸 English Summary

AWS App Runner is a fully managed service for quickly deploying and operating web applications and APIs. It can deploy applications from source code or container images while simplifying infrastructure tasks such as auto scaling, health checks, and load balancing.

AWS App2Container (A2C) is a CLI tool designed to help containerize and modernize existing Java and .NET applications. It analyzes existing applications, creates container images and deployment artifacts, and helps migrate applications to services such as Amazon ECS, Amazon EKS, or AWS App Runner.

---

# 📚 Vocabulary

| Term | Meaning |
|---|---|
| App Runner | 웹 애플리케이션/API 배포를 단순화하는 완전관리형 서비스 |
| App2Container (A2C) | 기존 Java/.NET 애플리케이션의 Container화를 지원하는 CLI 도구 |
| Container Image | Container 실행에 필요한 애플리케이션과 환경을 패키징한 이미지 |
| Amazon ECR | Container Image를 저장하고 관리하는 AWS Registry |
| Concurrency | 동시에 처리되는 요청의 수 |
| Health Check | 애플리케이션의 정상 동작 여부를 확인하는 검사 |
| Auto Scaling | 부하에 따라 실행 용량을 자동으로 조절하는 기능 |
| Legacy Application | 기존에 장기간 운영되어 온 애플리케이션 |
| Modernization | 기존 애플리케이션을 현대적인 아키텍처로 전환하는 과정 |
| Lift and Shift | 기존 시스템을 큰 변경 없이 다른 환경으로 이전하는 방식 |
| CloudFormation | AWS Infrastructure를 Template으로 정의하고 생성하는 IaC 서비스 |
| Deployment Artifact | 애플리케이션 배포에 필요한 파일 및 설정 |
| CI/CD | Build, Test, Deployment 등을 자동화하는 개발 및 배포 방식 |

---

# 📝 Review Questions

### Q1. 웹 애플리케이션이나 API를 최소한의 인프라 관리로 빠르게 배포하고 싶다. 적합한 서비스는?

<details>
<summary>정답 보기</summary>

**AWS App Runner**

소스 코드 또는 Container Image를 기반으로 웹 애플리케이션/API를 배포하고 Auto Scaling, Load Balancing 등의 운영 부담을 줄일 수 있다.

</details>

### Q2. App Runner에 애플리케이션을 제공할 수 있는 대표적인 두 가지 형태는?

<details>
<summary>정답 보기</summary>

**Source Code 또는 Container Image**

</details>

### Q3. App Runner와 ECS의 가장 중요한 차이는?

<details>
<summary>정답 보기</summary>

App Runner는 웹 애플리케이션/API를 간단하게 배포하고 운영하는 데 초점을 두며, ECS는 Task와 Service 등 Container 운영 구조를 더 세밀하게 제어할 수 있다.

</details>

### Q4. On-Premises에서 실행 중인 기존 Java/.NET 애플리케이션을 코드 변경을 최소화하면서 Container화하려 한다. 적합한 도구는?

<details>
<summary>정답 보기</summary>

**AWS App2Container (A2C)**

</details>

### Q5. App2Container로 생성된 Container Image를 저장하는 대표적인 AWS 서비스는?

<details>
<summary>정답 보기</summary>

**Amazon ECR**

</details>

### Q6. App2Container로 Container화한 애플리케이션을 배포할 수 있는 대표적인 서비스는?

<details>
<summary>정답 보기</summary>

**Amazon ECS, Amazon EKS, AWS App Runner**

</details>

---

## ⚠️ Current AWS Note

강의 및 SAA 학습에서는 다음과 같이 기억한다.

```text
Legacy Java / .NET
        ↓
AWS App2Container
        ↓
Container Migration
```

다만 최신 AWS 환경에서는 App2Container의 신규 고객 제공 여부 등 서비스 상태가 변경될 수 있으므로, 실제 프로젝트에서 도입할 때는 AWS 공식 문서를 기준으로 최신 상태를 확인한다.

---

## 한 줄 정리

```text
App Runner
= "내 Web App/API를 쉽게 배포하고 운영해줘"

App2Container
= "내 기존 Java/.NET 앱을 Container로 옮기는 걸 도와줘"
```
