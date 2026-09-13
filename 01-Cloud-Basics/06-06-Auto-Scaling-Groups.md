# Auto Scaling Groups (ASG)

## Overview

**Auto Scaling Group (ASG)**은 애플리케이션의 부하와 설정에 따라 **EC2 Instance 수를 자동으로 관리**하는 기능이다.

```text
Load 증가
→ Scale Out
→ EC2 추가

Load 감소
→ Scale In
→ EC2 제거
```

ASG의 주요 역할:

- 부하 증가 시 EC2 Instance 추가
- 부하 감소 시 EC2 Instance 제거
- 지정된 Instance 수 유지
- Unhealthy Instance 자동 교체
- Load Balancer의 Target Group과 연동
- CloudWatch Metric을 기반으로 자동 Scaling

ASG 자체에는 추가 비용이 없으며, 생성된 EC2 등의 Resource에 대해서만 비용을 지불한다.

---

# 1. Scale Out / Scale In

## Scale Out

부하가 증가했을 때 EC2 Instance를 추가한다.

```text
Traffic ↑
   ↓
ASG
   ↓
EC2 추가
```

## Scale In

부하가 감소했을 때 불필요한 EC2 Instance를 제거한다.

```text
Traffic ↓
   ↓
ASG
   ↓
EC2 제거
```

### Key Point

```text
Scale Out = EC2 추가
Scale In  = EC2 제거
```

---

# 2. Minimum / Desired / Maximum Capacity

ASG는 세 가지 Capacity 값을 이용해 Instance 수를 관리한다.

```text
Minimum ≤ Desired ≤ Maximum
```

| Capacity | Description |
|---|---|
| Minimum Capacity | ASG가 유지해야 하는 최소 Instance 수 |
| Desired Capacity | ASG가 현재 유지하려는 Instance 수 |
| Maximum Capacity | ASG가 확장할 수 있는 최대 Instance 수 |

예:

```text
Minimum = 2
Desired = 4
Maximum = 7
```

ASG는 현재 Instance 수를 **Desired Capacity**에 맞추려고 한다.

예를 들어:

```text
Desired = 4

EC2 A
EC2 B
EC2 C
EC2 D
```

Instance 하나가 사라지면:

```text
현재 Instance = 3
Desired       = 4
        ↓
ASG가 새 EC2 생성
        ↓
현재 Instance = 4
```

Desired Capacity를 직접 변경하는 것도 가능하다.

```text
Desired: 1 → 2
→ EC2 추가

Desired: 2 → 1
→ EC2 제거
```

---

# 3. Launch Template

ASG가 새로운 EC2 Instance를 생성하려면 **어떤 설정으로 EC2를 만들 것인지** 알아야 한다.

이를 정의하는 것이 **Launch Template**이다.

```text
Launch Template
      ↓
     ASG
      ↓
EC2  EC2  EC2
```

Launch Template에는 EC2 생성에 필요한 설정이 포함될 수 있다.

- AMI
- Instance Type
- EC2 User Data
- EBS Volumes
- Security Groups
- SSH Key Pair
- IAM Role
- Network 관련 설정

즉:

```text
Launch Template
= EC2 생성 설정

ASG
= Launch Template을 이용해
  EC2를 생성하고 Instance 수를 관리
```

> 기존의 **Launch Configuration**은 이전 방식이며, 현재는 Launch Template을 사용한다.

---

# 4. Multi-AZ Deployment

ASG는 여러 Subnet을 이용해 여러 Availability Zone에 EC2 Instance를 배치할 수 있다.

예:

```text
                 ASG
          ┌───────┼───────┐
          ↓       ↓       ↓
        AZ-A    AZ-B     AZ-C
          ↓       ↓       ↓
         EC2     EC2     EC2
```

이를 Load Balancer와 함께 사용하면 여러 AZ에 걸쳐 확장 가능한 애플리케이션을 구성할 수 있다.

---

# 5. ASG + Load Balancer

ASG는 Load Balancer의 **Target Group**과 연동할 수 있다.

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

두 서비스의 역할은 다르다.

```text
ALB
→ Traffic을 Target들에게 분산

ASG
→ EC2 Instance 수를 관리
```

ASG가 새로운 EC2 Instance를 생성하면 Target Group에 등록되어 Load Balancer가 해당 Instance에도 Traffic을 전달할 수 있다.

```text
ASG
 ↓
EC2 생성
 ↓
Target Group 등록
 ↓
Health Check
 ↓
Healthy
 ↓
ALB가 Traffic 전달
```

따라서 **ALB + ASG**는 확장성과 가용성을 높이는 대표적인 조합이다.

