# 📚 24. Security & Encryption

> [!NOTE]
> **AWS SAA-C03 Section Guide** · `GitHub Section 24` · `Udemy Section 25: Security & Encryption`

## 📌 Overview

KMS를 중심으로 암호화와 키·권한을 이해하고, SSM Parameter Store/Secrets Manager의 설정·자격 증명 관리, ACM의 TLS 인증서, WAF·Shield·Firewall Manager의 웹·DDoS 방어, GuardDuty·Inspector·Macie의 보안 탐지를 실제 아키텍처와 연결한다. 최신 AWS 기능 변경(키 교체·인증서·WCU·Managed Rotation)은 구버전 강의 내용과 구분해 기록했다.

## 🎯 Learning Objectives

1. 전송/저장/클라이언트 측 암호화와 KMS Envelope Encryption·Data Key를 설명한다.
2. Key Policy/IAM, 계정 간 암호화된 AMI·EBS 공유, MRK와 S3 SSE-KMS 복제를 구분한다.
3. Parameter Store 및 Secrets Manager의 저장·복호화·자동 교체를 선택한다.
4. ACM·WAF·Shield·Firewall Manager로 HTTPS 및 DDoS 방어 아키텍처를 설명한다.
5. GuardDuty·Inspector·Macie를 요구사항에 맞게 선택하고 보안 Findings를 연계한다.

## 📂 Contents

| File | Topic |
|---|---|
| [24-01-Encryption-Fundamentals-and-Envelope-Encryption.md](./24-01-Encryption-Fundamentals-and-Envelope-Encryption.md) | Encryption Fundamentals & Envelope Encryption |
| [24-02-AWS-KMS-Keys-Policies-Rotation-and-CLI.md](./24-02-AWS-KMS-Keys-Policies-Rotation-and-CLI.md) | AWS KMS: Keys, Policies, Rotation & CLI |
| [24-03-Cross-Account-EBS-Snapshots-and-Encrypted-AMIs.md](./24-03-Cross-Account-EBS-Snapshots-and-Encrypted-AMIs.md) | Cross-Account EBS Snapshots & Encrypted AMIs |
| [24-04-KMS-Multi-Region-Keys-and-Client-Side-Encryption.md](./24-04-KMS-Multi-Region-Keys-and-Client-Side-Encryption.md) | KMS Multi-Region Keys & Client-Side DB Encryption |
| [24-05-S3-Replication-with-SSE-KMS.md](./24-05-S3-Replication-with-SSE-KMS.md) | S3 Replication, SSE-KMS & MRK Caveat |
| [24-06-SSM-Parameter-Store-Core-Concepts.md](./24-06-SSM-Parameter-Store-Core-Concepts.md) | SSM Parameter Store: Storage, Hierarchy & Tiers |
| [24-07-SSM-Parameter-Store-CLI-and-Lambda-Labs.md](./24-07-SSM-Parameter-Store-CLI-and-Lambda-Labs.md) | Parameter Store Hands-On: CLI & Lambda SDK |
| [24-08-AWS-Secrets-Manager-and-Automatic-Rotation.md](./24-08-AWS-Secrets-Manager-and-Automatic-Rotation.md) | AWS Secrets Manager & Automatic Rotation |
| [24-09-Secrets-Manager-Console-and-Multi-Region-Secrets.md](./24-09-Secrets-Manager-Console-and-Multi-Region-Secrets.md) | Secrets Manager Console & Multi-Region Secrets |
| [24-10-ACM-TLS-Certificates-and-Service-Integration.md](./24-10-ACM-TLS-Certificates-and-Service-Integration.md) | AWS Certificate Manager: TLS, Validation & Integrations |
| [24-11-AWS-WAF-Web-ACL-and-Console-Lab.md](./24-11-AWS-WAF-Web-ACL-and-Console-Lab.md) | AWS WAF: Web ACL, Rules & Console Lab |
| [24-12-AWS-Shield-Standard-and-Advanced.md](./24-12-AWS-Shield-Standard-and-Advanced.md) | AWS Shield: DDoS Protection Standard & Advanced |
| [24-13-Firewall-Manager-and-Central-Security-Policies.md](./24-13-Firewall-Manager-and-Central-Security-Policies.md) | AWS Firewall Manager & Central Security Policies |
| [24-14-DDoS-Resilient-AWS-Architectures.md](./24-14-DDoS-Resilient-AWS-Architectures.md) | DDoS-Resilient Architecture & Best Practices |
| [24-15-Amazon-GuardDuty-Threat-Detection.md](./24-15-Amazon-GuardDuty-Threat-Detection.md) | Amazon GuardDuty: Intelligent Threat Detection |
| [24-16-Amazon-Inspector-Vulnerability-Scanning.md](./24-16-Amazon-Inspector-Vulnerability-Scanning.md) | Amazon Inspector: Vulnerability Assessment |
| [24-17-Amazon-Macie-Sensitive-Data-Discovery.md](./24-17-Amazon-Macie-Sensitive-Data-Discovery.md) | Amazon Macie: S3 Sensitive Data Discovery |
| [24-18-Security-Service-Comparison-and-Exam-Scenarios.md](./24-18-Security-Service-Comparison-and-Exam-Scenarios.md) | Security Service Comparison & SAA-C03 Scenarios |

## 🧠 Key Concept

**데이터 보호(Encryption/KMS) → 설정·비밀번호 보관(Parameter Store/Secrets Manager) → 안전한 연결(ACM) → 웹·DDoS 방어(WAF/Shield/Firewall Manager) → 위협·취약점·민감정보 탐지(GuardDuty/Inspector/Macie)** 순서로 이해한다.

## 🛠️ Study Notes

- **권한 분리:** SSM/Secrets Manager 리소스 조회와 KMS 복호화는 별개의 접근 권한이다.
- **강의 정정:** 지원되는 KMS CMK 90일 자동 교체 가능, ACM Exportable Public Certificates 지원, WAF 1,500 WCU는 기본 요금 포함 기준.
- **복습 운영:** 각 노트에 정답과 해설을 접어둔 시나리오 문제 5개가 있다.

## 🇯🇵 日本語 Summary

このセクションでは暗号化と KMS、Parameter Store と Secrets Manager、ACM による TLS、WAF/Shield/Firewall Manager による多層防御、GuardDuty/Inspector/Macie による脅威・脆弱性・機密データの検出を学ぶ。AWS サービスの役割と権限の境界を重視する。

## 🇺🇸 English Summary

This section explains encryption and AWS KMS, secrets and configuration management, TLS certificates with ACM, layered web and DDoS protection through WAF/Shield/Firewall Manager, and detection through GuardDuty, Inspector, and Macie. The emphasis is on service selection, key permissions, regional behavior, and current AWS capabilities.
