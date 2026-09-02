# Launching an EC2 Instance

## Overview

이번 실습에서는 AWS Management Console을 사용하여 Amazon Linux 기반의 첫 번째 EC2 Instance를 생성한다.

EC2 Instance를 생성하면서 AMI, Instance Type, Key Pair, Security Group, Storage, User Data와 같은 주요 설정을 살펴보고, User Data를 이용해 간단한 웹 서버를 자동으로 구성한다.

마지막으로 EC2 Instance의 Start, Stop, Terminate 동작과 Public IP의 변화를 확인한다.

---

## Launching an EC2 Instance

EC2 Console에서 다음 경로를 통해 새로운 인스턴스를 생성할 수 있다.

```text
EC2 Console
↓
Instances
↓
Launch instances
```

실습에서는 인스턴스 이름을 다음과 같이 설정한다.

```text
My First Instance
```

인스턴스 이름은 실제로 AWS Resource에 적용되는 Name Tag 역할을 한다.

---

## Amazon Machine Image (AMI)

AMI(Amazon Machine Image)는 EC2 Instance를 생성할 때 사용하는 기본 이미지이다.

AMI에는 운영 체제 및 기본 소프트웨어 환경이 포함될 수 있다.

이번 실습에서는 AWS가 Quick Start로 제공하는 Amazon Linux를 사용한다.

```text
AMI
↓
Amazon Linux
↓
64-bit (x86)
```

추후에는 직접 Custom AMI를 생성하여 사용할 수도 있다.

---

## Instance Type

Instance Type은 EC2 Instance가 사용할 컴퓨팅 자원의 크기를 결정한다.

주요 요소는 다음과 같다.

- CPU
- Memory
- Network Performance
- Cost

이번 강의에서는 다음 Instance Type을 사용한다.

```text
t2.micro
```

> 강의에서는 `t2.micro`를 Free Tier 예제로 사용한다. Free Tier 대상과 조건은 계정 및 시점에 따라 달라질 수 있으므로 실제 실습 시 AWS Console의 표시를 확인한다.

---

## Key Pair

Key Pair는 SSH를 이용하여 EC2 Instance에 접속할 때 사용한다.

실습에서는 새로운 Key Pair를 생성한다.

```text
Key Pair Name: EC2 Tutorial
Key Pair Type: RSA
```

### Key Pair File Format

강의에서 사용하는 기준은 다음과 같다.

```text
Mac / Linux / Windows 10+
→ .pem

Older Windows + PuTTY
→ .ppk
```

Key Pair 파일은 EC2 Instance에 접근할 때 중요한 인증 정보이므로 안전하게 보관해야 한다.

---

## Network Settings

이번 실습에서는 기본 Network 설정을 사용한다.

EC2 Instance에는 Public IPv4 Address가 할당되며, 외부에서 인스턴스에 접근할 수 있다.

또한 EC2 Instance에는 Security Group이 연결된다.

---

## Security Group

Security Group은 EC2 Instance의 네트워크 트래픽을 제어하는 방화벽 역할을 한다.

실습에서는 두 개의 Inbound Rule을 사용한다.

### SSH

```text
Protocol: SSH
Port: 22
Source: Anywhere
```

SSH는 EC2 Instance에 원격으로 접속하기 위해 사용한다.

### HTTP

```text
Protocol: HTTP
Port: 80
Source: Anywhere
```

HTTP를 허용하는 이유는 EC2 Instance에 웹 서버를 실행하고 인터넷에서 접속하기 위해서이다.

이번 실습에서는 HTTPS를 사용하지 않는다.

---

## Storage

실습에서는 EC2 Instance에 하나의 Root EBS Volume을 연결한다.

강의 예시:

```text
Volume Type: gp2
Size: 8 GB
```

Advanced 설정에서 다음 속성을 확인할 수 있다.

```text
Delete on Termination: Yes
```

즉, EC2 Instance를 Terminate하면 해당 Root EBS Volume도 함께 삭제된다.

