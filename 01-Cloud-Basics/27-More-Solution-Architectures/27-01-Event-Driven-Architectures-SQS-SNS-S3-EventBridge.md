# 📘 27-01. Event-Driven Architectures

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `이벤트 기반 아키텍처`, `SQS → Lambda`, `SQS Standard와 FIFO`, `SNS와 Lambda 실패 구분`

## 🇰🇷 1. 이벤트 기반 아키텍처

서비스가 서로 직접 호출하는 대신 이벤트나 메시지를 통해 느슨하게 결합한다. 생산자는 이벤트를 발행하고 소비자는 독립적으로 처리한다. 메시지 중복·실패·재시도를 고려해야 한다.

## 🇰🇷 2. SQS → Lambda

Lambda의 Event Source Mapping이 SQS를 폴링하여 메시지 배치를 전달한다. 실패 시 Visibility Timeout 뒤 재처리될 수 있다. maxReceiveCount를 넘으면 SQS Redrive Policy에 따라 메시지가 SQS DLQ로 이동한다. Partial Batch Response를 사용하면 실패한 메시지만 재시도할 수 있다.

## 🇰🇷 3. SQS Standard와 FIFO

Standard는 높은 처리량과 At-least-once 전달, 순서 비보장을 기본으로 한다. FIFO는 중복 제거 및 MessageGroupId별 순서를 지원한다. 실패한 메시지가 막는 범위는 일반적으로 동일 메시지 그룹이다. 소비자는 멱등성을 구현해야 한다.

## 🇰🇷 4. SNS와 Lambda 실패 구분

SNS → Lambda는 비동기 호출이다. SNS가 Lambda에 이벤트를 전달하지 못한 경우 SNS 구독 DLQ를 사용할 수 있다. Lambda가 이벤트를 수락한 뒤 실행 실패하면 Lambda 비동기 재시도와 DLQ/Destination을 검토한다. SQS → Lambda 실패 메시지는 SQS DLQ로 처리한다.

## 🇰🇷 5. SNS Fan-out 및 S3 이벤트

SNS 토픽에서 여러 SQS 구독으로 이벤트를 팬아웃하여 독립 소비자를 둔다. S3 Event Notifications는 객체 생성/삭제 등의 이벤트를 SNS, SQS, Lambda로 보낼 수 있고 prefix/suffix 필터를 지원한다. EventBridge는 정교한 패턴 매칭, 여러 타깃, 아카이브·리플레이 등에 적합하다.

## 🇰🇷 6. 기타 패턴

CloudTrail에 기록되는 DeleteTable 같은 API 활동을 EventBridge 규칙으로 탐지하여 SNS로 알릴 수 있다. API Gateway → Kinesis Data Streams → Amazon Data Firehose → S3 파이프라인은 대량 이벤트 수집 및 저장에 활용된다. 중복 결제 방지 등은 멱등성 키로 처리한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **이벤트 기반 아키텍처:** 서비스가 서로 직접 호출하는 대신 이벤트나 메시지를 통해 느슨하게 결합한다.
- **SQS → Lambda:** Lambda의 Event Source Mapping이 SQS를 폴링하여 메시지 배치를 전달한다.
- **SQS Standard와 FIFO:** Standard는 높은 처리량과 At-least-once 전달, 순서 비보장을 기본으로 한다.
- **SNS와 Lambda 실패 구분:** SNS → Lambda는 비동기 호출이다.
- **SNS Fan-out 및 S3 이벤트:** SNS 토픽에서 여러 SQS 구독으로 이벤트를 팬아웃하여 독립 소비자를 둔다.
- **기타 패턴:** CloudTrail에 기록되는 DeleteTable 같은 API 활동을 EventBridge 규칙으로 탐지하여 SNS로 알릴 수 있다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. SQS에서 Lambda가 메시지 처리를 반복 실패하면 무엇이 DLQ로 이동하는가?

<details>
<summary>정답 및 해설 보기</summary>

실패한 **메시지**다. Lambda 함수 자체가 이동하는 것이 아니다.

</details>

### Q2. SQS Standard와 FIFO의 순서 보장 차이는?

<details>
<summary>정답 및 해설 보기</summary>

Standard는 순서를 보장하지 않으며 FIFO는 MessageGroupId별 순서를 유지한다.

</details>

### Q3. SNS가 Lambda에 이벤트를 전달하지 못한 경우와 Lambda 실행 실패의 DLQ 주체는?

<details>
<summary>정답 및 해설 보기</summary>

전자는 SNS 구독 DLQ, 후자는 Lambda 비동기 DLQ 또는 Destination을 검토한다.

</details>

### Q4. 이벤트를 여러 독립 서비스에 안전하게 배포하려면?

<details>
<summary>정답 및 해설 보기</summary>

SNS 토픽에 각 서비스용 SQS 큐를 구독시키는 팬아웃 패턴을 사용한다.

</details>

### Q5. S3 이벤트를 복잡한 조건으로 필터링하고 리플레이하려면?

<details>
<summary>정답 및 해설 보기</summary>

Amazon EventBridge를 고려한다.

</details>

## 🇯🇵 日本語 Summary

Event-Driven Architectures：AWS SAA-C03 では、要件に応じてサービスを組み合わせ、可用性・性能・運用自動化を設計する。主要な選択基準と障害時の動作を理解する。

## 🇺🇸 English Summary

Event-Driven Architectures: Understand the architecture, trade-offs, failure handling, and SAA-C03 service selection criteria. Combine managed AWS services based on performance, availability, and operational requirements.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| 이벤트 기반 아키텍처 | 이벤트 기반 아키텍처 | 이벤트 기반 아키텍처 |
| SQS → Lambda | SQS → Lambda | SQS → Lambda |
| SQS Standard와 FIFO | SQS Standard와 FIFO | SQS Standard와 FIFO |
| SNS와 Lambda 실패 구분 | SNS와 Lambda 실패 구분 | SNS와 Lambda 실패 구분 |
| SNS Fan-out 및 S3 이벤트 | SNS Fan-out 및 S3 이벤트 | SNS Fan-out 및 S3 이벤트 |
| 기타 패턴 | 기타 패턴 | 기타 패턴 |
