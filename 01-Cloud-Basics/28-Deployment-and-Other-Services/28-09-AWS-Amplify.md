
# 📘 28-09. AWS Amplify

> [!NOTE]
> **AWS SAA-C03 Study Note** · `GitHub Section 28` · `Udemy Section 30`
> **핵심 키워드:** Amplify, Full-Stack, Frontend, Backend, Authentication, Hosting, CI/CD, Gen 2

## 🇰🇷 1. 웹·모바일 앱 개발의 어려움

웹 서비스를 만들려면 화면만 개발하는 것으로 끝나지 않는다. 로그인, 데이터 저장, API, 파일 업로드, 권한, 배포를 각각 설계해야 한다.

**AWS Amplify는 웹·모바일 앱 개발자가 AWS 기반 백엔드 기능을 연결하고 프런트엔드 앱을 배포하도록 돕는 도구와 서비스의 집합**이다. 서비스 하나가 모든 기능을 독자적으로 수행하는 것이 아니라 필요한 AWS 기능을 더 쉽게 활용하도록 도와준다.

```text
개발자: React / Next.js 등 프런트엔드
                 ↓
              Amplify
        ├─ Authentication
        ├─ Data / API
        ├─ Storage / Functions
        └─ Hosting / CI/CD
                 ↓
      필요한 AWS 백엔드 리소스
      Cognito / AppSync / S3 / Lambda 등
```

이것이 강사가 말한 **웹·모바일 앱용 원스톱 개발 환경**의 의미다.

## 🇰🇷 2. Amplify의 두 관점: Backend와 Hosting

### (1) 백엔드 개발·연동

- **Authentication:** 회원 가입, 로그인, 사용자 인증을 Amazon Cognito 등과 연결.
- **Data / API:** 프런트엔드에서 데이터에 접근하도록 백엔드 API와 데이터 리소스를 구성.
- **Storage:** 파일 업로드·다운로드에 Amazon S3 등의 스토리지를 활용.
- **Functions:** 필요한 서버리스 로직을 AWS Lambda 등과 통합.
- **Frontend Libraries:** 프런트엔드 코드에서 AWS 백엔드 기능을 다루기 위한 라이브러리.

### (2) Amplify Hosting과 CI/CD

GitHub 등의 Git 저장소에 코드를 올려 두고 **Amplify Hosting**을 연결하면, 브랜치 변경을 감지해 빌드·배포 과정을 자동화할 수 있다.

```text
개발자: GitHub에 코드 Push
             ↓
      Amplify Hosting
      소스 가져오기 → 빌드 → 배포
             ↓
     AWS 글로벌 CDN을 통해 서비스
             ↓
          웹 사용자
```

즉 **Amplify의 백엔드 기능**과 **Amplify Hosting**은 관련되지만 역할이 다르다. Hosting만 사용해서 기존 프런트엔드 웹앱을 배포하는 구성도 가능하다.

## 🇰🇷 3. 실제 예시: 간단한 할 일(Todo) 웹 앱

요구사항: 사용자가 가입해서 자기 할 일을 만들고, 수정하고, 저장할 수 있는 웹앱.

| 기능 | 예시 구성 |
|---|---|
| 로그인·회원가입 | Amplify Auth + Amazon Cognito |
| 할 일 데이터 생성·조회 | Amplify Data + 백엔드 데이터/API |
| 첨부 파일 저장 | Amplify Storage + Amazon S3 |
| GitHub Push 후 자동 배포 | Amplify Hosting |

흐름은 다음과 같다.

```text
사용자 → Amplify Hosting으로 배포된 웹 화면
                 ↓ 로그인
             Amazon Cognito
                 ↓ 인증된 요청
             데이터/API 계층
                 ↓
              Todo 저장
```

이렇게 **프런트엔드와 백엔드를 한 흐름으로 만들 수 있다는 점**이 Amplify의 장점이다. 인증·스토리지 서비스를 직접 일일이 결합하는 부담을 줄일 수 있다.

## 🇰🇷 4. 강의 내용과 최신 Amplify의 차이

강의는 주로 **Amplify CLI로 백엔드 리소스를 구성하는 흐름**을 소개한다. 하지만 AWS Amplify에는 **Gen 1과 Gen 2**가 있으며, 현대 Gen 2는 **TypeScript 기반 code-first 방식으로 백엔드를 정의**하는 개발 경험을 제공한다.

- 시험에서는 CLI 명령어보다는 **웹·모바일 개발 지원, 인증/API/스토리지 연동, Git 기반 호스팅**을 먼저 이해한다.
- Amplify가 모든 AWS 서비스를 자동 생성하는 건 아니다. **선택한 기능에 맞는 리소스만** 구성·연동된다.
- 모바일 앱 개발 지원과 **모바일 앱 스토어 배포**는 별개다. Amplify Hosting은 주로 웹 프런트엔드 호스팅에 해당한다.

## 🇰🇷 5. Elastic Beanstalk와 AppFlow 비교

