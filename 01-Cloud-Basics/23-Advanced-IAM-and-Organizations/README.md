
# 📚 23. Advanced IAM & Organizations

> [!NOTE]
> **AWS SAA-C03 Section Guide** · `GitHub Section 23`

## 📌 Overview

AWS Organizations, SCP, IAM Conditions, STS AssumeRole, Resource-Based Policy, Permissions Boundary, IAM Identity Center, AWS Directory Service, AWS Control Tower를 통해 **다중 계정 거버넌스, 인증, 권한 부여 및 권한 제한**을 학습한다.

## 🎯 Learning Objectives

1. Organizations와 Control Tower를 활용한 다중 계정 구조 및 거버넌스 목적을 설명한다.
2. SCP, Permissions Boundary, Identity-Based/Resource-Based Policy의 권한 평가와 적용 범위를 구분한다.
3. IAM Identity Center, Permission Set, AWS Directory Service를 통한 직원 인증과 접근 권한을 설명한다.
4. SAA-C03 문제에서 Cross-Account Access 및 Preventive/Detective/Proactive Control의 선택 기준을 적용한다.

## 📂 Contents

| File | Topic |
|---|---|
| [23-01-AWS-Organizations-and-Account-Structure.md](./23-01-AWS-Organizations-and-Account-Structure.md) | AWS Organizations, Management/Member Accounts, OU, Consolidated Billing |
| [23-02-SCP-Inheritance-and-Console-Lab.md](./23-02-SCP-Inheritance-and-Console-Lab.md) | SCP Inheritance, FullAWSAccess, Explicit Deny, Organizations Console Lab |
| [23-03-IAM-Conditions-and-S3-Resource-ARNs.md](./23-03-IAM-Conditions-and-S3-Resource-ARNs.md) | IAM Conditions, S3 Bucket/Object ARNs, PrincipalOrgID |
| [23-04-AssumeRole-and-Resource-Based-Policies.md](./23-04-AssumeRole-and-Resource-Based-Policies.md) | STS AssumeRole, Cross-Account S3, EventBridge Permissions |
| [23-05-Permissions-Boundaries-and-IAM-Evaluation.md](./23-05-Permissions-Boundaries-and-IAM-Evaluation.md) | Permissions Boundaries, SCP Comparison & IAM Policy Evaluation |
| [23-06-IAM-Identity-Center-and-Permission-Sets.md](./23-06-IAM-Identity-Center-and-Permission-Sets.md) | IAM Identity Center, SSO, Permission Sets, ABAC |
| [23-07-Active-Directory-and-Directory-Service.md](./23-07-Active-Directory-and-Directory-Service.md) | Microsoft AD, AWS Managed Microsoft AD, AD Connector, Simple AD |
| [23-08-AWS-Control-Tower-and-Governance-Controls.md](./23-08-AWS-Control-Tower-and-Governance-Controls.md) | Control Tower, Landing Zone, Account Factory, Guardrails |
| [23-09-Final-Comparison-and-Exam-Scenarios.md](./23-09-Final-Comparison-and-Exam-Scenarios.md) | Final Comparison & Scenario-Based Service Selection |

## 🧠 Key Concept

**계정 관리(Organizations) → 권한 제한(SCP/Boundary) → 신원 인증(Identity Center/AD) → 실제 권한(IAM Role/Resource Policy) → 조직 거버넌스(Control Tower)**를 하나의 흐름으로 이해한다.

## 🇯🇵 日本語 Summary

AWS Organizations は複数アカウントの管理基盤、SCP と Permissions Boundary は権限の上限、IAM Identity Center は SSO と Permission Set によるアクセス管理を担当する。AWS Directory Service は Microsoft AD との統合を提供し、Control Tower は Landing Zone と継続的なガバナンスを実現する。

## 🇺🇸 English Summary

This section covers advanced AWS identity and multi-account governance: AWS Organizations and SCPs, IAM conditions and policy evaluation, STS AssumeRole and resource-based policies, permissions boundaries, IAM Identity Center and permission sets, Active Directory integration, and AWS Control Tower landing zones and controls.
