# 15-03. Amazon SNS

## 🇰🇷 1. Amazon SNS란?

**Amazon Simple Notification Service**

Publish / Subscribe 방식의 Messaging Service입니다.

Producer는 각각의 Service에 직접 Message를 보내지 않고 **SNS Topic**에 한 번 Publish합니다.

```text
                 ┌─ Subscriber A
Producer → Topic ├─ Subscriber B
                 └─ Subscriber C
```

Topic을 구독한 Subscriber들에게 Message가 전달됩니다.

---

# 2. Subscriber

SNS는 다양한 Endpoint로 Message를 전달할 수 있습니다.

대표적으로:

```text
SQS
Lambda
Email
SMS
HTTP(S)
Firehose
Mobile Notification
```

즉 SNS의 핵심은:

```text
One Message
     ↓
Many Subscribers
```

입니다.

---

# 3. SNS Standard

일반적인 Pub/Sub Messaging에 사용합니다.

```text
높은 처리량
At-Least-Once Delivery
Best-Effort Ordering
```

다양한 Subscriber Type을 사용할 수 있습니다.

---

# 4. SNS FIFO

Message의 순서와 중복 제거가 중요한 경우 사용합니다.

```text
SNS FIFO
→ Ordering
→ Deduplication
→ Message Group
```

현재 SNS FIFO Topic에는 **SQS Standard Queue와 SQS FIFO Queue 모두 구독할 수 있습니다.**

단:

```text
Ordering + Deduplication을 끝까지 유지

SNS FIFO
    ↓
SQS FIFO
```

조합을 사용합니다.

---

# 5. Message Filtering

Subscriber마다 필요한 Message만 받을 수 있습니다.

예:

```text
SNS Topic
│
├─ status = placed
│      ↓
│   Order Queue
│
├─ status = cancelled
│      ↓
│   Cancel Queue
│
└─ Filter 없음
       ↓
    All Events Queue
```

이를 **Subscription Filter Policy**라고 합니다.

---

# 6. Security

```text
In Transit
→ HTTPS

At Rest
→ KMS

Access
→ IAM Policy
→ SNS Access Policy
```

---

## 🎯 Exam Notes

```text
Pub/Sub
One-to-Many
Notification
Multiple Subscribers
→ SNS

Subscriber별 Message 선택
→ Filter Policy

Ordering + Deduplication
→ SNS FIFO
```

---

## 🇯🇵 日本語 Summary

Amazon SNSはPub/Sub型のMessaging Serviceです。

ProducerはSNS TopicにMessageをPublishし、複数のSubscriberへMessageを配信できます。

Subscription Filter Policyを使用すると、Subscriberごとに必要なMessageだけを受信できます。

---

## 🇺🇸 English Summary

Amazon SNS is a publish/subscribe messaging service.

Publishers send messages to a topic, and SNS distributes them to subscribers.

Subscription filter policies allow subscribers to receive only selected messages.

---

## 📝 Review

**Q1. 하나의 Event를 여러 Subscriber에게 전달하려면?**

Amazon SNS

**Q2. Subscriber별로 특정 Message만 받고 싶다면?**

Subscription Filter Policy

**Q3. SNS에서 Ordering과 Deduplication이 필요하면?**

SNS FIFO Topic
