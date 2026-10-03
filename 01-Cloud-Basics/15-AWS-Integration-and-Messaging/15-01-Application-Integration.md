# 15-01. Application Integration

## 🇰🇷 Application Communication

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

## 🔑 AWS Integration

```text
SQS
→ Queue 기반 Decoupling

SNS
→ Pub/Sub

Kinesis
→ Real-Time Streaming
```

## 🎯 Exam Notes

```text
Decouple Applications
Traffic Spike
Independent Scaling
Asynchronous Processing

→ SQS를 우선 고려
```

## 🇯🇵 日本語 Summary

Application間にMessaging Layerを配置することで、サービスを疎結合化（Decoupling）できます。

SQSはQueue、SNSはPub/Sub、KinesisはReal-Time Streamingに使用されます。

## 🇺🇸 English Summary

A messaging layer can decouple application components and allow them to scale independently.

SQS provides queues, SNS provides pub/sub messaging, and Kinesis handles real-time streaming.

## 📝 Review

**Q. Decoupling이란?**

Service 사이의 직접적인 의존성을 줄여 각각 독립적으로 동작하고 Scaling할 수 있도록 하는 것입니다.
