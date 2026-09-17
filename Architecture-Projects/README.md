# 🏗️ AWS Architecture Projects

## Overview

이 디렉터리는 AWS Certified Solutions Architect Associate 학습 과정에서 다룬 요구사항을 기반으로 작성한 **Architecture Design Projects**이다.

개별 AWS 서비스의 기능을 정리하는 것보다 다음 과정에 초점을 맞춘다.

```text
Requirements
     ↓
Initial Architecture
     ↓
Identify Problems
     ↓
Architecture Evolution
     ↓
Design Decisions
     ↓
Final Architecture
     ↓
Trade-offs
```

각 프로젝트에서는 단순히 AWS 서비스를 나열하지 않고,

> **왜 해당 AWS 서비스를 선택했는가?**

를 중심으로 Architecture가 발전하는 과정을 기록한다.

---

# Projects

## 01. WhatIsTheTime.com

### Theme

**Stateless Web Application Scaling & High Availability**

단순한 단일 EC2 Web Application에서 시작하여 Traffic 증가와 Availability 요구사항에 대응하도록 Architecture를 발전시킨다.

### Key AWS Services

- Amazon EC2
- Elastic IP
- Amazon Route 53
- Elastic Load Balancing
- Auto Scaling Groups
- Security Groups
- Multi-AZ Architecture

### Architecture Topics

```text
Single EC2
    ↓
Vertical Scaling
    ↓
Horizontal Scaling
    ↓
DNS
    ↓
Load Balancing
    ↓
Auto Scaling
    ↓
Multi-AZ High Availability
```

---

## 02. MyClothes.com

### Theme

**Stateful Application → Stateless Web Tier**

Shopping Cart와 User Session을 가진 Stateful Web Application을 수평 확장 가능한 Architecture로 개선한다.

### Key AWS Services

- Elastic Load Balancing
- Auto Scaling Groups
- Amazon ElastiCache
- Amazon RDS
- RDS Read Replicas
- Multi-AZ
- Security Groups

### Architecture Topics

```text
Local Session
    ↓
Stickiness
    ↓
Client Cookies
    ↓
External Session Store
    ↓
Stateless Web Tier
    ↓
Database Scaling
    ↓
Multi-AZ
```

---

## 03. MyWordPress.com

### Theme

**Shared Storage for Horizontally Scaled Applications**

여러 EC2 Instance가 동일한 WordPress Upload File을 사용할 수 있도록 Storage Architecture를 개선한다.

### Key AWS Services

- Amazon EC2
- Auto Scaling Groups
- Amazon EBS
- Amazon EFS
- Amazon Aurora
- Multi-AZ

### Architecture Topics

```text
Single EC2 + EBS
        ↓
Horizontal Scaling
        ↓
File Inconsistency
        ↓
Shared File System
        ↓
EFS
        ↓
Aurora + Multi-AZ
```

---

# Architecture Design Principles

세 프로젝트를 관통하는 핵심 원칙은 다음과 같다.

| Requirement / Problem | Architecture Pattern |
|---|---|
| Traffic 증가 | Horizontal Scaling |
| Traffic 분산 | Elastic Load Balancing |
| 자동 Compute Scaling | Auto Scaling Groups |
| AZ 장애 대응 | Multi-AZ |
| EC2에 묶인 Session | External Session Store |
| 반복적인 Database Read | Cache |
| Database Read 증가 | Read Replica |
| 여러 EC2의 Shared Files | Amazon EFS |
| Persistent Relational Data | RDS / Aurora |

---

# Design Approach

Architecture를 설계할 때 다음 질문을 반복한다.

```text
What happens if traffic increases?

What happens if this instance dies?

What happens if an Availability Zone fails?

Where is the application state stored?

Is the data temporary or persistent?

Is the bottleneck compute, storage, or database?

Can instances be replaced without losing data?
```

AWS Architecture Design은 서비스를 많이 사용하는 것이 목적이 아니다.

**요구사항을 만족시키는 최소한의 적절한 구성 요소를 선택하고, 각 선택의 이유와 Trade-off를 설명할 수 있어야 한다.**

---

# Project Status

These projects currently focus on **architecture design and technical reasoning**.

They do not claim production deployment or infrastructure implementation.

Future extensions may include:

- Terraform / AWS CloudFormation
- Deployment verification
- Architecture screenshots
- Monitoring
- Cost analysis
- Failure testing
