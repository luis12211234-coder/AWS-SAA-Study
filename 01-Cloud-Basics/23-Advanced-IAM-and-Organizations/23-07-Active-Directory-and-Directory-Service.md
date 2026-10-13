# 📘 23-07. Microsoft Active Directory & AWS Directory Service

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Microsoft Active Directory` · `Domain Controller` · `AWS Managed Microsoft AD` · `Trust Relationship` · `AD Connector` · `Simple AD` · `Identity Center`

## 🇰🇷 1. Microsoft Active Directory(AD)부터 이해하기

**Microsoft Active Directory Domain Services(AD DS)**는 Microsoft의 기업용 디렉터리·인증 기술이다. AWS 전용 서비스가 아니라 Windows 기업 환경에서 오래 사용해 온 체계다.

- **Directory Object:** User, Computer, Printer, Security Group 등의 정보.
- **Domain Controller(DC):** 로그인 인증 및 디렉터리 정보를 제공하는 서버.
- **Domain:** 공통 디렉터리와 인증 체계를 사용하는 관리 범위.
- **Forest:** 여러 도메인 트리를 포함할 수 있는 최상위 논리 경계.

회사 직원 John의 계정을 도메인 컨트롤러에서 만들고 회사 PC를 도메인에 가입시키면, John은 승인된 회사 컴퓨터에 **같은 AD 계정**으로 로그인할 수 있다. 다만 중앙 인증이 된다고 **모든 컴퓨터와 파일에 대한 사용 권한까지 자동 허용되는 것은 아니다**.

## 🇰🇷 2. AWS Managed Microsoft AD: AWS에 '진짜 AD' 만들기

**AWS Managed Microsoft AD**는 AWS가 관리형 Microsoft AD 도메인 컨트롤러를 AWS VPC에 제공하는 서비스다. AWS에서 사용자·그룹·도메인 정책을 관리하며 Windows EC2, FSx for Windows File Server, WorkSpaces 등에서 사용할 수 있다.

```text
AWS VPC
  ├─ AWS Managed Microsoft AD (독립적인 AD)
  ├─ Windows EC2 A (도메인 가입)
  └─ Windows EC2 B (도메인 가입)
```

- **기존 회사 AD가 없어도** AWS에서 Windows 서버·파일 서버를 중앙 인증하고 싶으면 구축할 수 있다.
- 기존 온프레미스 AD가 있으면 네트워크(VPN / Direct Connect 등), DNS와 **Trust Relationship(신뢰 관계)**을 구성해 양쪽 도메인의 인증을 활용할 수 있다.
- **Trust는 개별 직원을 1:1 복사·매칭하는 작업이 아니다.** 한쪽 AD가 인증한 신원을 다른 쪽 AD가 신뢰하는 체계이며, 실제 리소스 접근 권한은 별도로 검사한다.
- **Standard Edition:** 약 **30,000 Directory Objects**, **Enterprise Edition:** 약 **500,000 Objects** 수준을 지원(대략적 규모이며 실제 처리량 등에 따라 다름).

## 🇰🇷 3. AD Connector: 기존 회사 AD로 프록시

**AD Connector**는 AWS에 새로운 AD 사용자를 저장하는 디렉터리가 아니라, **AWS 서비스의 디렉터리 요청을 온프레미스 AD로 전달하는 프록시**다.

```text
AWS 서비스 → AD Connector → 회사 내부 Microsoft AD
                  (사용자·비밀번호·그룹은 회사 AD에서 관리)