---

## EC2 User Data

EC2 User Data는 인스턴스가 처음 생성될 때 실행할 명령 또는 Script를 전달하는 기능이다.

이번 실습에서는 User Data를 이용하여 웹 서버를 자동으로 설치하고 구성한다.

```text
Launch EC2
↓
User Data 실행
↓
System Update
↓
HTTP Web Server 설치
↓
HTML 파일 생성
↓
Web Server 실행
```

User Data Script는 인스턴스의 **최초 시작 시 한 번 실행**된다.

이를 Bootstrapping이라고 한다.

---

## Web Server with User Data

User Data Script에서는 다음과 같은 작업을 수행한다.

- 시스템 업데이트
- HTTP Web Server 설치
- HTML 파일 생성
- 간단한 Hello World 페이지 제공

인스턴스 생성 이후 Public IPv4 Address를 이용하여 웹 서버에 접근할 수 있다.

```text
http://<Public-IPv4>
```

이번 실습에서는 HTTPS가 아닌 HTTP를 사용한다.

따라서

```text
https://
```

가 아니라

```text
http://
```

를 사용해야 한다.

---

## Public IP vs Private IP

EC2 Instance에는 Public IPv4 Address와 Private IPv4 Address가 존재한다.

### Public IPv4

인터넷에서 EC2 Instance에 접근할 때 사용한다.

예:

```text
Browser
↓
Public IPv4
↓
EC2 Instance
```

### Private IPv4

AWS 내부 네트워크에서 EC2 Instance를 식별하고 통신할 때 사용한다.

실습에서 웹 페이지에 출력된 `172.31.x.x` 형태의 주소는 Private IPv4 Address이다.

---

## Instance Information

EC2 Console에서는 인스턴스의 다양한 정보를 확인할 수 있다.

예시:

- Instance Name
- Instance ID
- Public IPv4 Address
- Private IPv4 Address
- Public DNS
- Private DNS
- Instance Type
- AMI ID
- Operating System
- Key Pair
- Security Group
- Storage

### Instance ID

Instance ID는 각 EC2 Instance를 식별하는 고유한 ID이다.

---

## Security Group Rules

실습에서 생성된 Security Group에는 다음 Inbound Rules가 존재한다.

```text
SSH
Port 22
→ Allow

HTTP
Port 80
→ Allow
```

Outbound Traffic은 인터넷 접근을 위해 허용되어 있다.

---

## EC2 Instance States

EC2 Instance는 여러 상태로 전환할 수 있다.

이번 실습에서는 다음 세 가지 동작을 확인한다.

### Start

중지된 EC2 Instance를 다시 실행한다.

```text
Stopped
↓
Start
↓
Pending
↓
Running
```

### Stop

EC2 Instance의 실행을 중지한다.

```text
Running
↓
Stop
↓
Stopped
```

인스턴스가 중지되면 웹 서버도 실행되지 않으므로 접근할 수 없다.

### Terminate

EC2 Instance를 삭제한다.

```text
Running / Stopped
↓
Terminate
↓
Terminated
```

Terminate는 Instance를 삭제하는 작업이므로 Stop과 다르다.

---

## Stop vs Terminate

### Stop

```text
Instance 유지
EBS 유지
Compute 실행 중지
```

나중에 다시 Start할 수 있다.

### Terminate

```text
Instance 삭제
↓
기본 Root EBS도 삭제 가능
```

Terminate된 인스턴스는 다시 시작할 수 없다.

---

## Public IP Changes After Stop / Start

이번 실습에서 매우 중요한 동작이다.

EC2 Instance를 Stop한 뒤 다시 Start하면 Public IPv4 Address가 변경될 수 있다.

```text
Before Stop

Public IP
54.x.x.x

↓

Stop

↓

Start

↓

New Public IP
3.x.x.x
```

따라서 이전 Public IP로 접근하면 웹 서버에 연결되지 않는다.

