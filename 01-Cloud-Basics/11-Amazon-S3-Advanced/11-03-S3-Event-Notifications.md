# 11-03. S3 Event Notifications

## 🇰🇷 1. S3 Event Notification이란?

S3 Bucket에서 특정 Event가 발생했을 때 다른 AWS 서비스가 반응하도록 알림을 전달하는 S3 기능입니다.

예:

```text
User
 ↓
profile.jpg Upload
 ↓
S3
 ↓ ObjectCreated Event
Lambda
 ↓
Thumbnail 생성
```

즉 Object를 저장하는 것으로 끝나는 것이 아니라 **S3에서 발생한 Event를 기반으로 자동화된 처리를 시작**할 수 있습니다.

---

## 2. 대표적인 S3 Event

대표적인 Event는 다음과 같습니다.

```text
ObjectCreated
ObjectRemoved
ObjectRestore
Replication Event
...
```

예를 들어 새로운 Object가 업로드될 때만 Event Notification을 발생시킬 수 있습니다.

```text
coffee.jpg Upload
       ↓
ObjectCreated:Put
       ↓
Event Notification
```

---

## 3. Event Filtering

모든 Object에 반응할 필요가 없다면 Object Key의 Prefix와 Suffix를 이용해 Event를 필터링할 수 있습니다.

예:

```text
Prefix = images/
Suffix = .jpg
```

그러면:

```text
images/cat.jpg       ✅
images/dog.jpg       ✅

documents/cat.jpg    ❌
images/manual.pdf    ❌
```

### Important

S3 Event Notification의 Object Key Filtering은 Prefix/Suffix 기반입니다.

```text
Suffix = .jpg
```

처럼 구성하며 일반적인 Wildcard Pattern Matching과 동일하게 생각하면 안 됩니다.

---

## 4. Native Event Notification Destinations

S3 Event Notification에서 직접 연결하는 대표적인 Destination은 다음과 같습니다.

```text
             S3
              │
       Event Notification
              │
      ┌───────┼───────┐
      ▼       ▼       ▼
     SNS     SQS    Lambda
```

각 서비스의 역할을 간단히 기억하면:

```text
SNS
= 여러 Subscriber에게 메시지를 전달

SQS
= 메시지를 Queue에 저장하여 처리

Lambda
= Event가 발생하면 Code 실행
```

---

## 5. Resource Policy

S3가 Destination에 메시지를 전달하거나 함수를 실행하려면 Destination 측에서 S3를 허용해야 합니다.

```text
S3
 ↓
SQS

SQS Resource Policy
"Amazon S3가 SendMessage 하는 것을 허용"
```

Destination별로 보면:

```text
S3 → SNS
SNS Topic Resource Policy

S3 → SQS
SQS Queue Resource Policy

S3 → Lambda
Lambda Resource-based Policy
```

즉 단순히 S3에 IAM Role 하나를 붙이는 문제로 이해하면 안 됩니다.

Destination Resource가 S3의 접근을 허용하는 구조가 중요합니다.

---

## 6. SQS Hands-on Example

실습에서는 다음 흐름을 확인했습니다.

```text
S3 Bucket
   ↓
Event Notification
   ↓
All Object Create Events
   ↓
SQS Queue
```

처음에는 SQS Queue가 S3의 `SendMessage`를 허용하지 않아 설정에 실패했습니다.

```text
S3 ──X──▶ SQS
```

SQS Resource Policy를 수정한 후:

```text
S3 ─────▶ SQS
          Allow
```

Event Notification 설정이 성공했습니다.

이후 `coffee.jpg`를 업로드했습니다.

```text
coffee.jpg Upload
       ↓
S3
       ↓
ObjectCreated:Put
       ↓
SQS Queue
       ↓
Event Message
```

SQS Message 안에서 생성된 Object의 Key가 `coffee.jpg`임을 확인할 수 있습니다.

---

## 7. Amazon EventBridge

Amazon EventBridge는 S3 Event를 처리할 수 있는 또 다른 방법입니다.

S3 Event Notification은 S3 자체 기능인 반면, EventBridge는 별도의 AWS Event Routing Service입니다.

### Direct Event Notification

```text
S3
 │
 ├─ SNS
 ├─ SQS
 └─ Lambda
```

### EventBridge

```text
S3
 ↓
Amazon EventBridge
 ↓
Rules
 ↓
Various Targets
```

EventBridge는 보다 복잡한 Filtering, Routing, 여러 Target 연결, Event Archive 및 Replay와 같은 기능이 필요한 경우 사용할 수 있습니다.

---

## 8. Event Notification vs EventBridge

둘은 서로를 완전히 대체하는 관계로 이해하지 않습니다.

### Simple Event Processing

```text
"S3에 Object가 올라오면 Lambda 실행"

S3
 ↓
Lambda
```

단순한 경우 S3 Event Notification으로 직접 연결할 수 있습니다.

### Complex Event Routing

