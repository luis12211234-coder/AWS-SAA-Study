# 📘 01. AWS Global Infrastructure

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Region` · `Availability Zone` · `Data Center` · `Edge Location` · `High Availability`

## 🇰🇷 1. Overview

AWS(Amazon Web Services)는 Amazon에서 제공하는 세계 최대의 클라우드 컴퓨팅 플랫폼이다.

2002년 Amazon 내부 IT 인프라에서 시작하여, 현재는 전 세계 수백만 명의 고객이 사용하는 클라우드 서비스로 성장했다.

## 🇰🇷 2. AWS History

| Year | Event |
|------|-------|
| 2002 | Amazon 내부 IT 인프라 프로젝트 시작 |
| 2004 | Amazon SQS 공개 |
| 2006 | Amazon S3, EC2 출시 |
| Today | 세계 최대 클라우드 플랫폼 |

## 🇰🇷 3. What can AWS do?

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

## 🇰🇷 4. Global Infrastructure

AWS는 전 세계에 인프라를 구축하여 서비스를 제공한다.

구성 요소

- Region
- Availability Zone (AZ)
- Data Center
- Edge Location (Point of Presence)

## 🇰🇷 5. Summary

- AWS는 세계 최대 클라우드 플랫폼이다.
- 2002년 Amazon 내부 프로젝트에서 시작했다.
- 2004년 SQS를 공개했다.
- 2006년 S3와 EC2를 출시했다.
- 현재 전 세계 Region과 AZ를 통해 서비스를 제공한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- 대부분의 AWS 서비스는 Regional Service이다.
- IAM은 대표적인 Global Service이다.
- 각 AWS Region은 최소 3개의 Availability Zone(AZ)으로 구성된다.
- Availability Zone은 하나 이상의 독립적인 Data Center를 포함한다.
- Region 선택 시 고려 요소:
  - Compliance
  - Latency
  - Service Availability
  - Pricing

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

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Region과 Availability Zone의 차이점은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Region은 여러 Availability Zone을 포함하는 지리적 영역이다. Availability Zone은 Region 안에서 서로 격리된 하나 이상의 데이터센터로 구성된다.

</details>

### Q2. Region을 선택할 때 고려해야 하는 요소는 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

데이터 거버넌스와 규정 준수, 사용자와의 지연 시간, 필요한 서비스의 제공 여부, Region별 가격을 고려한다.

</details>

### Q3. 대부분의 AWS 서비스는 Global인가, Regional인가?

<details>
<summary>정답 및 해설 보기</summary>

대부분은 특정 Region에 종속되는 Regional Service이다. IAM, Route 53, CloudFront 등 일부 서비스는 Global Service이다.

</details>

### Q4. 여러 AZ에 EC2를 배포하는 이유는 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

하나의 AZ에 장애가 발생해도 다른 AZ의 인스턴스가 서비스를 계속 제공하도록 하여 고가용성과 장애 격리를 확보하기 위해서다.

</details>

### Q5. AWS 글로벌 인프라의 핵심 구성 요소는 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Region, Availability Zone, Data Center, Edge Location(Point of Presence)이 핵심 구성 요소다.

</details>

## 🇯🇵 日本語 Summary

### AWSとは

AWS（Amazon Web Services）は、Amazonが提供する世界最大のクラウドコンピューティングサービスである。

2002年にAmazon社内のITインフラとして始まり、現在では世界中で利用されている。

### AWSの歴史

| Year | Event |
|------|-------|
| 2002 | Amazon社内プロジェクト開始 |
| 2004 | Amazon SQS公開 |
| 2006 | Amazon S3・EC2公開 |
| Today | 世界最大のクラウドサービス |

### AWSでできること

- Webサイトのホスティング
- モバイルバックエンド
- ゲームサーバー
- バックアップ・ストレージ
- ビッグデータ分析
- AI / Machine Learning

### Summary

- AWSは世界最大のクラウドサービスである。
- 各AWS Regionは最低3つのAvailability Zoneで構成される。
- Availability Zoneは1つ以上の独立したData Centerで構成される。

## 🇺🇸 English Summary

### What is AWS?

AWS (Amazon Web Services) is the world's largest cloud computing platform.

It started as Amazon's internal IT infrastructure in 2002 and has grown into the leading cloud service provider.

### AWS History

| Year | Event |
|------|-------|
| 2002 | Internal IT project |
| 2004 | Amazon SQS released |
| 2006 | Amazon S3 and EC2 released |
| Today | World's leading cloud platform |

### What can AWS do?

- Website Hosting
- Mobile Backend
- Game Server
- Backup & Storage
- Big Data Analytics
- AI / Machine Learning

### Summary

- AWS is the world's largest cloud platform.
- It started in 2002.
- SQS was released in 2004.
- S3 and EC2 were released in 2006.
- Each AWS Region contains at least three Availability Zones.
- An Availability Zone consists of one or more discrete data centers.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|----------|---------|---------|
| Region | 리전 | リージョン |
| Availability Zone | 가용 영역 | アベイラビリティゾーン |
| Data Center | 데이터센터 | データセンター |
| Edge Location | 엣지 로케이션 | エッジロケーション |
| High Availability | 고가용성 | 高可用性 |
| Latency | 지연 시간 | レイテンシー |
