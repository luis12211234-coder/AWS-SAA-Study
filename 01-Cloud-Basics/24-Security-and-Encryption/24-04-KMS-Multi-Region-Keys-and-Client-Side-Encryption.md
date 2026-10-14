# 📘 24-04. KMS Multi-Region Keys & Client-Side DB Encryption

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Multi-Region Key` · `MRK` · `Primary Key` · `Replica Key` · `DynamoDB Global Tables` · `Aurora Global Database` · `Client-Side Encryption`

## 🇰🇷 1. 일반 KMS Key의 리전 제한에서 출발
일반적인 KMS Key는 리전별 리소스다. 미국의 키로 암호화한 클라이언트 측 데이터가 일본으로 복제되었더라도 일본의 **무관한 별도 KMS Key**가 같은 암호문을 자동으로 복호화할 수는 없다. 다중 리전 애플리케이션은 이를 위한 키 설계가 필요하다.

## 🇰🇷 2. Multi-Region Key(MRK)의 구조
**Primary MRK**를 생성하고 다른 AWS 리전에 **Replica MRK**를 만들 수 있다. 두 키는 같은 Key ID 접두사(`mrk-...`)와 동일한 암호화 키 재료를 공유하므로 **호환되는 MRK 암호문을 리전별로 복호화**할 수 있다. 키 재료의 교체가 관련 리전에 동기화된다.

하지만 **Key Policy, IAM 권한, Alias 등 지역별 관리 속성은 독립적으로 관리**한다. MRK는 전 세계에서 하나의 글로벌 API 엔드포인트처럼 쓰는 키가 아니라 **리전마다 별도 KMS 리소스**를 보유하는 구조다.

## 🇰🇷 3. DynamoDB Global Tables와 Aurora Global Database에 적용
예를 들어 글로벌 서비스의 사용자 주민등록번호를 Aurora Global Database에 저장한다.

```text
미국 App ── 클라이언트 측 암호화(MRK Primary) ──> Aurora 미국
                                                      │
                                              암호문 데이터 복제
                                                      ↓
일본 App <── 클라이언트 측 복호화(MRK Replica) <── Aurora 일본
```

**중요:** Aurora/DynamoDB가 이 민감 속성을 알아서 클라이언트 측 암호화해 주는 것이 아니다. **애플리케이션/SDK가 DB에 기록하기 전에 선택한 속성을 암호화**한다. 데이터 복제는 DB의 기능이고 MRK 복제는 KMS의 기능이다. DB 관리자가 복호화 권한이 없다면 저장된 암호문만 볼 수 있다.

## 🇰🇷 4. MRK의 실제 이점과 오해
- 해외 애플리케이션이 원본 리전의 KMS까지 왕복하지 않고 **현지 리전 MRK**로 호환 암호문을 복호화할 수 있다.
- 리전 사이에서 애플리케이션 암호문을 **반드시 다시 암호화할 필요는 없다.**
- 하지만 **모든 AWS 서비스가 MRK의 이러한 특징을 활용하는 것은 아니다.** 대표 반례가 Amazon S3 Replication이다.

## 🇰🇷 5. 시험 선택 기준
`Aurora Global Database/DynamoDB Global Tables` + `민감 속성` + `클라이언트 측 암호화` + `다중 리전 읽기` → **KMS MRK**를 고려한다. CloudHSM은 고객 전용 HSM을 제공하는 다른 목적의 서비스로, MRK와 같은 리전 간 호환성 기능의 단순 대체재가 아니다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- MRK는 동일 암호화 키 재료를 공유하지만 각 리전 Key Policy/권한은 독립적이다.
- Aurora Global Database는 데이터를 복제, 애플리케이션이 CSE 수행, KMS MRK는 복호화 호환성 제공.
- DB 관리자가 민감정보 평문을 보지 못하게 하려면 암호화 시점과 KMS 권한 분리가 중요.
- MRK를 사용하더라도 AWS 서비스별 동작 차이가 있다. S3 Replication 예외 유의.
- MRK = 단일 글로벌 키 엔드포인트라는 이해는 잘못이다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 미국에서 앱이 암호화한 민감정보를 일본에서 지연을 줄여 복호화하려면 어떤 KMS 키 유형이 유용한가?

<details>
<summary>정답 및 해설 보기</summary>

**KMS Multi-Region Key.** 호환되는 복제 키로 리전 로컬 복호화를 수행할 수 있다.

</details>

### Q2. MRK Replica를 만든 뒤 일본 Key Policy도 자동으로 미국과 완전히 동일해지는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 리전별 권한과 정책은 별도로 관리한다.

</details>

### Q3. Aurora Global Database에 저장된 주민등록번호가 자동으로 클라이언트 측 암호화되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 애플리케이션이 저장 전에 암호화하도록 구현해야 한다.

</details>

### Q4. DB 관리자에게 복호화 권한이 없다. 앱이 저장 전에 민감 열을 암호화한 경우 관리자는 평문을 볼 수 있는가?

<details>
<summary>정답 및 해설 보기</summary>

**암호화된 열의 평문을 볼 수 없다.** 앱에서 암호화하고 관리자에게 키 권한을 주지 않았기 때문이다.

</details>

### Q5. Global Tables와 MRK가 모두 필요한 이유는?

<details>
<summary>정답 및 해설 보기</summary>

**DB는 데이터 복제, MRK는 암호화 키의 리전 간 호환성**을 제공하므로 역할이 서로 다르다.

</details>

## 🇯🇵 日本語 Summary

KMS Multi-Region Key は各リージョンに Primary/Replica を持ち、同一キーマテリアルで互換性のある暗号文を復号できる。権限と Key Policy はリージョンごとに独立。DynamoDB Global Tables や Aurora Global Database の機密属性をクライアント側で暗号化する設計に有用。

## 🇺🇸 English Summary

KMS multi-Region keys provide compatible key material in separate Regions, enabling regional cryptographic operations on compatible ciphertext. Policies are Region-specific. Applications must implement client-side encryption before writing sensitive attributes to DynamoDB Global Tables or Aurora Global Database.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Multi-Region Key | 다중 리전 키 | マルチリージョンキー |
| Primary Key | 기본 키 | プライマリキー |
| Replica Key | 복제 키 | レプリカキー |
| Key Material | 키 재료 | キーマテリアル |
| Client-Side Encryption | 클라이언트 측 암호화 | クライアント側暗号化 |
| Global Database | 글로벌 데이터베이스 | グローバルデータベース |
