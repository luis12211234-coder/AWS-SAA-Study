# 17-07. Amazon Cognito

## 1. Amazon Cognito

Amazon Cognito는 **웹 및 모바일 애플리케이션 사용자의 인증과 AWS 리소스 접근을 지원하는 서비스**이다.

IAM과 구별해야 한다.

```text
IAM
→ AWS 환경을 사용하는 사용자/주체의 AWS 권한 관리

Cognito
→ 내가 만든 Web / Mobile App의 사용자 인증 및 접근
```

Cognito의 핵심 구성:

```text
Amazon Cognito
 │
 ├── User Pool
 │     └── 회원가입 / 로그인 / 인증
 │
 └── Identity Pool
       └── Temporary AWS Credentials
```

---

# 2. Cognito User Pool

User Pool은 웹/모바일 애플리케이션의 **사용자 회원가입과 로그인**을 처리한다.

```text
User
 ↓
Login
 ↓
Cognito User Pool
 ↓
Authentication
 ↓
Token
```

제공 기능:

- Username / Email + Password
- Password Reset
- Email / Phone Verification
- MFA
- Social Login
- Federated Identity Provider

Google, Facebook, SAML 등의 외부 Identity Provider와도 연동할 수 있다.

---

# 3. User Pool + API Gateway

로그인 후 발급받은 Token을 API Gateway에서 검증할 수 있다.

```text
User
 ↓ Login
Cognito User Pool
 ↓
Token
 ↓
API Gateway
 ↓
Backend
```

API Gateway가 인증을 처리하므로 Backend에서 모든 로그인 검증 로직을 직접 구현할 필요를 줄일 수 있다.

User Pool은 Application Load Balancer와도 통합할 수 있다.

---

# 4. Cognito Identity Pool

Identity Pool은 사용자에게 **Temporary AWS Credentials**를 제공한다.

```text
User
 ↓
Login
 ↓
Identity Pool
 ↓
Temporary AWS Credentials
 ↓
AWS Resource
```

이를 통해 애플리케이션 사용자가 S3, DynamoDB 등의 AWS 리소스에 접근할 수 있다.

---

# 5. User Pool + Identity Pool

둘을 함께 사용할 수 있다.

```text
User
 ↓
User Pool
 ↓
Authentication
 ↓
Token
 ↓
Identity Pool
 ↓
Temporary AWS Credentials
 ↓
S3 / DynamoDB
```

핵심 차이:

```text
User Pool
= "너 누구야?"
= Authentication

Identity Pool
= "확인됐으니 AWS 임시 출입증을 줄게."
= Temporary AWS Credentials
```

Identity Pool은 User Pool뿐 아니라 외부 Identity Provider에서 얻은 인증 정보도 사용할 수 있다.

---

# 6. IAM Roles

Identity Pool에서 발급되는 AWS Credentials에는 IAM 권한이 연결된다.

```text
Identity Pool
 ↓
IAM Role / Policy
 ↓
Temporary Credentials
 ↓
Allowed AWS Resources
```

인증된 사용자와 Guest 사용자 등에 서로 다른 Role을 적용할 수 있다.

---

# 7. DynamoDB Fine-Grained Access

Identity Pool의 사용자 정보와 IAM Policy 조건을 활용해 DynamoDB 접근을 세밀하게 제한할 수 있다.

예:

```text
DynamoDB

UserID | Data
-------|-------------
Bin    | Bin Data
Kim    | Kim Data
Lee    | Lee Data
```

사용자 Bin에게:

```text
Bin Data    ✅
Kim Data    ❌
Lee Data    ❌
```

처럼 자신의 데이터에만 접근하도록 제한할 수 있다.

즉:

```text
Identity Pool
       ↓
Temporary Credentials
       ↓
IAM Policy
       ↓
DynamoDB
       ↓
자신에게 허용된 Item만 접근
```

---

## 🎯 Exam Notes

가장 중요:

```text
Cognito User Pool
= Sign-up / Sign-in
= Authentication

Cognito Identity Pool
= Temporary AWS Credentials
= AWS Resource Access
```

시험 키워드:

```text
Web / Mobile Users
Authentication
Sign-up / Sign-in
→ Cognito User Pool
```

```text
Temporary AWS Credentials
Direct S3 / DynamoDB Access
Fine-Grained AWS Permissions
→ Cognito Identity Pool
```

IAM과 Cognito를 혼동하지 않는다.

## 💡 Practical Example

사진 앱:

```text
User
 ↓
Cognito User Pool
 ↓
Login
 ↓
Cognito Identity Pool
 ↓
Temporary AWS Credentials
 ↓
자신의 S3 Folder에 사진 Upload
```

사용자는 AWS 계정의 IAM User가 아니지만 제한된 임시 AWS 권한을 받아 필요한 리소스에 접근할 수 있다.

## 🇯🇵 日本語 Summary

Amazon Cognitoは、Web・モバイルアプリケーションのユーザー認証とAWSリソースへのアクセスを支援します。User Poolはサインアップ・サインインを担当し、Identity Poolは一時的なAWS認証情報を提供します。

## 🇺🇸 English Summary

Amazon Cognito provides identity features for web and mobile application users. User Pools handle sign-up and authentication, while Identity Pools provide temporary AWS credentials that allow users to access authorized AWS resources.

## 📚 Vocabulary

| Term | Meaning |
|---|---|
| User Pool | 앱 사용자의 회원가입 및 인증을 관리 |
| Identity Pool | 사용자에게 임시 AWS Credentials를 제공 |
| Authentication | 사용자가 누구인지 확인 |
| Credentials | AWS 접근에 사용하는 자격 증명 |
| Federation | 외부 Identity Provider와 인증을 연동하는 방식 |
| MFA | Multi-Factor Authentication |
| Fine-Grained Access | 사용자별로 세밀하게 제한된 접근 권한 |

## 📝 Review Questions

### Q1. 모바일 앱의 회원가입과 로그인을 구현하려면?

<details>
<summary>정답 보기</summary>

Cognito User Pool

</details>

### Q2. 로그인한 모바일 사용자가 S3에 직접 접근할 수 있도록 임시 AWS Credentials를 제공하려면?

<details>
<summary>정답 보기</summary>

Cognito Identity Pool

</details>

### Q3. User Pool과 Identity Pool의 핵심 차이는?

<details>
<summary>정답 보기</summary>

User Pool은 사용자 회원가입과 인증을 담당하고, Identity Pool은 사용자에게 AWS 리소스 접근에 사용할 Temporary AWS Credentials를 제공한다.

</details>