# 📘 26-07. AWS Backup: Plans, Rules, Vaults & Vault Lock

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `AWS Backup` · `Backup Plan` · `Backup Rule` · `Resource Assignment` · `Recovery Point` · `Backup Vault` · `Vault Lock` · `PITR`

## 🇰🇷 1. AWS Backup의 목적
EBS Snapshot, RDS 자동 백업, DynamoDB Backup 등을 일일이 설정하는 대신, **AWS Backup**은 여러 AWS 서비스의 백업 정책을 중앙에서 관리하고 자동 실행하는 완전 관리형 서비스다. EC2/EBS, S3, RDS/Aurora, DynamoDB, DocumentDB, Neptune, EFS, FSx, Storage Gateway Volume 등 다양한 리소스가 지원되지만 **백업 방식·PITR·콜드 전환 가능 여부는 서비스별로 다를 수 있다**.

## 🇰🇷 2. 다섯 가지 구성요소를 구별한다
```text
Backup Plan (전체 계획)
  ├─ Backup Rule A: 매일, 35일 보존
  ├─ Backup Rule B: 매달, 1년 보존
  └─ Resource Assignment: Environment=Production
                │
                ▼
       EBS / RDS / EFS 등
                │ 예약·온디맨드 백업
                ▼
       Backup Vault (논리적 저장소)
                └─ Recovery Point (복구 시점의 실제 백업)
```

| 용어 | 뜻 |
|---|---|
| **Backup Plan** | 여러 Backup Rule을 묶은 전체 백업 계획 |
| **Backup Rule** | 백업 주기, 시작 창, 대상 Vault, 수명 주기, 복사 대상 지정 |
| **Resource Assignment** | 어느 리소스에 Plan을 적용할지 태그/리소스 기준으로 결정 |
| **Backup Vault** | 백업 Recovery Point를 관리하는 논리적 컨테이너 |
| **Recovery Point** | 특정 시점에 만들어진 실질적인 복구 대상 |

Vault는 사용자 S3 버킷을 뜻하지 않는다. AWS Backup이 관리하는 논리적 백업 저장소이며 저장 기술은 리소스 유형에 따라 다를 수 있다.

## 🇰🇷 3. Backup Rule의 생명주기 설정
- **Frequency/Schedule**: 매일, 매주, 매월 또는 Cron 표현식. **On-demand**로 즉시 백업할 수도 있다.
- **Backup Window**: 작업이 언제 시작할 수 있는지의 시간 범위. `Start within 8h`는 **8시간 보관**이 아니라 시작 허용 시간이다.
- **Cold Storage Transition**: 지원하는 백업을 일정 시간이 지난 뒤 저비용 보관 단계로 이동. *모든 리소스가 가능한 것은 아님*.
- **Retention Period**: 백업이 만료되기까지 총 보존 기간.
- **Copy**: 재해 복구를 위해 다른 리전·다른 계정의 볼트로 백업 복사. 리소스 종류·암호화 조건에 따라 제한될 수 있다.
- **PITR(Point-in-Time Recovery)**: 지원 리소스에서 보존 범위 안의 특정 시점을 선택해 복원할 수 있는 기능. 정기 Snapshot과는 구별.

## 🇰🇷 4. Tag 기반 자동 백업
`Environment=Production` 태그가 붙은 EBS/RDS 등을 선택하면, 신규 리소스에도 태그와 정책이 일치하는 경우 해당 Backup Plan을 적용할 수 있다. 필요한 서비스/권한과 태그 조건을 확인한다. AWS Backup 작업이 리소스에 접근하도록 **IAM Role**이 필요하다.

## 🇰🇷 5. Vault Lock: 백업을 함부로 삭제하지 못하게
**WORM(Write Once, Read Many)** 성격의 백업 보존 정책을 적용해 악의적/실수 삭제와 보존기간 변경을 제한한다.

| Vault Lock 모드 | 보호 강도 |
|---|---|
| **Governance** | 지정 IAM 권한이 있는 사용자는 잠금 설정을 관리·해제할 수 있음 |
| **Compliance** | 유예기간 경과 후 잠금이 확정되면 **루트 사용자도 잠금 해제·보존기간 내 백업 삭제 불가** |

**주의:** Compliance Lock이 확정되면 잘못된 장기 보존 설정 때문에 비용이 계속 발생할 수 있다. 보존기간과 정책을 매우 신중히 검증해야 한다.

## 🇰🇷 6. DR 관점의 위치
AWS Backup은 **복구 가능한 데이터 사본을 확보**하는 기능이다. 다른 리전에 백업을 저장했다고 애플리케이션이 자동 실행되는 것은 아니다. 복원 절차, 서버 실행, DNS 전환 등의 DR 계획과 함께 구성한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Plan(정책) / Rule(주기·수명 주기) / Assignment(대상) / Vault(보관) / Recovery Point(백업) 구분.
- Cross-Region + Cross-Account Backup은 DR·격리 목적.
- PITR은 지원 대상에 한정되는 특정 시점 복원.
- Vault Lock은 WORM 보호, Compliance 확정 후 루트도 해제 불가.
- AWS Backup으로 중앙 관리해도 앱 자동 Failover는 별도 설계.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. AWS 계정의 EBS·RDS·EFS를 동일한 주기로 관리하려면?

<details>
<summary>정답 및 해설 보기</summary>

**AWS Backup**으로 중앙 Backup Plan을 구성한다.

</details>

### Q2. 매일 35일, 매달 1년 보관의 두 정책은 어디에 정의하는가?

<details>
<summary>정답 및 해설 보기</summary>

하나의 **Backup Plan** 안에 **Backup Rule** 두 개로 정의할 수 있다.

</details>

### Q3. Environment=Production인 리소스만 자동 백업하려면?

<details>
<summary>정답 및 해설 보기</summary>

**Tag 기반 Resource Assignment**를 사용한다.

</details>

### Q4. 백업의 실제 복구 가능 시점을 나타내는 것은?

<details>
<summary>정답 및 해설 보기</summary>

**Recovery Point**이다.

</details>

### Q5. 루트 관리자도 잠금 해제 불가능한 WORM 백업을 구성하려면?

<details>
<summary>정답 및 해설 보기</summary>

유예기간이 끝나 확정된 **Vault Lock Compliance Mode**가 적합하다.

</details>

## 🇯🇵 日本語 Summary

AWS Backup は EBS・RDS・EFS などのバックアップを一元管理する。Plan に Rule と Resource Assignment を設定し、Vault に Recovery Point を保存する。Vault Lock の Compliance Mode は猶予期間後、root ユーザーでも解除できない WORM 保護を提供する。

## 🇺🇸 English Summary

AWS Backup centralizes policies across supported services. A plan groups rules, resource assignments choose targets, a vault manages recovery points, and Vault Lock can enforce WORM retention. Compliance mode becomes immutable after its grace period.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Backup Plan | 백업 계획 | バックアッププラン |
| Backup Rule | 백업 규칙 | バックアップルール |
| Recovery Point | 복구 지점 | 復旧ポイント |
| Backup Vault | 백업 볼트 | バックアップボールト |
| Vault Lock | 볼트 잠금 | ボールトロック |
| Retention | 보존 기간 | 保持期間 |

## 🔗 Official References

- https://docs.aws.amazon.com/aws-backup/latest/devguide/vault-lock.html
