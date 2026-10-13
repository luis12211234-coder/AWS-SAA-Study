# 📘 15-07. SQS vs SNS vs Kinesis

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `SQS` · `SNS` · `Kinesis Data Streams` · `Data Firehose`

## 🇰🇷 1. 핵심 비교

| Service | 핵심 목적 | 방식 | 데이터 저장 / Replay |
|---|---|---|---|
| SQS | Work Queue / Decoupling | Consumer Pull | 처리 후 삭제 |
| SNS | Pub/Sub / Notification | Push | 기본적으로 지속 저장하지 않음 |
| Kinesis Data Streams | Real-Time Streaming | Stream Consumer | Retention + Replay |
| Data Firehose | Destination Delivery | Managed Delivery | 자체 Replay 없음 |

## 🇰🇷 2. SQS

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

## 🇰🇷 3. SNS

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

## 🇰🇷 4. Kinesis Data Streams

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

## 🇰🇷 5. Data Firehose

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

## 🇰🇷 6. 최종 선택법 ⭐

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

## 🇰🇷 7. 한 줄 암기

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

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **SNS**의 선택 기준, 동작 방식과 제한 사항을 구분한다.
- **Kinesis Data Streams**의 선택 기준, 동작 방식과 제한 사항을 구분한다.
- **Data Firehose**의 선택 기준, 동작 방식과 제한 사항을 구분한다.
- **최종 선택법 ⭐**의 선택 기준, 동작 방식과 제한 사항을 구분한다.
- **한 줄 암기**의 선택 기준, 동작 방식과 제한 사항을 구분한다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 핵심 비교의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

**SNS**의 선택 기준, 동작 방식과 제한 사항을 구분한다.

</details>

### Q2. SQS의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Consumer가 Message를 가져가 처리하고 삭제합니다.

</details>

### Q3. SNS의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

하나의 Message를 여러 Subscriber에게 전달합니다.

</details>

### Q4. Kinesis Data Streams의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Streaming Data를 저장하고 Consumer들이 읽습니다.

</details>

### Q5. Data Firehose의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Delivery 자체를 AWS가 관리합니다.

</details>

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

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| SQS | SQS | SQS |
| SNS | SNS | SNS |
| Kinesis Data Streams | Kinesis Data Streams | Kinesis Data Streams |
| Data Firehose | Data Firehose | Data Firehose |
