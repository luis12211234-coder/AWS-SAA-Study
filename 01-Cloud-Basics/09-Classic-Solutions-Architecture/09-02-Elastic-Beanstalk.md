# 09-02. AWS Elastic Beanstalk

## Overview

일반적인 Web Application은 비슷한 AWS Architecture를 반복해서 사용하는 경우가 많다.

예를 들어:

```text
User
 ↓
Load Balancer
 ↓
Auto Scaling Group
 ↓
EC2 Instances
 ↓
Database / Cache
```

개발자가 새로운 Application을 만들 때마다 이러한 Infrastructure를 직접 구성하고 관리해야 한다면 많은 작업이 반복된다.

예를 들어 다음을 관리해야 할 수 있다.

- Infrastructure Provisioning
- Application Deployment
- Load Balancer
- Auto Scaling
- Instance Configuration
- Health Monitoring

개발자의 주요 목적은 Infrastructure 자체를 반복해서 조립하는 것이 아니라 **Application Code를 실행하는 것**이다.

AWS Elastic Beanstalk는 이러한 문제를 해결하기 위한 개발자 중심의 Managed Service이다.

---

## 1. Elastic Beanstalk란?

Elastic Beanstalk는 AWS에서 Application을 배포하고 관리하기 위한 Managed Service이다.

중요한 점은 Beanstalk가 EC2나 Auto Scaling을 대체하는 새로운 Compute Service가 아니라는 것이다.

Beanstalk는 지금까지 배운 AWS Service들을 내부적으로 활용한다.

```text
Developer
    │
    │ Application Code
    ▼
Elastic Beanstalk
    │
    ├── EC2
    ├── Auto Scaling Group
    ├── Elastic Load Balancing
    └── RDS
```

즉 Beanstalk는 기존 AWS Infrastructure를 **개발자 중심의 하나의 인터페이스로 통합하고 관리하는 역할**을 한다.

---

## 2. Elastic Beanstalk가 관리하는 것

Elastic Beanstalk는 다음과 같은 작업을 처리할 수 있다.

- Capacity Provisioning
- Load Balancing
- Auto Scaling
- Application Health Monitoring
- Instance Configuration
- Application Deployment

따라서 개발자는 Infrastructure의 반복적인 관리보다 Application Code에 집중할 수 있다.

```text
Developer
    ↓
Application Code
    ↓
Elastic Beanstalk
    ↓
AWS Infrastructure
```

그러나 Infrastructure의 Configuration에 대한 제어권이 완전히 사라지는 것은 아니다.

필요하다면 Beanstalk가 사용하는 각 구성 요소의 설정을 변경할 수 있다.

---

## 3. 비용

Elastic Beanstalk 서비스 자체에는 별도의 추가 비용이 없다.

하지만 Beanstalk가 생성하고 사용하는 AWS Resource에는 일반적인 비용이 발생한다.

예:

```text
Elastic Beanstalk
→ 추가 서비스 비용 없음

EC2
ELB
RDS
...
→ 각 Resource 사용 비용 발생
```

즉 Beanstalk가 EC2를 대신 생성했다고 해서 EC2가 무료가 되는 것은 아니다.

---

## 4. Beanstalk Components

Elastic Beanstalk에서는 다음 세 개념을 구분해야 한다.

```text
Application
Application Version
Environment
```

---

### Application

Application은 Elastic Beanstalk Component들의 집합이다.

```text
MyApplication
│
├── Application Versions
├── Environments
└── Configurations
```

하나의 Application 아래에서 여러 Version과 Environment를 관리할 수 있다.

---

### Application Version

Application Version은 Application Code의 특정 버전이다.

예:

```text
MyApplication

├── v1
├── v2
└── v3
```

Application을 수정하고 새로운 Code를 배포할 때 새로운 Application Version을 만들 수 있다.

---

### Environment

Environment는 특정 Application Version을 실행하는 AWS Resource들의 집합이다.

예:

```text
Production Environment
        │
        └── Application v3
                 │
                 ├── ELB
                 ├── ASG
                 └── EC2
```

한 Environment에서는 한 시점에 하나의 Application Version을 실행한다.

Version을 업데이트하면:

```text
Production
    │
    ├── Before: v1
    │
    └── After:  v2
```

처럼 기존 Environment에 새로운 Application Version을 배포할 수 있다.

---

## 5. Multiple Environments

