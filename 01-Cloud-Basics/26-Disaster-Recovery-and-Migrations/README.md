# 📚 26. Disaster Recovery & Migrations

> [!NOTE]
> **AWS SAA-C03 Section Guide** · `GitHub Section 26` · `Udemy Section 28: Disaster Recovery & Migrations`

## 📌 Overview

재해 복구(RPO/RTO, 네 가지 DR 전략), AWS DMS와 Aurora 마이그레이션, 온프레미스 VM/서버 이전, AWS Backup, MGN(Lift-and-Shift), 대용량 데이터 전송, VMware Cloud on AWS를 **왜 선택하는지와 실제 이동·복구 흐름** 중심으로 정리했다.

강의 영상 대신 대본으로 학습하는 상황을 고려해, 콘솔 실습도 메뉴·옵션·결과의 의미까지 서술형으로 재구성했다.

## 🎯 Learning Objectives

1. RPO와 RTO를 구분하고 Backup & Restore, Pilot Light, Warm Standby, Multi-Site 중 적절한 DR 전략을 선택한다.
2. DMS, CDC, SCT/Schema Conversion 및 RDS/Aurora 이전 경로를 구분한다.
3. AWS Backup의 Plan, Rule, Vault, Recovery Point, Vault Lock 및 태그 기반 콘솔 실습 흐름을 이해한다.
4. 서버 전체 이전(MGN), 데이터베이스 이전(DMS), VM Import/Export, VMware Cloud on AWS의 역할과 결합 방식을 선택한다.
5. 대용량 전송 시 네트워크 속도, Direct Connect 개통 기간, 물리적 전송 및 지속 복제 요건을 비교한다.

## 📂 Contents

| File | Topic |
|---|---|
| [26-01](./26-01-Disaster-Recovery-RPO-RTO-and-Four-Strategies.md) | Disaster Recovery: RPO, RTO & Four Strategies |
| [26-02](./26-02-DR-AWS-Services-Automation-and-Chaos-Engineering.md) | DR Services, Automation & Chaos Engineering |
| [26-03](./26-03-AWS-DMS-SCT-CDC-and-Replication.md) | AWS DMS: Migration, SCT, CDC & Multi-AZ |
| [26-04](./26-04-AWS-DMS-Console-Lab-and-Serverless.md) | AWS DMS Console Lab: Endpoints, Tasks & Serverless |
| [26-05](./26-05-Migrating-RDS-and-External-DBs-to-Aurora.md) | Migrate MySQL & PostgreSQL to Amazon Aurora |
| [26-06](./26-06-On-Premises-VM-Import-Export-and-Migration-Services.md) | On-Premises VM Migration & Legacy Services |
| [26-07](./26-07-AWS-Backup-Plans-Rules-Vaults-and-Vault-Lock.md) | AWS Backup: Plans, Rules, Vaults & Vault Lock |
| [26-08](./26-08-AWS-Backup-Console-Lab-Tags-and-Cleanup.md) | AWS Backup Console Lab: Tags, Jobs & Cleanup |
| [26-09](./26-09-Discovery-Agentless-Agent-and-Transform-MGN.md) | Application Discovery & AWS Transform MGN: Rehosting |
| [26-10](./26-10-Large-Data-Transfers-VPN-DX-Snowball-DataSync.md) | Large Data Transfer: Internet, DX, Snowball & DataSync |
| [26-11](./26-11-VMware-Cloud-on-AWS-and-Amazon-EVS.md) | VMware Cloud on AWS & Amazon Elastic VMware Service |
| [26-12](./26-12-Disaster-Recovery-and-Migrations-SAA-Exam-Scenarios.md) | DR & Migration: Final Comparison and SAA Scenarios |

## 🧠 Key Concept

**DR는 복구 목표(RPO/RTO)로 대기 전략을 고르고, Migration은 이전 단위(서버 전체·DB 데이터·VMware 환경·대량 파일)로 도구를 고른다.**

두 영역은 AWS Backup, 지속 복제(CDC), 네트워크 연결, 자동화와 함께 설계한다.

## 🛠️ Study Notes

- **학습 방식:** 대본의 이론과 콘솔 메뉴/설정 목적을 텍스트로 재구성했다.
- **강의 기준과 현재 AWS 구분:** 종료·이름 변경된 서비스는 관련 노트에 시점과 공식 출처를 표시했다.
- **시험 함정:** RPO ≠ RTO, Pilot Light ≠ Warm Standby, MGN ≠ DMS, DMS Endpoint ≠ VPC Endpoint, DataSync ≠ DMS.
- **복습 구성:** 각 노트에 Exam Notes, 접기형 복습 문제 5개, 일본어·영어 요약과 Vocabulary를 포함한다.
- **범위:** 이론·실습을 포함한 강의 내용을 12개 노트로 재편성했으며, 마지막 노트는 종합 비교와 SAA 상황별 선택 문제다.

## 🇯🇵 日本語 Summary

DR では RPO・RTO と 4 つの復旧戦略を比較する。移行では DMS/CDC/SCT によるデータベース移行、MGN によるサーバーのリホスト、AWS Backup による保護、DataSync・専用線・オフライン転送、VMware 環境の拡張を学び、要件ごとに適切なサービスを組み合わせる。

## 🇺🇸 English Summary

This section explains disaster recovery objectives and four recovery strategies, AWS Backup governance, DMS and Aurora migration options, server rehosting via MGN, migration discovery, large-scale data transfer, and VMware migration approaches, with hands-on console workflows and SAA-C03 decision criteria.
