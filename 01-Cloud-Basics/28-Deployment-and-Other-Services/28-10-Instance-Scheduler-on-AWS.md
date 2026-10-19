# 📘 28-10. Instance Scheduler on AWS

> [!NOTE]
> **AWS SAA-C03 Study Note** · GitHub Section 28 · Udemy Section 30  
> **Keywords:** Scheduling, Resource Tag

## 🇰🇷 1. 핵심 개념

Instance Scheduler는 업무 시간에 맞춰 EC2·RDS 등 지원 리소스를 자동 시작·중지하는 AWS 솔루션이다.

## 🇰🇷 2. 동작 원리 및 사용 사례

독립 관리형 서비스가 아니라 CloudFormation으로 배포하는 솔루션이다. DynamoDB에 일정·설정을 저장하고 Lambda가 태그로 대상 리소스를 식별하여 시작·중지한다. 계정·리전 간 운영을 지원할 수 있다. 중지 후에도 EBS 등의 저장 비용은 남을 수 있다.

## 🇰🇷 3. 서비스 선택 기준

비업무 시간 EC2/RDS 중지로 비용 절감 → Instance Scheduler. CloudFormation은 솔루션 배포 도구다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **핵심 역할:** Instance Scheduler는 업무 시간에 맞춰 EC2·RDS 등 지원 리소스를 자동 시작·중지하는 AWS 솔루션이다.
- **시험 선택 기준:** 비업무 시간 EC2/RDS 중지로 비용 절감 → Instance Scheduler. CloudFormation은 솔루션 배포 도구다.
- **구현 시 주의점:** 독립 관리형 서비스가 아니라 CloudFormation으로 배포하는 솔루션이다. DynamoDB에 일정·설정을 저장하고 Lambda가 태그로 대상 리소스를 식별하여 시작·중지한다. 계정·리전 간 운영을 지원할 수 있다. 중지 후에도 EBS 등의 저장 비용은 남을 수 있다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 이 서비스의 핵심 목적은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Instance Scheduler는 업무 시간에 맞춰 EC2·RDS 등 지원 리소스를 자동 시작·중지하는 AWS 솔루션이다.

</details>

### Q2. 대표적인 사용 사례를 하나 설명하라.

<details>
<summary>정답 및 해설 보기</summary>

독립 관리형 서비스가 아니라 CloudFormation으로 배포하는 솔루션이다. DynamoDB에 일정·설정을 저장하고 Lambda가 태그로 대상 리소스를 식별하여 시작·중지한다. 계정·리전 간 운영을 지원할 수 있다. 중지 후에도 EBS 등의 저장 비용은 남을 수 있다.

</details>

### Q3. 어떤 요구사항에서 이 서비스를 선택하는가?

<details>
<summary>정답 및 해설 보기</summary>

비업무 시간 EC2/RDS 중지로 비용 절감 → Instance Scheduler. CloudFormation은 솔루션 배포 도구다.

</details>

### Q4. 구현 시 알아야 할 조건 또는 제한은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

독립 관리형 서비스가 아니라 CloudFormation으로 배포하는 솔루션이다. DynamoDB에 일정·설정을 저장하고 Lambda가 태그로 대상 리소스를 식별하여 시작·중지한다. 계정·리전 간 운영을 지원할 수 있다. 중지 후에도 EBS 등의 저장 비용은 남을 수 있다.

</details>

### Q5. 시험에서 혼동하기 쉬운 기능과 어떻게 구분하는가?

<details>
<summary>정답 및 해설 보기</summary>

비업무 시간 EC2/RDS 중지로 비용 절감 → Instance Scheduler. CloudFormation은 솔루션 배포 도구다.

</details>

## 🇯🇵 日本語 Summary

Instance Scheduler は CloudFormation で導入するソリューションで、EC2 や RDS を時間に合わせて起動・停止する。

## 🇺🇸 English Summary

Instance Scheduler is a CloudFormation-deployed solution that starts and stops supported resources on schedules.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Scheduling | 일정 관리 | スケジューリング |
| Resource Tag | 리소스 태그 | リソースタグ |