```

- AWS 클라우드에 사용자·그룹을 복제하거나 저장하지 않는다.
- 기존 온프레미스 AD를 **그대로 Identity Source**로 활용하려는 상황에 적합.
- **Small / Large AD Connector**는 작업량에 맞춘 처리 용량 선택지. **500명/5,000명을 강제하는 사용자 수 한도가 아니다.** AWS 공식 문서상 강제된 사용자·연결 수 제한은 없다.
- AWS와 온프레미스 AD 간 통신 가능한 네트워크·DNS 구성이 중요하다.

## 🇰🇷 4. Simple AD: 간단한 AD 호환 디렉터리

**Simple AD**는 실제 Microsoft AD가 아니라 **Samba 4 기반 AD 호환 독립 디렉터리**이다. 기본 계정·그룹·도메인 가입 등을 지원하지만 Microsoft AD의 고급 기능과 **AD 간 Trust Relationship**은 지원하지 않는다.

- Small은 약 500 Users, Large는 약 5,000 Users를 기준으로 설계되었다. 이는 **Simple AD의 규모**이지 AD Connector의 인원 제한과 혼동하면 안 된다.
- **중요 변경(2026-07-30): Simple AD는 신규 고객에게 더 이상 제공되지 않는다.** 강의의 역사적 비교를 이해하되 신규 구축 대안은 AWS Managed Microsoft AD 등으로 검토한다.
- **IAM Identity Center의 AD Identity Source로 Simple AD를 선택하는 방식은 지원되지 않는다.**

## 🇰🇷 5. AWS Directory Service 3종 선택표

| 질문 | Managed Microsoft AD | AD Connector | Simple AD |
|---|---|---|---|
| AWS 내부에 디렉터리 생성? | **실제 Microsoft AD 생성** | **아니오, 프록시** | **AD 호환 디렉터리 생성** |
| 사용자·그룹 관리 위치 | AWS Managed AD | 온프레미스 AD | AWS Simple AD |
| 기존 AD와 신뢰 관계 | 지원 | 신뢰가 아니라 프록시 | 미지원 |
| IAM Identity Center용 AD 소스 | 지원 | 지원 | 미지원 |
| 신규 고객 가용성 | 가능 | 가능 | **2026-07-30 이후 불가** |

## 🇰🇷 6. IAM Identity Center(SSO)와 연결

회사 직원이 **기존 회사 AD 계정으로 AWS SSO**를 사용하고 싶다면 두 경로가 대표적이다.

**A. 회사 내부 AD → AD Connector → Identity Center**

직원 인증 요청을 온프레미스 AD로 전달한다. AWS에 새로운 Microsoft AD를 생성할 필요가 없다.

**B. 회사 내부 AD ↔ (양방향 Trust) AWS Managed Microsoft AD → Identity Center**

AWS에도 독립 AD를 두고 회사 AD와 양방향 신뢰 관계를 맺는다. **Identity Center에서 온프레미스 AD를 Trusted Domain으로 사용하려면 양방향 Trust가 필요하다.**

공통적으로 Identity Center는 **직원 인증을 연결**하고, AWS API 권한은 **Account Assignment + Permission Set + 대상 IAM Role**로 별도 결정한다.

```text
기존 사내 AD의 John
    → AD Connector 또는 Managed AD Trust
    → Identity Center에 인증
    → Dev Account + ReadOnly Permission Set
    → AWSReservedSSO Role로 AWS 콘솔 사용
