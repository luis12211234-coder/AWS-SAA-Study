# Amazon ECR & Amazon EKS

## 1. Amazon ECR

Amazon ECR(Elastic Container Registry)은 **Container Image를 저장하고 관리하는 AWS의 Container Registry 서비스**이다.

```text
Dockerfile
    ↓
  Build
    ↓
Docker Image
    ↓
   Push
    ↓
Amazon ECR
    ↓
   Pull
    ↓
ECS / EKS
```

Docker Hub와 ECR은 같은 종류의 역할을 하지만 서로 다른 서비스이다.

```text
Container Image Registry

├── Docker Hub
└── Amazon ECR
```

### ECR 주요 특징

- Container Image 저장 및 관리
- Private Repository
- Public Repository
- Amazon ECR Public Gallery
- IAM 기반 접근 제어
- Image Vulnerability Scanning
- Image Tag 및 Version 관리
- Image Lifecycle 관리
- ECS와 통합

즉,

> **ECR = AWS에서 사용하는 Container Image 저장소**

라고 이해하면 된다.

---

# 2. Amazon EKS

Amazon EKS(Elastic Kubernetes Service)는 **AWS에서 Kubernetes Cluster를 운영할 수 있도록 제공되는 Managed Kubernetes 서비스**이다.

Kubernetes는 Containerized Application의 배포, 확장 및 관리를 자동화하기 위한 오픈소스 시스템이다.

```text
Container Orchestration

├── Amazon ECS
│     └── AWS 자체 방식
│
└── Amazon EKS
      └── Kubernetes 방식
```

ECS와 EKS의 목적은 비슷하지만 사용하는 관리 체계와 API가 다르다.

---

## 3. ECS vs EKS

SAA 수준에서는 다음과 같이 대응해서 이해하면 편하다.

| ECS | EKS / Kubernetes |
|---|---|
| Cluster | Cluster |
| Task | Pod |
| EC2 Instance | Node |
| Container | Container |

완전히 동일한 개념은 아니지만 전체 구조를 이해하기 위한 대응 관계이다.

```text
ECS

Cluster
  ↓
Task
  ↓
Container


EKS

Cluster
  ↓
Pod
  ↓
Container
```

핵심 차이는 다음과 같다.

```text
ECS
= AWS 자체 Container Orchestration

EKS
= Kubernetes 기반 Container Orchestration
```

회사가 이미 On-Premises 또는 다른 Cloud에서 Kubernetes를 사용하고 있다면 AWS에서도 Kubernetes 환경을 유지하기 위해 EKS를 사용할 수 있다.

---

# 4. EKS Cluster / Node / Pod

## Cluster

Cluster는 Kubernetes 리소스를 관리하기 위한 **논리적인 관리 영역**이다.

Cluster 자체를 하나의 서버라고 생각하면 안 된다.

## Node

Node는 실제로 Pod를 실행하는 Compute Resource이다.

EC2를 사용하는 경우:

```text
EKS Cluster
     ↓
EC2 Node
     ↓
    Pod
     ↓
 Container
```

## Pod

Pod는 Kubernetes에서 Container를 실행하는 **기본 실행 단위**이다.

하나의 Pod에는 하나 이상의 Container가 포함될 수 있다.

```text
Pod
├── Container
└── Container
```

SAA에서는 우선 다음과 같이 기억하면 된다.

```text
ECS Task ≈ EKS Pod
```

---

# 5. EKS Compute Options

EKS에서도 Container를 실제로 실행할 Compute가 필요하다.

```text
EKS Cluster
     │
     ├── EC2 Nodes
     │
     └── AWS Fargate
```

즉 ECS와 마찬가지로 EC2 또는 Fargate를 사용할 수 있다.

---

## Managed Node Groups

AWS/EKS가 EC2 Node의 생성과 관리를 지원한다.

```text
EKS
 ↓
Managed Node Group
 ↓
Auto Scaling Group
 ↓
EC2 Nodes
 ↓
Pods
```

- EC2 기반
- Auto Scaling Group 사용
- On-Demand Instance 지원
- Spot Instance 지원

---

## Self-Managed Nodes

사용자가 직접 EC2 Node를 생성하고 EKS Cluster에 등록한다.

```text
User
 ↓
EC2 Node 생성
 ↓
EKS Cluster 등록
 ↓
Pod 실행
```

Managed Node Group보다 관리 책임과 자유도가 높다.

---

## AWS Fargate

Fargate를 사용하면 사용자가 EC2 Node를 직접 관리하지 않아도 된다.

```text
EKS
 ↓
Fargate
 ↓
Pod
 ↓
Container
```

즉,

```text
EKS + EC2
→ EC2 Node에서 Pod 실행

EKS + Fargate
→ EC2 Node 관리 없이 Pod 실행
```

---

# 6. EKS IAM Roles

EKS Cluster와 EC2 Node는 서로 다른 역할을 수행하므로 IAM Role도 구분된다.

```text
EKS Cluster
     ↓
Cluster IAM Role


EC2 Node
     ↓
Node IAM Role
```

Cluster Role은 EKS Cluster 자체가 AWS 서비스와 상호작용하기 위해 사용한다.

Node Role은 EKS의 EC2 Worker Node가 필요한 AWS 리소스에 접근하기 위해 사용한다.

---

# 7. EKS Data Volumes

Kubernetes에서는 StorageClass와 CSI(Container Storage Interface) Driver를 통해 AWS Storage Service와 연결할 수 있다.

```text
EKS Pod
   ↓
CSI Driver
   ↓
AWS Storage
```

대표적으로 다음 Storage Service를 사용할 수 있다.

- Amazon EBS
- Amazon EFS
- Amazon FSx for Lustre
- Amazon FSx for NetApp ONTAP