반면 실습에서는 Private IPv4 Address가 그대로 유지되는 것을 확인한다.

```text
Stop → Start

Public IPv4
→ 변경될 수 있음

Private IPv4
→ 유지
```

---

## EC2 Hands-On Flow

```text
Launch Instance
↓
Select AMI
↓
Select Instance Type
↓
Create Key Pair
↓
Configure Security Group
↓
Configure EBS
↓
Add User Data
↓
Launch
↓
Running
↓
Access Web Server through Public IP
```

---

## Summary

- EC2 Console에서 가상 서버를 빠르게 생성할 수 있다.
- AMI는 EC2 Instance의 기본 이미지를 정의한다.
- Instance Type은 CPU와 Memory 등의 성능을 결정한다.
- Key Pair는 SSH 접속에 사용한다.
- Security Group은 Instance의 네트워크 트래픽을 제어한다.
- HTTP는 Port 80, SSH는 Port 22를 사용한다.
- EBS Volume은 EC2 Instance의 Storage로 사용할 수 있다.
- User Data를 이용하여 Instance 초기 설정을 자동화할 수 있다.
- User Data는 최초 시작 시 실행된다.
- Public IPv4를 이용해 인터넷에서 EC2 Instance에 접근할 수 있다.
- Stop과 Terminate는 서로 다른 동작이다.
- Stop 후 다시 Start하면 Public IPv4 Address가 변경될 수 있다.

---

## Exam Notes

- AMI = EC2 Instance를 생성하기 위한 이미지
- Instance Type = CPU / RAM 등의 컴퓨팅 사양
- Key Pair = SSH 인증에 사용
- Security Group = EC2의 방화벽
- SSH = Port 22
- HTTP = Port 80
- User Data = Bootstrapping
- User Data는 최초 시작 시 실행
- Root EBS는 기본 설정에서 Instance Termination 시 삭제될 수 있음
- Stop ≠ Terminate
- Stop → Start 시 Public IPv4는 변경될 수 있음
- Private IPv4는 실습에서 유지됨

---

## Practical Example

간단한 웹 서버를 AWS에 배포한다고 가정한다.

```text
Amazon Linux AMI
+
t2.micro
+
EBS
+
Security Group
   ├─ SSH : 22
   └─ HTTP: 80
+
User Data
↓
EC2 Instance
↓
HTTP Web Server 자동 설치
↓
Public IPv4
↓
Hello World
```

사용자는 브라우저에서 다음 주소로 웹 서버에 접근한다.

```text
http://<EC2-Public-IP>
```

EC2 Instance를 Stop하면 웹 서버에 접근할 수 없으며, 다시 Start했을 때 Public IPv4가 변경되었다면 새로운 IP를 사용해야 한다.

---

# 🇯🇵 日本語

## EC2インスタンスの起動

AWS Management ConsoleからEC2 Instanceを作成できる。

主な設定項目は以下のとおりである。

- AMI
- Instance Type
- Key Pair
- Security Group
- Storage
- User Data

---

## AMI

AMI（Amazon Machine Image）はEC2 Instanceを作成するための基本イメージである。

今回の実習ではAmazon Linuxを使用する。

---

## Security Group

Security GroupはEC2 Instanceのファイアウォールとして動作する。

今回の実習では以下の通信を許可する。

```text
SSH  → Port 22
HTTP → Port 80
```

---

## EC2 User Data

User Dataを利用すると、EC2 Instanceの初回起動時にスクリプトを実行できる。

これをBootstrappingという。

今回の実習ではWeb ServerのインストールとHTMLファイルの作成を自動化する。

---

## EC2 Instance State

主な状態変更は以下のとおりである。

```text
Start
Stop
Terminate
```

StopしたInstanceは再度Startできるが、TerminateしたInstanceは削除される。

StopしてからStartするとPublic IPv4 Addressが変更される可能性がある。

---

## Summary