---

# 6. Health Checks

ASG는 Instance의 상태를 확인하여 비정상 Instance를 교체할 수 있다.

## EC2 Health Check

EC2 자체의 상태가 비정상이라면 ASG가 해당 Instance를 종료하고 새로운 Instance를 생성할 수 있다.

```text
EC2 Unhealthy
      ↓
ASG
      ↓
Terminate
      ↓
Launch New EC2
```

## ELB Health Check

ASG를 Load Balancer와 연동하면 ELB Health Check도 사용할 수 있다.

Load Balancer가 Target을 Unhealthy로 판단하면 ASG가 해당 Instance를 교체할 수 있다.

```text
ELB Health Check
       ↓
EC2 Unhealthy
       ↓
ASG
       ↓
Instance 교체
       ↓
Desired Capacity 복구
```

### Troubleshooting

새로운 Instance가 계속 Unhealthy 상태가 되어 반복적으로 교체된다면 다음 항목을 확인한다.

- Security Group
- EC2 User Data
- Application / Web Server
- Health Check 설정

---

# 7. Auto Scaling Policies

ASG는 **Scaling Policy**를 사용해 EC2 Capacity를 자동으로 조절할 수 있다.

주요 방식:

```text
Scaling Policies

├─ Dynamic Scaling
│  ├─ Target Tracking Scaling
│  ├─ Step Scaling
│  └─ Simple Scaling
│
├─ Scheduled Scaling
│
└─ Predictive Scaling
```

---

## 7.1 Target Tracking Scaling

**Target Tracking Scaling**은 지정한 Metric을 목표값 근처로 유지하도록 ASG가 자동으로 Scale Out / Scale In하는 방식이다.

예:

```text
Target Average CPU = 40%
```

CPU 사용률이 목표보다 높아지면:

```text
CPU ↑
 ↓
Scale Out
 ↓
EC2 추가
 ↓
CPU 부하 분산
```

CPU 사용률이 충분히 낮아지면:

```text
CPU ↓
 ↓
Scale In
 ↓
EC2 제거
```

전체 흐름:

```text
CloudWatch Metric
       ↓
CloudWatch Alarm
       ↓
Target Tracking Policy
       ↓
      ASG
       ↓
Scale Out / Scale In
```

Target Tracking에서는 사용자가 목표값을 정의하고 ASG가 필요한 Capacity를 자동으로 조절한다.

### Hands-On Verification

실습에서는 Average CPU Utilization을 Target Metric으로 사용했다.

EC2 Instance에 높은 CPU Load를 발생시키자:

```text
CPU Utilization ↑
       ↓
CloudWatch Alarm
       ↓
Target Tracking Policy
       ↓
Desired Capacity 증가
       ↓
새 EC2 생성
```

CPU Load를 제거한 뒤에는:

```text
CPU Utilization ↓
       ↓
CloudWatch Alarm
       ↓
Target Tracking Policy
       ↓
Desired Capacity 감소
       ↓
EC2 제거
```

즉 실제로 CPU Load에 따라 ASG가 자동으로 Scale Out / Scale In하는 것을 확인했다.

Target Tracking Policy를 생성하면 Scaling에 필요한 CloudWatch Alarm이 자동으로 생성될 수 있다.

---

## 7.2 Simple Scaling

**Simple Scaling**은 CloudWatch Alarm이 발생하면 지정된 Scaling Action을 수행한다.

예:

```text
CPU Alarm
   ↓
Add 2 Instances
```

Scaling Action으로 Instance를 추가하거나 제거하거나 특정 Capacity로 설정할 수 있다.

```text
Alarm
 ↓
Add
Remove
Set Capacity
```

---

## 7.3 Step Scaling

**Step Scaling**은 Metric 값의 정도에 따라 Scaling 크기를 다르게 설정한다.

예:

```text
CPU 조금 높음
→ +1 Instance

CPU 많이 높음
→ +2 Instances

CPU 매우 높음
→ +3 Instances
```

즉 부하의 심각도에 따라 단계적으로 대응할 수 있다.

### Target Tracking vs Step Scaling

```text
Target Tracking
→ "CPU를 40% 정도로 유지해줘"

Step Scaling
→ "CPU가 이 구간이면 이만큼 늘려줘"
```

---

## 7.4 Scheduled Scaling

**Scheduled Scaling**은 미리 알고 있는 Traffic 패턴이나 이벤트에 맞춰 Capacity 변경을 예약한다.

예:

```text
매주 금요일 17:00
        ↓
Minimum Capacity 증가
```

또는:

