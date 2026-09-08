# IAM Password Policy and MFA

## Overview

IAM 사용자와 Root User를 보호하기 위해 AWS에서는 비밀번호 정책(Password Policy)과 다중 인증(Multi-Factor Authentication, MFA)을 사용할 수 있다.

비밀번호 정책은 강력한 비밀번호 사용을 요구하며, MFA는 비밀번호 외에 추가 인증 수단을 사용하여 계정 보안을 강화한다.

---

## IAM Password Policy

IAM Password Policy는 IAM 사용자의 비밀번호에 적용할 보안 규칙이다.

다음과 같은 항목을 설정할 수 있다.

- 최소 비밀번호 길이
- 대문자 포함
- 소문자 포함
- 숫자 포함
- 특수 문자 포함
- 사용자의 비밀번호 변경 허용
- 일정 기간 후 비밀번호 만료
- 이전 비밀번호 재사용 방지

강력한 비밀번호 정책은 비밀번호 추측 및 무차별 대입 공격(Brute-Force Attack)의 위험을 줄이는 데 도움이 된다.

---

## Multi-Factor Authentication (MFA)

MFA는 두 가지 이상의 인증 요소를 함께 사용하는 보안 방식이다.

AWS에서는 일반적으로 다음 두 요소를 사용한다.

```text
사용자가 알고 있는 것
Password

+

사용자가 소유한 것
MFA Device
```

비밀번호가 유출되더라도 공격자가 MFA 장치까지 가지고 있지 않으면 계정에 로그인하기 어렵다.

AWS에서는 특히 다음 계정에 MFA를 설정하는 것이 권장된다.

- Root User
- 관리자 권한을 가진 IAM User
- 중요한 리소스에 접근할 수 있는 IAM User

---

## MFA Device Options

### Virtual MFA Device

스마트폰의 인증 애플리케이션을 사용하는 방식이다.

예시

- Google Authenticator
- Authy

인증 애플리케이션은 일정 시간마다 변경되는 일회용 인증 코드(TOTP)를 생성한다.

### Security Key

물리적인 보안 키를 사용하는 방식이다.

예시

- YubiKey

하나의 보안 키를 여러 Root User 및 IAM User에 사용할 수 있다.

### Hardware TOTP Token

일회용 인증 코드를 생성하는 전용 하드웨어 장치이다.

AWS GovCloud 환경에서도 호환되는 별도의 하드웨어 토큰을 사용할 수 있다.

---

## Enabling MFA

MFA를 설정하는 기본 흐름은 다음과 같다.

```text
Security Credentials
↓
Assign MFA Device
↓
MFA 장치 유형 선택
↓
QR Code 스캔
↓
연속된 MFA Code 입력
↓
MFA 등록 완료
```

MFA 설정 후 로그인할 때는 비밀번호와 인증 애플리케이션에서 생성된 MFA Code를 모두 입력해야 한다.

MFA 장치에 접근할 수 없으면 계정 로그인이 어려워질 수 있으므로 장치의 분실과 복구 방법에도 주의해야 한다.

---

## Summary

- 비밀번호 정책은 IAM 사용자의 비밀번호 보안 규칙을 정의한다.
- 비밀번호의 길이와 문자 유형을 지정할 수 있다.
- 비밀번호 만료와 재사용 방지를 설정할 수 있다.
- MFA는 비밀번호와 보안 장치를 함께 사용하는 인증 방식이다.
- 비밀번호가 유출되어도 MFA를 통해 계정을 추가로 보호할 수 있다.
- Root User와 관리자 IAM User에는 MFA 설정이 강력히 권장된다.

---

## Exam Notes

- 강력한 비밀번호는 계정 보안을 강화한다.
- IAM Password Policy는 IAM 사용자에게 적용된다.
- 비밀번호 정책으로 최소 길이, 문자 유형, 만료 및 재사용 방지를 설정할 수 있다.
- MFA는 `Password + Security Device`의 조합이다.
- Root User에는 MFA를 활성화해야 한다.
- MFA의 가장 큰 장점은 비밀번호가 유출되어도 계정을 추가로 보호한다는 것이다.
- Virtual MFA Device는 하나의 장치에서 여러 계정의 토큰을 관리할 수 있다.
- Security Key는 여러 Root User 및 IAM User를 지원할 수 있다.
- MFA 장치를 분실하면 계정 접근에 문제가 생길 수 있다.