- AMIはEC2の基本イメージである。
- Instance TypeはCPUやMemoryなどの性能を決める。
- Key PairはSSH接続に利用する。
- Security Groupはネットワークアクセスを制御する。
- User Dataで初期設定を自動化できる。
- SSHはPort 22、HTTPはPort 80を使用する。
- StopとTerminateは異なる。
- Stop / Start後にPublic IPが変更される可能性がある。

---

# 🇺🇸 English

## Launching an EC2 Instance

An EC2 instance can be launched from the AWS Management Console.

Important configuration options include:

- AMI
- Instance Type
- Key Pair
- Security Group
- Storage
- User Data

---

## AMI

An Amazon Machine Image (AMI) provides the base image used to launch an EC2 instance.

Amazon Linux is used in this hands-on exercise.

---

## Security Group

A Security Group acts as a firewall for an EC2 instance.

The exercise allows:

```text
SSH  → Port 22
HTTP → Port 80
```

---

## EC2 User Data

EC2 User Data allows a script to run when an instance is launched for the first time.

This process is called bootstrapping.

The script in this exercise installs and configures a simple HTTP web server.

---

## EC2 Instance States

Important instance actions include:

- Start
- Stop
- Terminate

A stopped instance can be started again.

A terminated instance is deleted and cannot be restarted.

Stopping and starting an instance may change its Public IPv4 Address.

---

## Summary

- AMIs define the base image of an EC2 instance.
- Instance Types define compute resources.
- Key Pairs are used for SSH authentication.
- Security Groups control network traffic.
- User Data automates initial configuration.
- SSH uses port 22.
- HTTP uses port 80.
- Stop and Terminate have different meanings.
- The Public IPv4 address may change after Stop / Start.

---

## Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Launch Instance | 인스턴스 시작/생성 | インスタンスを起動 |
| Amazon Machine Image (AMI) | 아마존 머신 이미지 | Amazon Machine Image |
| Instance Type | 인스턴스 유형 | インスタンスタイプ |
| Key Pair | 키 페어 | キーペア |
| SSH | 보안 원격 접속 | SSH |
| Security Group | 보안 그룹 | セキュリティグループ |
| Inbound Rule | 인바운드 규칙 | インバウンドルール |
| Outbound Rule | 아웃바운드 규칙 | アウトバウンドルール |
| Public IPv4 | 공인 IPv4 | パブリックIPv4 |
| Private IPv4 | 사설 IPv4 | プライベートIPv4 |
| Root Volume | 루트 볼륨 | ルートボリューム |
| Delete on Termination | 종료 시 삭제 | 終了時に削除 |
| User Data | 사용자 데이터 | ユーザーデータ |
| Bootstrapping | 초기 설정 자동화 | ブートストラッピング |
| Pending | 시작 대기 상태 | 保留中 |
| Running | 실행 상태 | 実行中 |
| Stopped | 중지 상태 | 停止中 |
| Terminated | 종료/삭제 상태 | 終了済み |

---

## Review Questions

1. AMI는 EC2 Instance 생성 과정에서 어떤 역할을 하는가?

2. Instance Type을 선택할 때 달라지는 주요 자원은 무엇인가?

3. Key Pair는 어떤 목적으로 사용하는가?

4. SSH와 HTTP는 각각 어떤 Port를 사용하는가?

5. Security Group은 EC2 Instance에서 어떤 역할을 하는가?

6. EC2 User Data는 언제 실행되는가?

7. Bootstrapping이란 무엇인가?

8. `Delete on Termination`이 활성화된 Root EBS Volume은 EC2를 Terminate하면 어떻게 되는가?

9. Stop과 Terminate의 차이는 무엇인가?

10. EC2 Instance를 Stop한 뒤 다시 Start하면 Public IPv4 Address는 어떻게 될 수 있는가?

11. Public IPv4와 Private IPv4는 각각 어떤 용도로 사용되는가?

12. 이번 실습에서 웹 서버에 접근할 때 `https://`가 아닌 `http://`를 사용한 이유는 무엇인가?