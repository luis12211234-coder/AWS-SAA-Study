# 09-01. 애플리케이션을 빠르게 생성하기

## Overview

EC2, EBS, RDS 등을 포함한 전체 애플리케이션 스택을 새롭게 시작할 때는 여러 초기 작업 때문에 시간이 필요할 수 있다.

예를 들어 다음 작업들이 필요하다.

- 애플리케이션 설치
- OS 및 Dependency 설치
- 초기 데이터 또는 복구 데이터 입력
- 애플리케이션 설정
- Storage 준비
- 애플리케이션 실행

AWS에서는 이러한 작업을 매번 처음부터 반복하지 않고, **이미 준비된 이미지나 Snapshot을 재사용하여 애플리케이션 시작 시간을 줄일 수 있다.**

---

## 1. Golden AMI

### AMI 복습

AMI(Amazon Machine Image)는 EC2 Instance를 생성할 때 사용하는 이미지이다.

```text
AMI
 ↓
EC2 Instance
```

운영체제와 필요한 설정 등을 포함하는 EC2 생성 Template 역할을 한다.

---

### Golden AMI란?

Golden AMI는 별도의 AWS 서비스나 특별한 AMI 종류가 아니다.

애플리케이션과 필요한 OS Dependency 등을 미리 설치하고 검증하여 **배포할 준비가 된 표준 AMI**를 의미한다.

일반적인 방식은 다음과 같다.

```text
EC2 Launch
    ↓
OS Configuration
    ↓
Dependency 설치
    ↓
Application 설치
    ↓
Application 실행
```

새로운 Instance가 생성될 때마다 같은 작업을 반복해야 한다.

Golden AMI를 사용하면 미리 준비한다.

```text
EC2 준비
    ↓
Dependency 설치
    ↓
Application 설치
    ↓
필요한 공통 설정
    ↓
AMI 생성
    ↓
Golden AMI
```

이후 새로운 Instance는 다음과 같이 생성된다.

```text
Golden AMI
     ↓
New EC2 Instance
     ↓
Application Ready
```

### 장점

Auto Scaling Group이 새로운 EC2 Instance를 생성해야 하는 상황을 생각해볼 수 있다.

```text
Traffic 증가
    ↓
ASG Scale Out
    ↓
New EC2
```

새 EC2가 생성될 때마다 애플리케이션과 Dependency를 처음부터 설치하면 Instance가 실제 Traffic을 받을 준비가 되기까지 시간이 오래 걸릴 수 있다.

Golden AMI를 사용하면 이러한 공통 작업이 이미 완료되어 있기 때문에 시작 시간을 줄일 수 있다.

---

## 2. EC2 User Data

User Data는 EC2 Instance가 시작될 때 Bootstrap 작업을 수행하기 위해 사용할 수 있다.

```text
EC2 Launch
    ↓
User Data
    ↓
Configuration
    ↓
Application Ready
```

Golden AMI가 **미리 준비할 수 있는 정적인 부분**에 적합하다면, User Data는 Instance가 시작되는 시점에 결정되는 **동적인 구성**에 사용할 수 있다.

예:

```text
Golden AMI
├── Application
├── OS Dependencies
└── Common Configuration

User Data
└── Launch-time Configuration
```

모든 애플리케이션 설치 작업을 User Data에서 수행할 수도 있지만, 설치 작업이 많을수록 Instance가 준비되기까지 시간이 길어질 수 있다.

---

## 3. Golden AMI + User Data

두 방법은 서로 배타적인 것이 아니다.

함께 사용할 수 있다.

```text
Golden AMI
│
├── Application
├── Dependencies
└── Common Configuration
        +
User Data
│
└── Dynamic Configuration
        ↓
Ready EC2 Instance
```

이를 Hybrid 방식으로 이해할 수 있다.

### 핵심

```text
미리 준비 가능한 것
→ Golden AMI

시작할 때 결정해야 하는 것
→ User Data
```

Elastic Beanstalk에서도 이러한 접근 방식을 활용할 수 있다.

---

## 4. RDS Snapshot Restore

Database를 처음부터 생성하면 Database Engine만 준비되는 것으로 끝나지 않을 수 있다.

애플리케이션에서 필요한:

- Schema
- 기존 Data
- Recovery Data

등이 필요할 수 있다.

기존 RDS Snapshot이 있다면 이를 Restore하여 준비된 Database를 생성할 수 있다.

```text
RDS Snapshot
     ↓
Restore
     ↓
RDS Database
├── Schema Ready
└── Data Ready
```

즉 Database를 완전히 처음부터 구성하는 작업을 줄일 수 있다.

---

## 5. EBS Snapshot Restore

EBS도 같은 원리를 적용할 수 있다.

새로운 빈 Volume을 생성하여 File System과 Data를 처음부터 준비하는 대신 기존 Snapshot에서 Volume을 복원할 수 있다.