| 서비스 | 주된 목적 | 시험에서 보이는 표현 |
|---|---|---|
| **AWS Amplify** | 웹·모바일 앱의 프런트엔드·백엔드 개발 및 호스팅 지원 | Full-stack web app, authentication, CI/CD |
| **Elastic Beanstalk** | 웹 애플리케이션 실행 환경의 프로비저닝·배포 관리 | Managed platform, application environment |
| **Amazon AppFlow** | SaaS와 AWS 사이 데이터 전송·통합 | Salesforce → S3 / Redshift |

강사의 '**웹·모바일용 Elastic Beanstalk**'라는 표현은 **개발 및 배포의 운영 부담을 줄여 준다**는 비유다. 기능과 배포 대상이 동일하다는 뜻은 아니다.

### 시험에서 이렇게 생각하자

- React 웹앱에서 로그인·API·파일 업로드를 AWS와 쉽게 연결 → **Amplify**.
- GitHub 코드가 변경될 때 웹앱 자동 빌드·배포 → **Amplify Hosting**.
- Java 웹 애플리케이션 실행 환경을 AWS에서 쉽게 관리 → **Elastic Beanstalk**.
- Salesforce 고객 데이터를 S3로 정기 복사 → **Amazon AppFlow**.

## 🎯 Exam Notes

> [!IMPORTANT]
> **웹·모바일 앱 개발 + 인증·API·스토리지 백엔드 연동 + 프런트엔드 CI/CD → AWS Amplify.**

- **Amplify Hosting:** Git 기반 호스팅과 지속적 배포.
- **Amplify Backend:** Auth, Data, Storage, Functions 등을 AWS와 연동.
- **Amplify ≠ AppFlow:** 앱 개발과 SaaS 데이터 전송은 다르다.
- **Amplify ≠ Elastic Beanstalk:** 앱 실행 플랫폼 관리와 풀스택 프런트엔드 개발 지원은 중심점이 다르다.

## 📝 Review Questions

> [!TIP]
> 답을 먼저 떠올리고 펼쳐 확인한다.

### Q1. 로그인·API·스토리지를 빠르게 연결해 React 웹앱을 만들려면 무엇을 고려할까?

<details>
<summary>정답 및 해설 보기</summary>

AWS Amplify. 인증, 데이터/API, 스토리지 등의 백엔드 기능을 웹 프런트엔드와 쉽게 통합하도록 돕는다.

</details>

### Q2. GitHub Push 후 자동으로 웹사이트를 다시 빌드·배포하는 기능은?

<details>
<summary>정답 및 해설 보기</summary>

Amplify Hosting의 Git 기반 CI/CD 흐름이다.

</details>

### Q3. Amplify Auth에서 대표적으로 연계하는 AWS 인증 서비스는?

<details>
<summary>정답 및 해설 보기</summary>

Amazon Cognito. 사용자 가입·로그인 및 인증을 담당하는 관리형 서비스다.

</details>

### Q4. Salesforce의 데이터를 정기적으로 Redshift에 보내려면 Amplify와 AppFlow 중 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Amazon AppFlow. SaaS와 AWS 데이터 저장소 간 통합이 목적이다. Amplify는 앱 개발·호스팅 중심이다.

</details>

### Q5. 'Amplify는 웹·모바일용 Elastic Beanstalk'라는 말은 두 서비스가 동일하다는 뜻인가?

<details>
<summary>정답 및 해설 보기</summary>

아니다. 둘 다 개발자의 인프라 관리 부담을 줄이지만, Amplify는 프런트엔드·백엔드 앱 개발 및 호스팅, Elastic Beanstalk는 애플리케이션 실행 환경 관리에 더 초점을 둔다.

</details>

## 🇯🇵 日本語 Summary

**AWS Amplify** は Web・モバイルアプリ開発向けのツールとサービスの集合であり、認証、データ/API、ストレージ、関数などの AWS バックエンド連携を簡素化する。**Amplify Hosting** は Git リポジトリを使ったビルド・継続的デプロイを提供する。SaaS 間データ転送の **Amazon AppFlow**、実行環境管理の **Elastic Beanstalk** とは目的が異なる。

## 🇺🇸 English Summary

AWS Amplify helps developers build web and mobile apps by integrating authentication, APIs, storage, and serverless backend capabilities. Amplify Hosting offers Git-based build and continuous deployment for web frontends. Amplify focuses on app development and hosting, while Elastic Beanstalk manages application environments and AppFlow integrates SaaS data.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Full-stack Application | 풀스택 애플리케이션 | フルスタックアプリケーション |
| Frontend | 프런트엔드 | フロントエンド |
| Backend | 백엔드 | バックエンド |
| Authentication | 인증 | 認証 |
| Hosting | 호스팅 | ホスティング |
| Continuous Deployment | 지속적 배포 | 継続的デプロイ |
| Code-first | 코드 중심 구성 | コードファースト |
| Git Repository | Git 저장소 | Git リポジトリ |

## 📖 References

- [AWS Amplify Hosting](https://docs.aws.amazon.com/amplify/latest/userguide/welcome.html)
- [AWS Amplify Gen 2](https://docs.amplify.aws/)
- [Elastic Beanstalk overview](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html)
