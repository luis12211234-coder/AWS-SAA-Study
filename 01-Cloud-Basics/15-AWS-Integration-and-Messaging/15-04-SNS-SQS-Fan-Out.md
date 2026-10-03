# 15-04. SNS + SQS Fan-Out

## 🇰🇷 Fan-Out Pattern

하나의 Event를 여러 시스템이 각각 처리해야 할 때 SNS와 SQS를 함께 사용할 수 있습니다.

```text
                  ┌─ SQS A → Service A
Producer → SNS ───┤
                  └─ SQS B → Service B
```

Producer는 SNS에 Message를 **한 번만** Publish합니다.

SNS는 각 SQS Queue에 Message를 전달하고 각 Service는 자신의 Queue에서 독립적으로 처리합니다.

---

# Example

상품 구매 Event가 발생했다고 가정합니다.

```text
Purchase
   ↓
  SNS
 ┌─┴─────────┐
 ↓           ↓
SQS         SQS
 ↓           ↓
Fraud      Shipping
Detection  Service
```

Fraud Detection과 Shipping Service 모두 동일한 Event를 받지만 서로 독립적으로 처리합니다.

---

# S3 Event + Fan-Out

하나의 S3 Event를 여러 Consumer가 처리해야 할 때도 사용할 수 있습니다.

```text
S3 Event
   ↓
  SNS
 ┌─┼────────┐
 ↓ ↓        ↓
SQS Lambda Email
```

---

# SNS FIFO + SQS FIFO

순서와 중복 제거까지 유지해야 한다면:

```text
Producer
   ↓
SNS FIFO
   ↓
SQS FIFO
   ↓
Consumer
```

를 사용합니다.

---

## 🎯 Exam Notes

```text
One Event
+
Multiple Independent Consumers
+
Reliable Queue

→ SNS + SQS Fan-Out
```

SNS:

```text
Message 분배
```

SQS:

```text
Message 저장
Retry / Delayed Processing
Consumer Decoupling
```

---

## 🇯🇵 日本語 Summary

Fan-Out Patternでは、SNS Topicに一度MessageをPublishし、複数のSQS Queueへ配信できます。

各Consumerは自分のQueueから独立してMessageを処理できます。

---

## 🇺🇸 English Summary

The fan-out pattern combines SNS and SQS.

A message is published once to SNS and delivered to multiple SQS queues, allowing each consumer to process the event independently.

---

## 📝 Review

**Q. 하나의 Event를 여러 독립적인 Backend가 안정적으로 처리해야 한다면?**

SNS Topic에 여러 SQS Queue를 구독시키는 Fan-Out Pattern을 사용합니다.