특히 EFS는 Fargate와 함께 사용할 수 있다.

```text
EKS + Fargate
      ↓
     EFS
```

---

# 🎯 Exam Notes

### Amazon ECR

> **Container Image를 저장하고 관리하는 AWS Registry**

```text
Docker Image
     ↓
    ECR
     ↓
ECS / EKS
```

시험 키워드:

- Container Image Registry
- Private / Public Repository
- IAM
- Image Scanning
- ECS Integration

---

### Amazon EKS

> **AWS Managed Kubernetes**

시험 키워드:

- Kubernetes
- Container Orchestration
- EC2 Worker Nodes
- Fargate
- Managed Node Groups
- Kubernetes Migration

```text
"회사가 이미 Kubernetes 사용 중"
              +
       "AWS로 Migration"

              ↓

          Amazon EKS
```

---

### Node vs Pod

```text
Node
= Pod를 실행하는 Compute

Pod
= Container를 실행하는 Kubernetes 기본 단위
```

---

### Managed Node Group

```text
EKS
 ↓
Managed Node Group
 ↓
ASG
 ↓
EC2 Nodes
```

AWS가 EC2 Node 관리를 지원한다.

---

### Fargate

```text
EKS + Fargate
= EC2 Node 직접 관리 X
```

---

# 💡 Practical Example

회사가 기존 On-Premises Kubernetes 환경에서 여러 Microservice를 운영하고 있다고 가정한다.

AWS로 이전하면서 기존 Kubernetes 환경을 최대한 유지하려 한다.

```text
On-Premises

Kubernetes
├── Pod
├── Pod
└── Pod

      ↓ Migration

AWS

Amazon EKS
├── EC2 Node
│    ├── Pod
│    └── Pod
│
└── EC2 Node
     └── Pod
```

Container Image는 Amazon ECR에 저장한다.

```text
Developer
    ↓
Docker Image
    ↓
Amazon ECR
    ↓
Amazon EKS
    ↓
Pod
```

EC2 Node를 직접 관리하고 싶지 않다면 AWS Fargate를 사용할 수 있다.

---

# 🇯🇵 日本語 Summary

Amazon ECR（Elastic Container Registry）は、コンテナイメージを保存・管理するAWSのコンテナレジストリサービスです。Private RepositoryとPublic Repositoryを利用でき、IAMによるアクセス制御やイメージスキャンなどの機能を提供します。

Amazon EKS（Elastic Kubernetes Service）は、AWS上でKubernetesを利用するためのマネージドサービスです。Podはコンテナを実行する基本単位であり、NodeはPodを実行するコンピューティングリソースです。

EKSでは、Managed Node GroupsやSelf-Managed Nodesを使用してEC2上でPodを実行することも、AWS Fargateを使用してEC2 Nodeを直接管理せずにPodを実行することもできます。

---

# 🇺🇸 English Summary

Amazon ECR (Elastic Container Registry) is an AWS container registry service used to store and manage container images. It supports private and public repositories, IAM-based access control, image scanning, tags, and lifecycle management.

Amazon EKS (Elastic Kubernetes Service) is a managed Kubernetes service on AWS. In Kubernetes, Pods are the basic execution units for containers, while Nodes provide the compute resources on which Pods run.

EKS can run Pods on EC2 nodes using managed or self-managed node groups, or on AWS Fargate without directly managing EC2 nodes.

---

# 📚 Vocabulary

| Term | Meaning |
|---|---|
| ECR | AWS Container Image Registry |
| Repository | Container Image를 저장하는 공간 |
| EKS | AWS Managed Kubernetes Service |
| Kubernetes | Container 배포 및 관리를 위한 Open Source Orchestration System |
| Cluster | Kubernetes 리소스를 관리하는 논리적 영역 |
| Node | Pod를 실행하는 Compute Resource |
| Pod | Kubernetes의 기본 Container 실행 단위 |
| Managed Node Group | AWS/EKS가 관리를 지원하는 EC2 Node 그룹 |
| Self-Managed Node | 사용자가 직접 관리하는 EKS EC2 Node |
| Fargate | EC2 서버 관리 없이 Container를 실행하는 Serverless Compute |
| CSI | Kubernetes와 Storage System을 연결하기 위한 표준 Interface |
| StorageClass | Kubernetes에서 Storage 유형 및 Provisioning 방법을 정의하는 Resource |

---

# 📝 Review Questions

### Q1. AWS에서 Container Image를 저장하고 관리하는 서비스는?

<details>
<summary>정답 보기</summary>

**Amazon ECR**

</details>

### Q2. AWS에서 Kubernetes Cluster를 운영하기 위한 Managed Service는?

<details>
<summary>정답 보기</summary>

**Amazon EKS**

</details>

### Q3. Kubernetes에서 Container를 실행하는 기본 단위는?

<details>
<summary>정답 보기</summary>

**Pod**

</details>

### Q4. Kubernetes에서 Pod가 실제로 실행되는 Compute Resource는?

<details>
<summary>정답 보기</summary>

**Node**

EC2 기반 EKS에서는 EC2 Instance가 Node 역할을 할 수 있다.

</details>

### Q5. EKS에서 EC2 Node를 직접 관리하지 않고 Pod를 실행하고 싶다면?

<details>
<summary>정답 보기</summary>

**AWS Fargate**

</details>

### Q6. 회사가 이미 Kubernetes를 사용하고 있으며 AWS로 이전하면서 Kubernetes 환경을 유지하려 한다. 적합한 서비스는?

<details>
<summary>정답 보기</summary>

**Amazon EKS**

</details>

### Q7. Kubernetes에서 AWS Storage를 연결하기 위해 사용하는 표준 Interface는?

<details>
<summary>정답 보기</summary>

**CSI (Container Storage Interface)**

</details>
