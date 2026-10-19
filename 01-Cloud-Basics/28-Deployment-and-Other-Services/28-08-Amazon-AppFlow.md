# 📘 28-08. Amazon AppFlow

> [!NOTE]
> **AWS SAA-C03 Study Note** · GitHub Section 28 · Udemy Section 30  
> **Keywords:** SaaS, Connector

## 🇰🇷 1. 핵심 개념

AppFlow는 Salesforce 등 SaaS와 AWS 서비스 사이 데이터를 전송하는 관리형 통합 서비스다.

## 🇰🇷 2. 동작 원리 및 사용 사례

지원 커넥터를 통해 Salesforce·ServiceNow 등의 SaaS 데이터를 S3·Redshift 등의 대상으로 보낸다. 예약, 수동, 지원 이벤트 기반 전송과 필터링·매핑·검증을 지원한다. 전송 경로는 커넥터에 따라 암호화된 인터넷 또는 PrivateLink를 사용할 수 있다.

## 🇰🇷 3. 서비스 선택 기준

Salesforce에서 S3로 데이터 이동 → AppFlow. 앱 개발 플랫폼 Amplify와 혼동하지 않는다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **핵심 역할:** AppFlow는 Salesforce 등 SaaS와 AWS 서비스 사이 데이터를 전송하는 관리형 통합 서비스다.
- **시험 선택 기준:** Salesforce에서 S3로 데이터 이동 → AppFlow. 앱 개발 플랫폼 Amplify와 혼동하지 않는다.
- **구현 시 주의점:** 지원 커넥터를 통해 Salesforce·ServiceNow 등의 SaaS 데이터를 S3·Redshift 등의 대상으로 보낸다. 예약, 수동, 지원 이벤트 기반 전송과 필터링·매핑·검증을 지원한다. 전송 경로는 커넥터에 따라 암호화된 인터넷 또는 PrivateLink를 사용할 수 있다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 이 서비스의 핵심 목적은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

AppFlow는 Salesforce 등 SaaS와 AWS 서비스 사이 데이터를 전송하는 관리형 통합 서비스다.

</details>

### Q2. 대표적인 사용 사례를 하나 설명하라.

<details>
<summary>정답 및 해설 보기</summary>

지원 커넥터를 통해 Salesforce·ServiceNow 등의 SaaS 데이터를 S3·Redshift 등의 대상으로 보낸다. 예약, 수동, 지원 이벤트 기반 전송과 필터링·매핑·검증을 지원한다. 전송 경로는 커넥터에 따라 암호화된 인터넷 또는 PrivateLink를 사용할 수 있다.

</details>

### Q3. 어떤 요구사항에서 이 서비스를 선택하는가?

<details>
<summary>정답 및 해설 보기</summary>

Salesforce에서 S3로 데이터 이동 → AppFlow. 앱 개발 플랫폼 Amplify와 혼동하지 않는다.

</details>

### Q4. 구현 시 알아야 할 조건 또는 제한은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

지원 커넥터를 통해 Salesforce·ServiceNow 등의 SaaS 데이터를 S3·Redshift 등의 대상으로 보낸다. 예약, 수동, 지원 이벤트 기반 전송과 필터링·매핑·검증을 지원한다. 전송 경로는 커넥터에 따라 암호화된 인터넷 또는 PrivateLink를 사용할 수 있다.

</details>

### Q5. 시험에서 혼동하기 쉬운 기능과 어떻게 구분하는가?

<details>
<summary>정답 및 해설 보기</summary>

Salesforce에서 S3로 데이터 이동 → AppFlow. 앱 개발 플랫폼 Amplify와 혼동하지 않는다.

</details>

## 🇯🇵 日本語 Summary

AppFlow は SaaS と AWS のデータ連携を簡単にするマネージドサービス。

## 🇺🇸 English Summary

AppFlow integrates supported SaaS applications with AWS data destinations using managed connectors.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| SaaS | 서비스형 소프트웨어 | SaaS |
| Connector | 커넥터 | コネクタ |
