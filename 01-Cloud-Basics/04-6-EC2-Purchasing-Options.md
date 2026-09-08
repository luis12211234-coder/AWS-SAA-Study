# EC2 Purchasing Options

## Overview

Amazon EC2는 Workload의 기간, 중단 가능 여부, 비용 최적화 요구사항에 따라 여러 Purchasing Option을 제공한다.

주요 EC2 Purchasing Options는 다음과 같다.

- On-Demand Instances
- Reserved Instances
- Convertible Reserved Instances
- Savings Plans
- Spot Instances
- Dedicated Hosts
- Dedicated Instances
- Capacity Reservations

EC2 Purchasing Option을 선택할 때 가장 중요한 것은 다음 질문이다.

```text
얼마나 오래 사용할 것인가?

사용량을 예측할 수 있는가?

Instance가 중단되어도 괜찮은가?

물리적 Hardware를 전용으로 사용해야 하는가?

특정 AZ의 Capacity를 반드시 확보해야 하는가?
```

---

## On-Demand Instances

On-Demand Instance는 장기적인 약정 없이 필요한 만큼 EC2 Instance를 실행하는 방식이다.

```text
필요할 때 실행
↓
사용한 만큼 지불
↓
장기 약정 없음
```

### Characteristics

- Upfront Payment 없음
- Long-Term Commitment 없음
- 사용한 만큼 비용 지불
- 다른 구매 옵션보다 비용이 높은 편

### Use Cases

다음과 같은 Workload에 적합하다.

- Short-Term Workloads
- Uninterrupted Workloads
- Application 사용량을 예측하기 어려운 경우

```text
Short-Term
+
Unpredictable
+
Must Not Be Interrupted
↓
On-Demand
```

---

## Reserved Instances

Reserved Instance는 장기간 EC2를 사용할 것을 약정하여 On-Demand보다 할인받는 방식이다.

강의에서는 다음 기간을 설명한다.

```text
1 Year
or
3 Years
```

기간이 길수록 더 큰 할인을 받을 수 있다.

### Reserved Attributes

강의에서는 다음과 같은 Instance Attribute를 기준으로 예약한다고 설명한다.

- Instance Type
- Region
- Tenancy
- Operating System

### Payment Options

```text
No Upfront

Partial Upfront

All Upfront
```

일반적으로 더 많이 선결제할수록 할인 폭이 커진다.

### Use Cases

사용량이 일정하고 장기간 실행되는 Application에 적합하다.

대표적인 예:

```text
Database
```

```text
Predictable
+
Long-Term
↓
Reserved Instance
```

---

## Convertible Reserved Instances

Convertible Reserved Instance는 일반 Reserved Instance보다 더 많은 변경 유연성을 제공한다.

강의에서는 다음 Attribute를 변경할 수 있다고 설명한다.

- Instance Type
- Instance Family
- Operating System
- Scope
- Tenancy

유연성이 더 큰 대신 일반 Reserved Instance보다 할인 폭은 작을 수 있다.

```text
Reserved Instance
+
More Flexibility
↓
Convertible Reserved Instance
```

---

## EC2 Savings Plans

Savings Plans는 장기간의 EC2 사용량을 약정하여 비용을 할인받는 방식이다.

Reserved Instance처럼 특정 Instance 하나만을 예약하는 대신 일정한 **시간당 사용 금액**을 약정한다.

강의 예시:

```text
$10 / hour
for
1 or 3 years
```

약정한 사용량을 넘어서는 부분은 On-Demand 가격으로 청구된다.

PDF에서도 Savings Plans는 장기 사용량을 약정하고, 약정량을 초과한 사용량은 On-Demand 가격으로 청구된다고 설명한다. 2

### Flexibility

강의와 PDF에서는 특정 Instance Family와 Region에 연결되면서 다음 요소에는 유연성이 있다고 설명한다. 3

- Instance Size
- Operating System
- Tenancy

예:

```text
M5
+
us-east-1
```

안에서

```text
m5.xlarge
m5.2xlarge
...
```

등의 크기를 사용할 수 있다.

---

## Reserved Instances vs Savings Plans

둘 다 장기간 사용을 약정하여 비용을 최적화한다.

### Reserved Instance

```text
특정 EC2 Configuration을 장기간 사용
↓
Reserved Instance
```

### Savings Plans

```text
특정 수준의 시간당 사용 금액을 장기간 약정
↓
Savings Plans
```

시험에서는 세부 가격보다 **Long-Term Commitment를 통한 비용 절감**이라는 공통점을 먼저 기억한다.

