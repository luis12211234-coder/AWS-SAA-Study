# Amazon EC2 Overview

## Overview

Amazon EC2(Elastic Compute Cloud)는 AWS에서 가상 서버를 임대하여 사용할 수 있는 서비스이다.

EC2는 Infrastructure as a Service(IaaS)의 대표적인 AWS 서비스이며, 클라우드에서 필요한 컴퓨팅 자원을 필요할 때 생성하고 사용할 수 있도록 한다.

---

## What is EC2?

EC2 = Elastic Compute Cloud

EC2를 사용하면 AWS에서 가상 머신을 생성하고 사용할 수 있다.

이 가상 머신을 EC2 Instance라고 한다.

EC2와 함께 다음과 같은 기능들을 사용할 수 있다.

- EC2 Instance: 가상 머신
- EBS: 가상 드라이브에 데이터 저장
- Elastic Load Balancer: 여러 서버에 트래픽 분산
- Auto Scaling Group: 필요한 만큼 인스턴스를 확장 또는 축소

---

## EC2 Configuration Options

EC2 Instance를 생성할 때 다양한 설정을 선택할 수 있다.

### Operating System

지원되는 운영 체제 예시

- Linux
- Windows
- macOS

Linux가 가장 일반적으로 사용된다.

---

### CPU

인스턴스에서 사용할 컴퓨팅 성능과 vCPU 수를 선택할 수 있다.

---

### RAM

애플리케이션에 필요한 메모리 용량을 선택할 수 있다.

---

### Storage

EC2에서 사용할 스토리지 방식도 선택할 수 있다.

#### Network-Attached Storage

- EBS
- EFS

#### Hardware-Attached Storage

- EC2 Instance Store

---

### Network

EC2 Instance의 네트워크 성능과 Public IP 등의 설정을 선택할 수 있다.

---

### Security Group

Security Group은 EC2 Instance의 방화벽 역할을 한다.

인스턴스로 들어오거나 나가는 네트워크 트래픽을 제어한다.

---

## EC2 User Data

EC2 User Data는 EC2 Instance가 처음 시작될 때 실행되는 스크립트이다.

이 과정을 Bootstrapping이라고 한다.

### Bootstrapping

Bootstrapping은 머신이 시작될 때 자동으로 명령을 실행하여 초기 환경을 구성하는 것을 의미한다.

EC2 User Data Script는 인스턴스가 처음 시작될 때 한 번 실행된다.

---

## EC2 User Data Use Cases

EC2 User Data를 이용하여 다음 작업을 자동화할 수 있다.

- 시스템 업데이트
- 소프트웨어 설치
- 인터넷에서 파일 다운로드
- 애플리케이션 초기 설정
- 웹 서버 설정

EC2 User Data Script는 root user 권한으로 실행된다.

---

## EC2 Instance Types

AWS에서는 목적에 따라 다양한 EC2 Instance Type을 제공한다.

인스턴스에 따라 다음 특성이 달라진다.

- vCPU
- RAM
- Storage
- Network Performance
- EBS Bandwidth

---

## Instance Type Example

강의에서 소개된 예시

| Instance | vCPU | Memory | Storage | Network |
|----------|-----:|-------:|---------|---------|
| t2.micro | 1 | 1 GiB | EBS Only | Low to Moderate |
| t2.xlarge | 4 | 16 GiB | EBS Only | Moderate |
| c5d.4xlarge | 16 | 32 GiB | 1 × 400 GB NVMe SSD | Up to 10 Gbps |
| r5.16xlarge | 64 | 512 GiB | EBS Only | 20 Gbps |
| m5.8xlarge | 32 | 128 GiB | EBS Only | 10 Gbps |

인스턴스는 애플리케이션에 필요한 CPU, Memory, Storage, Network 특성에 따라 선택한다.

---

## Why EC2 Matters

EC2는 클라우드의 핵심 특징을 잘 보여준다.

기존 환경에서는 서버를 구매하고 설치해야 하지만,

EC2에서는 필요한 가상 서버를 빠르게 생성하고 사용할 수 있다.

```text
필요한 컴퓨팅 자원 선택
↓
EC2 Instance 생성
↓
필요한 만큼 사용
↓
필요하지 않으면 종료
```

---

## Summary

