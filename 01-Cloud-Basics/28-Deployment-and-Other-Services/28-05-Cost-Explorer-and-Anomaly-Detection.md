# 📘 28-05. Cost Explorer & Cost Anomaly Detection

> [!NOTE]
> **AWS SAA-C03 Study Note** · GitHub Section 28 · Udemy Section 30  
> **Keywords:** Cost Forecast, Anomaly Detection

## 🇰🇷 1. 핵심 개념

Cost Explorer는 AWS 비용을 분석·시각화·예측하고 Cost Anomaly Detection은 비정상 지출을 탐지한다.

## 🇰🇷 2. 동작 원리 및 사용 사례

Cost Explorer는 서비스·계정·시간별 비용과 사용량을 분석하고 Savings Plans 추천과 예측을 제공한다. Cost Anomaly Detection은 과거 지출 패턴을 ML로 학습해 급증·지속 증가를 감지하고 원인 분석 및 알림을 제공한다. 탐지 기준을 직접 고정하지 않아도 되지만 알림 임계값은 설정할 수 있다.

## 🇰🇷 3. 서비스 선택 기준

비용 분석·예측은 Cost Explorer, ML 이상 지출 탐지는 Cost Anomaly Detection, 예산 기준 경고는 AWS Budgets와 구분한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **핵심 역할:** Cost Explorer는 AWS 비용을 분석·시각화·예측하고 Cost Anomaly Detection은 비정상 지출을 탐지한다.
- **시험 선택 기준:** 비용 분석·예측은 Cost Explorer, ML 이상 지출 탐지는 Cost Anomaly Detection, 예산 기준 경고는 AWS Budgets와 구분한다.
- **구현 시 주의점:** Cost Explorer는 서비스·계정·시간별 비용과 사용량을 분석하고 Savings Plans 추천과 예측을 제공한다. Cost Anomaly Detection은 과거 지출 패턴을 ML로 학습해 급증·지속 증가를 감지하고 원인 분석 및 알림을 제공한다. 탐지 기준을 직접 고정하지 않아도 되지만 알림 임계값은 설정할 수 있다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 이 서비스의 핵심 목적은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Cost Explorer는 AWS 비용을 분석·시각화·예측하고 Cost Anomaly Detection은 비정상 지출을 탐지한다.

</details>

### Q2. 대표적인 사용 사례를 하나 설명하라.

<details>
<summary>정답 및 해설 보기</summary>

Cost Explorer는 서비스·계정·시간별 비용과 사용량을 분석하고 Savings Plans 추천과 예측을 제공한다. Cost Anomaly Detection은 과거 지출 패턴을 ML로 학습해 급증·지속 증가를 감지하고 원인 분석 및 알림을 제공한다. 탐지 기준을 직접 고정하지 않아도 되지만 알림 임계값은 설정할 수 있다.

</details>

### Q3. 어떤 요구사항에서 이 서비스를 선택하는가?

<details>
<summary>정답 및 해설 보기</summary>

비용 분석·예측은 Cost Explorer, ML 이상 지출 탐지는 Cost Anomaly Detection, 예산 기준 경고는 AWS Budgets와 구분한다.

</details>

### Q4. 구현 시 알아야 할 조건 또는 제한은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Cost Explorer는 서비스·계정·시간별 비용과 사용량을 분석하고 Savings Plans 추천과 예측을 제공한다. Cost Anomaly Detection은 과거 지출 패턴을 ML로 학습해 급증·지속 증가를 감지하고 원인 분석 및 알림을 제공한다. 탐지 기준을 직접 고정하지 않아도 되지만 알림 임계값은 설정할 수 있다.

</details>

### Q5. 시험에서 혼동하기 쉬운 기능과 어떻게 구분하는가?

<details>
<summary>정답 및 해설 보기</summary>

비용 분석·예측은 Cost Explorer, ML 이상 지출 탐지는 Cost Anomaly Detection, 예산 기준 경고는 AWS Budgets와 구분한다.

</details>

## 🇯🇵 日本語 Summary

Cost Explorer は費用分析と予測、Cost Anomaly Detection は機械学習による異常支出検知を担当する。

## 🇺🇸 English Summary

Cost Explorer analyzes and forecasts spending; Cost Anomaly Detection identifies unusual spending using ML.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Cost Forecast | 비용 예측 | 費用予測 |
| Anomaly Detection | 이상 탐지 | 異常検知 |
