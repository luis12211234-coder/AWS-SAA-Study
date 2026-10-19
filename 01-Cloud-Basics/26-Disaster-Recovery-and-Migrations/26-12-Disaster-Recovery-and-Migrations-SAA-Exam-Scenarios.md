# 📘 26-12. DR & Migration: Final Comparison and SAA Scenarios

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `RPO` · `RTO` · `DMS` · `MGN` · `Backup` · `Vault Lock` · `Direct Connect` · `Snowball` · `VMware`

## 🇰🇷 1. 전체 섹션 한눈에 보기
이번 섹션은 **목표를 먼저 구분하고 기존에 배운 AWS 서비스를 조합**하는 아키텍처 문제다.

```text
요구사항
 ├─ 장애 후 복구/데이터 보호? ──▶ DR 전략 / AWS Backup / Route 53
 ├─ 서버 전체를 EC2로 옮길까? ─▶ MGN (Lift-and-Shift)
 ├─ DB 데이터를 새 DB로 옮길까? ▶ DMS (필요시 Schema Conversion)
 ├─ VMware 운영 환경 유지? ───▶ VMware Cloud on AWS / Amazon EVS
 └─ 수백 TB를 빨리 옮길까? ───▶ 네트워크와 오프라인 전송 비교
```

## 🇰🇷 2. DR 네 전략 최종 비교
| Backup & Restore | Pilot Light | Warm Standby | Multi-Site / Hot Site |
|---|---|---|---|
| 백업만 | 핵심 DB 등 가동 | 전체 작게 가동 | 전체 충분한 용량 가동 |
| 비용 ↓ / 복구 느림 | 중간 | 복구 빠름 | 비용 ↑ / 복구 가장 빠름 |

**RPO = 데이터 손실, RTO = 서비스 중단.** 비용·회복성에 따라 전략 선택. Multi-AZ는 AZ 장애, Cross-Region은 리전 단위 DR에 활용한다.

## 🇰🇷 3. 마이그레이션 서비스 최종 매핑
| 요구 | 선택 도구/서비스 |
|---|---|
| 서버 목록·자원 사용량·서버 간 연결 조사 | 기존 Application Discovery Service / 현재 AWS Transform |
| 프로젝트 전체 진행 추적 | 기존 Migration Hub / 현재 AWS Transform |
| 서버 OS+앱+디스크를 일반 EC2로 이전 | **AWS Transform MGN** |
| MySQL 등의 데이터만 RDS/Aurora로 이전 | **AWS DMS** |
| DB 엔진이 서로 달라 스키마 변경 필요 | **DMS Schema Conversion / SCT** |
| RDS → Aurora, 다운타임 최소화 | 지원되는 **Aurora Read Replica → Promote** |
| 백업 통합 정책·태그·수명 주기 | **AWS Backup** |
| WORM/삭제 불가 보존 | **Backup Vault Lock Compliance** |
| VMware vSphere를 AWS에서도 유지 | 기존 **VMware Cloud on AWS** 개념 / 현재 EVS 등 검토 |
| 다량 파일·객체 반복 전송 | **AWS DataSync** + 네트워크 연결 |
| DB 변경 사항을 계속 복제 | **DMS + CDC** |
| 수백 TB+아주 느린 인터넷의 역사적 오프라인 문제 | **Snowball** (현 제공 상태 별도 확인) |

## 🇰🇷 4. 복합 문제 예제 A: 회사 전체 시스템을 옮긴다
온프레미스에 **웹/앱 서버**와 **Oracle DB**가 있고 최종 목표는 **EC2 앱 + Aurora PostgreSQL**이다.
1. AWS Transform 등으로 앱↔DB 종속성을 조사.
2. **MGN**으로 웹·앱 서버 OS와 디스크를 EC2로 리호스팅.
3. **Schema Conversion**으로 Oracle DB 스키마를 PostgreSQL용으로 조정.
4. **DMS Full Load + CDC**로 Oracle 데이터를 Aurora PostgreSQL에 동기화.
5. Cutover 시 데이터 지연과 앱의 DB 접속 주소를 확인하고 트래픽 전환.

**MGN과 DMS 둘 다 필요할 수 있다.** 반대로 Oracle DB 서버를 OS 포함하여 EC2로 통째로 옮긴다면 MGN만 사용하는 방식도 검토 가능하다. 이 경우 DB는 자동 RDS가 아니라 **Self-managed DB**다.

