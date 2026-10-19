# 📘 28-04. Systems Manager Operations

> [!NOTE]
> **AWS SAA-C03 Study Note** · GitHub Section 28 · Udemy Section 30  
> **Keywords:** Runbook, Patch Manager

## 🇰🇷 1. 핵심 개념

Run Command, Patch Manager, Maintenance Windows, Automation은 Systems Manager의 운영 관리 기능이다.

## 🇰🇷 2. 동작 원리 및 사용 사례

Run Command는 여러 관리형 노드에 명령을 원격 실행한다. Patch Manager는 OS 패치를 검사·설치하고 AWS-RunPatchBaseline 문서를 활용할 수 있다. Maintenance Windows는 작업의 실행 시간·기간·대상·작업을 정의한다. Automation은 Runbook으로 다단계 작업을 자동화하고 AWS Config remediation과 연계할 수 있다.

## 🇰🇷 3. 서비스 선택 기준

명령 일괄 실행 → Run Command; 패치 → Patch Manager; 예약 시간 → Maintenance Windows; 다단계 절차 → Automation.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **핵심 역할:** Run Command, Patch Manager, Maintenance Windows, Automation은 Systems Manager의 운영 관리 기능이다.
- **시험 선택 기준:** 명령 일괄 실행 → Run Command; 패치 → Patch Manager; 예약 시간 → Maintenance Windows; 다단계 절차 → Automation.
- **구현 시 주의점:** Run Command는 여러 관리형 노드에 명령을 원격 실행한다. Patch Manager는 OS 패치를 검사·설치하고 AWS-RunPatchBaseline 문서를 활용할 수 있다. Maintenance Windows는 작업의 실행 시간·기간·대상·작업을 정의한다. Automation은 Runbook으로 다단계 작업을 자동화하고 AWS Config remediation과 연계할 수 있다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 이 서비스의 핵심 목적은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Run Command, Patch Manager, Maintenance Windows, Automation은 Systems Manager의 운영 관리 기능이다.

</details>

### Q2. 대표적인 사용 사례를 하나 설명하라.

<details>
<summary>정답 및 해설 보기</summary>

Run Command는 여러 관리형 노드에 명령을 원격 실행한다. Patch Manager는 OS 패치를 검사·설치하고 AWS-RunPatchBaseline 문서를 활용할 수 있다. Maintenance Windows는 작업의 실행 시간·기간·대상·작업을 정의한다. Automation은 Runbook으로 다단계 작업을 자동화하고 AWS Config remediation과 연계할 수 있다.

</details>

### Q3. 어떤 요구사항에서 이 서비스를 선택하는가?

<details>
<summary>정답 및 해설 보기</summary>

명령 일괄 실행 → Run Command; 패치 → Patch Manager; 예약 시간 → Maintenance Windows; 다단계 절차 → Automation.

</details>

### Q4. 구현 시 알아야 할 조건 또는 제한은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Run Command는 여러 관리형 노드에 명령을 원격 실행한다. Patch Manager는 OS 패치를 검사·설치하고 AWS-RunPatchBaseline 문서를 활용할 수 있다. Maintenance Windows는 작업의 실행 시간·기간·대상·작업을 정의한다. Automation은 Runbook으로 다단계 작업을 자동화하고 AWS Config remediation과 연계할 수 있다.

</details>

### Q5. 시험에서 혼동하기 쉬운 기능과 어떻게 구분하는가?

<details>
<summary>정답 및 해설 보기</summary>

명령 일괄 실행 → Run Command; 패치 → Patch Manager; 예약 시간 → Maintenance Windows; 다단계 절차 → Automation.

</details>

## 🇯🇵 日本語 Summary

Run Command は命令実行、Patch Manager はパッチ、Maintenance Windows は時間指定、Automation は Runbook 実行。

## 🇺🇸 English Summary

Run Command executes commands, Patch Manager patches nodes, Maintenance Windows schedules tasks, and Automation runs workflows.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Runbook | 절차서 | ランブック |
| Patch Manager | 패치 관리 | パッチ管理 |