- EC2는 Elastic Compute Cloud의 약자이다.
- EC2는 Infrastructure as a Service(IaaS)이다.
- EC2 Instance는 AWS에서 임대하는 가상 머신이다.
- OS, CPU, RAM, Storage, Network 등을 선택할 수 있다.
- Security Group은 EC2의 방화벽 역할을 한다.
- EC2 User Data는 초기 설정을 자동화한다.
- User Data Script는 첫 시작 시 한 번 실행된다.
- EC2 Instance Type에 따라 CPU, RAM, Storage, Network 성능이 달라진다.

---

## Exam Notes

- EC2 = Elastic Compute Cloud
- EC2는 IaaS이다.
- EC2 Instance는 가상 서버이다.
- EBS는 네트워크 연결 스토리지이다.
- EC2 Instance Store는 물리적으로 연결된 스토리지이다.
- Security Group은 EC2의 방화벽 역할을 한다.
- EC2 User Data는 Bootstrapping에 사용된다.
- User Data Script는 첫 시작 시 한 번 실행된다.
- User Data Script는 root user 권한으로 실행된다.

---

## Practical Example

새로운 웹 서버를 만든다고 가정한다.

```text
EC2 Instance 생성
↓
Linux 선택
↓
CPU / RAM 선택
↓
EBS Storage 선택
↓
Security Group 설정
↓
User Data 실행
↓
Apache 또는 웹 서버 자동 설치
↓
웹 서버 실행
```

User Data를 사용하면 서버 생성 후 직접 접속해서 소프트웨어를 설치하지 않아도 초기 설정을 자동화할 수 있다.

---

# 🇯🇵 日本語

## Amazon EC2

Amazon EC2（Elastic Compute Cloud）は、AWS上で仮想サーバーを利用できるInfrastructure as a Service（IaaS）である。

EC2 Instanceでは、OS・CPU・RAM・Storage・Networkなどを選択できる。

---

## EC2 User Data

EC2 User Dataは、インスタンスの初回起動時に実行されるスクリプトである。

この初期設定を自動化する処理をBootstrappingという。

主な用途

- アップデート
- ソフトウェアのインストール
- ファイルのダウンロード
- 初期設定

---

## Summary

- EC2はAWSの仮想サーバーサービスである。
- EC2はIaaSである。
- EC2 User Dataで初期設定を自動化できる。
- User Dataは初回起動時に実行される。
- Security Groupはファイアウォールとして動作する。

---

# 🇺🇸 English

## Amazon EC2

Amazon EC2 (Elastic Compute Cloud) is an Infrastructure as a Service (IaaS) that allows users to rent virtual machines in AWS.

An EC2 instance can be configured with different operating systems, CPUs, memory, storage, and networking options.

---

## EC2 User Data

EC2 User Data is a script used to bootstrap an EC2 instance.

The script runs when the instance starts for the first time.

Common tasks include:

- Installing updates
- Installing software
- Downloading files
- Configuring applications

The EC2 User Data script runs as the root user.

---

## Summary

- EC2 stands for Elastic Compute Cloud.
- EC2 is an IaaS service.
- EC2 Instances are virtual machines.
- User Data automates bootstrapping.
- Security Groups act as firewalls.
- Instance types provide different CPU, memory, storage, and network capabilities.

---

## Vocabulary

| English | 한국어 | 日本語 |
|----------|---------|---------|
| EC2 | 엘라스틱 컴퓨트 클라우드 | Elastic Compute Cloud |
| Instance | 인스턴스 / 가상 서버 | インスタンス |
| Infrastructure as a Service | 서비스형 인프라 | Infrastructure as a Service |
| Virtual Machine | 가상 머신 | 仮想マシン |
| CPU | 중앙 처리 장치 | CPU |
| RAM | 메모리 | メモリ |
| Storage | 저장소 | ストレージ |
| Security Group | 보안 그룹 | セキュリティグループ |
| User Data | 사용자 데이터 | ユーザーデータ |
| Bootstrapping | 초기 자동 설정 | ブートストラッピング |
| Root User | 루트 사용자 | ルートユーザー |
| Instance Type | 인스턴스 유형 | インスタンスタイプ |

---

## Review Questions

1. EC2는 무엇의 약자인가?
2. EC2는 어떤 Cloud Service Model에 해당하는가?
3. EC2 Instance를 생성할 때 선택할 수 있는 주요 설정은 무엇인가?
4. EBS와 EC2 Instance Store의 차이는 무엇인가?
5. Security Group의 역할은 무엇인가?
6. EC2 User Data란 무엇인가?
7. Bootstrapping이란 무엇인가?
8. EC2 User Data Script는 언제 실행되는가?
