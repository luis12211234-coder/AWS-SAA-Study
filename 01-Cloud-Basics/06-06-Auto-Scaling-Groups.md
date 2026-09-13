# Auto Scaling Groups (ASG)

## Overview

**Auto Scaling Group (ASG)**은 부하와 설정에 따라 EC2 Instance 수를 자동으로 관리한다.

```text
Scale Out → EC2 추가
Scale In  → EC2 제거
```

ASG의 주요 역할:

- 부하 증가 시 EC2 추가
- 부하 감소 시 EC2 제거
- 지정된 Instance 수 유지
- Unhealthy Instance 교체
- Load Balancer에 새 Instance 자동 등록

ASG 자체에는 추가 비용이 없으며 생성된 EC2 등의 Resource 비용을 지불한다.

---

# 1. Min / Desired / Max Capacity

ASG는 세 가지 Capacity를 사용한다.

```text
Min ≤ Desired ≤ Max
```

| Capacity | 의미 |
|---|---|
| Minimum | 유지해야 하는 최소 Instance 수 |
| Desired | 현재 유지하려는 Instance 수 |
| Maximum | 확장 가능한 최대 Instance 수 |

예:

```text
Min     = 2
Desired = 4
Max     = 7
```

Desired Capacity가 증가하면 필요한 EC2를 생성하고, 감소하면 불필요한 EC2를 종료한다.

---

# 2. Launch Template

**Launch Template**은 ASG가 EC2 Instance를 생성할 때 사용하는 설정이다.

```text
Launch Template
      ↓
     ASG
      ↓
EC2  EC2  EC2
```

주요 설정:

- AMI
- Instance Type
- EC2 User Data
- EBS Volumes
- Security Groups
- SSH Key Pair
- IAM Role
- Network / Subnets

> 기존의 Launch Configuration은 deprecated되었으며 Launch Template을 사용한다.

---

# 3. ASG + Load Balancer

ASG는 Load Balancer의 Target Group과 연동할 수 있다.

```text
             Users
               ↓
              ALB
         ┌─────┼─────┐
         ↓     ↓     ↓
       EC2   EC2   EC2
         └─────┬─────┘
               │
              ASG
```

역할:

```text
ALB
→ Traffic 분산

ASG
→ EC2 Instance 수 관리
```

ASG가 새로운 EC2를 생성하면 Target Group에 등록되어 Load Balancer가 해당 Instance에도 Traffic을 전달할 수 있다.

---

# 4. Health Checks

ASG는 Instance 상태를 확인하고 비정상 Instance를 교체할 수 있다.

### EC2 Health Check

EC2 자체가 비정상 상태가 되면 ASG가 해당 Instance를 교체한다.

### ELB Health Check

ELB Health Check를 사용하도록 설정하면 Load Balancer가 Target을 비정상으로 판단했을 때 ASG가 해당 Instance를 교체할 수 있다.

```text
Unhealthy Instance
       ↓
ASG terminates it
       ↓
New EC2 launched
       ↓
Desired Capacity restored
```

---

# 5. Scaling with CloudWatch

ASG는 **CloudWatch Alarm**과 Scaling Policy를 이용해 자동으로 Scale Out / Scale In할 수 있다.

```text
CloudWatch Metric
       ↓
CloudWatch Alarm
       ↓
Scaling Policy
       ↓
      ASG
       ↓
Scale Out / Scale In
```

예:

```text
Average CPU High
       ↓
CloudWatch Alarm
       ↓
Scale Out
       ↓
EC2 추가
```

반대로 부하가 감소하면 Scaling Policy에 따라 EC2를 제거할 수 있다.

---

# 6. Hands-On Summary

실습에서는 다음 구조를 구성했다.

```text
Launch Template
      ↓
DemoASG
      ↓
EC2 Instance
      ↓
Target Group
      ↓
ALB
```

### Launch Template

실습 설정:

- Amazon Linux 2
- `t2.micro`
- Security Group
- EBS Volume
- User Data로 Web Server 구성

ASG가 Launch Template을 이용해 동일한 설정의 EC2를 자동 생성하는 것을 확인했다.

### Multi-AZ

ASG가 여러 Subnet을 사용하도록 설정하여 여러 AZ에서 Instance를 실행할 수 있다.

```text
ASG
├─ eu-west-1a
├─ eu-west-1b
└─ eu-west-1c
```

### Load Balancer Integration

ASG를 기존 ALB Target Group과 연결했다.

