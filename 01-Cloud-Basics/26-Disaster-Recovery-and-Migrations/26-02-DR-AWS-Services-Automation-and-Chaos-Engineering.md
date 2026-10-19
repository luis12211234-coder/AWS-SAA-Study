# 📘 26-02. DR Services, Automation & Chaos Engineering

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Route 53` · `EBS Snapshot` · `Aurora Global Database` · `CloudFormation` · `CloudWatch` · `Lambda` · `Chaos Engineering`

## 🇰🇷 1. 새로운 서비스가 아니라 기존 서비스를 DR용으로 조합한다
DR는 단독 제품이 아니라 **데이터 백업 → 다른 위치에 데이터 준비 → 트래픽 전환 → 인프라 재구축 → 복구 검증**을 설계하는 일이다. 이미 배운 AWS 서비스를 '재해 복구'라는 목표로 다시 배열한 강의다.

## 🇰🇷 2. DR 역할별 서비스 지도
| 단계 | 서비스 | 실제 역할 |
|---|---|---|
| 백업 | EBS Snapshot, AMI, RDS Backup, AWS Backup, S3/Glacier | 재해 전 데이터와 서버 이미지 보관 |
| 온프레미스 백업·이전 | Storage Gateway, 과거 Snowball | 온프레미스 데이터를 AWS에 적재 |
| 데이터 복제 | RDS Read Replica, Aurora Global Database | 다른 리전/장애 복구 환경에 변경 사항 반영 |
| DNS 전환 | Route 53 Failover + Health Check | 비정상 대상 대신 복구 사이트를 응답 |
| AZ 수준 HA | RDS Multi-AZ, ELB/Auto Scaling, EFS, S3 | 단일 인스턴스나 AZ 장애 완화 |
| 네트워크 복구 | Direct Connect + Site-to-Site VPN | DX 장애 시 VPN 등 대체 경로 확보 |
| 자동화 | CloudFormation, Elastic Beanstalk, CloudWatch, Lambda | 새 인프라 생성과 장애 후 동작 자동화 |

**주의:** RDS Multi-AZ는 AZ 장애 대응에 특히 유용하지만 **리전 전체 재해 대비 복제본**과는 범위가 다르다. S3·EFS도 리전 간 DR 요구가 있으면 별도 복제/복구 설계가 필요하다.

## 🇰🇷 3. 장애 후 복구 흐름의 예
```text
CloudWatch/외부 감시에서 장애 발견
                 │
                 ▼
         자동화(Lambda 등)
         ├─ CloudFormation으로 복구 인프라 생성
         ├─ DB 복원 또는 복제본 승격
         ├─ 앱 설정/접속 대상 확인
         └─ Route 53 Failover 또는 트래픽 전환
                 ▼
         사용자 정상 서비스 확인
```
CloudWatch Alarm은 지원되는 EC2 복구/재부팅 등의 작업이나 EventBridge·Lambda 연계의 **출발 신호**로 사용할 수 있다. 자동으로 모든 DR 단계가 완성되는 것은 아니다. CloudFormation은 인프라 코드를 다시 실행하는 IaC, Elastic Beanstalk는 애플리케이션 환경 재배포에 활용할 수 있다.

## 🇰🇷 4. 지속 복제와 별도 백업의 차이
DB를 다른 리전에 지속 복제하면 RPO를 낮추기 쉽다. 하지만 실수로 `DELETE`한 변경도 복제될 수 있다. 따라서 **실시간 복제 + 시점 복원(PITR)/스냅샷/백업 보존**을 조합하는 편이 안전하다.

## 🇰🇷 5. Chaos Engineering: DR 계획은 실제로 검증한다
Netflix **Chaos Monkey / Simian Army**는 운영 중 인스턴스 일부를 의도적으로 종료하는 장애 실험으로 유명하다. AWS에서는 **Fault Injection Service(FIS)**를 활용해 통제된 장애 실험을 실행할 수 있다. 실험 범위, 중단 조건, 복구 계획을 정하고 실시해야 한다. **백업이 존재함 ≠ 제대로 복구됨**이라는 사실을 확인하기 위한 테스트다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- 백업·복제·DNS 전환·IaC/자동화는 서로 역할이 다르다.
- DX의 장애 대비 경로는 Site-to-Site VPN을 고려.
- AZ 고가용성(Multi-AZ)과 리전 재해 복구(Cross-Region)는 다른 요구사항.
- RDS/Aurora Replica는 데이터 복제, Snapshot/PITR은 이전 상태로 복구.
- Chaos Engineering은 장애를 의도적으로 만들어 복구력을 검증한다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. DR에서 Route 53 Failover가 하는 역할은?

<details>
<summary>정답 및 해설 보기</summary>

**정상적인 다른 엔드포인트로 DNS 응답을 전환**한다. TTL/캐시를 고려해야 한다.

</details>

### Q2. Direct Connect가 끊겼을 때 백업 경로로 쓸 수 있는 AWS 연결은?

<details>
<summary>정답 및 해설 보기</summary>

**Site-to-Site VPN.** 독립적인 공용 인터넷 기반 경로로 구성한다.

</details>

### Q3. DB를 실시간 복제한다면 별도 백업이 필요 없나?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 오삭제·데이터 손상까지 복제될 수 있으므로 백업/PITR이 필요하다.

</details>

### Q4. 새 리전에 EC2, SG, ELB 등을 자동 재생성하려는 경우?

<details>
<summary>정답 및 해설 보기</summary>

**CloudFormation** 같은 Infrastructure as Code를 활용한다.

</details>

### Q5. 의도적으로 인스턴스를 종료해 장애 대응을 확인하는 실험은?

<details>
<summary>정답 및 해설 보기</summary>

**Chaos Engineering.** AWS FIS를 활용할 수 있다.

</details>

## 🇯🇵 日本語 Summary

DR では既存の AWS サービスを目的別に組み合わせる。Backup はデータ保護、Replica は継続複製、Route 53 は DNS 切り替え、CloudFormation・Lambda は復旧自動化を担当する。Chaos Engineering で実際の障害耐性を検証する。

## 🇺🇸 English Summary

DR orchestrates existing AWS services: backups for recovery points, replicas for current data, Route 53 for DNS failover, CloudFormation and Lambda for automation, and chaos engineering for validating recovery readiness.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Failover | 장애 조치 | フェイルオーバー |
| Infrastructure as Code | 코드형 인프라 | コードによるインフラ管理 |
| Chaos Engineering | 의도적 장애 실험 | カオスエンジニアリング |
| Read Replica | 읽기 복제본 | リードレプリカ |
| Recovery Workflow | 복구 작업 흐름 | 復旧ワークフロー |