```

**공식 참고:** [Directory Service Overview](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/what_is.html), [AD Connector](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_ad_connector.html), [Simple AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_simple_ad.html), [Identity Center & Self-managed AD](https://docs.aws.amazon.com/singlesignon/latest/userguide/connectonpremad.html).

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **Microsoft AD는 AWS와 별개**로 존재하는 Microsoft의 기업용 중앙 인증/디렉터리 기술.
- **Managed Microsoft AD:** AWS 안에 실제 Microsoft AD 생성, 온프레미스 AD와 Trust 구성 가능.
- **AD Connector:** 새 디렉터리가 아니라 기존 온프레미스 AD에 요청을 프록시. 강제 사용자 수 제한 없음.
- **Simple AD:** Samba 기반 독립 AD 호환 디렉터리. AD Trust 미지원, **2026-07-30부터 신규 고객 가입 불가**.
- 온프레미스 AD를 Identity Center와 연결: **AD Connector 또는 Managed Microsoft AD + 양방향 Trust**.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Windows EC2 20대를 같은 도메인으로 가입시키고 AWS 내부에서 Microsoft AD 자체를 운영하고 싶다. 무엇을 선택할까?

<details>
<summary>정답 및 해설 보기</summary>

**AWS Managed Microsoft AD.** AWS VPC에 관리형 AD를 생성해 Windows 서버의 인증·그룹 정책을 중앙 관리한다.

</details>

### Q2. 회사의 온프레미스 Microsoft AD를 유지하며 AWS에서 인증 요청만 전달하려면?

<details>
<summary>정답 및 해설 보기</summary>

**AD Connector.** 기존 온프레미스 AD로 디렉터리 요청을 프록시하고 AWS에 별도 사용자 디렉터리를 만들지 않는다.

</details>

### Q3. 온프레미스 AD와 AWS Managed Microsoft AD 사이에 Trust가 생기면 John의 계정을 AWS에 똑같이 복사해야 할까?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** Trust는 상대 도메인의 인증을 신뢰하는 구성이지 사용자 계정의 자동 복제가 아니다. 접근 권한도 별도다.

</details>

### Q4. AWS Managed Microsoft AD를 거쳐 온프레미스 AD 사용자에게 Identity Center SSO를 제공하려면 어느 방향의 신뢰가 필요한가?

<details>
<summary>정답 및 해설 보기</summary>

**양방향 Trust.** Identity Center가 사용자를 인증하고 사용자/그룹 메타데이터를 읽어야 하므로 이 통합 시나리오에는 양방향 신뢰가 필요하다.

</details>

### Q5. 강의에서 Simple AD와 AD Connector 각각 500/5,000 사용자 수 제한으로 암기해도 되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 500/5,000 규모는 Simple AD Small/Large의 대표 수치다. AD Connector Small/Large는 처리량 분류이며 강제 사용자 수 제한이 없다. Simple AD는 2026-07-30 이후 신규 고객에게 제공되지 않는다.

</details>

## 🇯🇵 日本語 Summary

Microsoft Active Directory は AWS とは独立した企業向けディレクトリ／認証技術。AWS Managed Microsoft AD は AWS に本物の AD を作成し、既存 AD と Trust Relationship を構成できる。AD Connector はオンプレミス AD へのプロキシでありユーザーを AWS 側にコピーしない。Simple AD は Samba ベースの互換ディレクトリだが、2026-07-30 以降新規顧客には提供されない。

**試験の判断:** AWS 上に Microsoft AD が必要なら Managed AD、既存 AD への転送なら AD Connector。Identity Center と既存 AD の連携は Connector または Managed AD の双方向 Trust。

## 🇺🇸 English Summary

Microsoft Active Directory is a Microsoft technology for centralized users, computers, and domain authentication. AWS Managed Microsoft AD creates an actual managed AD in AWS and supports trust relationships. AD Connector proxies directory requests to on-premises AD without copying users. Simple AD is a Samba-based compatible directory but has been closed to new customers since July 30, 2026. IAM Identity Center can integrate with existing AD through AD Connector or a two-way trust with AWS Managed Microsoft AD.

**Exam decision:** New AD in AWS versus proxy to an existing AD is the core distinction.

## 📚 Vocabulary

> GitHub 검색 시 영어 표제어 그대로 검색하면 관련 설명으로 이동하기 쉽다.

| English | 한국어 | 日本語 |
|---|---|---|
| Active Directory (AD) | 액티브 디렉터리 | Active Directory |
| Domain Controller (DC) | 도메인 컨트롤러 | ドメインコントローラー |
| Directory Object | 디렉터리 객체 | ディレクトリオブジェクト |
| Domain | 도메인 | ドメイン |
| Forest | 포리스트 | フォレスト |
| AWS Managed Microsoft AD | AWS 관리형 Microsoft AD | AWS Managed Microsoft AD |
| Trust Relationship | 신뢰 관계 | 信頼関係 |
| Two-Way Trust | 양방향 신뢰 | 双方向の信頼 |
| AD Connector | AD 연결 프록시 | AD Connector |
| Simple AD | 간이 AD 호환 디렉터리 | Simple AD |
| Directory Proxy | 디렉터리 프록시 | ディレクトリプロキシ |
| On-Premises | 사내 구축 환경 | オンプレミス |
