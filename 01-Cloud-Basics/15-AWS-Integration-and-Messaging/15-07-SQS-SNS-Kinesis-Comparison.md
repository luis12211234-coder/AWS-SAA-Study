# 15-07. SQS vs SNS vs Kinesis

## 🇰🇷 핵심 비교

| Service | 핵심 목적 | 방식 | 데이터 저장 / Replay |
|---|---|---|---|
| SQS | Work Queue / Decoupling | Consumer Pull | 처리 후 삭제 |
| SNS | Pub/Sub / Notification | Push | 기본적으로 지속 저장하지 않음 |
| Kinesis Data Streams | Real-Time Streaming | Stream Consumer | Retention + Replay |
| Data Firehose | Destination Delivery | Managed Delivery | 자체 Replay 없음 |

---

# 1. SQS

```text
Producer
   ↓
 Queue
   ↓
Consumer
```

핵심:

```text
"이 작업을 누군가 처리해줘"
```

Consumer가 Message를 가져가 처리하고 삭제합니다.

---

# 2. SNS

```text
             ┌─ A
Producer → SNS├─ B
             └─ C
```

핵심:

```text
"이 Event가 발생했어.
필요한 Subscriber들은 받아."
```

하나의 Message를 여러 Subscriber에게 전달합니다.

---

# 3. Kinesis Data Streams

```text
Producer
↓↓↓↓↓↓↓↓
Stream
↓↓↓↓↓↓↓↓
Consumers
```

핵심:

```text
"데이터가 계속 들어오고 있어."
```

Streaming Data를 저장하고 Consumer들이 읽습니다.

Retention 기간 내 Replay가 가능합니다.

---

# 4. Data Firehose

```text
Stream
  ↓
Firehose
  ↓
S3 / Redshift / OpenSearch
```

핵심:

```text
"이 Streaming Data를 목적지까지 전달해줘."
```

Delivery 자체를 AWS가 관리합니다.

---

# 5. 최종 선택법 ⭐

```text
작업 Queue
→ SQS

Application Decoupling
→ SQS

Traffic Spike Buffer
→ SQS

One-to-Many
→ SNS

Pub/Sub
→ SNS

Fan-Out
→ SNS + SQS

Real-Time Streaming
→ Kinesis Data Streams

Replay
→ Kinesis Data Streams

Streaming → S3 / Redshift / OpenSearch
→ Amazon Data Firehose
```

---

# 🔥 한 줄 암기

```text
SQS
= Queue

SNS
= Broadcast

Kinesis Data Streams
= Stream

Data Firehose
= Delivery
```

---

## 🇯🇵 日本語 Summary

```text
SQS
→ Queue / Decoupling

SNS
→ Pub/Sub / Fan-Out

Kinesis Data Streams
→ Real-Time Streaming / Replay

Amazon Data Firehose
→ Streaming Data Delivery
```

---

## 🇺🇸 English Summary

```text
SQS
→ Queue and application decoupling

SNS
→ Publish/subscribe and fan-out

Kinesis Data Streams
→ Real-time streaming with retention and replay

Amazon Data Firehose
→ Managed streaming data delivery
```

---

## 📝 Review

**Q1. 갑작스러운 Traffic Spike를 Buffering해야 한다면?**

Amazon SQS

**Q2. 하나의 Event를 여러 Consumer에게 전달하려면?**

Amazon SNS

**Q3. 하나의 Event를 여러 독립적인 Queue에 전달하려면?**

SNS + SQS Fan-Out

**Q4. Clickstream을 실시간으로 수집하고 나중에 Replay해야 한다면?**

Kinesis Data Streams

**Q5. Streaming Data를 S3에 자동으로 전달하려면?**

Amazon Data Firehose

**Q6. 딱 네 단어로 구분하면?**

```text
SQS      = Queue
SNS      = Broadcast
KDS      = Stream
Firehose = Delivery
```