하나의 Application에서 여러 Environment를 생성할 수 있다.

```text
MyApplication
│
├── Development
│      └── v3
│
├── Test
│      └── v2
│
└── Production
       └── v1
```

따라서 개발, 테스트, 운영 환경을 서로 분리하여 관리할 수 있다.

---

## 6. Deployment Workflow

Beanstalk의 기본적인 Application Deployment 흐름은 다음과 같다.

```text
Create Application
        ↓
Upload Application Version
        ↓
Launch Environment
        ↓
Manage Environment
```

새로운 Code가 준비되면:

```text
Upload New Version
        ↓
Deploy New Version
        ↓
Environment Updated
```

이렇게 Application Code를 업데이트할 수 있다.

---

## 7. Supported Platforms

Elastic Beanstalk는 다양한 Application Platform을 지원한다.

강의에서 다룬 예시는 다음과 같다.

- Go
- Java SE
- Java with Tomcat
- .NET
- Node.js
- PHP
- Python
- Ruby
- Docker

각 플랫폼의 전체 목록을 암기하는 것보다 **다양한 일반 Application Runtime과 Container 환경을 지원한다**는 점을 이해하는 것이 중요하다.

---

## 8. Web Server Environment

Web Server Environment는 일반적인 Web Traffic을 처리한다.

```text
                Users
                  ↓
                 ELB
                  ↓
          Auto Scaling Group
            /           \
          EC2           EC2
      Web Server     Web Server
         AZ-A           AZ-B
```

사용자가 HTTP Request를 보내면 Load Balancer가 Web Server 역할을 하는 EC2 Instance로 Traffic을 전달한다.

즉:

```text
HTTP Request
     ↓
Web Server Environment
```

라고 이해할 수 있다.

---

## 9. Worker Environment

Worker Environment는 사용자의 Web Request를 직접 처리하는 환경이 아니다.

Amazon SQS Queue에서 Message를 가져와 작업을 처리한다.

```text
             SQS Queue
          📩 📩 📩 📩 📩
                ↓
         Auto Scaling Group
           /           \
         EC2           EC2
       Worker         Worker
```

Worker EC2 Instance는 SQS Queue에서 Message를 가져와 처리한다.

SQS Message 수가 증가하면 더 많은 Worker Instance가 필요할 수 있으며, Message 수를 기준으로 Worker Environment를 확장할 수 있다.

### Web + Worker

Web Environment와 Worker Environment를 함께 사용할 수도 있다.

```text
User
 ↓
Web Environment
 ↓
SQS Queue
 ↓
Worker Environment
```

Web Application이 즉시 처리할 필요가 없는 작업을 SQS Queue에 전달하고 Worker가 이를 처리하는 구조이다.

---

## 10. Deployment Modes

### Single Instance

개발 환경에 적합한 간단한 구성이다.

```text
User
 ↓
Elastic IP
 ↓
EC2 Instance
```

단일 EC2 Instance를 사용하기 때문에 구조가 간단하다.

하지만 Instance에 장애가 발생하면 Application도 사용할 수 없기 때문에 높은 가용성을 제공하는 구성은 아니다.

---

### High Availability with Load Balancer

Production 환경에서는 Load Balancer와 여러 EC2 Instance를 사용하는 High Availability 구성을 사용할 수 있다.

```text
                Load Balancer
                      ↓
              Auto Scaling Group
                /           \
              EC2           EC2
             AZ-A           AZ-B
```

필요하다면 Multi-AZ RDS Database와 함께 사용할 수도 있다.

```text
RDS
├── Primary
└── Standby
```

핵심:

```text
Single Instance
→ Development

Load Balanced + Multi-AZ
→ Production / High Availability
```

---

## 11. Hands-on Verification

실습에서는 Sample Node.js Application을 Single Instance Environment로 배포했다.

과정은 다음과 같다.

```text
Create Application
        ↓
Create Web Server Environment
        ↓
Select Node.js Platform
        ↓
Use Sample Application
        ↓
Select Single Instance
        ↓
Configure IAM Access
        ↓
Create Environment
```

Environment를 생성하자 Elastic Beanstalk가 필요한 AWS Resource들을 자동으로 생성했다.

확인된 주요 Resource:

- EC2 Instance
- Auto Scaling Group
- Security Group
- Elastic IP
- IAM Role / Instance Profile

