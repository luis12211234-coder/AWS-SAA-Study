# 📘 27-05. Highly Available EC2 Architectures

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `EIP와 수동 장애 조치`, `ASG 1/1/1 + User Data`, `EBS와 AZ 종속성`, `Lifecycle Hook의 역할`

## 🇰🇷 1. EIP와 수동 장애 조치

Elastic IP는 고정 공인 IPv4 주소로 다른 인스턴스/ENI에 재연결할 수 있다. CloudWatch Alarm 또는 EventBridge 이벤트가 Lambda를 실행하여 대기 EC2를 시작하고 EIP를 재연결하는 설계가 가능하다. 사용자 접속 IP는 같아도 기존 연결이 끊기고 복구 시간이 발생한다. CPU 100%만으로 장애를 단정할 수 없고 상태 검사 및 애플리케이션 헬스 체크를 고려한다.

## 🇰🇷 2. ASG 1/1/1 + User Data

Multi-AZ Auto Scaling Group에서 Min=1, Desired=1, Max=1로 전체 EC2 한 대를 유지한다. 장애 시 ASG가 대체 EC2를 생성하며, User Data가 최초 부팅 때 EIP를 연결하는 AWS API(AssociateAddress)를 호출할 수 있다. 인스턴스 IAM Role에 필요한 권한이 있어야 한다. 대체 인스턴스가 반드시 다른 AZ에 배치된다는 보장은 없으며 장애·가용 용량·배치 정책에 따른다.

## 🇰🇷 3. EBS와 AZ 종속성

EBS Volume은 특정 AZ에 속하므로 다른 AZ의 EC2에 직접 연결할 수 없다. EBS Snapshot에서 대상 AZ에 새 Volume을 생성한 뒤 새 EC2에 연결한다. 데이터베이스라면 일관성 있는 백업·마운트·애플리케이션 복구 절차가 필요하다.

## 🇰🇷 4. Lifecycle Hook의 역할

ASG가 인스턴스를 생성/종료하는 과정에서 Pending:Wait 또는 Terminating:Wait로 진행을 대기시키는 기능이다. Hook이 Snapshot이나 EBS 연결을 직접 수행하지는 않는다. EventBridge/SNS 등의 이벤트를 받아 Lambda/자동화가 실제 작업을 수행하고 CompleteLifecycleAction으로 다음 단계 진행을 알린다. 시작 Hook은 복구된 EBS와 DB 준비 완료 전 InService로 넘어가는 것을 지연시킬 수 있다. EC2 자체가 정지하는 것은 아니다.

## 🇰🇷 5. 종료 시 Snapshot의 한계

정상적으로 접근 가능한 종료 절차라면 Hook으로 Snapshot을 시도할 수 있다. 하지만 EC2/AZ/EBS가 이미 손상되면 장애 직후 Snapshot이 불가능할 수 있으므로 정기 Snapshot/AWS Backup 및 DB 일관성 확보가 필수다.

## 🇰🇷 6. 대안과 선택 기준

ASG 1/1/1은 장애 후 재생성하는 복구 설계이며 무중단 고가용성을 보장하지 않는다. 높은 가용성이 필요하면 ALB + 여러 AZ의 활성 EC2, DB는 RDS Multi-AZ 등 관리형 HA를 검토한다. User Data만으로 순서 제어가 충분하면 시작 Lifecycle Hook이 필수는 아니며 외부 복구 완료를 ASG가 기다려야 할 때 유용하다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **EIP와 수동 장애 조치:** Elastic IP는 고정 공인 IPv4 주소로 다른 인스턴스/ENI에 재연결할 수 있다.
- **ASG 1/1/1 + User Data:** Multi-AZ Auto Scaling Group에서 Min=1, Desired=1, Max=1로 전체 EC2 한 대를 유지한다.
- **EBS와 AZ 종속성:** EBS Volume은 특정 AZ에 속하므로 다른 AZ의 EC2에 직접 연결할 수 없다.
- **Lifecycle Hook의 역할:** ASG가 인스턴스를 생성/종료하는 과정에서 Pending:Wait 또는 Terminating:Wait로 진행을 대기시키는 기능이다.
- **종료 시 Snapshot의 한계:** 정상적으로 접근 가능한 종료 절차라면 Hook으로 Snapshot을 시도할 수 있다.
- **대안과 선택 기준:** ASG 1/1/1은 장애 후 재생성하는 복구 설계이며 무중단 고가용성을 보장하지 않는다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. ASG Min/Desired/Max=1/1/1의 의미는?

<details>
<summary>정답 및 해설 보기</summary>

ASG 전체에서 기본적으로 EC2 한 대를 유지한다. AZ마다 한 대가 아니다.

</details>

### Q2. EIP를 새 EC2에 연결할 때 User Data 외에 필요한 권한 구성은?

<details>
<summary>정답 및 해설 보기</summary>

EC2 Instance Role의 적절한 IAM 권한(예: AssociateAddress)이다.

</details>

### Q3. Lifecycle Hook은 EBS Snapshot을 직접 생성하는가?

<details>
<summary>정답 및 해설 보기</summary>

아니다. 진행을 대기시키고 실제 작업은 Lambda나 자동화가 수행한다.

</details>

### Q4. AZ-A의 EBS를 AZ-B EC2에 옮기는 일반적인 방법은?

<details>
<summary>정답 및 해설 보기</summary>

EBS Snapshot을 사용해 AZ-B에 새 EBS 볼륨을 생성·연결한다.

</details>

### Q5. 장애가 난 순간의 종료 Hook만으로 백업이 보장되는가?

<details>
<summary>정답 및 해설 보기</summary>

아니다. 접근 불가·손상 가능성이 있으므로 사전 정기 백업이 필요하다.

</details>

## 🇯🇵 日本語 Summary

Highly Available EC2 Architectures：AWS SAA-C03 では、要件に応じてサービスを組み合わせ、可用性・性能・運用自動化を設計する。主要な選択基準と障害時の動作を理解する。

## 🇺🇸 English Summary

Highly Available EC2 Architectures: Understand the architecture, trade-offs, failure handling, and SAA-C03 service selection criteria. Combine managed AWS services based on performance, availability, and operational requirements.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| EIP와 수동 장애 조치 | EIP와 수동 장애 조치 | EIP와 수동 장애 조치 |
| ASG 1/1/1 + User Data | ASG 1/1/1 + User Data | ASG 1/1/1 + User Data |
| EBS와 AZ 종속성 | EBS와 AZ 종속성 | EBS와 AZ 종속성 |
| Lifecycle Hook의 역할 | Lifecycle Hook의 역할 | Lifecycle Hook의 역할 |
| 종료 시 Snapshot의 한계 | 종료 시 Snapshot의 한계 | 종료 시 Snapshot의 한계 |
| 대안과 선택 기준 | 대안과 선택 기준 | 대안과 선택 기준 |