## 🇰🇷 5. 복합 문제 예제 B: 재해 복구와 보안
프로덕션 DB를 매일 백업하고 다른 리전/계정에도 사본을 만들며, 관리자 계정 탈취 후에도 보존기간 중 백업 삭제를 못 하게 해야 한다. **AWS Backup Plan + Cross-Region/Cross-Account Copy + Vault Lock Compliance Mode**를 검토한다. 그리고 실제 서비스 재개에는 EC2/DB 복구와 DNS 전환 자동화가 추가로 필요하다.

## 🇰🇷 6. 복합 문제 예제 C: 200TB·100Mbps
온라인 100Mbps로 200TB를 보내면 이론상 약 **185일**이다. 강의/전통 시험에서는 **Snowball 오프라인 전송**이 유리한 답이었다. 실제 최신 설계는 현재 주문 가능한 서비스·배송 기간과 기존 DX 보유 여부 등을 다시 확인한다. 이후 DB 변경분은 DMS+CDC로 따라잡는 식으로 구분한다.

## 🇰🇷 7. 현재 서비스 상태에 대한 주의
- SMS: **2023-04-01 서비스 종료**.
- Discovery Service / Migration Hub: **2025-11-07 신규 고객 이용 중단**.
- DMS Fleet Advisor: **2026-05-20 서비스 종료**.
- MGN: **2026-06 AWS Transform MGN으로 명칭 변경**.
- Snowball Edge: **2025-11 신규 고객 가용성 변경**. 오래된 문제의 의도와 현재 조달 가능성을 구분.
- VMware Cloud on AWS: **2024-04 이후 AWS 직접 재판매 중단**. Amazon EVS는 별도 서비스.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- DR 문제 = RPO/RTO 및 대기 환경 규모를 우선 읽기.
- 서버(MGN), DB 데이터(DMS), 스키마(SCT), VMware 환경(VMC/EVS)을 혼동하지 말 것.
- MGN + DMS 조합 가능, DB 서버 전체를 EC2로 옮기면 RDS 관리형 기능 자동 적용 안 됨.
- 대용량 이전은 회선 속도뿐 아니라 구축 시간, 오프라인 배송, CDC까지 종합 판단.
- 과거 강의 서비스의 종료/개명 사항을 2026년 실무 선택과 구분.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 재해 시 5분 데이터 손실은 허용하지만 서버 다운타임은 최대 1시간이다. 각각 어떤 지표인가?

<details>
<summary>정답 및 해설 보기</summary>

5분은 **RPO**, 1시간은 **RTO**다.

</details>

### Q2. 복구 사이트에 DB만 항상 실행되고 앱 서버는 장애 후 기동된다. 어떤 DR 전략인가?

<details>
<summary>정답 및 해설 보기</summary>

**Pilot Light**다.

</details>

### Q3. 웹 서버는 EC2로, Oracle DB는 Aurora PostgreSQL로 옮기고 싶다. 서비스 조합은?

<details>
<summary>정답 및 해설 보기</summary>

**MGN(서버) + Schema Conversion(구조) + DMS(데이터·CDC)**를 검토한다.

</details>

### Q4. 관리자/루트 사용자도 보존기간 중 백업을 삭제하면 안 된다. 필요한 기능은?

<details>
<summary>정답 및 해설 보기</summary>

**AWS Backup Vault Lock의 Compliance Mode**(유예기간 후 확정)다.

</details>

### Q5. 기존 VMware vSphere·vSAN·NSX 운영을 AWS에서도 유지하고 싶다면?

<details>
<summary>정답 및 해설 보기</summary>

강의의 **VMware Cloud on AWS** 개념. 현재 구축에서는 **Amazon EVS** 같은 별도 대안도 확인한다.

</details>

## 🇯🇵 日本語 Summary

セクション全体では DR の RPO/RTO と 4 戦略、AWS Backup、MGN によるサーバー Rehosting、DMS による DB データ移行、SCT、DataSync、VMware、オフラインデータ転送を比較する。実際の案件では複数のサービスを組み合わせる。

## 🇺🇸 English Summary

The section combines disaster recovery objectives and strategies with backup governance, server rehosting via MGN, database migration via DMS, schema conversion, large-scale data transfer, and VMware infrastructure. Real architectures often require multiple complementary services.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Disaster Recovery Strategy | 재해 복구 전략 | 災害復旧戦略 |
| Rehosting | 서버 환경 그대로 이전 | リホスティング |
| Replatforming | 관리형 플랫폼으로 전환 | リプラットフォーム |
| Backup Governance | 백업 거버넌스 | バックアップ統制 |
| Migration Cutover | 마이그레이션 운영 전환 | 移行カットオーバー |

## 🔗 Official References

- https://docs.aws.amazon.com/general/latest/gr/full_shutdown_services.html
- https://aws.amazon.com/about-aws/whats-new/2026/06/aws-transform-mgn-rebrand/