생성된 Infrastructure는 CloudFormation Stack에서도 확인할 수 있었다.

```text
Elastic Beanstalk
       ↓
CloudFormation
       ↓
AWS Resources
├── EC2
├── ASG
├── Security Group
└── Elastic IP
```

이 실습에서 가장 중요한 점은 **Beanstalk가 별도의 서버를 제공하는 것이 아니라 실제 AWS Resource를 내부적으로 생성하고 관리한다는 것**이다.

---

## 12. IAM과 Beanstalk

Elastic Beanstalk가 AWS Resource를 생성하고 관리하려면 필요한 IAM Permission이 있어야 한다.

또한 생성된 EC2 Instance가 AWS Service에 접근해야 하는 경우 EC2 Instance Profile을 사용할 수 있다.

```text
Elastic Beanstalk
        ↓
IAM Service Role
        ↓
AWS Resource 관리


EC2 Instance
        ↓
Instance Profile
        ↓
필요한 AWS API 접근
```

IAM의 기본 원칙은 기존에 학습한 내용과 동일하다.

---

## 13. Elastic Beanstalk와 CloudFormation

실습에서는 Elastic Beanstalk가 생성한 Infrastructure를 CloudFormation Stack에서 확인했다.

현재 단계에서 CloudFormation 자체를 자세히 학습할 필요는 없다.

이번 섹션에서는 다음 정도로 이해한다.

```text
Elastic Beanstalk
= Application 배포 중심의 Managed Interface

CloudFormation
= Infrastructure Resource 생성에 사용되는 기반 자동화
```

CloudFormation은 이후 별도의 섹션에서 자세히 학습한다.

---

## Mental Model

Elastic Beanstalk를 다음과 같이 기억할 수 있다.

```text
직접 구성

Developer
   ↓
EC2
ALB
ASG
Configuration
Deployment
Monitoring
```

반면:

```text
Elastic Beanstalk

Developer
   ↓
Application Code
   ↓
Elastic Beanstalk
   ↓
EC2 / ASG / ELB / ...
```

즉:

> **Elastic Beanstalk = 기존 AWS 인프라를 활용하면서 Application Deployment와 Environment 관리를 단순화하는 개발자 중심 Managed Service**

---

## Summary

| 개념 | 의미 |
|---|---|
| Elastic Beanstalk | Application Deployment를 단순화하는 Managed Service |
| Application | Beanstalk 구성 요소들의 집합 |
| Application Version | Application Code의 특정 버전 |
| Environment | 특정 Version을 실행하는 AWS Resource 집합 |
| Web Server Environment | HTTP Request 처리 |
| Worker Environment | SQS Message 처리 |
| Single Instance | 개발용 간단한 환경 |
| High Availability | Load Balancer + 여러 EC2를 사용하는 운영 환경 |

---

## Exam Notes

- Elastic Beanstalk는 Managed Application Deployment Service이다.
- EC2, ASG, ELB, RDS 등 기존 AWS Service를 활용한다.
- 개발자는 Application Code에 집중할 수 있다.
- Infrastructure Configuration에 대한 제어는 유지된다.
- Beanstalk 자체에는 별도의 추가 서비스 비용이 없지만 기반 AWS Resource에는 비용이 발생한다.
- Application Version은 Application Code의 특정 버전이다.
- Environment는 Application Version을 실행하는 AWS Resource들의 집합이다.
- 하나의 Application에 Dev / Test / Prod 등 여러 Environment를 생성할 수 있다.
- Web Server Environment는 Web Request를 처리한다.
- Worker Environment는 SQS Message를 처리한다.
- Worker Environment는 SQS Message 수를 기반으로 Scaling할 수 있다.
- Single Instance는 개발 환경에 적합하다.
- Load Balanced High Availability 구성은 Production 환경에 적합하다.

---

## 日本語まとめ

AWS Elastic Beanstalkは、AWS上でアプリケーションのデプロイと管理を簡単にする開発者向けのマネージドサービスです。

Beanstalkは新しいComputeサービスではなく、EC2、Auto Scaling Group、Elastic Load Balancing、RDSなどの既存AWSサービスを利用します。

```text
Developer
    ↓
Application Code
    ↓
Elastic Beanstalk
    ↓
EC2 / ASG / ELB / RDS
```

### 主なコンポーネント