---

## Spot Instances

Spot Instance는 AWS의 남는 EC2 Capacity를 매우 저렴하게 사용할 수 있는 방식이다.

강의와 PDF에서는 On-Demand 대비 최대 90%까지 할인될 수 있다고 설명한다. 4

하지만 중요한 단점이 있다.

```text
Very Cheap
↓
하지만
↓
Instance를 잃을 수 있음
```

따라서 중단에 대응할 수 있는 Workload에 적합하다.

### Use Cases

- Batch Jobs
- Data Analysis
- Image Processing
- Distributed Workloads
- Flexible Start / End Time Workloads

### Not Suitable

- Critical Jobs
- Databases

핵심:

```text
Can tolerate interruption?
        │
        ├─ YES → Spot 고려
        │
        └─ NO  → Spot 부적합
```

---

## Dedicated Hosts

Dedicated Host는 EC2 Instance를 실행하기 위한 **전체 Physical Server**를 한 고객에게 전용으로 제공한다.

PDF에서는 Physical Server의 EC2 Capacity가 완전히 사용자에게 전용으로 제공된다고 설명한다. 5

```text
AWS Physical Server
└─ Only Your Workloads
```

### Use Cases

- Compliance Requirements
- Regulatory Requirements
- Server-Bound Software Licenses
- BYOL (Bring Your Own License)

특히 다음과 같이 Hardware 기반 License가 있는 경우 사용할 수 있다.

```text
Per-Socket License
Per-Core License
Per-VM License
```

### Characteristics

- Physical Server를 전용으로 사용
- Hardware에 대한 높은 수준의 제어 및 가시성
- 매우 비싼 Option

---

## Dedicated Instances

Dedicated Instance는 다른 AWS 고객과 Hardware를 공유하지 않고 전용 Hardware에서 실행되는 EC2 Instance이다.

하지만 Dedicated Host와는 다르다.

### Dedicated Instance

```text
Dedicated Hardware
↓
EC2 Instance 실행
```

같은 AWS Account의 다른 Instance와 Hardware를 공유할 수 있으며 Instance Placement를 직접 제어하지 않는다.

### Dedicated Host

```text
Physical Server 자체
↓
사용자에게 전용 제공
```

Hardware 및 Instance Placement에 대한 더 높은 수준의 제어를 제공한다.

---

## Dedicated Host vs Dedicated Instance

```text
Dedicated Host
→ Physical Server 자체를 전용 사용
→ Hardware Visibility / Placement Control
→ BYOL / Compliance

Dedicated Instance
→ 다른 고객과 Hardware를 공유하지 않음
→ Physical Server 자체에 대한 Control은 없음
```

시험에서는 **Physical Server 자체가 필요하다**는 조건이 나오면 Dedicated Host를 먼저 생각한다.

---

## EC2 Capacity Reservations

Capacity Reservation은 특정 Availability Zone에서 EC2 Capacity를 확보하는 기능이다.

```text
Specific AZ
↓
Reserve Capacity
↓
필요할 때 Instance 실행 가능
```

PDF에서는 원하는 기간 동안 특정 AZ의 On-Demand Instance Capacity를 예약할 수 있다고 설명한다. 6

### Characteristics

- 특정 AZ에 Capacity 예약
- 기간 약정 없음
- 언제든 생성 / 취소 가능
- Billing Discount 없음
- Instance를 실행하지 않아도 예약한 Capacity에 대한 비용 발생

즉:

```text
Capacity Reservation
≠ Discount

Capacity Reservation
= Capacity Guarantee
```

### Use Cases

특정 AZ에서 반드시 실행되어야 하는 단기적이고 중단 없는 Workload에 적합하다.

---

## Capacity Reservation and Discounts

Capacity Reservation 자체에는 할인 효과가 없다.

PDF에서는 비용 할인을 받고 싶다면 다음과 결합할 수 있다고 설명한다. 7

```text
Capacity Reservation
+
Regional Reserved Instance
```

또는

```text
Capacity Reservation
+
Savings Plans
```

즉:

```text
Capacity Reservation
→ Capacity 확보

Reserved / Savings Plans
→ Cost 절감
```

역할이 다르다.

---

## Purchasing Options Comparison

