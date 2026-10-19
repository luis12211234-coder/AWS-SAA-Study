# 📘 28-01. CloudFormation & Service Roles

> [!NOTE]
> **AWS SAA-C03 Study Note** · GitHub Section 28 · Udemy Section 30  
> **Keywords:** Template, Stack, Change Set, Service Role

## 🇰🇷 1. 핵심 개념

CloudFormation은 YAML/JSON Template으로 AWS 인프라를 선언하고 Stack으로 배포한다.

## 🇰🇷 2. 동작 원리 및 사용 사례

Template은 설계도, Stack은 배포된 리소스 묶음이다. Change Set은 변경 사항을 미리 확인하고 Drift Detection은 템플릿과 실제 상태의 차이를 찾는다. CloudFormation Service Role은 Stack 작업을 수행할 때 CloudFormation이 맡는 IAM Role이다. 사용자는 해당 Role을 전달할 iam:PassRole 권한이 필요하다.

## 🇰🇷 3. 서비스 선택 기준

iam:PassRole은 IAM Role 생성 권한이 아니다. Replacement가 표시되면 기존 리소스 교체와 데이터 손실 가능성을 검토한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **핵심 역할:** CloudFormation은 YAML/JSON Template으로 AWS 인프라를 선언하고 Stack으로 배포한다.
- **시험 선택 기준:** iam:PassRole은 IAM Role 생성 권한이 아니다. Replacement가 표시되면 기존 리소스 교체와 데이터 손실 가능성을 검토한다.
- **구현 시 주의점:** Template은 설계도, Stack은 배포된 리소스 묶음이다. Change Set은 변경 사항을 미리 확인하고 Drift Detection은 템플릿과 실제 상태의 차이를 찾는다. CloudFormation Service Role은 Stack 작업을 수행할 때 CloudFormation이 맡는 IAM Role이다. 사용자는 해당 Role을 전달할 iam:PassRole 권한이 필요하다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 이 서비스의 핵심 목적은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

CloudFormation은 YAML/JSON Template으로 AWS 인프라를 선언하고 Stack으로 배포한다.

</details>

### Q2. 대표적인 사용 사례를 하나 설명하라.

<details>
<summary>정답 및 해설 보기</summary>

Template은 설계도, Stack은 배포된 리소스 묶음이다. Change Set은 변경 사항을 미리 확인하고 Drift Detection은 템플릿과 실제 상태의 차이를 찾는다. CloudFormation Service Role은 Stack 작업을 수행할 때 CloudFormation이 맡는 IAM Role이다. 사용자는 해당 Role을 전달할 iam:PassRole 권한이 필요하다.

</details>

### Q3. 어떤 요구사항에서 이 서비스를 선택하는가?

<details>
<summary>정답 및 해설 보기</summary>

iam:PassRole은 IAM Role 생성 권한이 아니다. Replacement가 표시되면 기존 리소스 교체와 데이터 손실 가능성을 검토한다.

</details>

### Q4. 구현 시 알아야 할 조건 또는 제한은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Template은 설계도, Stack은 배포된 리소스 묶음이다. Change Set은 변경 사항을 미리 확인하고 Drift Detection은 템플릿과 실제 상태의 차이를 찾는다. CloudFormation Service Role은 Stack 작업을 수행할 때 CloudFormation이 맡는 IAM Role이다. 사용자는 해당 Role을 전달할 iam:PassRole 권한이 필요하다.

</details>

### Q5. 시험에서 혼동하기 쉬운 기능과 어떻게 구분하는가?

<details>
<summary>정답 및 해설 보기</summary>

iam:PassRole은 IAM Role 생성 권한이 아니다. Replacement가 표시되면 기존 리소스 교체와 데이터 손실 가능성을 검토한다.

</details>

## 🇯🇵 日本語 Summary

CloudFormation はテンプレートとスタックでインフラを宣言的に管理する。Service Role はスタック操作時に使用する IAM ロール。

## 🇺🇸 English Summary

CloudFormation provisions infrastructure from templates. Change Sets preview changes, drift detection checks deviations, and service roles grant deployment permissions.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Template | 템플릿 | テンプレート |
| Stack | 스택 | スタック |
| Change Set | 변경 집합 | 変更セット |
| Service Role | 서비스 역할 | サービスロール |
