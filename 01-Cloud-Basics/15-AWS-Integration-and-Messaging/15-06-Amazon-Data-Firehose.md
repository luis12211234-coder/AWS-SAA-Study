# 15-06. Amazon Data Firehose

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

---

# 2. Source

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

---

# 3. Data Transformation

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

---

# 4. Buffering ⭐

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

---

# 5. Data Streams vs Firehose

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

---

## 🎯 Exam Notes

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

---

## 🇯🇵 日本語 Summary

Amazon Data FirehoseはStreaming DataをS3、Redshift、OpenSearchなどへ配信する完全マネージドサービスです。

Bufferingを行うため、一般的にNear Real-Timeのサービスとして扱われます。

---

## 🇺🇸 English Summary

Amazon Data Firehose is a fully managed service that delivers streaming data to destinations such as S3, Redshift, and OpenSearch.

Because records are typically buffered before delivery, Firehose is considered near real-time.

---

## 📝 Review

**Q1. Streaming Data를 S3로 자동 전달하려면?**

Amazon Data Firehose

**Q2. Firehose가 Real-Time이 아니라 Near Real-Time인 핵심 이유는?**

Destination으로 전달하기 전에 데이터를 Buffering하기 때문입니다.

**Q3. Stream 자체를 저장하고 Replay해야 한다면?**

Kinesis Data Streams
