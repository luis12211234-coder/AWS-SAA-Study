# S3 Server Access Logging

## 🇰🇷 1. S3 Server Access Logging이란?

S3 Bucket에 들어오는 Request를 감사 목적으로 기록하는 기능입니다.

```text
User / Application
        ↓
     Request
        ↓
   Source Bucket
        ↓
   Access Log
        ↓
Logging Bucket
```

승인된 Request뿐 아니라 거부된 Request에 대한 정보도 기록할 수 있습니다.

---

## 2. 로그는 다른 S3 Bucket에 저장

Access Logging을 활성화하면 로그가 Destination S3 Bucket에 Object 형태로 저장됩니다.

```text
Application Bucket
       │
       │ Access Logs
       ▼
Logging Bucket
```

Destination Logging Bucket은 Source Bucket과 같은 AWS Region에 있어야 합니다.

---

## 3. Logging Bucket 권한

S3 Logging Service가 Destination Bucket에 Log Object를 저장할 수 있어야 합니다.

따라서 Destination Bucket에는 S3 Logging Service가 Object를 작성할 수 있도록 필요한 권한이 구성됩니다.

```text
S3 Logging Service
        ↓
   Put Log Object
        ↓
Logging Bucket
```

---

## 4. 로그 활용

Access Log에는 다음과 같은 정보를 분석하는 데 필요한 데이터가 포함될 수 있습니다.

```text
Who accessed?
When?
Which Bucket / Object?
Which Request?
Success / Failure?
```

저장된 로그는 Amazon Athena 등의 분석 도구와 함께 분석할 수 있습니다.

---

## 5. Logging Loop 주의

Source Bucket과 Logging Bucket을 동일하게 설정하면 안 됩니다.

```text
Bucket
 ↓
Request 발생
 ↓
Log 생성
 ↓
같은 Bucket에 Log 저장
 ↓
또 새로운 Access 발생
 ↓
새 Log 생성
 ↓
...
```

Logging Loop가 발생하여 Log Object가 계속 증가할 수 있습니다.

따라서:

```text
Source Bucket
≠
Logging Bucket
```

으로 구성합니다.

---

## 6. 실시간 로그가 아니다

Server Access Log는 즉시 나타나는 실시간 로그로 생각하면 안 됩니다.

Log가 Destination Bucket에 전달되기까지 시간이 걸릴 수 있습니다.

---

## 🎯 Exam Scenario

```text
Audit S3 Requests
+
Store Access Records
+
Analyze Later
        ↓
S3 Server Access Logging
```

특히 Source와 Destination을 같은 Bucket으로 설정하는 선택지는 피합니다.

---

## 🔑 핵심 정리

```text
S3 Access Logging
= S3 Request 감사 기록

Log 저장
→ 다른 S3 Bucket

Destination
→ Same Region

분석
→ Athena 등 활용 가능

주의
→ Source Bucket = Logging Bucket ❌
```

한 줄 정리:

> **누가 S3에 어떤 요청을 보냈는지 기록하고 감사한다 = S3 Server Access Logging**

---

## 🇯🇵 日本語 Summary

S3 Server Access Loggingは、S3 BucketへのRequestを監査目的で記録する機能です。

Access Logは別のS3 Bucketに保存され、Amazon Athenaなどを利用して分析できます。

Source BucketとLogging Bucketを同じBucketにするとLogging Loopが発生する可能性があるため避けます。

---

## 🇺🇸 English Summary

S3 Server Access Logging records requests made to an S3 bucket for auditing purposes.

Logs are delivered to a destination S3 bucket and can be analyzed using tools such as Amazon Athena.

The source bucket and logging bucket should not be the same because this can create a logging loop.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Server Access Logging | サーバーアクセスログ | 서버 액세스 로깅 |
| Audit | 監査 | 감사 |
| Source Bucket | ソースバケット | 소스 버킷 |
| Destination Bucket | 宛先バケット | 대상 버킷 |
| Access Log | アクセスログ | 액세스 로그 |
| Logging Loop | ロギングループ | 로깅 루프 |

---

## 📝 Review Questions

<details>
<summary>Q1. S3 Request를 감사 목적으로 기록하는 기능은?</summary>

S3 Server Access Logging입니다.

</details>

<details>
<summary>Q2. Source Bucket과 Logging Bucket을 동일하게 설정하면 왜 위험한가?</summary>

Log 저장 자체가 다시 Access를 발생시켜 Logging Loop가 발생할 수 있기 때문입니다.

</details>

<details>
<summary>Q3. S3 Access Log를 분석하는 데 사용할 수 있는 AWS 서비스의 예는?</summary>

Amazon Athena입니다.

</details>
