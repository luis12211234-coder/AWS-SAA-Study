# 📘 17-06. AWS Step Functions

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Workflow` · `Orchestration` · `Sequence` · `Parallel` · `Condition`

## 🇰🇷 1. Step Functions

AWS Step Functions는 여러 작업으로 구성된 **Workflow를 관리하고 Orchestration하는 서비스**이다.

```text
Task A
  ↓
Task B
  ↓
Task C
```

각 작업 자체를 수행하는 것보다 **어떤 작업을 언제 실행할지 관리하는 것**이 핵심이다.

## 🇰🇷 2. Workflow Control

Step Functions에서 구성할 수 있는 대표적인 흐름:

#### Sequence

```text
A → B → C
```

#### Parallel

```text
    ┌→ B
A ──┤
    └→ C
```

#### Condition

```text
      Success?
      /      \
    YES      NO
     ↓        ↓
     B        C
```

그 외:

- Timeout
- Retry
- Error Handling
- Human Approval

## 🇰🇷 3. AWS Service Integration

Lambda뿐 아니라 다양한 AWS 서비스와 Workflow를 구성할 수 있다.

예:

- Lambda
- ECS
- EC2
- API Gateway
- SQS
- 기타 AWS 서비스

## 🇰🇷 4. Human Approval

Workflow 중간에 사람의 승인을 기다리는 구조도 만들 수 있다.

```text
Request
   ↓
Human Approval
   ↓
Approved?
 ┌──────┴──────┐
YES            NO
 ↓              ↓
Continue       Stop
```

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

```text
Step Functions
= Workflow
= Orchestration
```

문제에서 다음 표현이 나오면 Step Functions를 떠올린다.

- 여러 Lambda의 실행 순서 관리
- Workflow
- Sequence
- Parallel
- Conditional Branch
- Retry / Error Handling
- Human Approval

주문 처리 Workflow:

```text
Order
  ↓
Payment
  ↓
Payment Success?
 ├── No → Fail
 └── Yes
       ↓
Inventory
       ↓
Shipping
       ↓
Complete
```

각 단계의 실제 작업은 Lambda 등의 서비스가 수행하고 Step Functions는 전체 흐름을 관리한다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 정답 보기

<details>
<summary>정답 및 해설 보기</summary>

AWS Step Functions

</details>

### Q2. 정답 보기

<details>
<summary>정답 및 해설 보기</summary>

아니다. 각 작업은 Lambda 등의 서비스가 수행할 수 있으며 Step Functions의 핵심 역할은 전체 Workflow를 관리하고 Orchestration하는 것이다.

</details>

### Q3. Step Functions의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

AWS Step Functions는 여러 작업으로 구성된 **Workflow를 관리하고 Orchestration하는 서비스**이다.

</details>

### Q4. Workflow Control의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Step Functions에서 구성할 수 있는 대표적인 흐름:

</details>

### Q5. AWS Service Integration의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Lambda뿐 아니라 다양한 AWS 서비스와 Workflow를 구성할 수 있다.

</details>

## 🇯🇵 日本語 Summary

AWS Step Functionsは、複数の処理をワークフローとしてオーケストレーションするサービスです。順次実行、並列実行、条件分岐、エラー処理、人による承認などを構成できます。

## 🇺🇸 English Summary

AWS Step Functions orchestrates multiple tasks as a workflow. It supports sequencing, parallel execution, conditions, retries, error handling, and human approval steps.

## 📚 Vocabulary

| Term | Meaning |
|---|---|
| Workflow | 여러 작업으로 구성된 처리 흐름 |
| Orchestration | 여러 작업의 실행 흐름을 통합 관리하는 것 |
| Sequence | 순차 실행 |
| Parallel | 병렬 실행 |
| Condition | 조건에 따른 분기 |
| Human Approval | 사람의 승인을 기다리는 단계 |