```text
EBS Snapshot
     ↓
Restore
     ↓
EBS Volume
├── File System Ready
└── Data Ready
```

---

## Summary

빠른 애플리케이션 생성의 핵심은 다음 한 문장으로 정리할 수 있다.

> **매번 처음부터 만들지 말고, 이미 준비된 상태를 재사용한다.**

| Resource | 방법 | 목적 |
|---|---|---|
| EC2 | Golden AMI | Application과 Dependency를 미리 준비 |
| EC2 | User Data | 시작 시 동적인 구성 |
| EC2 | Golden AMI + User Data | 빠른 시작 + 동적 구성 |
| RDS | Snapshot Restore | 기존 Schema와 Data 복원 |
| EBS | Snapshot Restore | 준비된 File System과 Data 복원 |

---

## Exam Notes

- Golden AMI는 별도의 AWS 서비스가 아니다.
- Golden AMI는 Application과 Dependency 등을 미리 준비한 표준 AMI이다.
- User Data는 Instance 시작 시 Bootstrap 및 동적 구성에 사용할 수 있다.
- Golden AMI와 User Data는 함께 사용할 수 있다.
- RDS Snapshot을 Restore하면 기존 Schema와 Data를 가진 Database를 생성할 수 있다.
- EBS Snapshot을 Restore하면 기존 File System과 Data를 가진 Volume을 생성할 수 있다.

---

## 日本語まとめ

AWSでは、アプリケーションを毎回最初から構築するのではなく、事前に準備されたイメージやSnapshotを再利用することで起動時間を短縮できます。

### Golden AMI

アプリケーション、OSの依存関係、共通設定などを事前に準備したAMIです。

```text
Golden AMI
→ EC2
→ Application Ready
```

### User Data

EC2起動時にBootstrap処理や動的な設定を行うために使用できます。

### Hybrid

Golden AMIとUser Dataを組み合わせることができます。

```text
Golden AMI
→ 静的・共通設定

User Data
→ 動的設定
```

### Snapshot

- RDS Snapshot → SchemaとDataを含むDatabaseを復元
- EBS Snapshot → File SystemとDataを含むVolumeを復元

---

## English Summary

AWS can reduce application initialization time by reusing prepared resources instead of rebuilding everything from scratch.

### Golden AMI

A Golden AMI is a preconfigured and tested AMI containing applications, dependencies, and common configuration.

### User Data

EC2 User Data can perform bootstrap operations and dynamic configuration when an instance starts.

### Hybrid

Golden AMIs and User Data can be combined.

```text
Golden AMI
→ Preconfigured components

User Data
→ Dynamic configuration
```

### Snapshots

- RDS snapshots can restore databases with existing schemas and data.
- EBS snapshots can restore volumes with existing file systems and data.

---

## Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Golden AMI | ゴールデンAMI | 사전 구성된 표준 AMI |
| Bootstrap | ブートストラップ | 초기 구성 |
| User Data | ユーザーデータ | 사용자 데이터 |
| Dependency | 依存関係 | 의존성 |
| Snapshot | スナップショット | 스냅샷 |
| Restore | 復元 | 복원 |
| Dynamic Configuration | 動的設定 | 동적 구성 |
| Application Stack | アプリケーションスタック | 애플리케이션 스택 |

---

## Review Questions

### 1. EC2가 생성될 때마다 Application과 Dependency를 설치하여 시작 시간이 오래 걸린다. 어떤 방법을 사용할 수 있는가?

<details>
<summary>정답 보기</summary>

**정답: Golden AMI**

Application과 Dependency를 미리 설치한 AMI를 사용하여 Instance 초기화 작업을 줄일 수 있다.

</details>

### 2. Instance가 시작될 때 필요한 동적인 Configuration에는 무엇을 사용할 수 있는가?

<details>
<summary>정답 보기</summary>

**정답: EC2 User Data**

User Data를 통해 Instance 시작 시 Bootstrap 및 동적 구성을 수행할 수 있다.

</details>

### 3. Golden AMI와 User Data는 동시에 사용할 수 있는가?

<details>
<summary>정답 보기</summary>

**정답: 가능하다.**

공통적인 Application과 Dependency는 Golden AMI에 준비하고, 시작 시 필요한 동적 구성은 User Data에서 처리할 수 있다.

</details>

### 4. 기존 Schema와 Data가 준비된 RDS Database를 빠르게 생성하려면?

<details>
<summary>정답 보기</summary>

**정답: RDS Snapshot에서 Restore한다.**

Snapshot에 저장된 Database 상태를 기반으로 새로운 RDS Database를 생성할 수 있다.

</details>

### 5. 기존 File System과 Data가 준비된 EBS Volume을 생성하려면?

<details>
<summary>정답 보기</summary>

**정답: EBS Snapshot에서 Restore한다.**

</details>
