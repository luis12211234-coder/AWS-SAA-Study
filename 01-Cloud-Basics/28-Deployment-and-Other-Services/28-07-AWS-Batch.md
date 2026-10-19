# 📘 28-07. AWS Batch

> [!NOTE]
> **AWS SAA-C03 Study Note** · GitHub Section 28 · Udemy Section 30  
> **Keywords:** Job Queue, Batch Processing

## 🇰🇷 1. 핵심 개념

AWS Batch는 시작과 끝이 있는 대량의 컴퓨팅 작업을 예약하고 실행한다.

## 🇰🇷 2. 동작 원리 및 사용 사례

작업은 Job Definition으로 컨테이너 이미지와 자원을 정의하고 Job Queue에서 대기한다. Batch는 컴퓨팅 환경을 조정해 ECS/EKS 기반 컨테이너 작업을 실행한다. EC2, Spot, Fargate 등 지원 환경을 이용할 수 있다. Lambda는 최대 15분이지만 Batch는 장시간·대량 처리에 적합하다.

## 🇰🇷 3. 서비스 선택 기준

대량 이미지 처리·오래 걸리는 계산 작업은 Batch, 짧은 이벤트 기반 함수는 Lambda. Batch가 EC2만 지원한다는 설명은 오래됐다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **핵심 역할:** AWS Batch는 시작과 끝이 있는 대량의 컴퓨팅 작업을 예약하고 실행한다.
- **시험 선택 기준:** 대량 이미지 처리·오래 걸리는 계산 작업은 Batch, 짧은 이벤트 기반 함수는 Lambda. Batch가 EC2만 지원한다는 설명은 오래됐다.
- **구현 시 주의점:** 작업은 Job Definition으로 컨테이너 이미지와 자원을 정의하고 Job Queue에서 대기한다. Batch는 컴퓨팅 환경을 조정해 ECS/EKS 기반 컨테이너 작업을 실행한다. EC2, Spot, Fargate 등 지원 환경을 이용할 수 있다. Lambda는 최대 15분이지만 Batch는 장시간·대량 처리에 적합하다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 이 서비스의 핵심 목적은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

AWS Batch는 시작과 끝이 있는 대량의 컴퓨팅 작업을 예약하고 실행한다.

</details>

### Q2. 대표적인 사용 사례를 하나 설명하라.

<details>
<summary>정답 및 해설 보기</summary>

작업은 Job Definition으로 컨테이너 이미지와 자원을 정의하고 Job Queue에서 대기한다. Batch는 컴퓨팅 환경을 조정해 ECS/EKS 기반 컨테이너 작업을 실행한다. EC2, Spot, Fargate 등 지원 환경을 이용할 수 있다. Lambda는 최대 15분이지만 Batch는 장시간·대량 처리에 적합하다.

</details>

### Q3. 어떤 요구사항에서 이 서비스를 선택하는가?

<details>
<summary>정답 및 해설 보기</summary>

대량 이미지 처리·오래 걸리는 계산 작업은 Batch, 짧은 이벤트 기반 함수는 Lambda. Batch가 EC2만 지원한다는 설명은 오래됐다.

</details>

### Q4. 구현 시 알아야 할 조건 또는 제한은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

작업은 Job Definition으로 컨테이너 이미지와 자원을 정의하고 Job Queue에서 대기한다. Batch는 컴퓨팅 환경을 조정해 ECS/EKS 기반 컨테이너 작업을 실행한다. EC2, Spot, Fargate 등 지원 환경을 이용할 수 있다. Lambda는 최대 15분이지만 Batch는 장시간·대량 처리에 적합하다.

</details>

### Q5. 시험에서 혼동하기 쉬운 기능과 어떻게 구분하는가?

<details>
<summary>정답 및 해설 보기</summary>

대량 이미지 처리·오래 걸리는 계산 작업은 Batch, 짧은 이벤트 기반 함수는 Lambda. Batch가 EC2만 지원한다는 설명은 오래됐다.

</details>

## 🇯🇵 日本語 Summary

AWS Batch はジョブキューと計算リソースを管理して、大量のコンテナ処理を実行する。

## 🇺🇸 English Summary

AWS Batch schedules finite container jobs and provisions compute capacity for large-scale workloads.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Job Queue | 작업 대기열 | ジョブキュー |
| Batch Processing | 배치 처리 | バッチ処理 |
