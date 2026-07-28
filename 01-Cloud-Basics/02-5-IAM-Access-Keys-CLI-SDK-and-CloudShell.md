# IAM Access Keys, CLI, SDK, and CloudShell

## Overview

AWS에 접근하는 방법에는 Management Console, CLI, SDK가 있으며, 각 방식은 IAM 자격 증명과 권한을 사용하여 보호된다.

| Access Method | 사용 방법 | 인증 방식 |
|---|---|---|
| AWS Management Console | 웹 브라우저 | Password + MFA |
| AWS CLI | 명령줄 | Access Keys |
| AWS SDK | 애플리케이션 코드 | Access Keys 또는 AWS 자격 증명 |

---

## Access Keys

Access Key는 CLI 또는 SDK를 통해 AWS에 프로그래밍 방식으로 접근하기 위한 자격 증명이다.

- Access Key ID는 사용자 이름과 비슷한 역할을 한다.
- Secret Access Key는 비밀번호와 비슷한 역할을 한다.
- 사용자별로 자신의 Access Key를 생성하고 관리한다.
- 다른 사람과 공유하거나 공개 GitHub 저장소에 올리면 안 된다.
- Secret Access Key는 생성 시점에만 확인할 수 있다.

---

## AWS CLI

AWS CLI(Command Line Interface)는 명령줄에서 AWS 서비스를 관리하는 도구이다.

주요 특징

- AWS 서비스의 공개 API를 호출한다.
- 반복 작업을 스크립트로 자동화할 수 있다.
- AWS Management Console 대신 사용할 수 있다.
- 오픈 소스로 제공된다.

로컬 컴퓨터에서는 `aws configure` 명령으로 다음 정보를 설정할 수 있다.

- AWS Access Key ID
- AWS Secret Access Key
- Default Region Name
- Default Output Format

CLI에서 수행할 수 있는 작업은 해당 IAM User에게 부여된 권한에 따라 결정된다. 콘솔과 CLI에는 동일한 IAM Policy가 적용된다.

---

## AWS SDK

AWS SDK(Software Development Kit)는 애플리케이션 코드에서 AWS 서비스를 사용할 수 있게 해주는 언어별 라이브러리 모음이다.

지원 예시

- JavaScript
- Python
- Java
- Go
- C++
- .NET
- PHP
- Ruby
- Android 및 iOS
- IoT Device SDK

CLI는 터미널에서 사용하지만 SDK는 애플리케이션 코드 안에서 사용한다. 예를 들어 Python에서는 Boto3를 사용하여 AWS API를 호출할 수 있다.

---

## AWS CloudShell

AWS CloudShell은 AWS Management Console에서 사용할 수 있는 브라우저 기반 터미널이다.

특징

- AWS CLI가 미리 설치되어 있다.
- 별도의 로컬 설치가 필요 없다.
- 현재 로그인한 사용자의 자격 증명과 권한을 사용한다.
- 현재 선택한 Region이 기본 Region으로 설정된다.
- 파일을 저장할 수 있는 영구 저장 공간을 제공한다.
- 파일 업로드와 다운로드를 지원한다.
- 모든 Region에서 제공되는 것은 아니다.

---

## Summary

- Console, CLI, SDK는 AWS에 접근하는 주요 방법이다.
- Console은 Password와 MFA로 보호한다.
- CLI와 SDK는 프로그래밍 방식으로 AWS API에 접근한다.
- Access Key는 비밀번호처럼 안전하게 관리해야 한다.
- CLI는 명령 실행과 자동화에 사용한다.
- SDK는 애플리케이션 코드에서 사용한다.
- CloudShell은 브라우저에서 사용할 수 있는 AWS 터미널이다.

---

## Exam Notes

