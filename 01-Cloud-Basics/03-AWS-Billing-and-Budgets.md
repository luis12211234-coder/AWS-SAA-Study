# AWS Billing and Budgets

## Overview

AWS Billing and Cost Management는 AWS 계정에서 발생하는 비용과 사용량을 확인하고 관리하기 위한 기능이다.

AWS를 학습하거나 실제 서비스를 운영할 때는 예상하지 못한 비용 발생을 방지하기 위해 Billing 정보와 AWS Budgets를 확인하는 것이 중요하다.

---

## Billing Access for IAM Users

기본적으로 IAM User는 관리자 권한을 가지고 있더라도 Billing 정보에 접근할 수 없을 수 있다.

이 경우 Root User로 로그인한 뒤 계정 설정에서 IAM 사용자의 Billing 정보 접근을 활성화해야 한다.

### 설정 흐름

```text
Root User 로그인
↓
Account
↓
IAM user and role access to Billing information
↓
Activate IAM Access
```

활성화한 이후에는 적절한 권한을 가진 IAM User도 Billing 정보를 확인할 수 있다.

---

## Bills

Bills에서는 실제 AWS 사용 비용을 확인할 수 있다.

월별 청구서를 선택하면 서비스별로 발생한 비용을 확인할 수 있다.

예시

- Amazon EC2
- EBS
- NAT Gateway
- Elastic IP

비용이 예상보다 많이 발생했다면 먼저 Bills에서 어떤 서비스가 비용을 발생시키고 있는지 확인하는 것이 유용하다.

---

## Free Tier

Free Tier 페이지에서는 AWS 무료 사용 범위와 현재 사용량을 비교할 수 있다.

확인할 수 있는 내용

- 현재 사용량
- 예상 사용량
- Free Tier 한도
- 한도 초과 여부

Free Tier 한도를 초과하면 비용이 발생할 수 있으므로 사용하지 않는 리소스는 종료하거나 삭제해야 한다.

---

## AWS Budgets

AWS Budgets를 사용하면 비용이 특정 임계값에 도달했을 때 알림을 받을 수 있다.

알림은 이메일 등의 방식으로 받을 수 있다.

---

## Zero-Spend Budget

학습용 AWS 계정에서는 작은 비용이라도 발생하면 바로 알림을 받도록 예산을 설정할 수 있다.

예시

```text
Budget Threshold: $0.01
```

$0.01 이상의 비용이 발생하면 알림을 받을 수 있다.

---

## Monthly Cost Budget

월별 비용 한도를 설정할 수도 있다.

예시

```text
Monthly Budget: $10
```

강의 예시에서는 다음과 같은 시점에 알림을 받을 수 있다.

- 실제 지출이 85%에 도달
- 실제 지출이 100%에 도달
- 예상 지출이 100%에 도달할 것으로 예측

---

## Cost Monitoring Flow

```text
AWS 사용
↓
Free Tier 사용량 확인
↓
Bills에서 서비스별 비용 확인
↓
Budgets로 임계값 설정
↓
비용 증가 시 이메일 알림
```

---

## Summary

- AWS Billing에서는 계정의 비용과 사용량을 확인할 수 있다.
- IAM User는 Billing 정보 접근이 기본적으로 제한될 수 있다.
- Root User에서 IAM Billing Access를 활성화할 수 있다.
- Bills에서는 서비스별 비용을 확인할 수 있다.
- Free Tier 페이지에서는 무료 사용 한도와 현재 사용량을 비교할 수 있다.
- AWS Budgets를 이용해 비용 알림을 설정할 수 있다.

---

## Exam Notes

- Billing 정보 접근은 IAM 권한과 별도로 계정 수준에서 활성화가 필요할 수 있다.
- AWS Budgets는 비용 임계값에 대한 알림을 설정할 수 있다.
- Free Tier는 무료 사용량을 추적하는 데 사용할 수 있다.
- Bills는 서비스별 실제 비용 분석에 사용할 수 있다.

---

## Practical Example

AWS 학습용 계정을 사용한다고 가정한다.

```text
Zero-Spend Budget
$0.01
↓
비용이 발생하면 즉시 알림
```

추가로

```text
Monthly Budget
$10
↓
85% 도달 알림
100% 도달 알림
100% 예상 알림
```

을 설정해 두면 예상하지 못한 비용을 빠르게 발견할 수 있다.

---

# 🇯🇵 日本語

## AWS Billing and Budgets

AWS Billing and Cost Managementでは、AWSアカウントの利用料金と使用量を確認できる。

IAM Userから請求情報へアクセスする場合、Root UserでIAMのBilling Accessを有効化する必要がある場合がある。

AWS Budgetsを利用すると、設定した予算のしきい値に達した際に通知を受け取ることができる。

---

## Summary

- Billsでサービス別の料金を確認できる。
- Free Tierで無料利用枠を確認できる。
- AWS Budgetsで料金アラートを設定できる。
- 予期しない料金を防ぐために予算設定が有効である。

---

# 🇺🇸 English

## AWS Billing and Budgets

AWS Billing and Cost Management allows you to monitor AWS costs and usage.

IAM users may need account-level Billing access to view billing information.

AWS Budgets can send notifications when actual or forecasted costs reach configured thresholds.

---

## Summary

- Bills show service-level costs.
- Free Tier shows free usage limits.
- AWS Budgets can send cost alerts.
- Budgets help prevent unexpected AWS charges.

---

## Vocabulary

	
---

## Review Questions

1. IAM User가 Billing 정보에 접근하지 못하는 이유는 무엇일 수 있는가?
2. Billing IAM Access는 어디에서 활성화할 수 있는가?
3. Bills에서는 어떤 정보를 확인할 수 있는가?
4. Free Tier 페이지의 목적은 무엇인가?
5. AWS Budgets는 어떤 기능을 제공하는가?
6. Zero-Spend Budget을 설정하는 이유는 무엇인가?