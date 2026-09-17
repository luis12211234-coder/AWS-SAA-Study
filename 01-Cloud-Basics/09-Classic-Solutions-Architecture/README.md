# 09. Classic Solutions Architecture

## Overview

이번 섹션의 핵심은 새로운 AWS 서비스를 많이 배우는 것이 아니라, 지금까지 학습한 AWS 서비스들을 **실제 요구사항에 맞게 조합하여 아키텍처를 설계하는 방법**을 이해하는 것이다.

지금까지 개별적으로 학습했던 다음 서비스들이 하나의 시스템 안에서 어떻게 연결되는지를 살펴본다.

- Amazon EC2
- Elastic Load Balancing
- Auto Scaling Groups
- Amazon RDS / Aurora
- Amazon ElastiCache
- Amazon EBS / EFS
- Amazon Route 53
- Security Groups
- Multi-AZ Architecture

핵심 사고 과정은 다음과 같다.

```text
Requirement
    ↓
현재 Architecture의 문제 발견
    ↓
Bottleneck / Failure Point 분석
    ↓
적절한 AWS Service 선택
    ↓
Architecture 개선
    ↓
Scalability / Availability / Cost 검토
```

즉,

> **서비스 이름을 외우는 것보다 "왜 이 위치에 이 서비스를 사용하는가?"를 이해하는 것이 핵심이다.**

---

## 이 섹션에서 새롭게 학습한 내용

### 1. 빠른 애플리케이션 생성

새로운 환경을 처음부터 구성하는 시간을 줄이기 위해 다음 방법을 사용할 수 있다.

- Golden AMI
- EC2 User Data
- Golden AMI + User Data
- RDS Snapshot Restore
- EBS Snapshot Restore

자세한 내용:

`09-01-Application-Instantiation.md`

---

### 2. AWS Elastic Beanstalk

Elastic Beanstalk는 EC2, Auto Scaling Group, Elastic Load Balancing 등의 기존 AWS 서비스를 활용하여 애플리케이션 배포와 인프라 관리를 단순화하는 관리형 서비스이다.

개발자는 인프라를 매번 직접 구성하는 대신 애플리케이션 코드와 Environment를 중심으로 배포를 관리할 수 있다.

자세한 내용:

`09-02-Elastic-Beanstalk.md`

---

## Architecture Design Projects

이번 섹션에서 다룬 세 가지 Architecture Case Study는 일반 학습 노트와 분리하여 별도의 Architecture Project로 정리한다.

### WhatIsTheTime.com

주요 주제:

- Stateless Web Application
- Vertical / Horizontal Scaling
- Elastic Load Balancing
- Auto Scaling
- Multi-AZ
- High Availability

### MyClothes.com

주요 주제:

- Stateful Application
- Session Management
- Stateless Web Tier
- ElastiCache
- RDS
- Read Scaling
- Multi-AZ

### MyWordPress.com

주요 주제:

- Horizontally Scalable WordPress
- Shared File Storage
- EBS와 EFS
- Amazon Aurora
- Multi-AZ

프로젝트 문서:

```text
/Architecture-Projects/
```

---

## Architecture Thinking

이번 섹션에서 가장 중요한 것은 특정 Architecture 자체를 암기하는 것이 아니다.

요구사항이 변화할 때 Architecture가 어떻게 진화하는지를 이해해야 한다.

```text
Simple Architecture
        ↓
Traffic 증가
        ↓
Scaling 필요
        ↓
State 문제
        ↓
Storage 문제
        ↓
Availability 문제
        ↓
Automation / Management 문제
```

각 문제를 해결하기 위해 AWS 서비스를 하나씩 추가한다.

```text
Traffic Distribution
→ ELB

Automatic Compute Scaling
→ ASG

Session State
→ ElastiCache

Persistent Relational Data
→ RDS / Aurora

Shared File Storage
→ EFS

DNS
→ Route 53

Repeated Application Deployment
→ Elastic Beanstalk
```

이러한 **Problem → Solution → Trade-off** 사고방식이 AWS Solutions Architecture의 핵심이다.

---

## 日本語まとめ

このセクションでは、新しいAWSサービスを学ぶことよりも、これまで学習したサービスを組み合わせて実際のアーキテクチャを設計することが重要です。

主なポイントは以下の通りです。

- 要件から問題点を特定する
- ボトルネックや障害点を分析する
- 適切なAWSサービスを選択する
- スケーラビリティと高可用性を考慮する
- コストやトレードオフを検討する

また、Golden AMI、User Data、Snapshotを利用した高速なアプリケーション起動と、Elastic Beanstalkによるアプリケーションデプロイについて学習しました。

---

## English Summary

This section focuses on combining previously learned AWS services into practical architectures.

The main objective is to understand how requirements lead to architecture decisions.

The general design process is:

```text
Requirement
→ Problem
→ AWS Service
→ Architecture
→ Scalability / Availability
→ Trade-off
```

The section also introduces application initialization patterns using Golden AMIs, User Data, and snapshots, as well as AWS Elastic Beanstalk for managed application deployment.
