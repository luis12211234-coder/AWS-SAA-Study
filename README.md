# AWS SAA-C03 Study Notes

AWS Certified Solutions Architect – Associate(SAA-C03)를 준비하며 작성하는 학습 기록입니다. 강의 내용을 그대로 옮기기보다, 이해한 내용을 한국어로 정리하고 일본어·영어로 핵심을 다시 설명합니다.

> Status: AWS 글로벌 인프라부터 Classic Solutions Architecture까지 학습 노트 작성 중

## 학습 목차

| 주제 | 문서 |
|---|---|
| AWS 글로벌 인프라 | [01 · AWS Global Infrastructure](./01-Cloud-Basics/01-AWS-Global-Infrastructure/README.md) |
| IAM | [02 · IAM](./01-Cloud-Basics/02-IAM/README.md) |
| 비용 관리 | [03 · AWS Billing and Budgets](./01-Cloud-Basics/03-AWS-Billing-and-Budgets/README.md) |
| Amazon EC2 | [04 · Amazon EC2](./01-Cloud-Basics/04-Amazon-EC2/README.md) |
| EC2 스토리지 | [05 · EC2 Storage](./01-Cloud-Basics/05-EC2-Storage/README.md) |
| 고가용성과 확장성 | [06 · High Availability and Scalability](./01-Cloud-Basics/06-High-Availability-and-Scalability/README.md) |
| 데이터베이스 | [07 · Databases](./01-Cloud-Basics/07-Databases/README.md) |
| Amazon Route 53 | [08 · Amazon Route 53](./01-Cloud-Basics/08-Amazon-Route-53/README.md) |
| 클래식 솔루션 아키텍처 | [09 · Classic Solutions Architecture](./01-Cloud-Basics/09-Classic-Solutions-Architecture/README.md) |

[Cloud Basics 전체 목차](./01-Cloud-Basics/README.md) · [아키텍처 실습 프로젝트](./Architecture-Projects/README.md)

## 저장소 구조

```text
AWS-SAA-Study/
├─ README.md
├─ 01-Cloud-Basics/
│  ├─ README.md
│  └─ 01-AWS-Global-Infrastructure/ ... 09-Classic-Solutions-Architecture/
│     ├─ README.md
│     └─ 주제별 학습 노트
└─ Architecture-Projects/
   └─ 아키텍처 실습 기록
```

## 작성 방식

각 노트는 한국어 개념 설명을 중심으로 시험 포인트와 실무 예시를 정리합니다. 일본어·영어 요약, 핵심 용어와 복습 문제를 덧붙여 기술 개념을 여러 언어로 설명하는 연습을 합니다. 문서는 이해가 깊어지면 계속 수정합니다.

## 보안 원칙

- 비밀번호, 액세스 키, 세션 토큰, MFA 정보를 커밋하지 않습니다.
- AWS 계정 ID, 개인 이메일, 불필요한 리소스 식별자를 공개하지 않습니다.
- 정책 예시와 스크린샷에는 필요한 경우 자리표시자를 사용합니다.
- 최신 운영 지침은 [AWS 공식 문서](https://docs.aws.amazon.com/)에서 확인합니다.

## 학습 자료

- Stephane Maarek, *AWS Certified Solutions Architect Associate SAA-C03*
- [AWS Documentation](https://docs.aws.amazon.com/)
- [AWS Architecture Center](https://aws.amazon.com/architecture/)

이 저장소는 개인 학습 기록이며, 실제 운영 환경에 적용할 때는 최신 AWS 공식 문서를 확인해야 합니다.
