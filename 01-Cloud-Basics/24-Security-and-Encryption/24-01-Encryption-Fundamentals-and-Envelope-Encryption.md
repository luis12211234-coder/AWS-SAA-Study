# 📘 24-01. Encryption Fundamentals & Envelope Encryption

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Encryption in Transit` · `Encryption at Rest` · `Client-Side Encryption` · `Server-Side Encryption` · `Data Key` · `Envelope Encryption` · `Base64`

## 🇰🇷 1. 암호화가 필요한 이유와 암호화의 대상
데이터 보호에는 서로 다른 두 장면이 있다. **전송 중 암호화(Encryption in Transit)**는 브라우저 ↔ 웹 서버처럼 이동하는 데이터의 도청과 변조를 막는다. HTTPS는 TLS를 사용하며, TLS 인증서는 상대 서버의 신원을 검증하고 안전한 연결을 수립하는 데 사용된다. 인증서 자체가 모든 HTTP 본문을 직접 암호화하는 것은 아니다.

**저장 시 암호화(Encryption at Rest)**는 디스크, 객체 저장소, 데이터베이스에 기록된 데이터를 암호문으로 보관하는 방식이다. 예: S3 객체, EBS 볼륨, RDS 스토리지.

## 🇰🇷 2. 누가 암호화를 수행하는가
- **Server-Side Encryption(SSE):** 데이터를 서비스에 전달하면 S3 등 AWS 서비스가 저장할 때 암호화/읽을 때 복호화한다. 이용자에게는 적절한 권한과 서비스 API를 통해 평문이 반환될 수 있다.
- **Client-Side Encryption(CSE):** 프로그램이 AWS에 보내기 **전에** 암호화한다. S3나 DB에는 암호문만 전달된다. 서비스 운영자에게 평문을 노출하지 않으려면 키와 복호화 권한을 별도로 분리해야 한다.

중요한 차이는 암호화 시점과 **누가 복호화 키를 다루는가**다. 저장 시 암호화만 켰다고 모든 DB 관리자에게 데이터 내용이 완전히 숨겨지는 것은 아니다.

## 🇰🇷 3. KMS Key와 Data Key의 역할
대용량 객체를 KMS `Encrypt` API에 통째로 넣는 구조가 아니다. 대칭 KMS `Encrypt`는 일반적인 작은 평문(최대 4,096바이트)을 암호화하기 위한 API이다. 큰 파일은 **Data Key(데이터를 실제 암호화하는 대칭 키)**를 이용해 로컬 또는 서비스 내부에서 암호화한다.

**Envelope Encryption(봉투 암호화) 흐름**

```text
KMS Key (마스터 역할)
  └─ GenerateDataKey → 평문 Data Key + 암호화된 Data Key
                            │
평문 Data Key로 10GB 파일 암호화 ──┘
  ↓
암호문 파일 + 암호화된 Data Key 저장
  ↓
나중에 읽을 때: KMS Decrypt로 Data Key 복원 → 파일 복호화
```

평문 Data Key는 사용 후 메모리에서 제거하도록 설계한다. 저장된 암호화 Data Key는 KMS 권한이 없는 사람이 풀 수 없다. **KMS Key**는 Data Key를 보호하고 접근을 통제하는 상위 계층으로 이해하면 된다.

## 🇰🇷 4. Base64와 암호화는 별개다
**Base64는 바이너리 바이트를 텍스트로 나타내는 인코딩**이다. 예: `Hi` → `SGk=`. Base64를 디코딩해도 보안이 해제되는 것이 아니다. AWS CLI가 바이너리 `CiphertextBlob`을 Base64 문자열로 표시할 수 있지만, 진짜 복호화는 KMS `Decrypt` 단계에서 일어난다.

## 🇰🇷 5. 어떤 요구사항에 어떤 방식을 고르는가
- **인터넷 통신 보호**: HTTPS/TLS.
- **S3·EBS 등 저장 데이터 보호**: SSE와 KMS 통합.
- **AWS 서비스나 DB 관리자가 데이터의 평문을 보지 못하도록 설계**: 애플리케이션 측 CSE와 별도 키 접근 통제.
- **대용량 파일 암호화**: Data Key로 실제 데이터 암호화 + KMS로 Data Key 보호.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- TLS 인증서는 서버 인증과 TLS 연결에 사용되며, HTTPS = 전송 중 암호화다.
- SSE는 AWS 측 암호화, CSE는 업로드/저장 전 애플리케이션 측 암호화다.
- Envelope Encryption은 실제 데이터 암호화 키를 KMS Key로 보호하는 방식이다.
- Base64는 텍스트 표현 방식이지 암호화가 아니다.
- 대용량 파일을 KMS Encrypt API에 그대로 전달하는 설계가 아니다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. S3 저장 데이터에 대한 암호화는 필요하지만 앱 구현을 최소화하고 싶다. 우선 고려할 암호화 위치는?

<details>
<summary>정답 및 해설 보기</summary>

**Server-Side Encryption.** S3가 저장과 읽기 과정의 암호화 작업을 수행한다.

</details>

### Q2. 데이터베이스 관리자가 민감 속성의 원문을 보지 못하게 설계하려면 어디에서 암호화해야 하는가?

<details>
<summary>정답 및 해설 보기</summary>

**애플리케이션/클라이언트 측.** DB로 보내기 전에 암호화하고 복호화 권한도 관리자에게 부여하지 않는다.

</details>

### Q3. 10GB 영상의 암호화를 KMS Encrypt에 영상 전체를 넣어 수행하는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** Data Key로 대량 데이터를 암호화하고 KMS Key로 해당 Data Key를 보호한다.

</details>

### Q4. CLI가 반환한 Base64 CiphertextBlob을 Base64 디코딩하면 평문이 나타나는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 바이너리 암호문이 나올 뿐이며 KMS Decrypt가 필요하다.

</details>

### Q5. 브라우저와 ALB 사이의 암호화된 통신에 쓰는 프로토콜과 인증서는?

<details>
<summary>정답 및 해설 보기</summary>

**HTTPS/TLS와 TLS 인증서.** 저장 시 암호화와 달리 통신 중 정보를 보호한다.

</details>

## 🇯🇵 日本語 Summary

AWS では通信中の暗号化（TLS/HTTPS）と保存時の暗号化を区別する。SSE は AWS 側、CSE はアプリケーション側で暗号化する。Envelope Encryption では Data Key が実データを暗号化し、KMS Key が Data Key を保護する。Base64 は暗号化ではなくエンコード。

## 🇺🇸 English Summary

Distinguish encryption in transit (TLS/HTTPS) from encryption at rest. Server-side encryption is performed by AWS services; client-side encryption happens before uploading. Envelope encryption uses a data key for bulk data and a KMS key to protect that key. Base64 is encoding, not encryption.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Encryption in Transit | 전송 중 암호화 | 転送中の暗号化 |
| Encryption at Rest | 저장 시 암호화 | 保管時の暗号化 |
| Server-Side Encryption | 서버 측 암호화 | サーバー側暗号化 |
| Client-Side Encryption | 클라이언트 측 암호화 | クライアント側暗号化 |
| Data Key | 데이터 암호화 키 | データキー |
| Envelope Encryption | 봉투 암호화 | エンベロープ暗号化 |
| Base64 Encoding | Base64 인코딩 | Base64 エンコード |
