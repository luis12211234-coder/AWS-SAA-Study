# 📘 15-05. Kinesis Data Streams

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Kinesis Data Streams란?` · `핵심은 Real-Time` · `Shard ⭐` · `Partition Key` · `Retention & Replay ⭐`

## 🇰🇷 1. Kinesis Data Streams란?

실시간으로 계속 생성되는 Streaming Data를 **수집하고 저장**하는 서비스입니다.

대표적인 데이터:

```text
Application Logs
Clickstream
Metrics
IoT Telemetry
User Activity
```

구조:

```text
Producer
↓↓↓↓↓↓↓↓↓↓
Kinesis Data Streams
↓↓↓↓↓↓↓↓↓↓
Consumers
```

Consumer는 Application, Lambda, Firehose, Managed Service for Apache Flink 등이 될 수 있습니다.

## 🇰🇷 2. 핵심은 Real-Time

SQS가:

```text
"처리해야 할 작업"
```

을 Queue에 넣는 개념이라면,

Kinesis는:

```text
Data
Data
Data
Data
Data
...
```

처럼 **계속 발생하는 Data Stream**을 다루는 것이 핵심입니다.

## 🇰🇷 3. Shard ⭐

Shard는 Kinesis Data Stream을 구성하는 **처리량 단위**입니다.

```text
Kinesis Data Stream

├─ Shard 1
├─ Shard 2
└─ Shard 3
```

Provisioned Mode 기준 Shard 하나의 기본 처리량:

```text
WRITE
→ 1 MB/s
→ 1,000 records/s

READ
→ 2 MB/s
```

Shard를 증가시키면 Stream 전체 처리량도 증가합니다.

## 🇰🇷 4. Partition Key

Producer는 Record를 보낼 때 Partition Key를 지정합니다.

```text
Record
+
Partition Key
      ↓
Kinesis
      ↓
Shard
```

같은 Partition Key를 사용하는 Record는 같은 Shard로 전달되어 순서를 유지할 수 있습니다.

## 🇰🇷 5. Retention & Replay ⭐

Kinesis Data Streams는 데이터를 저장합니다.

따라서 Consumer가 데이터를 읽었다고 삭제되지 않습니다.

```text
Stream
│
├─ Consumer A 읽음
├─ Consumer B 읽음
└─ 다시 Replay 가능
```

Retention 기간 내에서는 과거 데이터를 다시 처리할 수 있습니다.

```text
Replay
→ Kinesis 핵심 특징
```

## 🇰🇷 6. Capacity Mode

#### Provisioned

```text
Shard 수를 직접 지정
→ 처리량 직접 관리
```

#### On-Demand

```text
Capacity 자동 관리
→ Traffic에 따라 자동 Scaling
```

## 🇰🇷 7. Consumer

기본 Consumer는 Kinesis에서 데이터를 읽어오는 **Pull Model**입니다.

Enhanced Fan-Out을 사용하면 각 등록 Consumer에게 전용 Read Throughput을 제공할 수 있습니다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

```text
Real-Time
Streaming
Clickstream
IoT
Logs
Replay

→ Kinesis Data Streams
```

```text
처리량 단위
→ Shard

Record 분배
→ Partition Key

과거 데이터 재처리
→ Replay
```

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Kinesis Data Streams란?의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

실시간으로 계속 생성되는 Streaming Data를 **수집하고 저장**하는 서비스입니다.

</details>

### Q2. 핵심은 Real-Time의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

을 Queue에 넣는 개념이라면,

</details>

### Q3. Shard ⭐의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Shard는 Kinesis Data Stream을 구성하는 **처리량 단위**입니다.

</details>

### Q4. Partition Key의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Producer는 Record를 보낼 때 Partition Key를 지정합니다.

</details>

### Q5. Retention & Replay ⭐의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Kinesis Data Streams는 데이터를 저장합니다.

</details>

## 🇯🇵 日本語 Summary

Kinesis Data StreamsはリアルタイムのStreaming Dataを収集・保存するサービスです。

StreamはShardで構成され、Partition KeyによってRecordがShardに分配されます。

Dataは一定期間保存されるためReplayが可能です。

## 🇺🇸 English Summary

Kinesis Data Streams collects and stores real-time streaming data.

A stream consists of shards, and partition keys determine how records are distributed.

Stored records can be replayed during the retention period.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Kinesis Data Streams란? | Kinesis Data Streams란? | Kinesis Data Streams란? |
| 핵심은 Real-Time | 핵심은 Real-Time | 핵심은 Real-Time |
| Shard ⭐ | Shard ⭐ | Shard ⭐ |
| Partition Key | Partition Key | Partition Key |
| Retention & Replay ⭐ | Retention & Replay ⭐ | Retention & Replay ⭐ |
