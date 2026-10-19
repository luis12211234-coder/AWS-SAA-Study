# 📘 26-09. Application Discovery & AWS Transform MGN: Rehosting

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Agentless Collector` · `Discovery Agent` · `AWS Transform MGN` · `Lift-and-Shift` · `Staging Area` · `Cutover` · `Block-Level Replication`

## 🇰🇷 1. 조사(Discovery)와 실제 이전(Migration)은 다른 단계
회사가 처음부터 AWS에서 새 서비스를 만든다면 옮길 서버가 없다. 반면 **기존 온프레미스 데이터센터**를 이전한다면 서버 목록·성능·네트워크·종속성을 조사한 뒤 이전 순서(Wave)를 정해야 한다.

**Application Discovery Service(과거 이름)**는 기존 서버 정보를 수집하며, **Migration Hub(과거 이름)**는 진행 상태를 추적하는 역할이었다. 2025-11부터 두 서비스는 신규 고객이 사용할 수 없고, 신규 프로젝트는 **AWS Transform**이 대응하는 조사·계획·진행 기능을 제공한다.

## 🇰🇷 2. Agentless와 Agent-based 수집의 차이
| 수집 방식 | 설치 위치 | 주요 데이터 |
|---|---|---|
| **Agentless** | VMware 관리 환경 등에 별도 Collector 설치; 각 VM OS에 Agent를 설치하지 않음 | VM 구성, 성능 사용량, 지원되는 연결 정보 등 |
| **Agent-based** | 개별 서버 OS 내부에 Discovery Agent 설치 | CPU·메모리·프로세스, 연결 정보, 종속성 분석 자료 등 |

**Server A(웹) → Server B(앱) → Server C(DB)** 관계를 파악하면 'A만 옮길지, B와 C도 함께 이동할지' 결정하기 쉬워진다. Discovery는 데이터 수집과 관계 분석을 돕지, **DMS를 사용하라**고 자동 확정하는 시스템이 아니다.

## 🇰🇷 3. MGN = Rehosting / Lift-and-Shift
**AWS Application Migration Service(MGN)**는 2026년 6월 **AWS Transform MGN**으로 이름이 바뀌었다. OS·애플리케이션·설치된 DB·디스크 데이터 등을 최대한 유지한 채 기존 서버를 **일반 EC2**로 이전하는 데 활용한다. 이를 **Lift-and-Shift/Rehosting**이라고 한다.

```text
[온프레미스 서버]
 OS + Web/App + (경우에 따라 MySQL) + 데이터
          │
          │ Replication Agent / 지원되는 방식의 복제
          ▼
[AWS Staging Area]
 저비용 Replication Server + EBS 등 복제 스토리지
          │    초기 전체 동기화 → 변경된 블록 계속 반영
          │
          ├─ Test Launch (테스트 서버로 부팅·검증)
          └─ Cutover (최종 운영 이전)
                    ▼
           [운영 EC2 + EBS]
```

**Staging Area**는 데이터를 동기화하는 준비 영역으로, 운영용 EC2가 이미 사용자 서비스를 제공한다는 뜻이 아니다. Cutover는 AWS에 최종 서버를 띄우고 원본 서버에서 실제 운영을 넘기는 작업이다. 다운타임을 줄일 수 있지만 완전 무중단은 보장하지 않는다.

## 🇰🇷 4. MGN과 DMS는 경쟁 관계가 아니다
| 실제 요구 | 가능한 선택 |
|---|---|
| Linux + 설치된 MySQL + DB 데이터를 환경째 EC2로 가져오기 | **MGN**: EC2에서 직접 DB 관리(Self-managed DB) |
| 기존 MySQL의 **데이터만** RDS MySQL로 옮기기 | **DMS**: AWS 관리형 DB에 적재 |
| 웹/앱 서버는 EC2, DB는 Aurora로 재구성 | **MGN + DMS**를 조합 |

MGN으로 DB 서버 전체를 옮기면 **RDS Multi-AZ·Aurora Global Database 같은 관리형 DB 기능이 자동 적용되지 않는다**. 그래서 빠른 1차 이전은 MGN으로 하고, 후속 개선 과정에서 DB를 RDS/Aurora로 분리 이전할 수도 있다. 이것이 Rehosting과 Replatforming 전략의 차이다.

## 🇰🇷 5. 최신 주의점
기본 강의에서는 Replication Agent 설치 기반 흐름을 설명했지만, **MGN에는 VMware 환경에 한정된 별도 Agentless 복제 방식**도 있다. 이때도 회사 내부에 MGN vCenter Client 등의 구성요소가 필요하다. 서비스/엔진별 지원 조건을 확인한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Discovery Agent와 MGN Replication Agent는 목적이 다르다.
- MGN = OS/앱/DB 설치 환경을 EC2로 리호스팅, DMS = 데이터 이전.
- Staging Area는 복제 공간; 운영용 EC2와 구분.
- Cutover는 실제 서비스 전환이며 복제 완료/정합성 확인이 필요.
- 2026년 정식 이름 AWS Transform MGN, MGN에 VMware용 Agentless 복제도 존재.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 회사 서버 중 어떤 서버가 DB에 의존하는지 파악하려면?

<details>
<summary>정답 및 해설 보기</summary>

**Discovery/Dependency Mapping**이 먼저 필요하다.

</details>

### Q2. 기존 Linux, 앱, MySQL 설치 환경을 EC2로 통째로 복제하고 싶다면?

<details>
<summary>정답 및 해설 보기</summary>

**AWS Transform MGN**의 Rehosting을 고려한다.

</details>

### Q3. MGN Staging Area는 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

원본 서버의 디스크를 계속 동기화하며 **컷오버를 준비하는 영역**이다.

</details>

### Q4. 온프레미스 웹 서버는 EC2, MySQL 데이터는 RDS로 이동하려면?

<details>
<summary>정답 및 해설 보기</summary>

**MGN과 DMS를 함께** 사용할 수 있다.

</details>

### Q5. MGN이 이전한 MySQL은 자동으로 RDS가 되나?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** EC2 내부의 자기관리형 MySQL로 남는다.

</details>

## 🇯🇵 日本語 Summary

Discovery はサーバー構成や依存関係を調査し、MGN は OS・アプリ・ディスクを EC2 へ Rehosting する。MGN の Staging Area はデータを継続レプリケーションし、Cutover で本番を切り替える。DMS は DB データ移行なので MGN と併用できる。

## 🇺🇸 English Summary

Discovery maps server dependencies; AWS Transform MGN rehosts whole servers through replication, staging, test launches, and cutover. DMS migrates database data. Both can be used together when application servers move to EC2 and databases move to RDS or Aurora.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Agentless | 서버 내부 에이전트 없는 수집/복제 | エージェントレス |
| Agent-based | 에이전트 기반 | エージェント方式 |
| Rehosting | 그대로 서버 이전 | リホスト |
| Staging Area | 이전 준비 영역 | ステージング領域 |
| Cutover | 운영 전환 | カットオーバー |
| Block-level Replication | 블록 단위 복제 | ブロック単位のレプリケーション |

## 🔗 Official References

- https://aws.amazon.com/about-aws/whats-new/2026/06/aws-transform-mgn-rebrand/
- https://docs.aws.amazon.com/mgn/latest/ug/migration_at_scale.html
