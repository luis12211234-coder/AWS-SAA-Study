# AWS Global Infrastructure

## Overview

AWS(Amazon Web Services)는 Amazon에서 제공하는 세계 최대의 클라우드 컴퓨팅 플랫폼이다.

2002년 Amazon 내부 IT 인프라에서 시작하여, 현재는 전 세계 수백만 명의 고객이 사용하는 클라우드 서비스로 성장했다.

---

# AWS History

| Year | Event |
|------|-------|
| 2002 | Amazon 내부 IT 인프라 프로젝트 시작 |
| 2004 | Amazon SQS 공개 |
| 2006 | Amazon S3, EC2 출시 |
| Today | 세계 최대 클라우드 플랫폼 |

---

# What can AWS do?

AWS는 다양한 서비스를 제공한다.

- Website Hosting
- Mobile Backend
- Game Server
- Backup & Storage
- Big Data Analytics
- AI / Machine Learning
- Enterprise Infrastructure

대표 고객

- Netflix
- Dropbox
- Airbnb
- NASA

---

# Global Infrastructure

AWS는 전 세계에 인프라를 구축하여 서비스를 제공한다.

구성 요소

- Region
- Availability Zone (AZ)
- Data Center
- Edge Location (Point of Presence)

---

# Summary

- AWS는 세계 최대 클라우드 플랫폼이다.
- 2002년 Amazon 내부 프로젝트에서 시작했다.
- 2004년 SQS를 공개했다.
- 2006년 S3와 EC2를 출시했다.
- 현재 전 세계 Region과 AZ를 통해 서비스를 제공한다.

---

# Exam Notes

- 대부분의 AWS 서비스는 Regional Service이다.
- IAM은 대표적인 Global Service이다.
- Region은 하나 이상의 Availability Zone(AZ)으로 구성된다.
- Availability Zone은 하나 이상의 독립적인 Data Center를 포함한다.
- Region 선택 시 고려 요소:
  - Compliance
  - Latency
  - Service Availability
  - Pricing

---
 
# Practical Example

서울 지역에 서비스를 운영한다고 가정한다.

Region
↓

ap-northeast-2 (Seoul)

↓

Availability Zone

- ap-northeast-2a
- ap-northeast-2c

EC2를 두 개의 AZ에 배포하면,
하나의 AZ에 장애가 발생하더라도
다른 AZ가 서비스를 계속 제공할 수 있다.

→ High Availability

---

# Vocabulary

| English | 한국어 | 日本語 |
|----------|---------|---------|
| Region | 리전 | リージョン |
| Availability Zone | 가용 영역 | アベイラビリティゾーン |
| Data Center | 데이터센터 | データセンター |
| Edge Location | 엣지 로케이션 | エッジロケーション |
| High Availability | 고가용성 | 高可用性 |
| Latency | 지연 시간 | レイテンシー |

---

# 日本語

## AWSとは

AWS（Amazon Web Services）は、Amazonが提供する世界最大のクラウドコンピューティングサービスである。

2002年にAmazon社内のITインフラとして始まり、現在では世界中で利用されている。

---

## AWSの歴史

| Year | Event |
|------|-------|
| 2002 | Amazon社内プロジェクト開始 |
| 2004 | Amazon SQS公開 |
| 2006 | Amazon S3・EC2公開 |
| Today | 世界最大のクラウドサービス |

---

## AWSでできること

- Webサイトのホスティング
- モバイルバックエンド
- ゲームサーバー
- バックアップ・ストレージ
- ビッグデータ分析
- AI / Machine Learning

---

## Summary

- AWSは世界最大のクラウドサービスである。
- 世界中にRegionとAvailability Zoneを持つ。

---

# English

## What is AWS?

AWS (Amazon Web Services) is the world's largest cloud computing platform.

It started as Amazon's internal IT infrastructure in 2002 and has grown into the leading cloud service provider.

---

## AWS History

| Year | Event |
|------|-------|
| 2002 | Internal IT project |
| 2004 | Amazon SQS released |
| 2006 | Amazon S3 and EC2 released |
| Today | World's leading cloud platform |

---

## What can AWS do?

- Website Hosting
- Mobile Backend
- Game Server
- Backup & Storage
- Big Data Analytics
- AI / Machine Learning

---

## Summary

- AWS is the world's largest cloud platform.
- It started in 2002.
- SQS was released in 2004.
- S3 and EC2 were released in 2006.
- AWS provides services through Regions and Availability Zones.