- Management Console은 Password와 MFA를 사용한다.
- CLI와 SDK는 Access Key를 이용한 프로그래밍 방식 접근을 지원한다.
- Secret Access Key는 절대 공유하면 안 된다.
- CLI와 Console에는 동일한 IAM 권한이 적용된다.
- CLI는 AWS 서비스의 공개 API를 호출한다.
- SDK는 애플리케이션 코드 내부에서 사용한다.
- CloudShell에는 AWS CLI가 미리 설치되어 있다.
- CloudShell은 로그인한 사용자의 권한을 사용한다.
- CloudShell은 모든 Region에서 지원되는 것은 아니다.

---

## Practical Example

IAM User에게 `iam:GetUser`와 `iam:ListUsers` 권한만 부여했다고 가정한다.

이 사용자는 콘솔과 CLI에서 IAM 사용자 정보를 조회할 수 있지만 사용자나 그룹을 생성할 수는 없다.

즉, Management Console과 CLI는 서로 다른 권한 체계를 사용하는 것이 아니라 동일한 IAM Policy를 따른다.

---

# 🇯🇵 日本語

## AWSへのアクセス方法

AWSへアクセスする主な方法は、Management Console、CLI、SDKの三つである。

Management ConsoleはパスワードとMFAで保護される。CLIとSDKでは、AWS APIへプログラムからアクセスできる。

---

## Access Key

Access KeyはCLIやSDKからAWSへアクセスするための認証情報である。

Secret Access Keyはパスワードと同様に管理し、他人と共有してはいけない。

---

## CLI、SDK、CloudShell

AWS CLIはコマンドラインからAWSサービスを操作するツールである。

AWS SDKはアプリケーションコードからAWSサービスを操作するためのライブラリである。

CloudShellを利用すると、ブラウザ上でAWS CLIを実行できる。

---

## Summary

- ConsoleはパスワードとMFAを使用する。
- CLIとSDKではAccess Keyを利用できる。
- Access Keyは秘密情報として管理する。
- CloudShellにはAWS CLIがあらかじめ用意されている。

---

# 🇺🇸 English

## Accessing AWS

There are three main ways to access AWS: the Management Console, AWS CLI, and AWS SDK.

The Management Console is protected by a password and MFA. The CLI and SDK provide programmatic access to AWS services.

---

## Access Keys

Access Keys consist of an Access Key ID and a Secret Access Key. The Secret Access Key must be protected like a password and must never be shared.

---

## CLI, SDK, and CloudShell

The AWS CLI manages AWS services through command-line commands. AWS SDKs allow applications to call AWS APIs programmatically.

AWS CloudShell is a browser-based shell with the AWS CLI already installed.

---

## Summary

- The Console uses a password and MFA.
- The CLI and SDK support programmatic access.
- Access Keys must remain secret.
- IAM permissions apply equally to the Console and CLI.
- CloudShell provides a preconfigured AWS CLI environment.

---

## Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Management Console | 관리 콘솔 | マネジメントコンソール |
| Command Line Interface | 명령줄 인터페이스 | コマンドラインインターフェース |
| Software Development Kit | 소프트웨어 개발 키트 | ソフトウェア開発キット |
| Programmatic Access | 프로그래밍 방식 접근 | プログラムによるアクセス |
| Access Key ID | 액세스 키 ID | アクセスキーID |
| Secret Access Key | 비밀 액세스 키 | シークレットアクセスキー |
| Credential | 자격 증명 | 認証情報 |
| Public API | 공개 API | パブリックAPI |
| Automation | 자동화 | 自動化 |
| CloudShell | 클라우드셸 | CloudShell |
| Default Region | 기본 리전 | デフォルトリージョン |

---

## Review Questions

1. AWS에 접근하는 세 가지 주요 방법은 무엇인가?
2. Management Console은 어떤 인증 방식으로 보호되는가?
3. Access Key ID와 Secret Access Key는 각각 무엇에 비유할 수 있는가?
4. AWS CLI와 AWS SDK의 차이점은 무엇인가?
5. IAM User의 권한을 제거하면 CLI 호출에는 어떤 영향이 있는가?
6. CloudShell의 장점은 무엇인가?
7. Secret Access Key를 GitHub에 올리면 안 되는 이유는 무엇인가?
