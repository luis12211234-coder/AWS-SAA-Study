# 📘 15-04. SNS + SQS Fan-Out

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Fan-Out Pattern` · `S3 Event + Fan-Out` · `SNS FIFO + SQS FIFO`

## 🇰🇷 1. Fan-Out Pattern

하나의 Event를 여러 시스템이 각각 처리해야 할 때 SNS와 SQS를 함께 사용할 수 있습니다.

```text
                  ┌─ SQS A → Service A
Producer → SNS ───┤
                  └─ SQS B → Service B
```

Producer는 SNS에 Message를 **한 번만** Publish합니다.

SNS는 각 SQS Queue에 Message를 전달하고 각 Service는 자신의 Queue에서 독립적으로 처리합니다.

## 🇰🇷 2. S3 Event + Fan-Out

하나의 S3 Event를 여러 Consumer가 처리해야 할 때도 사용할 수 있습니다.

```text
S3 Event
   ↓
  SNS
 ┌─┼────────┐
 ↓ ↓        ↓
SQS Lambda Email
```

## 🇰🇷 3. SNS FIFO + SQS FIFO

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

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

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

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Fan-Out Pattern의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

하나의 Event를 여러 시스템이 각각 처리해야 할 때 SNS와 SQS를 함께 사용할 수 있습니다.

</details>

### Q2. S3 Event + Fan-Out의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

하나의 S3 Event를 여러 Consumer가 처리해야 할 때도 사용할 수 있습니다.

</details>

### Q3. SNS FIFO + SQS FIFO의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

순서와 중복 제거까지 유지해야 한다면:

</details>

### Q4. 이 문서에서 반드시 기억해야 할 시험 포인트 4은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

하나의 Event를 여러 시스템이 각각 처리해야 할 때 SNS와 SQS를 함께 사용할 수 있습니다.

</details>

### Q5. 이 문서에서 반드시 기억해야 할 시험 포인트 5은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

하나의 Event를 여러 시스템이 각각 처리해야 할 때 SNS와 SQS를 함께 사용할 수 있습니다.

</details>

## 🇯🇵 日本語 Summary

Fan-Out Patternでは、SNS Topicに一度MessageをPublishし、複数のSQS Queueへ配信できます。

各Consumerは自分のQueueから独立してMessageを処理できます。

## 🇺🇸 English Summary

The fan-out pattern combines SNS and SQS.

A message is published once to SNS and delivered to multiple SQS queues, allowing each consumer to process the event independently.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Fan-Out Pattern | Fan-Out Pattern | Fan-Out Pattern |
| S3 Event + Fan-Out | S3 Event + Fan-Out | S3 Event + Fan-Out |
| SNS FIFO + SQS FIFO | SNS FIFO + SQS FIFO | SNS FIFO + SQS FIFO |
