# 05. MyBlog.com - Global Serverless Website

## Project Status

**Architecture Design / Not Yet Deployed**

AWS SAA 학습 사례를 기반으로 작성한 아키텍처 설계 프로젝트다.

## 1. Project Overview

### Theme

**Global Content Delivery & Event-Driven Serverless Processing**

전 세계 사용자가 접근하는 블로그 서비스를 설계한다.

정적 콘텐츠 전달, 동적 API, 글로벌 데이터베이스, 자동 이메일 발송, 이미지 처리를 단계적으로 구성한다.

## 2. Requirements

### Functional

- 글로벌 웹사이트 제공
- 정적 웹 콘텐츠 호스팅
- 게시글 및 구독 데이터 관리
- 신규 구독자 환영 이메일 전송
- 이미지 업로드 시 썸네일 자동 생성

### Non-Functional

- Global Scalability
- Low Latency
- Secure Content Delivery
- Read Optimization
- Event-Driven Processing
- High Availability

## 3. Architecture Evolution

### Stage 1. Static Website Hosting

```text
Users
  |
  v
Amazon S3
```

### Problem

글로벌 사용자에게 빠르게 콘텐츠를 제공하고 S3 Origin을 안전하게 보호해야 한다.

### Stage 2. CloudFront + OAC

```text
Global Users
      |
      v
CloudFront
      |
      | OAC
      v
Private S3 Bucket
```

### Design Decisions

- CloudFront: CDN 및 Edge Caching
- S3: Static Content Storage
- OAC: Private S3 Origin Access
- Bucket Policy: CloudFront 접근 허용

### Stage 3. Dynamic Backend

```text
Browser
   |
   v
API Gateway
   |
   v
Lambda
   |
   v
DynamoDB
```

게시글, 댓글, 구독 정보 등 동적 데이터를 처리한다.

읽기 요청이 많은 경우 DAX를 선택적으로 추가할 수 있다.

### Stage 4. Multi-Region Data

```text
Asia Users             US Users
     |                     |
     v                     v
Tokyo API               US API
     |                     |
     v                     v
Tokyo DynamoDB <----> US DynamoDB
             Global Tables
```

### Design Decision

DynamoDB Global Tables를 이용해 다중 리전 데이터 복제를 고려한다.

단, 실제 Multi-Region API 배포와 사용자 라우팅이 함께 필요하다.

### Stage 5. Welcome Email Automation

```text
Subscriber Registration
         |
         v
      DynamoDB
         |
         v
   DynamoDB Streams
         |
         v
       Lambda
         |
         v
      Amazon SES
         |
         v
    Welcome Email
```

### Design Decision

사용자 등록과 이메일 전송을 분리한다.

이벤트 기반으로 후속 작업을 실행하여 API 응답 경로의 의존성을 줄인다.

### Stage 6. Thumbnail Generation

```text
Image Upload
      |
      v
   Amazon S3
      |
      v
S3 ObjectCreated Event
      |
      v
     Lambda
      |
      v
Thumbnail Generation
      |
      v
   Amazon S3
```

## 4. Final Architecture

```text
                    Global Users
                         |
              +----------+----------+
              |                     |
              v                     v
         CloudFront             API Gateway
              |                     |
              | OAC                 v
              v                   Lambda
         Private S3                 |
         Static Files               v
                                  DAX
                                   |
                                   v
                                DynamoDB
                                   |
                     +-------------+-------------+
                     |                           |
                     v                           v
                Global Tables              DynamoDB Streams
                                                 |
                                                 v
                                               Lambda
                                                 |
                                                 v
                                                SES

Image Upload
     |
     v
    S3
     |
     v
S3 Event Notification
     |
     v
   Lambda
     |
     v
Thumbnail in S3
```

## 5. Architecture Decisions

