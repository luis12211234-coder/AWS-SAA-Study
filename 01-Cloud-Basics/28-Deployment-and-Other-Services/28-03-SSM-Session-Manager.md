# 📘 28-03. Systems Manager Session Manager

> [!NOTE]
> **AWS SAA-C03 Study Note** · GitHub Section 28 · Udemy Section 30  
> **Keywords:** SSM Agent, VPC Endpoint

## 🇰🇷 1. 핵심 개념

Session Manager는 SSH 키나 인바운드 22번 포트 없이 관리형 EC2에 Shell 접속하도록 돕는다.

## 🇰🇷 2. 동작 원리 및 사용 사례

EC2의 SSM Agent가 Systems Manager 엔드포인트와 HTTPS 443으로 통신한다. 사용자에게 ssm:StartSession, 인스턴스 측에는 SSM 관리 권한이 필요하다. 네트워크는 NAT 또는 Interface VPC Endpoint 등을 활용할 수 있다. Default Host Management Configuration을 이용할 수도 있다. 세션 내용의 S3/CloudWatch 로깅은 별도 설정이 필요하다.

## 🇰🇷 3. 서비스 선택 기준

EC2 Instance Connect는 SSH 방식이고 Session Manager는 SSM 통신을 사용한다. SSM에도 Agent·IAM·네트워크 연결이 필요하다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **핵심 역할:** Session Manager는 SSH 키나 인바운드 22번 포트 없이 관리형 EC2에 Shell 접속하도록 돕는다.
- **시험 선택 기준:** EC2 Instance Connect는 SSH 방식이고 Session Manager는 SSM 통신을 사용한다. SSM에도 Agent·IAM·네트워크 연결이 필요하다.
- **구현 시 주의점:** EC2의 SSM Agent가 Systems Manager 엔드포인트와 HTTPS 443으로 통신한다. 사용자에게 ssm:StartSession, 인스턴스 측에는 SSM 관리 권한이 필요하다. 네트워크는 NAT 또는 Interface VPC Endpoint 등을 활용할 수 있다. Default Host Management Configuration을 이용할 수도 있다. 세션 내용의 S3/CloudWatch 로깅은 별도 설정이 필요하다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 이 서비스의 핵심 목적은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

Session Manager는 SSH 키나 인바운드 22번 포트 없이 관리형 EC2에 Shell 접속하도록 돕는다.

</details>

### Q2. 대표적인 사용 사례를 하나 설명하라.

<details>
<summary>정답 및 해설 보기</summary>

EC2의 SSM Agent가 Systems Manager 엔드포인트와 HTTPS 443으로 통신한다. 사용자에게 ssm:StartSession, 인스턴스 측에는 SSM 관리 권한이 필요하다. 네트워크는 NAT 또는 Interface VPC Endpoint 등을 활용할 수 있다. Default Host Management Configuration을 이용할 수도 있다. 세션 내용의 S3/CloudWatch 로깅은 별도 설정이 필요하다.

</details>

### Q3. 어떤 요구사항에서 이 서비스를 선택하는가?

<details>
<summary>정답 및 해설 보기</summary>

EC2 Instance Connect는 SSH 방식이고 Session Manager는 SSM 통신을 사용한다. SSM에도 Agent·IAM·네트워크 연결이 필요하다.

</details>

### Q4. 구현 시 알아야 할 조건 또는 제한은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

EC2의 SSM Agent가 Systems Manager 엔드포인트와 HTTPS 443으로 통신한다. 사용자에게 ssm:StartSession, 인스턴스 측에는 SSM 관리 권한이 필요하다. 네트워크는 NAT 또는 Interface VPC Endpoint 등을 활용할 수 있다. Default Host Management Configuration을 이용할 수도 있다. 세션 내용의 S3/CloudWatch 로깅은 별도 설정이 필요하다.

</details>

### Q5. 시험에서 혼동하기 쉬운 기능과 어떻게 구분하는가?

<details>
<summary>정답 및 해설 보기</summary>

EC2 Instance Connect는 SSH 방식이고 Session Manager는 SSM 통신을 사용한다. SSM에도 Agent·IAM·네트워크 연결이 필요하다.

</details>

## 🇯🇵 日本語 Summary

Session Manager は SSH ポートを開けずに EC2 に接続できる。SSM Agent と IAM 権限が必要。

## 🇺🇸 English Summary

Session Manager offers shell access without inbound SSH. SSM Agent, permissions, and endpoint connectivity remain necessary.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| SSM Agent | 에이전트 | エージェント |
| VPC Endpoint | 엔드포인트 | エンドポイント |
