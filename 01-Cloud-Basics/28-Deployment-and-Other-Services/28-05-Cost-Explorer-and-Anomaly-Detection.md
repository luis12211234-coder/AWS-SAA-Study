
# 📘 28-05. AWS Cost Explorer & Cost Anomaly Detection

> [!NOTE]
> **AWS SAA-C03 Study Note** · `GitHub Section 28` · `Udemy Section 30`
> **핵심 키워드:** Cost Explorer, Cost Forecast, Savings Plans, Cost Anomaly Detection, AWS Budgets, ML, SNS

## 🇰🇷 1. AWS 비용 관리의 세 가지 질문

AWS 비용 관리 문제는 다음 세 질문으로 구분하면 이해하기 쉽다.

1. **어디에 돈을 썼지?** → **AWS Cost Explorer**.
2. **평소보다 이상하게 많이 나왔나?** → **AWS Cost Anomaly Detection**.
3. **정해 둔 예산을 넘었나?** → **AWS Budgets**.

세 도구는 경쟁 관계가 아니라 함께 사용하면 좋은 보완 관계다.

## 🇰🇷 2. Cost Explorer: 과거·현재·미래 비용 분석

**Cost Explorer**는 AWS의 비용·사용량을 시각화하고, 기준별로 묶거나 필터링해 비교하며 미래 비용을 예측하는 도구다.

### 예시: 이번 달 요금이 30만 원 나왔다면?

| 서비스 | 가상 월 비용 |
|---|---:|
| EC2 | 200,000원 |
| RDS | 70,000원 |
| S3 | 30,000원 |

합계가 30만 원이라는 사실보다, **어떤 서비스·계정·사용 유형이 비용 증가를 주도했는지**가 중요하다.

- **Group by Service:** EC2, RDS, S3별 비용 분리.
- **Group by Linked Account / Tag:** 팀·프로젝트·계정별 비용 배분.
- **Granularity:** 월별·일별 분석. **시간별 또는 리소스별 상세 데이터는 지원되는 서비스·설정과 추가 비용 조건**을 확인해야 한다.
- **Forecast:** 과거 지출을 기반으로 미래 비용을 예측한다. 현재 AWS 문서상 **최대 18개월(월 단위) 예측**을 지원한다. 예측은 보장된 청구액이 아니다.
- **Savings Plans / RI 분석:** 사용 패턴에 맞는 약정 할인 선택을 검토한다. **Savings Plans나 RI 자체가 Cost Explorer에서 자동 구매되는 것은 아니다.**

### 왜 Savings Plans 추천이 필요한가?

예를 들어 항상 일정량의 EC2 컴퓨팅을 사용한다면 종량제(On-Demand)만 고집하기보다 사용량 약정을 통해 할인을 받을 수 있다. 다만 **약정은 비용 의무를 동반**하므로 실제 사용 패턴을 분석해야 한다.

## 🇰🇷 3. Cost Anomaly Detection: ML로 이상 지출 감지

**Cost Anomaly Detection**은 과거의 정상적인 비용 패턴을 머신러닝으로 학습해 **일회성 급증**이나 **계속 증가하는 비정상 지출**을 탐지한다.

```text
평소 EC2 비용: 하루 3~5달러
               ↓
잘못된 배포로 고가 인스턴스 실행
               ↓
비용 패턴이 평소와 달라짐
               ↓
Cost Anomaly Detection (ML)
               ↓
이상 탐지 + 영향 금액 + 원인 후보 + 알림
```

### 실제 설정 개념

1. **Cost Monitor 생성:** 서비스, 연결 계정, Cost Allocation Tag, Cost Category 등 어떤 비용 범위를 감시할지 정한다.
2. **Alert Subscription 생성:** 수신 방식과 알림 조건을 설정한다.
3. 이상 지출 발생 시 AWS가 **비용 영향도와 원인 분석 정보**를 제공한다.

탐지 모델의 '정상 비용'을 사람이 고정값으로 일일이 정의할 필요는 없다. 그러나 **알림 기준 금액(절대값)이나 영향 비율(%)은 설정할 수 있다.**

> [!CAUTION]
> 비용 이상 탐지는 실시간 시스템 모니터링과 다르다. 청구 데이터 처리에 지연이 있어 사용 후 탐지까지 시간이 걸릴 수 있다. 초 단위 과금 사고 차단이나 리소스 자동 종료를 보장하지 않는다.

## 🇰🇷 4. AWS Budgets와의 비교

**AWS Budgets**는 '이번 달 예상 또는 실제 AWS 비용이 내가 정한 예산을 넘는지'를 기준으로 관리한다.

| 상황 | 적합한 도구 |
|---|---|
| 지난달 S3 비용이 왜 늘었는지 조사 | **Cost Explorer** |
| 향후 몇 달 비용 추세 예측 | **Cost Explorer** |
| 평소와 다른 갑작스러운 비용 급증 탐지 | **Cost Anomaly Detection** |
| 월별 예산 100달러 도달 시 알림 | **AWS Budgets** |
| 특정 프로젝트가 얼마나 비용을 사용했는지 분석 | **Cost Explorer + 태그** |