| Requirement | AWS Service | Reason |
|---|---|---|
| Static Hosting | S3 | Durable Object Storage |
| Global Delivery | CloudFront | Edge Caching |
| Secure S3 Origin | OAC | Private Origin Access |
| REST API | API Gateway | Managed API |
| Backend Logic | Lambda | Event-Driven Compute |
| Data Storage | DynamoDB | Managed NoSQL |
| Read Optimization | DAX | DynamoDB Caching |
| Multi-Region Data | Global Tables | Regional Replication |
| Database Events | DynamoDB Streams | Change Event Processing |
| Email | Amazon SES | Managed Email Service |
| Image Processing | Lambda | S3 Event-Driven Execution |

## 6. Security Considerations

### S3 Origin Protection

CloudFront OAC와 S3 Bucket Policy를 사용한다.

### API Security

공개 게시글 API와 관리자 API의 권한 요구사항을 구분한다.

### IAM Least Privilege

각 Lambda Function에 필요한 권한만 부여한다.

### SES

발신자 검증과 발송 제한을 확인한다.

### Upload Security

파일 유형, 크기, 업로드 권한을 제한한다.

## 7. Trade-offs

### Advantages

- 글로벌 정적 콘텐츠 전달
- 서버 운영 부담 감소
- 이벤트 기반 자동화
- 독립적인 백엔드 확장
- 선택적 Multi-Region 지원

### Disadvantages

- 서비스 구성 요소 증가
- DAX 및 Global Tables 추가 비용
- 데이터 복제 지연
- 이벤트 중복 처리 가능성
- 이미지 처리 실패 시 재시도 설계 필요

## 8. Alternatives

| Requirement | Alternative | Consideration |
|---|---|---|
| Database | Aurora | Relational Workloads |
| Image Processing | Container Worker | Heavy Processing |
| Event Delivery | SQS | Buffering and Retry Control |
| Email | External Email Provider | Integration Requirements |
| Global Database | Single Region | Lower Cost and Simpler Operations |

## 9. Future Implementation Plan

- [ ] AWS Budget 설정
- [ ] S3 정적 사이트 파일 준비
- [ ] CloudFront + OAC 구성
- [ ] S3 직접 접근 차단 테스트
- [ ] API Gateway + Lambda 구성
- [ ] DynamoDB 데이터 모델 설계
- [ ] 구독자 등록 API 구현
- [ ] DynamoDB Streams 연동
- [ ] SES 발신자 검증 및 테스트
- [ ] S3 이미지 업로드 이벤트 설정
- [ ] Lambda 썸네일 생성 구현
- [ ] 이벤트 재귀 호출 방지 테스트
- [ ] Global Tables 필요성 및 비용 분석
- [ ] 리소스 정리

## 10. Validation Criteria

- CloudFront를 통해 정적 콘텐츠가 제공되는가?
- S3 Origin 직접 접근이 차단되는가?
- API Gateway를 통해 게시글을 조회할 수 있는가?
- 신규 구독자에게 이메일이 발송되는가?
- 중복 이벤트 발생 시 중복 이메일을 방지할 수 있는가?
- 이미지 업로드 후 썸네일이 생성되는가?
- 썸네일 저장으로 Lambda가 재귀 실행되지 않는가?

## 🇯🇵 日本語 Summary

本プロジェクトは、CloudFront と S3 によるグローバルなコンテンツ配信、API Gateway と Lambda によるサーバーレス API、DynamoDB Streams と SES によるメール送信、S3 イベントによる画像処理を組み合わせた設計である。

## 🇺🇸 English Summary

This project designs a global serverless blog using CloudFront, S3, API Gateway, Lambda, and DynamoDB. It also explores event-driven workflows for welcome emails and image thumbnails, with optional multi-region data replication and caching.

## References

- [Amazon CloudFront](https://docs.aws.amazon.com/cloudfront/)
- [Amazon S3](https://docs.aws.amazon.com/s3/)
- [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/)
- [Amazon SES](https://docs.aws.amazon.com/ses/)
- [AWS Lambda](https://docs.aws.amazon.com/lambda/)