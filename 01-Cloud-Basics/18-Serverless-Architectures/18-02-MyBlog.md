# 18-02. MyBlog.com - Serverless Hosted Website

## 1. Requirements

전 세계 사용자가 접근하는 블로그 서비스를 구축한다.

### Functional Requirements

- 글로벌 사용자가 웹사이트에 접근
- 정적 웹 콘텐츠 제공
- REST API를 통한 데이터 조회
- 신규 구독자에게 환영 이메일 발송
- 이미지 업로드 시 자동 썸네일 생성

### Non-Functional Requirements

- Global Scalability
- Low Latency
- High Availability
- Read Performance Optimization
- Event-Driven Processing
- Minimal Server Management

## 2. Static Website Delivery

블로그의 HTML, CSS, JavaScript, 이미지 등 정적 파일은 S3에 저장할 수 있다.

전 세계 사용자에게 빠르게 제공하기 위해 CloudFront를 사용한다.

```text
Global Users
     |
     v
Amazon CloudFront
     |
     | Cache Miss
     v
Amazon S3
(Static Website Files)
```

### Amazon S3

웹사이트의 정적 파일을 저장하는 Object Storage다.

### Amazon CloudFront

전 세계 Edge Location을 활용하는 CDN(Content Delivery Network)이다.

장점:

- 사용자와 가까운 위치에서 콘텐츠 제공
- 정적 콘텐츠 캐싱
- S3 Origin 요청 감소
- 글로벌 사용자에 대한 지연 시간 개선
- 원본 서버의 트래픽 부담 감소

## 3. Secure S3 Origin

S3 버킷을 누구나 직접 접근할 수 있도록 공개할 필요는 없다.

CloudFront Origin Access Control(OAC)을 사용할 수 있다.

```text
User
  |
  v
CloudFront
  |
  | OAC-Signed Request
  v
Private S3 Bucket
```

### Security Design

- S3 Block Public Access 유지
- CloudFront Distribution에 OAC 구성
- S3 Bucket Policy로 허용된 CloudFront Distribution 접근 허용
- 사용자는 CloudFront를 통해 정적 파일에 접근

OAC는 CloudFront가 S3 Origin에 안전하게 접근하기 위한 기능이다.

Cognito Identity Pool과는 목적이 다르다.

| Service | Purpose |
|---|---|
| CloudFront OAC | CloudFront의 S3 Origin 접근 제어 |
| Cognito Identity Pool | 사용자에게 임시 AWS 자격 증명 제공 |

## 4. Dynamic REST API

블로그 게시글, 댓글, 구독 정보와 같은 동적 데이터는 API를 통해 처리한다.

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
DAX
   |
   v
DynamoDB
```

### API Gateway

HTTP 요청을 수신하고 적절한 Lambda Function으로 전달한다.

### Lambda

게시글 조회, 댓글 처리, 구독 등록 등 비즈니스 로직을 수행한다.

### DynamoDB

블로그의 동적 데이터를 저장한다.

### DAX

반복적인 DynamoDB 읽기 요청을 캐싱한다.

DAX는 모든 블로그에 필수인 서비스가 아니다.

읽기 패턴과 비용을 분석한 후 도입한다.

## 5. Global Database Architecture

전 세계 사용자에게 낮은 지연 시간으로 데이터 서비스를 제공하려면 DynamoDB Global Tables를 고려할 수 있다.

```text
Users in Asia
      |
      v
DynamoDB Tokyo
      ^
      | Multi-Region Replication
      v
DynamoDB Virginia
      ^
      |
Users in America
```

### DynamoDB Global Tables

여러 AWS Region에 걸쳐 데이터를 복제하는 Multi-Region 데이터베이스 기능이다.

특징:

- Multi-Region Replication
- Active-Active Architecture
- Regional Read and Write
- 글로벌 사용자 대상 지연 시간 개선 가능

주의:

Global Tables만 도입한다고 전체 애플리케이션의 응답 속도가 자동으로 개선되는 것은 아니다.

각 Region에 API Gateway와 Lambda를 배치하고 사용자를 적절한 Region으로 라우팅하는 설계가 함께 필요하다.

복제 지연과 동시 쓰기 충돌도 고려해야 한다.

## 6. Welcome Email Automation

신규 구독자가 등록되면 환영 이메일을 자동으로 전송한다.

```text
New Subscriber
      |
      v
API Gateway
      |
      v
Lambda
      |
      v
DynamoDB
      |
      | Item Change
      v
DynamoDB Streams
      |
      v
Email Lambda
      |
      | AWS SDK
      v
Amazon SES
      |
      v
Welcome Email
```

### DynamoDB Streams

DynamoDB 테이블의 Item 변경 이벤트를 기록한다.

대표적인 이벤트:

- INSERT
- MODIFY
- REMOVE

### AWS Lambda

DynamoDB Streams 이벤트를 받아 필요한 후속 작업을 실행한다.

### Amazon SES

Simple Email Service.

이메일 전송을 위한 AWS 서비스다.

### Design Considerations

- 신규 구독자 INSERT 이벤트만 처리
- 구독자 데이터와 일반 게시글 이벤트 구분
- Lambda 실행 역할에 필요한 SES 권한 부여
- SES 발신자 검증 및 계정 발송 제한 확인
- 재시도에 따른 중복 이메일 발송 방지 고려

이 구조의 핵심은 이메일 발송을 사용자 등록 API의 동기 처리 과정에서 분리한다는 점이다.

## 7. Automatic Thumbnail Generation

사용자가 이미지를 업로드하면 Lambda가 썸네일을 자동 생성한다.

```text
User
  |
  | Upload Image
  v
