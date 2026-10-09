# MyTodoList Mobile Application

> Section 18 | AWS Solutions Architect Associate | Lecture study note

## 1. 요구사항
모바일 Todo 애플리케이션은 HTTPS REST API, 사용자 로그인, 사용자별 S3 파일 접근, DynamoDB의 높은 읽기 요청량을 처리해야 한다. 가능한 한 서버를 직접 관리하지 않는 서버리스 구성을 선택한다.

## 2. 최종 아키텍처
```text
Mobile App ── HTTPS ──> API Gateway ──> Lambda ──> DAX ──> DynamoDB
    │                         ▲
    ├── Cognito User Pool ────┘ (JWT 기반 API 인증)
    └── Cognito Identity Pool ──> 임시 AWS 자격 증명 ──> S3 사용자별 경로
```

## 3. 설계 흐름
**API Gateway**는 모바일 앱의 HTTPS 요청을 받는 진입점이다. **Lambda**는 Todo 생성·조회·수정·삭제 로직을 실행하고, **DynamoDB**는 Todo 데이터를 보관한다. 읽기 요청이 많다면 **DAX**를 DynamoDB 앞에 배치해 자주 조회하는 데이터를 캐싱한다. DAX는 선택 사항이며 별도 클러스터 비용이 든다.

**Cognito User Pool**은 사용자 가입·로그인·토큰 발급을 맡는다. API Gateway의 Cognito 인증과 연결해 인증된 사용자만 API를 호출하게 한다. **Cognito Identity Pool**은 사용자에게 제한된 권한의 임시 AWS 자격 증명을 제공한다. 앱은 이를 사용해 사용자별 허용된 S3 경로에 직접 접근할 수 있다. Identity Pool은 User Pool과 역할이 다르며, 직접 S3에 접근하는 경우에 특히 유용하다.

API Gateway 응답 캐싱도 가능하지만 모든 요청에 무조건 적용하는 것은 아니다. 사용자별 개인 응답을 캐싱한다면 인증·캐시 키 분리를 신중하게 설계해야 한다.

## 4. 설계상 주의점
- Lambda가 사용자 ID를 신뢰할 때는 요청 본문의 임의 값보다 검증된 인증 컨텍스트를 기준으로 한다.
- S3 사용자별 접근 권한은 Identity Pool IAM Role 정책으로 최소 권한을 부여한다.
- DAX는 읽기 성능 개선 도구이지 DynamoDB 자체를 대체하지 않는다.
- DAX 및 API 캐시는 최신 데이터 반영 요구사항과 비용을 고려해 도입한다.

## 🎯 Exam Notes

- User Pool: 사용자 인증과 토큰.
- Identity Pool: AWS 리소스 접근을 위한 임시 자격 증명.
- API Gateway + Lambda: 서버리스 REST API.
- DynamoDB + DAX: 읽기 중심 NoSQL과 캐시.
- 개인 데이터 캐싱은 사용자별 격리 필수.

## 💡 Practical Example

모바일 Todo 앱에서 사용자는 User Pool로 로그인하고, API Gateway와 Lambda를 통해 DynamoDB의 Todo를 조회한다. 첨부 파일은 Identity Pool의 임시 자격 증명으로 S3의 본인 경로에 업로드한다.

## 🇯🇵 日本語 Summary

モバイルToDoアプリでは、API GatewayとLambdaでREST APIを構築し、DynamoDBにデータを保存する。Cognito User Poolは認証、Identity PoolはS3アクセス用の一時的なAWS認証情報を担当する。読み取り負荷が高い場合はDAXを検討する。

## 🇺🇸 English Summary

A mobile ToDo app uses API Gateway and Lambda for its REST API and DynamoDB for storage. Cognito User Pools handle authentication, while Identity Pools provide temporary AWS credentials for scoped S3 access. DAX is an optional read cache.

## 📚 Vocabulary

User Pool=사용자 인증, Identity Pool=임시 AWS 자격 증명, DAX=DynamoDB Accelerator, Cache Hit=캐시 적중

## 📝 Review Questions

**Q. User Pool과 Identity Pool의 차이는?**

<details>
<summary>정답 보기</summary>

User Pool은 사용자 로그인/토큰, Identity Pool은 AWS 접근을 위한 임시 자격 증명을 제공한다.

</details>