### 함께 쓰는 운영 시나리오

```text
AWS Budgets: 월 예산 200달러 알림
        +
Cost Anomaly Detection: 비정상적인 지출 조기 발견
        +
Cost Explorer: 어떤 리소스가 비용 증가를 주도했는지 분석
```

**비용 이상 탐지**와 **비용의 원인 추적**은 연결되지만 역할이 다르다.

## 🇰🇷 5. 설계·운영상 주의사항

- **예측치는 추정값:** 신규 트래픽 급증, 할인 정책 변경, 신규 리소스 때문에 달라질 수 있다.
- **태그 기반 분석:** 리소스에 태그를 붙였다는 사실만으로 모든 과거 비용이 자동 분류되는 것은 아니다. Cost Allocation Tag 활성화 및 데이터 반영 조건을 확인한다.
- **실제 청구 시차:** 비용 데이터를 즉시 반영하는 실시간 지표처럼 해석하지 않는다.
- **할인 추천:** Savings Plans는 '싸 보인다'보다 지속적인 사용량과 약정 위험을 함께 판단해야 한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> **분석·시각화·예측 → Cost Explorer / ML 이상 지출 → Cost Anomaly Detection / 정해진 예산 임계값 → AWS Budgets.**

- Cost Explorer는 서비스·계정·시간·태그 기반 비용 분석 및 절감 기회 파악.
- 강의의 12개월 예측 설명은 오래된 표현이다. 현재 주요 공식 문서는 **최대 18개월**을 안내한다.
- Cost Anomaly Detection의 ML은 패턴을 학습하지만 **알림 임계값은 설정 가능**하다.
- 비용 분석 도구는 요금을 **직접 자동으로 절감하거나 리소스를 자동 종료하는 도구가 아니다**.

## 📝 Review Questions

> [!TIP]
> 답을 먼저 떠올리고 펼쳐 확인한다.

### Q1. 월별 EC2·RDS·S3 비용을 비교하고 싶다. 어떤 도구가 적합한가?

<details>
<summary>정답 및 해설 보기</summary>

Cost Explorer. 서비스별 Group By나 Filter를 활용해 비용 및 사용량을 분석한다.

</details>

### Q2. 지출이 매주 증가하는 정상적인 추세와 비정상적인 급증을 ML로 구분하려면?

<details>
<summary>정답 및 해설 보기</summary>

Cost Anomaly Detection. 과거 지출 패턴을 학습해 비정상적 증가를 탐지한다.

</details>

### Q3. 회사에서 월 예산 100달러를 초과할 것으로 예상되면 알림을 원한다. 무엇을 사용할까?

<details>
<summary>정답 및 해설 보기</summary>

AWS Budgets. 사용자가 설정한 예산과 실제·예상 지출을 비교해 알림을 보낸다.

</details>

### Q4. Cost Anomaly Detection에서는 어떤 알림 임계값도 설정할 수 없는가?

<details>
<summary>정답 및 해설 보기</summary>

아니다. 탐지에 필요한 비용 패턴은 ML이 학습하지만, 알림 구독에는 절대 비용 영향 또는 퍼센트 기준의 임계값을 설정할 수 있다.

</details>

### Q5. Cost Explorer가 18개월 후의 비용을 표시한다면 확정 청구액인가?

<details>
<summary>정답 및 해설 보기</summary>

아니다. 과거 사용 패턴을 기반으로 계산한 예측치다. 사용량과 할인 조건 등이 변하면 실제 청구액도 달라진다.

</details>

## 🇯🇵 日本語 Summary

**Cost Explorer** は AWS のコストと使用量を可視化・分析・予測するツールである。**Cost Anomaly Detection** は機械学習で通常と異なる支出を検出し、原因分析と通知を支援する。**AWS Budgets** は設定した予算のしきい値に基づいて通知する。用途は「分析」「異常検知」「予算管理」に分けて考える。

## 🇺🇸 English Summary

Cost Explorer visualizes, analyzes, and forecasts AWS costs. Cost Anomaly Detection applies machine learning to identify unusual spending patterns and provide alerts with potential root causes. AWS Budgets tracks actual or forecast spending against predefined budgets. These services complement each other.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Cost Explorer | 비용 탐색기·분석 도구 | コストエクスプローラー |
| Cost Forecast | 비용 예측 | コスト予測 |
| Granularity | 데이터 세부 수준 | 粒度 |
| Savings Plans | 사용량 약정 할인 | Savings Plans |
| Cost Anomaly | 비용 이상 징후 | コスト異常 |
| Cost Monitor | 비용 모니터 | コストモニター |
| Alert Subscription | 알림 구독 | アラートサブスクリプション |
| Cost Allocation Tag | 비용 할당 태그 | コスト配分タグ |

## 📖 References

- [AWS Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html)
- [Cost Explorer forecast](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-forecast.html)
- [AWS Cost Anomaly Detection](https://docs.aws.amazon.com/cost-management/latest/userguide/manage-ad.html)
- [AWS Budgets](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html)
