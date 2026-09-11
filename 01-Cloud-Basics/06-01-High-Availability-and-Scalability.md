# High Availability and Scalability

## Overview

Scalability는 시스템이 증가하는 Load를 처리할 수 있도록
확장하는 능력을 의미한다.

두 가지 방식이 있다.

```text
Vertical Scaling
Horizontal Scaling
```

High Availability는 Scalability와 관련되어 있지만
목적이 다른 개념이다.

---

# 1. Vertical Scaling

Vertical Scaling은
하나의 Instance 자체의 성능을 높이거나 낮추는 방식이다.

```text
t2.micro
↓
t2.large
```

AWS 용어:

```text
Scale Up
→ Instance Size 증가

Scale Down
→ Instance Size 감소
```

주로 Database와 같은
Non-Distributed System에서 사용된다.

예:

```text
RDS
ElastiCache
```

단점:

```text
Hardware Limit 존재
```

---

# 2. Horizontal Scaling

Horizontal Scaling은
Instance의 개수를 늘리거나 줄이는 방식이다.

```text
EC2
↓
EC2 EC2
↓
EC2 EC2 EC2
```

AWS 용어:

```text
Scale Out
→ Instance 증가

Scale In
→ Instance 감소
```

대표적으로 Web Application과 같은
Distributed System에서 사용된다.

```text
Auto Scaling Group
Load Balancer
```

와 함께 자주 사용된다.

---

# 3. High Availability

High Availability는
Infrastructure 장애가 발생해도
Application을 계속 사용할 수 있도록 설계하는 것이다.

일반적으로 여러 Availability Zone에
Application을 배치한다.

```text
AZ-A
EC2

AZ-B
EC2
```

AZ-A에 장애가 발생하더라도:

```text
AZ-A ❌
AZ-B ✅
```

서비스를 계속 제공할 수 있다.

---

# Exam Notes

```text
Vertical Scaling
→ Bigger Instance
→ Scale Up / Scale Down
```

```text
Horizontal Scaling
→ More Instances
→ Scale Out / Scale In
```

```text
High Availability
→ Multiple AZs
→ Survive AZ Failure
```

중요:

```text
Scalability
≠
High Availability
```

Scalability는 Load 처리 능력,
High Availability는 장애 상황에서도 서비스를 유지하는 능력이다.

---

# Summary

```text
Vertical
→ Bigger Machine

Horizontal
→ More Machines

High Availability
→ Multiple AZs
```

---

# Japanese Summary

```text
Vertical Scaling
→ インスタンス自体を大きくする

Horizontal Scaling
→ インスタンス数を増減する

High Availability
→ 複数のAZを利用して障害に備える
```

---

# English Summary

```text
Vertical Scaling
→ Increase instance size

Horizontal Scaling
→ Increase the number of instances

High Availability
→ Run applications across multiple AZs
```

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Scalability | スケーラビリティ | 확장성 |
| Vertical Scaling | 垂直スケーリング | 인스턴스 성능 자체를 확장 |
| Horizontal Scaling | 水平スケーリング | 인스턴스 수를 확장 |
| Scale Up | スケールアップ | 인스턴스 성능 증가 |
| Scale Down | スケールダウン | 인스턴스 성능 감소 |
| Scale Out | スケールアウト | 인스턴스 수 증가 |
| Scale In | スケールイン | 인스턴스 수 감소 |
| High Availability | 高可用性 | 장애 상황에서도 서비스를 유지하는 능력 |

---

# Review Questions

### Q1. Vertical Scaling이란?

Instance 자체의 성능을 증가시키는 것이다.

### Q2. Scale Out이란?

Instance의 개수를 증가시키는 것이다.

### Q3. Scale In이란?

Instance의 개수를 감소시키는 것이다.

### Q4. High Availability의 주요 목적은?

Infrastructure 장애가 발생해도
Application을 계속 사용할 수 있도록 하는 것이다.

### Q5. High Availability를 위해 AWS에서 주로 사용하는 구조는?

여러 Availability Zone에 Application을 배치하는 것이다.