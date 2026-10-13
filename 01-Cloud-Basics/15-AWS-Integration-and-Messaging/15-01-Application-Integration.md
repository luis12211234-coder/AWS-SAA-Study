# 📘 15-01. Application Integration

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Application Communication` · `AWS Integration` · `Application Integration` · `Application Communication Application` · `Synchronous Service`

## 🇰🇷 1. Application Communication

Application 간 통신에는 크게 두 가지 방식이 있습니다.

### Synchronous

Service끼리 직접 통신합니다.

```text
Purchase Service
      │
      ↓
Shipping Service
```

단순하지만 Traffic이 갑자기 증가하면 한 Service가 다른 Service를 압도할 수 있습니다.

### Asynchronous

중간에 Messaging Layer를 둡니다.

```text
Purchase Service
      │
      ↓
Messaging Layer
      │
      ↓
Shipping Service
```

두 Service가 직접 연결되지 않으므로 독립적으로 동작하고 Scaling할 수 있습니다.

이를 **Decoupling**이라고 합니다.

## 🇰🇷 2. AWS Integration

```text
SQS
→ Queue 기반 Decoupling

SNS
→ Pub/Sub

Kinesis
→ Real-Time Streaming
```

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

```text
Decouple Applications
Traffic Spike
Independent Scaling
Asynchronous Processing

→ SQS를 우선 고려
```

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Application Communication의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Application 간 통신에는 크게 두 가지 방식이 있습니다.

</details>

### Q2. AWS Integration의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

SNS
→ Pub/Sub

</details>

### Q3. 이 문서에서 반드시 기억해야 할 시험 포인트 3은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Application 간 통신에는 크게 두 가지 방식이 있습니다.

</details>

### Q4. 이 문서에서 반드시 기억해야 할 시험 포인트 4은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Application 간 통신에는 크게 두 가지 방식이 있습니다.

</details>

### Q5. 이 문서에서 반드시 기억해야 할 시험 포인트 5은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Application 간 통신에는 크게 두 가지 방식이 있습니다.

</details>

## 🇯🇵 日本語 Summary

Application間にMessaging Layerを配置することで、サービスを疎結合化（Decoupling）できます。

SQSはQueue、SNSはPub/Sub、KinesisはReal-Time Streamingに使用されます。

## 🇺🇸 English Summary

A messaging layer can decouple application components and allow them to scale independently.

SQS provides queues, SNS provides pub/sub messaging, and Kinesis handles real-time streaming.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Application Communication | Application Communication | Application Communication |
| AWS Integration | AWS Integration | AWS Integration |
| Application Integration | Application Integration | Application Integration |
| NOTE | NOTE | NOTE |
| C03 Study Note | C03 Study Note | C03 Study Note |