| Option | Main Purpose | Commitment | Interruption | Key Use Case |
|---|---|---|---|---|
| On-Demand | Flexible Usage | 없음 | 없음 | Short-Term / Unpredictable |
| Reserved Instance | Cost Saving | 1 / 3 Years | 없음 | Long-Term Predictable |
| Savings Plans | Cost Saving | 1 / 3 Years | 없음 | Long-Term Usage Commitment |
| Spot Instance | Maximum Cost Saving | 없음 | 가능 | Fault-Tolerant Workloads |
| Dedicated Host | Dedicated Physical Server | 선택 가능 | 없음 | BYOL / Compliance |
| Dedicated Instance | Dedicated Hardware | - | 없음 | Hardware Isolation |
| Capacity Reservation | Capacity Guarantee | 없음 | 없음 | Specific AZ Capacity |

---

## Which Option Should I Choose?

```text
갑자기 서버가 필요하다
사용량도 잘 모르겠다
↓
On-Demand
```

```text
DB를 몇 년 동안 계속 돌릴 예정이다
↓
Reserved Instance
```

```text
1~3년 동안 일정 수준의 Compute 사용량이 확실하다
↓
Savings Plans
```

```text
Batch 작업이고 중간에 Instance가 사라져도 다시 실행할 수 있다
↓
Spot Instance
```

```text
Software License가 Physical Core / Socket에 묶여 있다
↓
Dedicated Host
```

```text
다른 AWS 고객과 Hardware를 공유하면 안 된다
↓
Dedicated Instance
```

```text
반드시 특정 AZ에서 EC2를 실행할 Capacity가 필요하다
↓
Capacity Reservation
```

---

## Resort Analogy

강의에서는 EC2 Purchasing Options를 Resort에 비유한다.

### On-Demand

```text
원할 때 Resort 방문
→ 정상 가격 지불
```

### Reserved Instance

```text
1~3년 오래 머물 예정
→ 미리 약정
→ 할인
```

### Savings Plans

```text
일정 기간 동안
일정 금액을 Resort에 쓰겠다고 약정
```

### Spot Instance

```text
남는 객실을 매우 싸게 사용
↓
더 높은 우선순위 사용자가 나타나면
객실을 잃을 수 있음
```

### Dedicated Host

```text
Resort 건물 전체 예약
```

### Capacity Reservation

```text
객실을 미리 확보

실제로 머물지 않아도
객실 비용은 지불
```

---

## Summary

- On-Demand는 장기 약정 없이 필요한 만큼 사용하는 방식이다.
- Reserved Instance는 장기간 사용을 약정하여 비용을 절감한다.
- Savings Plans는 일정한 장기 사용량을 약정하여 비용을 절감한다.
- Spot Instance는 가장 저렴하지만 Instance가 중단될 수 있다.
- Dedicated Host는 Physical Server 전체를 전용으로 사용한다.
- Dedicated Instance는 다른 고객과 Hardware를 공유하지 않는다.
- Capacity Reservation은 특정 AZ의 EC2 Capacity를 확보한다.
- Capacity Reservation 자체에는 비용 할인이 없다.

---

## Exam Notes

### On-Demand

```text
Short-Term
Unpredictable
No Commitment
```

### Reserved Instance

```text
Long-Term
Predictable
1 / 3 Years
Cost Saving
```

### Savings Plans

```text
Long-Term Usage Commitment
1 / 3 Years
Cost Saving
```

### Spot

```text
Cheapest
Can Be Interrupted
Fault-Tolerant Workloads
Batch / Analytics / Distributed Processing
Not for Critical Database
```

### Dedicated Host

```text
Entire Physical Server
BYOL
Compliance
Hardware Visibility
```

### Dedicated Instance

```text
Dedicated Hardware
No Physical Server Control
```

### Capacity Reservation

```text
Specific AZ
Capacity Guarantee
No Discount
Pay Even If Not Used
```

---

## Most Important Exam Distinctions

```text
Reserved
→ Save Money

Capacity Reservation
→ Guarantee Capacity
```

```text
Spot
→ Cheapest
→ But Interruptible
```

```text
Dedicated Host
→ Physical Server

Dedicated Instance
→ Dedicated Hardware Only
```

---

## Practical Examples

### Example 1

회사가 3년 동안 항상 실행될 Database Server를 운영한다.

```text
Predictable
+
Long-Term
↓
Reserved Instance
```

### Example 2

수천 개의 Image Processing Job을 실행하며 실패한 작업은 다시 시작할 수 있다.

```text
Fault-Tolerant
+
Batch Processing
↓
Spot Instance
```

### Example 3

Software License가 Physical CPU Core에 연결되어 있다.

```text
Hardware-Based License
↓
Dedicated Host
```

### Example 4

특정 AZ에서 Application을 반드시 실행할 수 있어야 한다.