새 EC2가 생성되면:

```text
ASG launches EC2
       ↓
Target Group 등록
       ↓
Health Check
       ↓
Healthy
       ↓
ALB Traffic 수신
```

### Desired Capacity 변경

실습에서 Desired Capacity를 직접 변경하여 ASG 동작을 확인했다.

```text
Desired: 1 → 2
       ↓
새 EC2 생성
       ↓
Target Group 등록
       ↓
ALB가 두 Instance에 Traffic 분산
```

반대로:

```text
Desired: 2 → 1
       ↓
EC2 하나 종료
       ↓
Target Group에서 등록 해제
       ↓
Instance 1개 유지
```

즉 ASG는 현재 Instance 수를 **Desired Capacity에 맞추려고 동작한다.**

---

# 7. Troubleshooting

새 Instance가 계속 Unhealthy 상태가 되면 ASG가 Instance를 반복적으로 교체할 수 있다.

대표적으로 확인할 항목:

```text
Security Group
User Data
Application / Web Server
Health Check 설정
```

---

# Exam Notes

```text
Scale Out
→ EC2 추가

Scale In
→ EC2 제거

Min
→ 최소 Instance 수

Desired
→ 목표 Instance 수

Max
→ 최대 Instance 수

Launch Template
→ EC2 생성 설정

Unhealthy Instance
→ ASG가 교체

ALB + ASG
→ Traffic 분산 + Instance 수 자동 관리

CloudWatch Alarm
→ Scaling Policy
→ Scale Out / Scale In
```

### Architecture

```text
                    Users
                      ↓
                     ALB
                ┌─────┼─────┐
                ↓     ↓     ↓
              EC2   EC2   EC2
                └─────┬─────┘
                      │
                     ASG
                      ↑
               Launch Template


CloudWatch Metric
       ↓
CloudWatch Alarm
       ↓
Scaling Policy
       ↓
      ASG
```

---

# 日本語まとめ

- **Auto Scaling Group (ASG)** はEC2インスタンス数を自動的に管理する
- **Scale Out** はEC2を追加する
- **Scale In** はEC2を削除する
- **Minimum / Desired / Maximum Capacity** でインスタンス数を制御する
- **Launch Template** はASGが起動するEC2の設定を定義する
- ASGはLoad BalancerのTarget Groupと連携できる
- 異常なInstanceを終了し、新しいInstanceに置き換えることができる
- CloudWatch AlarmとScaling Policyを利用して自動スケーリングできる

---

# English Summary

- **Auto Scaling Groups (ASG)** automatically manage the number of EC2 instances.
- **Scale Out** adds instances and **Scale In** removes instances.
- **Minimum, Desired, and Maximum Capacity** control the size of an ASG.
- A **Launch Template** defines how new EC2 instances are launched.
- ASG can automatically register new instances with a Load Balancer target group.
- Unhealthy instances can be terminated and replaced.
- CloudWatch Alarms and Scaling Policies can trigger automatic scaling.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Auto Scaling Group | Auto Scalingグループ | 오토 스케일링 그룹 |
| Scale Out | スケールアウト | 인스턴스 확장 |
| Scale In | スケールイン | 인스턴스 축소 |
| Minimum Capacity | 最小キャパシティ | 최소 용량 |
| Desired Capacity | 希望するキャパシティ | 희망 용량 |
| Maximum Capacity | 最大キャパシティ | 최대 용량 |
| Launch Template | 起動テンプレート | 시작 템플릿 |
| Scaling Policy | スケーリングポリシー | 스케일링 정책 |
| Health Check | ヘルスチェック | 상태 확인 |
| CloudWatch Alarm | CloudWatchアラーム | CloudWatch 경보 |
| Target Group | ターゲットグループ | 대상 그룹 |

---

# Review Questions

1. Scale Out과 Scale In의 차이는 무엇인가?
2. Minimum, Desired, Maximum Capacity는 각각 무엇을 의미하는가?
3. Launch Template은 ASG에서 어떤 역할을 하는가?
4. ASG와 Load Balancer를 함께 사용하면 어떤 장점이 있는가?
5. ELB Health Check에서 Instance가 Unhealthy로 판단되면 ASG는 어떻게 동작할 수 있는가?
6. Desired Capacity를 1에서 2로 변경하면 ASG는 어떻게 동작하는가?
7. CloudWatch Alarm과 Scaling Policy는 ASG와 어떻게 연결되는가?