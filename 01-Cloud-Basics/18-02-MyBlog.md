# Serverless Hosted Website: MyBlog.com

> Section 18 | AWS Solutions Architect Associate | Lecture study note

## 1. 요구사항
전 세계 사용자가 방문하는 읽기 중심 블로그를 서버리스로 호스팅한다. 정적 페이지·이미지를 빠르게 제공하고, 공개 REST API를 운영하며, 새 구독자에게 환영 메일을 보내고, 이미지 업로드 시 썸네일을 자동 생성한다.

## 2. 최종 아키텍처
```text
Global Readers ──> CloudFront ── OAC ──> Private S3 (HTML/CSS/JS/images)
      │
      └──> API Gateway ──> Lambda ──> DAX ──> DynamoDB
                                              ⇅
                                      DynamoDB Global Tables

New subscriber ──> DynamoDB ──> DynamoDB Streams ──> Lambda ──> SES
Photo upload ──> S3 ObjectCreated event ──> Lambda ──> S3 thumbnail
```

## 3. 서비스 선택 이유
**S3 + CloudFront**는 정적 파일의 저장과 전 세계 캐싱을 분담한다. **OAC(Origin Access Control) + S3 Bucket Policy**로 S3 직접 공개 접근을 제한하고 CloudFront를 통해서만 읽게 할 수 있다.

**API Gateway + Lambda**는 공개 API를 서버 관리 없이 제공한다. **DynamoDB**는 블로그 데이터, **DAX**는 반복 조회 캐싱에 사용한다. **DynamoDB Global Tables**는 여러 리전에서 데이터를 복제하는 Active-Active 구성이지만, 데이터베이스만 복제한다고 API와 Lambda까지 자동으로 다중 리전 배포되는 것은 아니다.

새 구독자 항목이 DynamoDB에 기록되면 **DynamoDB Streams**가 변경 이벤트를 기록한다. **Lambda**가 새 구독자 생성 이벤트를 판별하고 **Amazon SES**로 환영 메일을 발송한다. SES 발송 권한을 Lambda 실행 역할에 부여한다.

이미지가 S3에 업로드되면 **S3 Event Notification**이 Lambda를 실행하고, Lambda는 썸네일을 생성해 S3에 저장한다. 같은 버킷에 저장할 때는 입력·출력 접두사를 구분해 무한 트리거 루프를 방지한다.

**S3 Transfer Acceleration**은 멀리 떨어진 사용자의 S3 업로드를 가속하는 선택지로, CloudFront의 정적 콘텐츠 다운로드 캐싱과 역할이 다르다.

## 4. 주의점
- 공개 API와 관리자용 API의 인증 정책을 분리한다.
- DynamoDB Streams 이벤트는 중복 처리를 고려해 멱등성을 설계한다.
- Global Tables의 복제는 리전 간 비동기적일 수 있어 충돌·지연을 고려한다.
- CloudFront, DAX, Global Tables는 각각 CDN, DB 캐시, DB 복제라는 다른 문제를 해결한다.

## 🎯 Exam Notes

- 정적 웹 호스팅: S3 + CloudFront.
- 비공개 S3 원본: CloudFront OAC + Bucket Policy.
- REST API: API Gateway + Lambda.
- 데이터 읽기 캐시: DAX.
- 다중 리전 DB: DynamoDB Global Tables.
- 변경 이벤트 기반 이메일: DynamoDB Streams + Lambda + SES.
- 썸네일 자동 생성: S3 이벤트 + Lambda.

## 💡 Practical Example

새 구독자가 등록되면 DynamoDB Streams에서 INSERT 이벤트를 감지해 Lambda가 SES로 환영 메일을 보낸다. 독자는 CloudFront에서 캐싱된 HTML과 이미지를 빠르게 받는다.

## 🇯🇵 日本語 Summary

グローバルブログでは、S3とCloudFrontで静的コンテンツを配信し、OACでオリジンへのアクセスを制限する。API Gateway、Lambda、DynamoDBでAPIを構築する。DynamoDB StreamsとSESは購読メール、S3イベントとLambdaはサムネイル生成に利用する。

## 🇺🇸 English Summary

A global blog serves static assets through CloudFront and private S3 with OAC. API Gateway, Lambda, and DynamoDB power the API. DynamoDB Streams trigger welcome emails through SES, while S3 events trigger thumbnail generation.

## 📚 Vocabulary

OAC=Origin Access Control, SES=Simple Email Service, Global Tables=다중 리전 복제, Event-driven=이벤트 기반

## 📝 Review Questions

**Q. CloudFront와 DynamoDB Global Tables는 같은 목적일까?**

<details>
<summary>정답 보기</summary>

아니다. CloudFront는 콘텐츠 캐싱·전송, Global Tables는 리전 간 데이터베이스 복제를 담당한다.

</details>