```text
Specific AZ
+
Guaranteed Capacity
↓
Capacity Reservation
```

---

# 🇯🇵 日本語

## EC2 Purchasing Options

EC2にはWorkloadに応じた複数の購入オプションがある。

### On-Demand

長期契約なしで必要な分だけ利用する。

短期的で予測できないWorkloadに適している。

### Reserved Instances

1年または3年間の長期利用を約束することで料金を削減できる。

予測可能で長期間実行するWorkloadに適している。

### Savings Plans

一定期間の利用量を約束することで料金を削減する。

### Spot Instances

非常に安いが、Instanceが中断される可能性がある。

Batch ProcessingやData Analysisなど、障害に強いWorkloadに適している。

### Dedicated Hosts

Physical Server全体を専用で利用する。

BYOLやCompliance要件に適している。

### Dedicated Instances

他のAWS顧客とHardwareを共有しないInstanceである。

### Capacity Reservations

特定のAZでEC2 Capacityを確保する。

料金割引はなく、使用していなくても料金が発生する。

---

## Summary

```text
Short-Term / Unpredictable
→ On-Demand

Long-Term / Predictable
→ Reserved Instance

Long-Term Usage Commitment
→ Savings Plans

Interruptible Workload
→ Spot

Physical Server / BYOL
→ Dedicated Host

Dedicated Hardware
→ Dedicated Instance

Specific AZ Capacity
→ Capacity Reservation
```

---

# 🇺🇸 English

## EC2 Purchasing Options

Amazon EC2 provides multiple purchasing options for different workload requirements.

### On-Demand

No long-term commitment.

Best for short-term and unpredictable workloads.

### Reserved Instances

Commit for one or three years to receive discounted pricing.

Best for predictable, long-running workloads.

### Savings Plans

Commit to a certain level of usage for one or three years.

### Spot Instances

Highly discounted but instances may be interrupted.

Best for fault-tolerant workloads such as batch processing and analytics.

### Dedicated Hosts

An entire physical server is dedicated to your use.

Useful for BYOL and compliance requirements.

### Dedicated Instances

Instances run on hardware dedicated to your account without direct control of the physical server.

### Capacity Reservations

Reserve EC2 capacity in a specific Availability Zone.

Capacity is guaranteed, but there is no billing discount.

---

## Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| On-Demand Instance | 온디맨드 인스턴스 | オンデマンドインスタンス |
| Reserved Instance | 예약 인스턴스 | リザーブドインスタンス |
| Convertible Reserved Instance | 전환형 예약 인스턴스 | コンバーティブルRI |
| Savings Plans | 절약 플랜 | Savings Plans |
| Spot Instance | 스폿 인스턴스 | スポットインスタンス |
| Dedicated Host | 전용 호스트 | Dedicated Host |
| Dedicated Instance | 전용 인스턴스 | Dedicated Instance |
| Capacity Reservation | 용량 예약 | キャパシティ予約 |
| Commitment | 약정 | コミットメント |
| Upfront Payment | 선결제 | 前払い |
| Tenancy | 테넌시 | テナンシー |
| Workload | 워크로드 | ワークロード |
| BYOL | 기존 라이선스 사용 | Bring Your Own License |
| Compliance | 규정 준수 | コンプライアンス |
| Fault-Tolerant | 장애 허용 | 耐障害性 |
| Physical Server | 물리 서버 | 物理サーバー |

---

## Review Questions

1. 단기적이고 사용량을 예측하기 어려운 Workload에는 어떤 구매 옵션이 적합한가?
2. 장기간 일정하게 실행되는 Database에는 어떤 옵션이 적합한가?
3. Reserved Instance의 약정 기간은 무엇인가?
4. Savings Plans는 무엇을 약정하여 할인을 받는 방식인가?
5. Spot Instance가 저렴한 대신 가지는 가장 중요한 위험은 무엇인가?
6. Spot Instance가 Batch Processing에 적합한 이유는 무엇인가?
7. 중요한 Database에 Spot Instance가 부적합한 이유는 무엇인가?
8. Dedicated Host와 Dedicated Instance의 핵심 차이는 무엇인가?
9. BYOL 요구사항이 있을 때 어떤 옵션을 고려할 수 있는가?
10. Capacity Reservation의 주요 목적은 비용 절감인가, Capacity 확보인가?
11. Capacity Reservation은 Instance를 실행하지 않아도 비용이 발생하는가?
12. 특정 AZ의 EC2 Capacity를 반드시 확보해야 한다면 어떤 옵션을 선택해야 하는가?