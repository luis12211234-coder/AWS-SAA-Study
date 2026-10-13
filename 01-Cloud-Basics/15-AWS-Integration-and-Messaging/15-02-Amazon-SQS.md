# 📘 15-02. Amazon SQS

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Amazon SQS란?` · `Standard Queue` · `Visibility Timeout ⭐` · `Long Polling` · `FIFO Queue`

## 🇰🇷 1. Amazon SQS란?

**Amazon Simple Queue Service**

Message Queue를 이용하여 Application을 Decouple하는 완전 관리형 서비스입니다.

```text
Producer
   │
   │ SendMessage
   ↓
SQS Queue
   │
   │ Poll
   ↓
Consumer
```

Producer가 Message를 Queue에 넣고 Consumer가 가져가 처리합니다.

처리가 완료되면 Consumer가 `DeleteMessage`를 호출합니다.

## 🇰🇷 2. Standard Queue

기본 SQS Queue입니다.

```text
높은 처리량
At-Least-Once Delivery
Best-Effort Ordering
```

#### At-Least-Once

같은 Message가 중복 전달될 수 있습니다.

#### Best-Effort Ordering

Message 순서를 최대한 유지하지만 보장하지 않습니다.

엄격한 순서와 중복 제거가 필요하면 **FIFO Queue**를 사용합니다.

## 🇰🇷 3. Visibility Timeout ⭐

Consumer가 Message를 가져가면 일정 시간 동안 다른 Consumer에게 보이지 않습니다.

```text
Consumer A
   ↓
Message 수신
   ↓
Visibility Timeout
   ↓
다른 Consumer에게 숨김
```

처리가 완료되면:

```text
DeleteMessage
→ Message 삭제
```

처리하지 못하고 Timeout이 끝나면:

```text
Message
→ 다시 Visible
→ 다른 Consumer가 처리 가능
```

처리에 시간이 더 필요하면:

```text
ChangeMessageVisibility
```

API로 시간을 연장할 수 있습니다.

## 🇰🇷 4. Long Polling

Queue가 비어 있을 때 즉시 빈 응답을 받는 대신 일정 시간 Message를 기다립니다.

```text
Consumer → Poll

Message 없음
     ↓
기다림
     ↓
Message 도착
     ↓
즉시 반환
```

장점:

```text
API 호출 감소
비용 감소
불필요한 Empty Response 감소
```

Long Polling의 Wait Time은 최대 **20초**입니다.

## 🇰🇷 5. FIFO Queue

FIFO:

```text
First In
First Out
```

```text
1 → 2 → 3 → 4

        ↓

1 → 2 → 3 → 4
```

특징:

```text
Strict Ordering
Deduplication
Message Group ID
```

Queue 이름은:

```text
name.fifo
```

형식을 사용합니다.

## 🇰🇷 6. SQS + Auto Scaling Group ⭐

SQS Queue에 Message가 많이 쌓이면 Consumer를 늘릴 수 있습니다.

```text
SQS Queue Length
      ↓
CloudWatch
      ↓
Alarm
      ↓
Auto Scaling Group
      ↓
EC2 Consumer 증가
```

대표 Metric:

```text
ApproximateNumberOfMessages
```

Queue가 길어지면 Consumer를 Scale Out하여 처리량을 증가시킵니다.

## 🇰🇷 7. SQS as Buffer ⭐

Database가 갑작스러운 요청을 직접 받지 않도록 SQS를 Buffer로 사용할 수 있습니다.

```text
Application
    ↓
   SQS
    ↓
Consumers
    ↓
Database
```

Traffic Spike가 발생해도 요청을 Queue에 보관하고 Backend가 자신의 속도로 처리할 수 있습니다.

## 🇰🇷 8. Security

```text
In Transit
→ HTTPS

At Rest
→ SSE-SQS 또는 KMS

Access Control
→ IAM Policy
→ SQS Access Policy
```

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

```text
Decoupling
Traffic Spike
Buffer
Asynchronous Processing
→ SQS

Strict Ordering
Deduplication
→ SQS FIFO

Message 처리 중 다른 Consumer에게 숨김
→ Visibility Timeout

Queue Length에 따라 EC2 증가
→ SQS + CloudWatch + ASG
```

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Amazon SQS란?의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

**Amazon Simple Queue Service**

</details>

### Q2. Standard Queue의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

기본 SQS Queue입니다.

</details>

### Q3. Visibility Timeout ⭐의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Consumer가 Message를 가져가면 일정 시간 동안 다른 Consumer에게 보이지 않습니다.

</details>

### Q4. Long Polling의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Queue가 비어 있을 때 즉시 빈 응답을 받는 대신 일정 시간 Message를 기다립니다.

</details>

### Q5. FIFO Queue의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

1 → 2 → 3 → 4
```

</details>

## 🇯🇵 日本語 Summary

Amazon SQSはMessage Queueを利用してApplicationを疎結合化するサービスです。

ConsumerはQueueをPollingしてMessageを取得し、処理後に削除します。

Visibility Timeout、Long Polling、FIFO Queueは重要な試験ポイントです。

## 🇺🇸 English Summary

Amazon SQS is a managed message queue used to decouple applications.

Consumers poll messages from the queue and delete them after successful processing.

Important concepts include Visibility Timeout, Long Polling, FIFO queues, and scaling consumers based on queue length.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Amazon SQS란? | Amazon SQS란? | Amazon SQS란? |
| Standard Queue | Standard Queue | Standard Queue |
| Visibility Timeout ⭐ | Visibility Timeout ⭐ | Visibility Timeout ⭐ |
| Long Polling | Long Polling | Long Polling |
| FIFO Queue | FIFO Queue | FIFO Queue |
