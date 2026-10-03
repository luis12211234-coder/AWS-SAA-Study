# 15. AWS Integration & Messaging

AWS Application 간 통신을 분리하고 독립적으로 확장하기 위한 Messaging / Streaming Service를 학습합니다.

## 📚 Contents

| No. | Topic | 핵심 |
|---|---|---|
| 15-01 | Application Integration | Synchronous / Asynchronous, Decoupling |
| 15-02 | Amazon SQS | Queue, Visibility Timeout, Long Polling, FIFO |
| 15-03 | Amazon SNS | Pub/Sub, Topic, Subscriber, Filtering |
| 15-04 | SNS + SQS Fan-Out | 하나의 Event를 여러 Queue로 전달 |
| 15-05 | Kinesis Data Streams | Real-Time Streaming, Shard, Replay |
| 15-06 | Amazon Data Firehose | Streaming Data Delivery |
| 15-07 | Comparison | SQS vs SNS vs Kinesis |

## 🔑 전체 그림

```text
SQS
→ Queue
→ 작업을 쌓아두고 Consumer가 처리

SNS
→ Pub/Sub
→ 하나의 Event를 여러 Subscriber에게 전달

Kinesis Data Streams
→ Real-Time Stream
→ 계속 발생하는 데이터를 수집 / 저장

Amazon Data Firehose
→ Delivery
→ Streaming Data를 S3 등의 목적지로 전달
```

## 🎯 Exam Keywords

```text
Decoupling / Buffer / Traffic Spike
→ SQS

Pub/Sub / Notification / Fan-Out
→ SNS

Real-Time / Clickstream / Logs / IoT / Replay
→ Kinesis Data Streams

Streaming → S3 / Redshift / OpenSearch
→ Amazon Data Firehose
```