- **Application**: Beanstalkコンポーネントの集合
- **Application Version**: アプリケーションコードの特定バージョン
- **Environment**: Application Versionを実行するAWSリソースの集合

### Environment Tier

- **Web Server Environment**: HTTPリクエストを処理
- **Worker Environment**: SQSメッセージを処理

### Deployment Mode

- **Single Instance**: 開発環境向け
- **High Availability**: Load Balancerと複数EC2を利用する本番環境向け

Beanstalk自体には追加料金はありませんが、EC2やELBなどの基盤AWSリソースには通常の料金が発生します。

---

## English Summary

AWS Elastic Beanstalk is a developer-centric managed service that simplifies application deployment and environment management.

It uses existing AWS services such as EC2, Auto Scaling Groups, Elastic Load Balancing, and RDS.

Developers primarily focus on application code while Beanstalk manages infrastructure provisioning, scaling, load balancing, health monitoring, and instance configuration.

### Core Components

- **Application**: Collection of Beanstalk components
- **Application Version**: A specific iteration of application code
- **Environment**: AWS resources running an application version

### Environment Tiers

- **Web Server Environment**: Handles web requests
- **Worker Environment**: Processes messages from Amazon SQS

### Deployment Modes

- **Single Instance**: Suitable for development
- **High Availability**: Suitable for production workloads

Beanstalk itself has no additional service charge, but the underlying AWS resources are billed normally.

---

## Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Elastic Beanstalk | Elastic Beanstalk | Elastic Beanstalk |
| Application | アプリケーション | 애플리케이션 |
| Application Version | アプリケーションバージョン | 애플리케이션 버전 |
| Environment | 環境 | 환경 |
| Web Server Tier | Webサーバーティア | 웹 서버 티어 |
| Worker Tier | ワーカーティア | 작업자 티어 |
| Managed Service | マネージドサービス | 관리형 서비스 |
| Provisioning | プロビジョニング | 리소스 프로비저닝 |
| Deployment | デプロイ | 배포 |
| Health Monitoring | ヘルスモニタリング | 상태 모니터링 |
| Instance Profile | インスタンスプロファイル | 인스턴스 프로파일 |
| Underlying Resource | 基盤リソース | 기반 리소스 |
| Asynchronous Processing | 非同期処理 | 비동기 처리 |

---

## Review Questions

### 1. Elastic Beanstalk의 핵심 목적은 무엇인가?

<details>
<summary>정답 보기</summary>

**정답: 개발자가 Application Code에 집중할 수 있도록 AWS Infrastructure의 배포와 관리를 단순화하는 것이다.**

Beanstalk는 EC2, ASG, ELB 등의 기존 AWS Service를 이용하여 Application Environment를 관리한다.

</details>

### 2. Application Version과 Environment의 차이는 무엇인가?

<details>
<summary>정답 보기</summary>

**정답: Application Version은 Code의 특정 버전이고, Environment는 그 Version을 실행하는 AWS Resource들의 집합이다.**

</details>

### 3. HTTP Request를 처리하는 Beanstalk Environment Tier는?

<details>
<summary>정답 보기</summary>

**정답: Web Server Environment**

Load Balancer와 Web Server EC2 Instance를 이용하여 Web Traffic을 처리한다.

</details>

### 4. SQS Message를 처리하는 Environment Tier는?

<details>
<summary>정답 보기</summary>

**정답: Worker Environment**

Worker Instance가 SQS Queue에서 Message를 가져와 처리한다.

</details>

### 5. 개발 환경에 적합한 간단한 Deployment Mode는?

<details>
<summary>정답 보기</summary>

**정답: Single Instance**

하나의 EC2 Instance를 사용하는 간단한 환경이다.

</details>

### 6. Production에서 High Availability가 필요하다면 어떤 구성을 사용할 수 있는가?

<details>
<summary>정답 보기</summary>

**정답: Load Balancer와 Auto Scaling Group의 여러 EC2 Instance를 사용하는 High Availability 환경**

여러 Availability Zone에 Instance를 배치하여 가용성을 높일 수 있다.

</details>

### 7. Elastic Beanstalk를 사용하면 EC2, ELB, ASG 등의 개념을 몰라도 되는가?

<details>
<summary>정답 보기</summary>

**정답: 아니다.**

Beanstalk는 이러한 서비스를 대체하는 것이 아니라 기존 AWS Service들을 기반으로 Infrastructure를 생성하고 관리한다.

</details>
