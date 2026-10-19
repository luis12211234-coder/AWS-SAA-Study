# 📘 28-02. Amazon SES & Pinpoint

> [!NOTE]
> **AWS SAA-C03 Study Note** · GitHub Section 28 · Udemy Section 30  
> **Keywords:** SES, SMTP

## 🇰🇷 1. 핵심 개념

Amazon SES는 애플리케이션 이메일 발송과 수신을 지원하는 관리형 서비스다.

## 🇰🇷 2. 동작 원리 및 사용 사례

SES는 SMTP/API를 통해 트랜잭션·대량 이메일을 발송하고 SPF/DKIM 같은 발신 도메인 인증을 지원한다. Amazon Pinpoint는 고객 세분화와 캠페인, 멀티채널 참여를 지원하던 서비스다. 주요 Pinpoint 고객 참여 기능은 2026-10-30 지원 종료 예정이므로 신규 설계에서는 SES, AWS End User Messaging 등의 대안을 검토한다.

## 🇰🇷 3. 서비스 선택 기준

이메일 발송은 SES, 강의의 고객 세분화·캠페인은 Pinpoint로 구분한다. Pinpoint의 지원 종료 일정을 주의한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **핵심 역할:** Amazon SES는 애플리케이션 이메일 발송과 수신을 지원하는 관리형 서비스다.
- **시험 선택 기준:** 이메일 발송은 SES, 강의의 고객 세분화·캠페인은 Pinpoint로 구분한다. Pinpoint의 지원 종료 일정을 주의한다.
- **구현 시 주의점:** SES는 SMTP/API를 통해 트랜잭션·대량 이메일을 발송하고 SPF/DKIM 같은 발신 도메인 인증을 지원한다. Amazon Pinpoint는 고객 세분화와 캠페인, 멀티채널 참여를 지원하던 서비스다. 주요 Pinpoint 고객 참여 기능은 2026-10-30 지원 종료 예정이므로 신규 설계에서는 SES, AWS End User Messaging 등의 대안을 검토한다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 이 서비스의 핵심 목적은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Amazon SES는 애플리케이션 이메일 발송과 수신을 지원하는 관리형 서비스다.

</details>

### Q2. 대표적인 사용 사례를 하나 설명하라.

<details>
<summary>정답 및 해설 보기</summary>

SES는 SMTP/API를 통해 트랜잭션·대량 이메일을 발송하고 SPF/DKIM 같은 발신 도메인 인증을 지원한다. Amazon Pinpoint는 고객 세분화와 캠페인, 멀티채널 참여를 지원하던 서비스다. 주요 Pinpoint 고객 참여 기능은 2026-10-30 지원 종료 예정이므로 신규 설계에서는 SES, AWS End User Messaging 등의 대안을 검토한다.

</details>

### Q3. 어떤 요구사항에서 이 서비스를 선택하는가?

<details>
<summary>정답 및 해설 보기</summary>

이메일 발송은 SES, 강의의 고객 세분화·캠페인은 Pinpoint로 구분한다. Pinpoint의 지원 종료 일정을 주의한다.

</details>

### Q4. 구현 시 알아야 할 조건 또는 제한은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

SES는 SMTP/API를 통해 트랜잭션·대량 이메일을 발송하고 SPF/DKIM 같은 발신 도메인 인증을 지원한다. Amazon Pinpoint는 고객 세분화와 캠페인, 멀티채널 참여를 지원하던 서비스다. 주요 Pinpoint 고객 참여 기능은 2026-10-30 지원 종료 예정이므로 신규 설계에서는 SES, AWS End User Messaging 등의 대안을 검토한다.

</details>

### Q5. 시험에서 혼동하기 쉬운 기능과 어떻게 구분하는가?

<details>
<summary>정답 및 해설 보기</summary>

이메일 발송은 SES, 강의의 고객 세분화·캠페인은 Pinpoint로 구분한다. Pinpoint의 지원 종료 일정을 주의한다.

</details>

## 🇯🇵 日本語 Summary

SES はメール配信サービス。Pinpoint は顧客キャンペーンを扱ってきたが、主要機能は終了予定。

## 🇺🇸 English Summary

SES delivers application emails. Pinpoint historically supported campaigns and segmentation, but key engagement features are being retired.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| SES | 이메일 | メール |
| SMTP | 전송 프로토콜 | 転送プロトコル |