```text
S3
 ↓
EventBridge
 ↓
Rule
 ├─ Target A
 ├─ Target B
 └─ Target C
```

더 복잡한 Event Routing과 Filtering이 필요한 경우 EventBridge가 유용합니다.

### Mental Model

```text
Simple S3 Event
→ S3 Event Notification

Complex Event Routing
→ Amazon EventBridge
```

---

## 9. EventBridge 활성화

S3 Bucket에서 EventBridge Integration을 활성화하면 해당 Bucket의 지원되는 S3 Event를 EventBridge로 전달할 수 있습니다.

중요한 점은 모든 S3 Bucket이 자동으로 EventBridge에 Event를 보내는 것은 아니라는 것입니다.

```text
S3 Bucket
 ↓
EventBridge Integration = Enabled
 ↓
EventBridge
```

---

## 10. Recursive Event Loop 주의

Lambda가 S3 Event에 의해 실행되고 같은 Bucket에 새로운 Object를 생성하면 잘못된 설정에서 반복 실행이 발생할 수 있습니다.

```text
S3 ObjectCreated
      ↓
Lambda
      ↓
Same Bucket에 Thumbnail 저장
      ↓
ObjectCreated
      ↓
Lambda
      ↓
...
```

핵심 해결 원칙은:

> Lambda가 생성한 Output Object가 동일한 Trigger 조건에 다시 걸리지 않도록 한다.

예를 들어 Prefix를 분리할 수 있습니다.

```text
Input
incoming/photo.jpg
       ↓
Lambda
       ↓
Output
thumbnails/photo.jpg
```

Trigger:

```text
Prefix = incoming/
```

그러면 `thumbnails/`에 생성된 Object는 같은 Event를 다시 발생시키는 대상에서 제외할 수 있습니다.

또는 Input Bucket과 Output Bucket을 분리할 수도 있습니다.

---

## 🔑 핵심 정리

```text
S3 Event Notification
= S3 Event에 반응

Direct Destinations
= SNS / SQS / Lambda

Filtering
= Prefix / Suffix

Permission
= Destination Resource Policy

Complex Routing
= EventBridge

Lambda Loop 방지
= Output이 동일 Trigger에 다시 걸리지 않도록 설계
```

---

## 🇯🇵 日本語 Summary

S3 Event Notificationsは、S3でオブジェクトの作成・削除などのイベントが発生したときに、他のAWSサービスへ通知する機能です。

主な宛先：

- Amazon SNS
- Amazon SQS
- AWS Lambda

PrefixとSuffixを使用して対象オブジェクトをフィルタリングできます。

複雑なイベントルーティングや高度なフィルタリングが必要な場合はAmazon EventBridgeを利用できます。

Lambdaが同じS3 Bucketに新しいオブジェクトを書き込む場合、再帰的なイベントループを防止する設計が重要です。

---

## 🇺🇸 English Summary

S3 Event Notifications allow applications to react to events such as object creation and deletion.

Native destinations include:

- SNS
- SQS
- Lambda

Notifications can be filtered using object key prefixes and suffixes.

Amazon EventBridge provides a more flexible event-routing path for advanced filtering and multiple targets.

When Lambda writes back to S3, the architecture should prevent the output object from triggering the same event recursively.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Event Notification | イベント通知 | 이벤트 알림 |
| Event | イベント | 이벤트 |
| Destination | 送信先 | 대상 |
| Prefix | プレフィックス | 접두사 |
| Suffix | サフィックス | 접미사 |
| Resource Policy | リソースポリシー | 리소스 정책 |
| EventBridge | EventBridge | 이벤트브릿지 |
| Event Routing | イベントルーティング | 이벤트 라우팅 |
| Recursive Loop | 再帰ループ | 재귀 루프 |
| Trigger | トリガー | 트리거 |

---

## 📝 Review Questions

<details>
<summary>Q1. S3 Event Notification의 대표적인 직접 Destination 3개는?</summary>

SNS, SQS, Lambda입니다.

</details>

<details>
<summary>Q2. 특정 확장자의 Object만 Event 대상으로 만들 때 사용할 수 있는 것은?</summary>

Suffix Filter를 사용할 수 있습니다.

</details>

<details>
<summary>Q3. S3가 SQS로 Event를 보내려면 어떤 권한이 필요한가?</summary>

SQS Queue의 Resource Policy에서 S3의 메시지 전송을 허용해야 합니다.

</details>

<details>
<summary>Q4. EventBridge가 Event Notification을 완전히 대체하는가?</summary>

아닙니다. 단순한 S3 Event 처리는 Event Notification을 사용할 수 있고, 더 복잡한 Filtering 및 Routing에는 EventBridge가 유용합니다.

</details>

<details>
<summary>Q5. S3 → Lambda → 같은 S3 구조에서 주의할 문제는?</summary>

Lambda의 Output Object가 같은 Event Trigger를 다시 발생시켜 Recursive Loop가 생기지 않도록 해야 합니다.

</details>
