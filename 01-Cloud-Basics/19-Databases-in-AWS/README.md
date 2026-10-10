# 🗄️ 19. Databases in AWS

> AWS Certified Solutions Architect – Associate (SAA-C03)  
> Udemy Section 21 | GitHub Section 19

## 📌 Overview

AWS는 데이터 모델, 접근 패턴, 확장성, 가용성, 비용 요구사항에 따라 다양한 데이터베이스 서비스를 제공한다.

이번 섹션에서는 기존에 학습한 데이터베이스를 복습하고, 새로운 데이터베이스 서비스를 이해한 뒤, 실제 아키텍처에서 **어떤 데이터베이스를 선택해야 하는지** 정리한다.

## 🎯 Learning Objectives

1. 관계형 DB, NoSQL, Document, Graph, Time-Series DB의 차이를 이해한다.
2. RDS, Aurora, DynamoDB, ElastiCache의 주요 기능을 복습한다.
3. DocumentDB, Neptune, Keyspaces, Timestream의 사용 사례를 이해한다.
4. 요구사항에 따라 적절한 데이터베이스를 선택한다.
5. Kinesis, Lambda, 데이터베이스의 역할을 구분한다.

## 📂 Contents

| File | Description |
|---|---|
| [19-01-Database-Review.md](./19-01-Database-Review.md) | 기존 데이터베이스 서비스 복습 |
| [19-02-New-Database-Services.md](./19-02-New-Database-Services.md) | 새로운 데이터베이스 서비스 |
| [19-03-Database-Selection-Guide.md](./19-03-Database-Selection-Guide.md) | 데이터베이스 선택 기준과 아키텍처 |

## 🧠 Key Concept

**Choose the right database for the right workload.**

데이터베이스를 선택할 때는 다음을 고려한다.

- Data Model
- Query / Access Pattern
- Read / Write Requirements
- Scalability
- High Availability
- Consistency
- Operational Complexity
- Cost

서비스를 많이 사용하는 것이 좋은 아키텍처는 아니다.

**요구사항을 만족하는 가장 단순하고 적절한 구조를 선택하는 것이 중요하다.**

## ⚠️ Service Status

- **Amazon QLDB:** 2025년 7월 31일 지원 종료. 역사적 개념으로 학습한다.
- **Amazon Timestream:** 제품별 기능과 이용 가능 여부를 실제 설계 전에 확인한다.

---

## 🇯🇵 日本語 Summary

このセクションでは、AWS のデータベースサービスを比較し、要件に応じた選択方法を学びます。

主な判断基準は、データモデル、アクセスパターン、可用性、拡張性、整合性、コストです。

不要なサービスを追加せず、要件を満たす適切なアーキテクチャを設計することが重要です。

---

## 🇺🇸 English Summary

This section covers AWS database services and workload-based database selection.

The main selection criteria include data models, access patterns, scalability, availability, consistency, and cost.

The goal is to choose the simplest architecture that satisfies application requirements.
