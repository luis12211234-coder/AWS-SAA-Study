# 📘 26-04. AWS DMS Console Lab: Endpoints, Tasks & Serverless

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Fleet Advisor` · `DMS Schema Conversion` · `Endpoint` · `Replication Task` · `Serverless` · `IAM Role` · `VPC`

## 🇰🇷 1. DMS 콘솔의 큰 메뉴: Assess → Convert → Migrate
이론에서 배운 과정을 콘솔 메뉴로 나누면 **Assess(평가) → Convert(스키마 변환) → Migrate(데이터 이전)**이다.

- 과거 **DMS Fleet Advisor**: 온프레미스 DB 인벤토리, 사용량, 잠재적 이전 대상 추천. **2026-05-20 지원 종료**. 강의 역사적 개념으로 기억한다.
- **DMS Schema Conversion**: 이종 DB의 스키마 호환성 분석 및 변환.
- **Homogeneous Data Migrations**: 동종 DB 엔진 간 이전 시 엔진 고유의 네이티브 도구 활용이 가능한 관리형 경로.
- **Serverless / 인스턴스 기반 DMS**: 실제 데이터 이동 수행. Serverless는 컴퓨팅 규모 관리를 자동화하며 모든 엔진·옵션을 보장하지 않는다.

## 🇰🇷 2. 실습 핵심 순서
```text
① Replication Instance 생성
      ↓
② Source Endpoint + Target Endpoint 생성
      ↓
③ Connection Test
      ↓
④ Database Migration Task 생성
      ↓
⑤ 작업 실행 · 작업 상태/복제 지연 모니터링
```
강사는 **비용과 원본 DB 부재 때문에 실제 작업을 실행하지 않고 설정 화면만 확인**했다.

## 🇰🇷 3. Step 1: Replication Instance 화면
| 콘솔 옵션 | 고르는 이유 |
|---|---|
| Name / Description | 리소스 식별 |
| Instance Class | 복제 작업에 필요한 CPU/메모리. 데이터량·변경량이 크면 성능 검토 |
| DMS Engine Version | 기능·호환성·릴리스 노트 확인 |
| Single-AZ / Multi-AZ | 실습 비용 절감 vs 프로덕션 장애 대비 |
| Allocated Storage | 복제 중 임시 데이터/로그 등을 위한 공간 |
| VPC / Replication Subnet Group | DMS 복제 서버가 사용할 네트워크 위치 |
| Publicly Accessible | 요구되는 DB 연결 방식과 보안 정책에 따라 설정 |
| Maintenance | 관리형 복제 엔진 유지보수 옵션 |

**네트워크 조건:** DMS 복제 인스턴스는 Source/Target DB 양쪽에 접근 가능해야 한다. SG, 경로, 포트, 온프레미스 VPN/DX 등의 네트워크를 확인한다. DB가 프라이빗 서브넷에 있어도 적절한 네트워크 연결만 확보하면 복제할 수 있다.

## 🇰🇷 4. Step 2: Endpoint 생성
**DMS Endpoint**는 소스/타깃 DB의 엔진, Hostname, Port, DB 사용자 정보 등 **접속 정의**다. 이것은 VPC Gateway/Interface Endpoint와 전혀 다른 종류의 'Endpoint'다. RDS라면 기존 RDS 인스턴스를 선택하기 쉬운 옵션이 있다. Wizard 방식과 JSON 기반 상세 설정을 사용할 수 있다. **Test Connection**으로 DMS 인스턴스에서 DB에 접근 가능한지 확인한다.

## 🇰🇷 5. Step 3: Migration Task 생성
Task에서 **Replication Instance + Source Endpoint + Target Endpoint**를 지정하고 `Full load`, `Full load + ongoing replication(CDC)`, `CDC only` 중 하나를 고른다. 테이블 선택/매핑, LOB 처리, 에러 처리, 검증, 로깅 등 세부 설정을 Wizard 또는 JSON으로 구성한다. 필요하면 사전 평가(Assessment)와 테스트를 실행한다.

## 🇰🇷 6. 시험에서 혼동하면 안 되는 것
- **Replication Instance**: 누가 복제할까? → 복제 컴퓨팅 자원.
- **Endpoint**: 어디서 읽고 어디로 쓸까? → DB 접속 정보.
- **Task**: 무엇을 어떤 방식으로 옮길까? → 마이그레이션 실행 정의.
- **Serverless**: 복제 서버의 용량 관리 부담을 줄이는 옵션이지, 엔진 호환성 검증이 불필요해지는 것은 아니다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- 콘솔 순서: Replication Instance → 양쪽 Endpoint → Task.
- Endpoint는 DMS의 DB 접속 정의; VPC Endpoint와 별개.
- Full Load+CDC는 Task에서 선택한다.
- Serverless가 모든 엔진/기능을 지원한다고 가정하지 않는다.
- 과거 Fleet Advisor는 **2026-05-20 종료**.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. DMS가 연결할 원본 DB와 대상 DB 주소·인증정보를 정의하는 리소스는?

<details>
<summary>정답 및 해설 보기</summary>

**Source/Target Endpoint**다.

</details>

### Q2. 어떤 테이블을 Full Load + CDC로 옮길지 지정하는 것은?

<details>
<summary>정답 및 해설 보기</summary>

**Database Migration Task**에서 지정한다.

</details>

### Q3. 복제 엔진의 CPU/메모리와 Multi-AZ를 선택하는 화면은?

<details>
<summary>정답 및 해설 보기</summary>

**Replication Instance 생성** 화면이다.

</details>

### Q4. DMS Endpoint와 VPC Interface Endpoint는 같은 기능인가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** DMS Endpoint는 DB 연결 정의이고 VPC Endpoint는 사설 서비스 연결 기능이다.

</details>

### Q5. 강의의 Fleet Advisor는 2026년 신규 구축에도 그대로 쓸 수 있는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 2026-05-20 지원이 종료됐다.

</details>

## 🇯🇵 日本語 Summary

DMS コンソールの基本手順は Replication Instance → Source/Target Endpoint → Migration Task。Endpoint は接続設定、Task は Full Load / CDC などの移行方法を定義する。Serverless は容量管理を簡素化する。Fleet Advisor は 2026 年 5 月に終了した。

## 🇺🇸 English Summary

The classic DMS workflow is to create a replication instance, define source and target endpoints, test connectivity, and create a migration task. Tasks select Full Load and/or CDC. DMS Serverless manages capacity; Fleet Advisor ended support in May 2026.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Endpoint | DB 접속 정의 | エンドポイント |
| Migration Task | 마이그레이션 작업 | 移行タスク |
| Replication Subnet Group | 복제 서브넷 그룹 | レプリケーションサブネットグループ |
| Serverless | 서버리스 | サーバーレス |
| Assessment | 사전 평가 | アセスメント |

## 🔗 Official References

- https://docs.aws.amazon.com/dms/latest/userguide/dms_fleet.advisor-end-of-support.html
