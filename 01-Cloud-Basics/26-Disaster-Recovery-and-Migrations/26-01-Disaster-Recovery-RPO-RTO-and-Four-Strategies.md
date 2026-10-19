# 📘 26-01. Disaster Recovery: RPO, RTO & Four Strategies

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Disaster Recovery` · `RPO` · `RTO` · `Backup and Restore` · `Pilot Light` · `Warm Standby` · `Multi-Site`

## 🇰🇷 1. DR(재해 복구)의 출발점
**Disaster Recovery(DR)**는 재해 때문에 서비스를 지속할 수 없게 되었을 때 데이터와 운영 환경을 복구하는 계획과 작업이다. 온프레미스 두 센터 사이, 온프레미스 ↔ AWS, AWS 리전 A ↔ 리전 B에서 모두 구성할 수 있다. 단순 **High Availability(일부 서버/AZ 장애 대비)**와 **리전 또는 사이트 전체의 복구**는 범위를 구별한다.

## 🇰🇷 2. RPO와 RTO: 목표가 서로 다르다
- **RPO(Recovery Point Objective)**: 허용 가능한 **최대 데이터 손실 시간**. 1시간마다 백업했다면 장애 시 최악에는 1시간 가까운 변경 사항을 잃을 수 있다.
- **RTO(Recovery Time Objective)**: 장애 시점부터 서비스 정상화까지 허용 가능한 **최대 중단 시간**. 2시간 RTO라면 2시간 내 복구하는 설계가 필요하다.
- 둘은 실제 측정값이 아니라 **설계·운영 목표**다. 목표를 낮출수록 복제·대기 인프라·자동화 비용이 대체로 높아진다.

```text
시간 ───────────────────────────────────────────────▶
마지막 복구 가능 시점   재해 발생                    서비스 재개
         │               │                              │
         └──── RPO ──────┘                              │
                         └──────── RTO ──────────────────┘
```

## 🇰🇷 3. 백업 상태에 따라 구분하는 네 가지 전략
| 전략 | 재해 전 복구 사이트의 상태 | 장애 후 추가 작업 | 비용/복구 시간 경향 |
|---|---|---|---|
| **Backup & Restore** | 백업만 저장 | 서버·DB 복원/재구성 | 저비용 / 긴 RTO |
| **Pilot Light** | DB 같은 **핵심 코어**를 계속 가동·복제 | 애플리케이션 서버 등 추가로 띄우기 | 중하 / 중간 RTO |
| **Warm Standby** | 전체 서비스가 **작은 용량으로 실행** | Auto Scaling 등으로 처리 용량 확대 | 중상 / 짧은 RTO |
| **Multi-Site / Hot Site** | 충분한 운영 규모를 여러 사이트에 확보 | 트래픽과 쓰기 대상 전환 | 고비용 / 매우 짧은 RTO |

**핵심 구별**: Pilot Light는 *핵심만 켜둠*, Warm Standby는 *전체를 작게 켜둠*, Hot Site는 *전체를 충분히 켜둠*. '작동 중'은 EC2/DB가 실제 실행되는 상태라는 뜻이지, 반드시 평소 고객 트래픽을 받는다는 뜻은 아니다. Hot Site도 Active-Passive가 가능하다.

## 🇰🇷 4. 데이터와 트래픽의 장애 전환
- Route 53 Failover + Health Check로 정상 엔드포인트를 향하도록 DNS를 변경할 수 있지만 TTL/캐시 때문에 즉시 전환을 보장하지 않는다.
- RDS Cross-Region Replica, Aurora Global Database 등으로 복구 리전에 데이터를 준비한다. **읽기 복제본 = 자동 쓰기 가능한 Primary**는 아니므로 승격과 연결 전환이 필요할 수 있다.
- Multi-Site Active-Active는 여러 사이트가 평소에도 요청을 처리하는 형태. DB의 쓰기 일관성 설계는 별개의 과제다.

## 🇰🇷 5. 선택 예시
저렴한 내부 업무 시스템 + 수 시간 중단 허용 → Backup & Restore. 데이터베이스 복제만 유지하고 앱 서버는 장애 후 실행 → Pilot Light. 시스템 전체를 미리 최소 규모로 켜둠 → Warm Standby. 수 초~수 분 수준의 복구가 중요한 핵심 서비스 → Multi-Site를 검토한다. 정확한 RPO/RTO는 구현에 좌우되므로 전략 이름만으로 보장되지 않는다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- RPO는 데이터 손실, RTO는 서비스 중단 시간이다.
- 비용: Backup < Pilot Light < Warm Standby < Multi-Site 경향.
- Pilot Light와 Warm Standby의 분기점은 **전체 애플리케이션의 가동 여부**.
- Hot Site는 Active-Active만 뜻하는 것이 아니다.
- 복제는 백업을 대체하지 않는다: 손상·삭제도 복제될 수 있다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 마지막 백업이 13시이고 14시에 재해가 났다. 약 1시간의 손실이 가능하다면 어떤 지표인가?

<details>
<summary>정답 및 해설 보기</summary>

**RPO.** 허용할 수 있는 데이터 손실의 시간 범위다.

</details>

### Q2. 장애 후 30분 안에 서비스가 정상 동작해야 한다. 어떤 목표인가?

<details>
<summary>정답 및 해설 보기</summary>

**RTO.** 서비스 가동 중단의 허용 최대 시간이다.

</details>

### Q3. AWS에서 RDS만 복제 중이며 EC2는 장애 후 실행한다. 어떤 전략인가?

<details>
<summary>정답 및 해설 보기</summary>

**Pilot Light.** 핵심 코어만 상시 운영한다.

</details>

### Q4. 앱·DB·ELB가 모두 최소 용량으로 실행 중이고 장애 시 ASG로 확장한다. 어떤 전략인가?

<details>
<summary>정답 및 해설 보기</summary>

**Warm Standby.** 전체 서비스가 축소된 형태로 미리 실행 중이다.

</details>

### Q5. 재해 복구용 리전에도 완전한 운영 용량을 확보했다. 반드시 두 리전에 평시 고객 요청이 가야 하는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** Hot Site는 Active-Passive일 수도 있고 Active-Active일 수도 있다.

</details>

## 🇯🇵 日本語 Summary

RPO は許容できるデータ損失の時間、RTO は復旧までの許容停止時間。Backup & Restore → Pilot Light → Warm Standby → Multi-Site の順に待機環境が充実し、一般に費用は増え、復旧時間は短くなる。Pilot Light は重要な中核だけ、Warm Standby はシステム全体を小規模で常時稼働する。

## 🇺🇸 English Summary

RPO defines acceptable data loss; RTO defines acceptable downtime. Backup and Restore stores backups only, Pilot Light runs a critical core, Warm Standby runs the whole application at reduced capacity, and Multi-Site maintains production-ready capacity in multiple locations.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Disaster Recovery | 재해 복구 | 災害復旧 |
| Recovery Point Objective | 복구 시점 목표 | 目標復旧時点 |
| Recovery Time Objective | 복구 시간 목표 | 目標復旧時間 |
| Pilot Light | 핵심 구성만 대기 가동 | パイロットライト |
| Warm Standby | 축소된 전체 환경 대기 | ウォームスタンバイ |
| Multi-Site | 다중 사이트 운영 | マルチサイト |
