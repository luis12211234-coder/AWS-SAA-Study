# WhatIsTheTime.com - Architecture

## Architecture Evolution

```mermaid
flowchart LR
    A[Single EC2] --> B[Vertical Scaling]
    B --> C[Horizontal Scaling]
    C --> D[Route 53]
    D --> E[Elastic Load Balancer]
    E --> F[Auto Scaling Group]
    F --> G[Multi-AZ]
```

---

## Final Architecture

```mermaid
flowchart TB
    USER[Users]

    R53[Amazon Route 53]
    ALB[Application Load Balancer]

    subgraph ASG["Auto Scaling Group"]

        subgraph AZA["Availability Zone A"]
            EC2A[EC2 Web Server]
        end

        subgraph AZB["Availability Zone B"]
            EC2B[EC2 Web Server]
        end

        subgraph AZC["Availability Zone C"]
            EC2C[EC2 Web Server]
        end

    end

    USER --> R53
    R53 -->|Alias| ALB

    ALB --> EC2A
    ALB --> EC2B
    ALB --> EC2C
```

---

## Traffic Flow

```text
User
 ↓
Route 53
 ↓
Application Load Balancer
 ↓
Healthy EC2 Instance
 ↓
Application Response
```

---

## Security Boundary

```mermaid
flowchart LR
    INTERNET[Internet]
    ALB["ALB<br/>Security Group"]
    EC2["EC2<br/>Security Group"]

    INTERNET -->|"HTTP / HTTPS"| ALB
    ALB -->|"Application Traffic"| EC2
```

EC2 Security Group은 Internet 전체가 아니라 ALB Security Group을 Source로 허용한다.

---

## Scaling

```text
Traffic Increase
      ↓
Scaling Policy
      ↓
Auto Scaling Group
      ↓
Launch EC2
      ↓
Health Check
      ↓
ALB Target
```

---

## Failure Handling

```text
EC2 Failure
→ ALB removes unhealthy target
→ ASG replaces instance

Traffic Spike
→ ASG scales out
→ ALB distributes traffic

AZ Failure
→ Instances in other AZs continue serving traffic
```

---

## Architecture Principles

```text
Stateless Compute
      +
Load Balancing
      +
Automatic Scaling
      +
Multi-AZ
      =
Scalable & Highly Available Web Tier
```