```text
다음 주 토요일
대규모 Promotion 예정
        ↓
미리 Capacity 증가
```

Desired / Minimum / Maximum Capacity를 특정 시간이나 반복 일정에 맞춰 변경할 수 있다.

### Exam Point

```text
Known Future Traffic / Event
            ↓
     Scheduled Scaling
```

---

## 7.5 Predictive Scaling

**Predictive Scaling**은 과거 Load 패턴을 분석하여 미래 Capacity 요구량을 예측하고 Scaling을 준비한다.

```text
Historical Load
      ↓
Load Prediction
      ↓
Future Capacity 예측
      ↓
Scaling
```

반복적인 Traffic 패턴이 있는 환경에서 유용하다.

### Scheduled vs Predictive

```text
Scheduled Scaling
→ 사용자가 미래 Traffic 패턴을 알고 직접 예약

Predictive Scaling
→ 과거 Load를 분석해 미래 Capacity를 예측
```

---

# 8. Scaling Metrics

어떤 Metric을 Scaling 기준으로 사용할지는 애플리케이션의 특성에 따라 달라진다.

대표적인 Metric:

| Metric | Use Case |
|---|---|
| Average CPU Utilization | CPU 작업이 많은 Application |
| Request Count Per Target | ALB 뒤의 Web Application |
| Network In / Out | Upload / Download가 많은 Application |
| Custom Metric | Application 고유의 Scaling 기준 |

## CPU Utilization

ASG 전체 Instance의 평균 CPU 사용률을 기준으로 Scaling할 수 있다.

```text
Average CPU ↑
→ Instance 부하 ↑
→ Scale Out
```

## Request Count Per Target

Load Balancer가 Target 하나당 처리하는 Request 수를 기준으로 Scaling할 수 있다.

```text
Request Count / Target ↑
          ↓
Instance당 부하 ↑
          ↓
Scale Out
```

## Network In / Out

Upload / Download가 많고 Network가 병목이 되는 Application에서는 Network 사용량을 기준으로 Scaling할 수 있다.

## Custom Metric

CloudWatch의 Custom Metric을 이용해 Application에 특화된 Scaling 기준을 만들 수도 있다.

### Key Point

```text
좋은 Scaling Metric
= 실제 Application Load와 병목을 잘 나타내는 Metric
```

---

# 9. Scaling Cooldown

Scaling Action 직후에는 새로운 Instance의 효과가 Metric에 반영될 시간이 필요하다.

이를 위해 **Scaling Cooldown**을 사용할 수 있다.

강의 기준 기본값:

```text
300 seconds
= 5 minutes
```

개념:

```text
Scaling Action
      ↓
Instance 추가 / 제거
      ↓
Cooldown
      ↓
Metric 안정화
      ↓
다음 Scaling 판단
```

예를 들어 Scale Out 직후 CPU가 아직 높다고 해서 즉시 계속 Instance를 추가하면 과도한 Scaling이 발생할 수 있다.

Cooldown은 새로운 Capacity의 효과가 나타날 시간을 제공한다.

EC2 Instance가 빠르게 Application을 실행할 수 있도록 미리 구성된 AMI 등을 사용하면 Scaling 반응 속도를 높이는 데 도움이 된다.

---

# 10. Hands-On Summary

실습에서는 다음 구조를 구성했다.

```text
Launch Template
      ↓
     ASG
      ↓
EC2 Instance
      ↓
Target Group
      ↓
     ALB
```

Launch Template을 이용해 ASG가 EC2 Instance를 자동으로 생성하는 것을 확인했다.

### Desired Capacity 변경

```text
Desired: 1 → 2
       ↓
새 EC2 생성
       ↓
Target Group 등록
       ↓
ALB가 Traffic 분산
```

반대로:

```text
Desired: 2 → 1
       ↓
EC2 하나 종료
       ↓
Target Group 등록 해제
       ↓
Instance 수 감소
```

### Target Tracking

CPU Load를 증가시키자:

```text
CPU ↑
 ↓
CloudWatch Alarm
 ↓
Target Tracking
 ↓
Scale Out
```

CPU Load가 감소하자:

```text
CPU ↓
 ↓
CloudWatch Alarm
 ↓
Target Tracking
 ↓
Scale In
```

이를 통해 ASG가 **Desired Capacity와 Application Load에 맞춰 EC2 Instance 수를 자동으로 관리**하는 것을 확인했다.

---

# Exam Notes

## ASG Core

