# 16. Containers on AWS

AWS에서 Container 기반 애플리케이션을 실행하고 관리하기 위한 주요 서비스를 정리한다.

이 섹션에서는 Docker의 기본 개념부터 시작하여 Amazon ECS, Amazon ECR, Amazon EKS, AWS Fargate, AWS App Runner 및 AWS App2Container를 학습한다.

---

## 📚 Contents

### [16-01. Docker and Amazon ECS](./16-01-Docker-and-ECS.md)

- Docker
- Docker Image & Container
- Amazon ECS
- ECS Cluster / Task / Service
- EC2 Launch Type
- AWS Fargate
- ECS IAM Roles
- Load Balancer Integration
- Amazon EFS
- ECS Service Auto Scaling
- Capacity Provider
- ECS Architecture Patterns

### [16-02. Amazon ECR and Amazon EKS](./16-02-ECR-and-EKS.md)

- Amazon ECR
- Private / Public Repository
- Amazon EKS
- Kubernetes
- Cluster / Node / Pod
- Managed Node Groups
- Self-Managed Nodes
- AWS Fargate
- EKS Data Volumes
- CSI Driver

### [16-03. AWS App Runner and App2Container](./16-03-App-Runner-and-App2Container.md)

- AWS App Runner
- Source Code / Container Image Deployment
- Auto Scaling
- Health Check
- AWS App2Container
- Java / .NET Application Modernization
- Container Migration

---

## 🧩 Container Services Overview

```text
Docker
│
│ Container Image 생성
↓
Amazon ECR
│
│ Image 저장
↓
┌──────────────────────────────────────┐
│ Container 실행 / 관리               │
│                                      │
│ ECS          EKS          App Runner │
│ │            │                │      │
│ AWS 방식     Kubernetes 방식   간편 배포 │
└──────────────────────────────────────┘

ECS / EKS
   ↓
┌─────────────┐
│ EC2         │
│ Fargate     │
└─────────────┘
```

---

## 핵심 서비스 구분

| Service | 역할 |
|---|---|
| Docker | 애플리케이션을 Container로 패키징 |
| Amazon ECR | Container Image 저장 |
| Amazon ECS | AWS 자체 Container Orchestration |
| Amazon EKS | AWS Managed Kubernetes |
| AWS Fargate | 서버 관리 없이 Container 실행 |
| AWS App Runner | Web App/API의 간편한 배포 및 운영 |
| AWS App2Container | 기존 Java/.NET 앱의 Container Migration 지원 |

---

## 🎯 Section 핵심

```text
Docker
= Container 기술

ECR
= Container Image 저장소

ECS
= AWS 방식 Container 관리자

EKS
= Kubernetes 방식 Container 관리자

Fargate
= EC2 관리 없이 Container 실행

App Runner
= Web App / API 간편 배포

App2Container
= Legacy Java / .NET → Container Migration
```
