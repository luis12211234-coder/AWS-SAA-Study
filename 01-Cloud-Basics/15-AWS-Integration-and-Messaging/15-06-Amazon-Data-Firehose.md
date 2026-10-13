# 📘 15-06. Amazon Data Firehose

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Amazon Data Firehose란?` · `Source` · `Data Transformation` · `Buffering ⭐` · `Data Streams vs Firehose`

## 🇰🇷 1. Amazon Data Firehose란?

Streaming Data를 Destination으로 전달하는 **완전 관리형 Delivery Service**입니다.

```text
Source
   ↓
Amazon Data Firehose
   ↓
Destination
```

대표 Destination:

```text
Amazon S3
Amazon Redshift
Amazon OpenSearch Service
HTTP Endpoint
Third-Party Services
```

## 🇰🇷 2. Source

Firehose는 여러 Source에서 데이터를 받을 수 있습니다.

```text
Application / SDK
Kinesis Data Streams
CloudWatch
AWS IoT
```

예:

```text
Producer
   ↓
Kinesis Data Streams
   ↓
Amazon Data Firehose
   ↓
Amazon S3
```

## 🇰🇷 3. Data Transformation

필요하면 Lambda를 사용하여 데이터를 변환할 수 있습니다.

```text
Data
 ↓
Firehose
 ↓
Lambda
 ↓
Transform
 ↓
Destination
```

Format Conversion과 Compression도 지원합니다.

## 🇰🇷 4. Buffering ⭐

Firehose는 일반적으로 데이터를 바로 하나씩 전달하지 않고 Buffer에 모은 후 전송합니다.

```text
Data
↓↓↓↓↓
Buffer
↓↓↓↓↓
Destination
```

Buffer는:

```text
Size
또는
Time
```

조건에 따라 Flush됩니다.

따라서 Firehose의 중요한 Keyword는:

```text
Near Real-Time
```

입니다.

## 🇰🇷 5. Data Streams vs Firehose

```text
Kinesis Data Streams
→ Streaming Data 수집 + 저장
→ Real-Time
→ Retention
→ Replay

Amazon Data Firehose
→ Streaming Data Delivery
→ Fully Managed
→ Near Real-Time
→ 자체 Data Retention / Replay 없음
```

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

```text
Streaming Data
+
S3 / Redshift / OpenSearch
+
Fully Managed Delivery

→ Amazon Data Firehose
```

```text
Real-Time Stream 저장 / Replay
→ Kinesis Data Streams

Destination으로 자동 Delivery
→ Amazon Data Firehose
```

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Amazon Data Firehose란?의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Streaming Data를 Destination으로 전달하는 **완전 관리형 Delivery Service**입니다.

</details>

### Q2. Source의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Firehose는 여러 Source에서 데이터를 받을 수 있습니다.

</details>

### Q3. Data Transformation의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

필요하면 Lambda를 사용하여 데이터를 변환할 수 있습니다.

</details>

### Q4. Buffering ⭐의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Firehose는 일반적으로 데이터를 바로 하나씩 전달하지 않고 Buffer에 모은 후 전송합니다.

</details>

### Q5. Data Streams vs Firehose의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Amazon Data Firehose
→ Streaming Data Delivery
→ Fully Managed
→ Near Real-Time
→ 자체 Data Retention / Replay 없음
```

</details>

## 🇯🇵 日本語 Summary

Amazon Data FirehoseはStreaming DataをS3、Redshift、OpenSearchなどへ配信する完全マネージドサービスです。

Bufferingを行うため、一般的にNear Real-Timeのサービスとして扱われます。

## 🇺🇸 English Summary

Amazon Data Firehose is a fully managed service that delivers streaming data to destinations such as S3, Redshift, and OpenSearch.

Because records are typically buffered before delivery, Firehose is considered near real-time.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Amazon Data Firehose란? | Amazon Data Firehose란? | Amazon Data Firehose란? |
| Source | Source | Source |
| Data Transformation | Data Transformation | Data Transformation |
| Buffering ⭐ | Buffering ⭐ | Buffering ⭐ |
| Data Streams vs Firehose | Data Streams vs Firehose | Data Streams vs Firehose |