```text
ASG
→ EC2 Instance 수 자동 관리

Scale Out
→ EC2 추가

Scale In
→ EC2 제거

Minimum
→ 최소 Instance 수

Desired
→ 현재 목표 Instance 수

Maximum
→ 최대 Instance 수

Launch Template
→ 새 EC2의 생성 설정

Unhealthy Instance
→ ASG가 교체 가능
```

## ALB + ASG

```text
ALB
→ Traffic 분산

ASG
→ Instance 수 관리

새 EC2
→ Target Group 등록
→ Health Check
→ Traffic 수신
```

## Scaling Policies

```text
Target Tracking
→ Metric 목표값 유지

Simple Scaling
→ Alarm 발생 시 지정된 Scaling Action

Step Scaling
→ Metric 구간에 따라 Scaling 크기 조절

Scheduled Scaling
→ 알려진 미래 Traffic / Event

Predictive Scaling
→ 과거 Load를 분석하여 미래 Capacity 예측
```

## Scaling Metrics

```text
CPU Utilization
Request Count Per Target
Network In / Out
Custom Metric
```

## Quick Architecture

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
       ↓
Scale Out / Scale In
```

---

# 日本語まとめ

- **Auto Scaling Group (ASG)** はEC2インスタンス数を自動的に管理する
- **Scale Out** はEC2インスタンスを追加する
- **Scale In** はEC2インスタンスを削除する
- **Minimum / Desired / Maximum Capacity** でASGのサイズを制御する
- **Launch Template** は新しいEC2インスタンスの起動設定を定義する
- ASGはLoad BalancerのTarget Groupと連携できる
- 異常なInstanceを終了し、新しいInstanceに置き換えることができる
- **Target Tracking Scaling** はMetricを指定した目標値付近に維持する
- **Step Scaling** はMetricの値に応じて段階的にCapacityを変更する
- **Scheduled Scaling** は既知のスケジュールに基づいてScalingする
- **Predictive Scaling** は過去の負荷を分析して将来のCapacityを予測する
- CloudWatch MetricとAlarmを利用して自動Scalingを実行できる
- **Scaling Cooldown** はScaling後にMetricが安定するための時間を与える

---

# English Summary

- **Auto Scaling Groups (ASG)** automatically manage EC2 capacity.
- **Scale Out** adds instances and **Scale In** removes instances.
- **Minimum, Desired, and Maximum Capacity** control the size of an ASG.
- A **Launch Template** defines how new EC2 instances are launched.
- ASG can integrate with a Load Balancer target group.
- Unhealthy instances can be terminated and replaced automatically.
- **Target Tracking Scaling** maintains a target metric value.
- **Simple Scaling** performs a predefined action when an alarm is triggered.
- **Step Scaling** adjusts capacity according to metric thresholds.
- **Scheduled Scaling** handles known future traffic patterns or events.
- **Predictive Scaling** forecasts future capacity based on historical load.
- CloudWatch metrics and alarms can trigger scaling activities.
- **Scaling Cooldown** gives metrics time to stabilize after a scaling activity.

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
| Target Tracking Scaling | ターゲット追跡スケーリング | 대상 추적 스케일링 |
| Simple Scaling | シンプルスケーリング | 단순 스케일링 |
| Step Scaling | ステップスケーリング | 단계 스케일링 |
| Scheduled Scaling | スケジュールスケーリング | 예약 스케일링 |
| Predictive Scaling | 予測スケーリング | 예측 스케일링 |
| Scaling Cooldown | スケーリングクールダウン | 스케일링 쿨다운 |
| Metric | メトリクス | 지표 |
| CloudWatch Alarm | CloudWatchアラーム | CloudWatch 경보 |
| Health Check | ヘルスチェック | 상태 확인 |
| Target Group | ターゲットグループ | 대상 그룹 |

---

# Review Questions

1. Scale Out과 Scale In의 차이는 무엇인가?
2. Minimum, Desired, Maximum Capacity는 각각 무엇을 의미하는가?
3. Launch Template은 ASG에서 어떤 역할을 하는가?
4. ASG와 Load Balancer를 함께 사용하면 각각 어떤 역할을 담당하는가?
5. EC2 또는 ELB Health Check에서 Instance가 Unhealthy로 판단되면 ASG는 어떻게 동작할 수 있는가?
6. Target Tracking Scaling은 어떤 방식으로 Capacity를 조절하는가?
7. Target Tracking과 Step Scaling의 차이는 무엇인가?
8. Scheduled Scaling과 Predictive Scaling의 차이는 무엇인가?
9. ASG Scaling에 사용할 수 있는 대표적인 Metric에는 무엇이 있는가?
10. Scaling Cooldown이 필요한 이유는 무엇인가?