---

## Practical Example

관리자 IAM User의 비밀번호가 공격자에게 유출되었다고 가정한다.

```text
공격자
↓
Password 획득
↓
MFA Code 없음
↓
로그인 실패
```

MFA가 활성화되어 있다면 공격자는 비밀번호뿐만 아니라 사용자의 MFA 장치에서 생성되는 인증 코드도 필요하다.

따라서 비밀번호 유출만으로는 계정에 로그인하기 어렵다.

---

# 🇯🇵 日本語

## パスワードポリシー

IAM Password Policyは、IAMユーザーのパスワードに適用するセキュリティルールである。

次のような設定ができる。

- パスワードの最小文字数
- 大文字・小文字・数字・特殊文字の使用
- パスワードの有効期限
- 過去に使用したパスワードの再利用防止
- ユーザーによるパスワード変更の許可

---

## 多要素認証（MFA）

MFAは、パスワードとセキュリティデバイスを組み合わせる認証方式である。

```text
知っているもの
パスワード

+

所有しているもの
MFAデバイス
```

パスワードが盗まれても、MFAデバイスがなければログインが困難になる。

Root Userと管理者権限を持つIAM Userには、MFAを設定することが推奨される。

---

## MFAデバイス

- 仮想MFAデバイス
- セキュリティキー
- ハードウェアTOTPトークン

仮想MFAデバイスでは、認証アプリが一定時間ごとにワンタイムコードを生成する。

---

## Summary

- パスワードポリシーはパスワードのセキュリティルールを定義する。
- MFAはパスワードとセキュリティデバイスを組み合わせる。
- Root UserにはMFAの設定が強く推奨される。
- パスワードが漏洩しても、MFAによってアカウントを保護できる。

---

# 🇺🇸 English

## Password Policy

An IAM Password Policy defines password security requirements for IAM Users.

It can enforce:

- Minimum password length
- Uppercase and lowercase letters
- Numbers and special characters
- Password expiration
- Password reuse prevention
- Permission for users to change their passwords

---

## Multi-Factor Authentication (MFA)

MFA combines two authentication factors:

```text
Something you know
Password

+

Something you own
MFA Device
```

If a password is stolen, an attacker still needs access to the MFA device.

MFA is strongly recommended for the Root User and IAM Users with administrative privileges.

---

## MFA Devices

- Virtual MFA Device
- Security Key
- Hardware TOTP Token

A virtual MFA application generates a time-based one-time password.

---

## Summary

- A Password Policy defines password requirements.
- MFA combines a password with a security device.
- Enable MFA for the Root User.
- MFA protects an account even when its password is compromised.

---

## Vocabulary

| English | 한국어 | 日本語 |
|----------|---------|---------|
| Password Policy | 비밀번호 정책 | パスワードポリシー |
| Multi-Factor Authentication | 다중 인증 | 多要素認証 |
| MFA Device | MFA 장치 | MFAデバイス |
| Virtual MFA Device | 가상 MFA 장치 | 仮想MFAデバイス |
| Security Key | 보안 키 | セキュリティキー |
| Hardware Token | 하드웨어 토큰 | ハードウェアトークン |
| One-Time Password | 일회용 비밀번호 | ワンタイムパスワード |
| Password Expiration | 비밀번호 만료 | パスワードの有効期限 |
| Password Reuse | 비밀번호 재사용 | パスワードの再利用 |
| Special Character | 특수 문자 | 特殊文字 |
| Brute-Force Attack | 무차별 대입 공격 | ブルートフォース攻撃 |
| Compromised | 침해된, 유출된 | 侵害された |
| Security Credentials | 보안 자격 증명 | セキュリティ認証情報 |

---

## Review Questions

1. IAM Password Policy에서 설정할 수 있는 항목은 무엇인가?
2. 비밀번호 재사용 방지는 어떤 기능인가?
3. MFA가 사용하는 두 가지 인증 요소는 무엇인가?
4. 비밀번호가 유출되어도 MFA가 계정을 보호할 수 있는 이유는 무엇인가?
5. MFA를 우선적으로 설정해야 하는 사용자는 누구인가?
6. Virtual MFA Device와 Security Key의 차이점은 무엇인가?
7. MFA 장치에 접근할 수 없게 되면 어떤 문제가 발생할 수 있는가?