Amazon S3
  |
  | ObjectCreated Event
  v
AWS Lambda
  |
  | Image Processing
  v
Thumbnail
  |
  v
Amazon S3
```

### Processing Flow

1. 사용자가 S3에 이미지를 업로드한다.
2. S3 ObjectCreated 이벤트가 발생한다.
3. 이벤트에 연결된 Lambda가 실행된다.
4. Lambda가 원본 이미지를 읽는다.
5. Lambda가 작은 썸네일 이미지를 생성한다.
6. 생성된 파일을 S3에 저장한다.

### Important Consideration

원본과 썸네일을 동일한 버킷에 저장하는 경우 이벤트 재귀 호출에 주의해야 한다.

예시:

```text
S3 Bucket
├── originals/
│   └── photo.jpg
└── thumbnails/
    └── photo-small.jpg
```

S3 Event Notification을 `originals/` Prefix에만 적용하면 썸네일 저장으로 Lambda가 다시 실행되는 문제를 줄일 수 있다.

## 8. Upload Acceleration

글로벌 사용자의 대용량 파일 업로드 성능을 개선하려면 S3 Transfer Acceleration을 고려할 수 있다.

```text
Global User
     |
     v
AWS Edge Location
     |
     v
Optimized AWS Network
     |
     v
Amazon S3
```

CloudFront와 S3 Transfer Acceleration은 동일한 기능이 아니다.

| Service | Main Purpose |
|---|---|
| CloudFront | 콘텐츠 다운로드 및 전달 최적화 |
| S3 Transfer Acceleration | 원거리 S3 업로드 가속 |

## 9. Final Architecture

```text
                     Global Users
                          |
                +---------+---------+
                |                   |
                v                   v
           CloudFront          API Gateway
                |                   |
                | OAC               v
                v                 Lambda
          Private S3                |
          Static Files              v
                                  DAX
                                    |
                                    v
                                DynamoDB
                                    |
                      +-------------+-------------+
                      |                           |
                      v                           v
               Global Tables               DynamoDB Streams
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

## 🎯 Exam Notes

- Global Static Content Delivery: CloudFront + S3
- Secure S3 Origin: OAC + Bucket Policy
- Serverless REST API: API Gateway + Lambda
- Read-Heavy DynamoDB: DAX
- Multi-Region Active-Active Database: DynamoDB Global Tables
- DynamoDB Change Events: DynamoDB Streams
- Email Sending: Amazon SES
- S3 Upload Event Processing: S3 Event Notifications + Lambda
- Accelerated Long-Distance S3 Upload: S3 Transfer Acceleration

## 💡 Practical Example

한국 사용자가 블로그에 접속하면 CloudFront가 캐싱된 정적 웹 콘텐츠를 전달한다.

사용자가 게시글을 조회하면 API Gateway와 Lambda가 DynamoDB에서 데이터를 조회한다.

새로운 사용자가 구독을 신청하면 DynamoDB Streams 이벤트를 통해 Lambda가 실행되고 SES가 환영 이메일을 전송한다.

이미지를 업로드하면 S3 이벤트가 Lambda를 실행하여 썸네일을 자동 생성한다.

## 🇯🇵 日本語 Summary

MyBlog.com は、S3 と CloudFront による静的コンテンツ配信、API Gateway と Lambda による動的 API、DynamoDB によるデータ保存を組み合わせたサーバーレスアーキテクチャである。DynamoDB Streams と SES を利用したメール送信、S3 イベントと Lambda を利用したサムネイル生成など、イベント駆動型の処理も実現できる。

## 🇺🇸 English Summary

MyBlog.com combines S3 and CloudFront for global static content delivery with API Gateway, Lambda, and DynamoDB for dynamic operations. DynamoDB Streams and SES enable event-driven welcome emails, while S3 events trigger Lambda functions to generate image thumbnails. Global Tables can support multi-region data access.

## 📚 Vocabulary

| Term | Meaning |
|---|---|
| CDN | Content Delivery Network |
| Origin | 원본 콘텐츠를 제공하는 서버 또는 저장소 |
| Edge Location | 사용자와 가까운 콘텐츠 전달 거점 |
| OAC | Origin Access Control |
| Global Tables | DynamoDB 다중 리전 복제 기능 |
| Event-Driven | 이벤트 기반 |
| DynamoDB Streams | 데이터 변경 이벤트 스트림 |
| Thumbnail | 축소된 미리보기 이미지 |
| Transfer Acceleration | S3 전송 가속 |
| Replication | 데이터 복제 |

## 📝 Review Questions

### Q1. 비공개 S3 버킷의 정적 콘텐츠를 CloudFront로 제공하려면?

<details>
<summary>정답 보기</summary>

CloudFront OAC와 S3 Bucket Policy를 구성한다.

</details>

### Q2. 신규 구독자 등록 후 자동 이메일을 발송하는 서비스 조합은?

<details>
<summary>정답 보기</summary>

DynamoDB Streams → Lambda → Amazon SES

</details>

### Q3. 이미지 업로드 후 썸네일을 자동 생성하려면?

<details>
<summary>정답 보기</summary>

S3 ObjectCreated Event → Lambda → S3

</details>

### Q4. CloudFront와 S3 Transfer Acceleration의 차이는?

<details>
<summary>정답 보기</summary>

CloudFront는 콘텐츠 전달 및 캐싱을 위한 CDN이다.

S3 Transfer Acceleration은 원거리에서 S3로 파일을 업로드할 때 전송 성능을 개선하는 기능이다.

</details>