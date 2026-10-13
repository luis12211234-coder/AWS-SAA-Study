# 📘 13-04. CloudFront Geo Restriction

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Geo Restriction` · `Allow List` · `Block List` · `GeoIP` · `Copyright`

## 🇰🇷 1. Geo Restriction이란?

CloudFront Distribution에 접근할 수 있는 사용자를 국가 단위로 제한하는 기능입니다.

사용자의 IP 주소를 GeoIP Database와 매핑하여 국가를 판단합니다.

## 🇰🇷 2. Allow List

지정한 국가에서만 접근을 허용합니다.

```text
Allow List

USA      → Allow
India    → Allow

Other Countries
→ Block
```

## 🇰🇷 3. Block List

지정한 국가의 접근을 차단합니다.

```text
Block List

Country A → Block
Country B → Block

Other Countries
→ Allow
```

## 🇰🇷 4. 대표 사용 사례

대표적인 사용 사례는 콘텐츠의 국가별 라이선스 및 저작권 제한입니다.

```text
Content
+
Country Restriction
=
CloudFront Geo Restriction
```

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

```text
Geo Restriction
= 국가 단위 CloudFront 접근 제어

Allow List
= 지정 국가만 허용

Block List
= 지정 국가 차단

국가 판단
= 사용자 IP 기반
```

한 줄 정리:

> **CloudFront 콘텐츠를 국가별로 허용하거나 차단한다 = Geo Restriction**

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Q1. 특정 국가에서만 CloudFront 콘텐츠에 접근하도록 하려면?

<details>
<summary>정답 및 해설 보기</summary>

Geo Restriction의 Allow List를 사용할 수 있습니다.

</details>

### Q2. Q2. 특정 국가만 차단하려면?

<details>
<summary>정답 및 해설 보기</summary>

Block List를 사용할 수 있습니다.

</details>

### Q3. Geo Restriction이란?의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

CloudFront Distribution에 접근할 수 있는 사용자를 국가 단위로 제한하는 기능입니다.

</details>

### Q4. Allow List의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

지정한 국가에서만 접근을 허용합니다.

</details>

### Q5. Block List의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

지정한 국가의 접근을 차단합니다.

</details>

## 🇯🇵 日本語 Summary

CloudFront Geo Restrictionは、国単位でDistributionへのアクセスを制御する機能です。

Allow Listでは指定した国のみを許可し、Block Listでは指定した国を拒否できます。

主な利用例は、コンテンツのライセンスや著作権による地域制限です。

## 🇺🇸 English Summary

CloudFront Geo Restriction controls access to a distribution based on the user's country.

An allow list permits selected countries, while a block list denies selected countries.

A common use case is enforcing geographic content licensing restrictions.

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Geo Restriction | 地理的制限 | 지리적 제한 |
| Allow List | 許可リスト | 허용 목록 |
| Block List | ブロックリスト | 차단 목록 |
| GeoIP | GeoIP | 지리 IP |
| Copyright | 著作権 | 저작권 |
