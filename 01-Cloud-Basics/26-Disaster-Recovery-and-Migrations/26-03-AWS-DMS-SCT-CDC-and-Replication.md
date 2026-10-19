# 📘 26-03. AWS DMS: Migration, SCT, CDC & Multi-AZ

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `AWS DMS` · `SCT` · `Schema Conversion` · `Full Load` · `CDC` · `Replication Instance` · `Multi-AZ`

## 🇰🇷 1. DMS는 서버 통째가 아니라 데이터 마이그레이션
**AWS Database Migration Service(DMS)**는 원본 데이터베이스/저장소의 데이터를 대상 데이터베이스/저장소로 옮기거나 계속 복제하는 관리형 서비스다. 원본 시스템을 운영 상태로 유지하면서 이전할 수 있으며, 복제 지연 및 최종 전환 절차를 관리해야 한다.

```text
Source DB (회사 Oracle) ──읽기──▶ DMS 복제 컴퓨팅 ──쓰기──▶ Target DB (RDS MySQL)
```

**Source = 가져오는 쪽, Target = 데이터를 넣는 쪽.** DMS는 온프레미스뿐 아니라 EC2, RDS, Azure SQL Database 등 다양한 소스와 RDS/Aurora, S3, Redshift, DynamoDB, OpenSearch, Kinesis, Kafka, DocumentDB, Neptune 등 여러 타깃 엔진/서비스를 지원한다. 단 **소스·타깃의 모든 조합이 가능한 것은 아니다**. 실제 조합은 공식 호환표에서 확인한다.

## 🇰🇷 2. 동종 vs 이종 마이그레이션
| 종류 | 예시 | 스키마 변환 |
|---|---|---|
| **Homogeneous(동종)** | 회사 PostgreSQL → RDS PostgreSQL | 보통 별도 이종 변환 불필요 |
| **Heterogeneous(이종)** | Oracle → RDS PostgreSQL | 스키마/DB 코드 변환을 고려 |

**RDS**는 관리형 데이터베이스 플랫폼이지 PostgreSQL과 별개의 DB 엔진이 아니다. `PostgreSQL → RDS PostgreSQL`은 같은 엔진이다.

## 🇰🇷 3. SCT와 DMS의 역할 차이
**SCT(Schema Conversion Tool)** 또는 관리형 **DMS Schema Conversion**은 DB 엔진이 다를 때 테이블 정의, 컬럼 자료형, SQL 코드 등의 호환성 차이를 분석·변환한다. DMS는 실제 데이터 행과 변경 사항을 이전한다.

```text
Oracle DB ──(스키마 구조/SQL)──▶ SCT / Schema Conversion ──▶ PostgreSQL용 구조
Oracle DB ──(테이블 데이터/변경분)─▶ AWS DMS ──────────────▶ PostgreSQL 데이터
```

변환된 스키마나 프로시저가 모두 100% 자동 호환된다는 뜻은 아니다. 수동 수정과 테스트가 필요할 수 있다. OLTP(Oracle/SQL Server → Aurora/MySQL/PostgreSQL), 분석계(Teradata/Oracle → Redshift) 같은 경우도 고려할 수 있다.

## 🇰🇷 4. Full Load와 CDC
| Task 유형 | 복제 방식 |
|---|---|
| **Full Load** | 기존 데이터 한 번 적재 |
| **CDC Only** | DB 로그 등의 변경 이벤트를 지속 반영 |
| **Full Load + CDC** | 기존 데이터를 옮긴 뒤 새 INSERT/UPDATE/DELETE도 따라감 |

```text
초기 시점: 주문 100만 건 ── Full Load ──▶ Target
이전 도중: 주문 추가·수정 ─── CDC ──────▶ Target
최종 Cutover: 새 쓰기 중단 → CDC 지연 확인 → 새 DB로 연결 전환
```
CDC는 대체로 비동기이므로 지연이 생길 수 있다. **Full Load + CDC는 다운타임 최소화에 유리하나 자동 무중단 보장은 아니다.**

## 🇰🇷 5. 복제 인스턴스와 Multi-AZ
전통적 **Replication Instance**는 DMS가 생성·관리하는 EC2 기반 복제 컴퓨팅 자원이다. 사용자는 사양·스토리지·VPC·서브넷·공개 접근 여부를 선택하지만 일반 EC2를 직접 운영하는 개념과 다르다. **Multi-AZ**는 다른 AZ에 대기 복제 인스턴스를 두어 복제 작업의 가용성을 높인다. 원본/타깃 DB가 자동 Multi-AZ가 되는 것은 아니다.

최신 **DMS Serverless**는 복제 컴퓨팅 용량 관리를 줄여 주지만 엔진·기능 지원에 제한이 있을 수 있다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- DMS = 데이터 이동, SCT/Schema Conversion = 구조·SQL 호환성 변환.
- 동종 PostgreSQL → RDS PostgreSQL에 SCT는 일반적으로 불필요.
- Full Load + CDC = 기존 데이터 + 이후 변경 사항 복제.
- 복제 인스턴스는 DMS가 관리하며, Multi-AZ는 복제 작업의 가용성.
- MGN은 서버/디스크 리호스팅, DMS는 DB 데이터 마이그레이션.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Oracle에서 RDS PostgreSQL로 이전하며 테이블 구조를 맞추려면?

<details>
<summary>정답 및 해설 보기</summary>

**SCT 또는 DMS Schema Conversion**이 구조 변환을 담당한다.

</details>

### Q2. 회사 PostgreSQL을 RDS PostgreSQL로 옮기면 이종 마이그레이션인가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다. 동종**이다. RDS는 플랫폼, PostgreSQL은 DB 엔진이다.

</details>

### Q3. 마이그레이션 도중 신규 주문 정보도 계속 따라가게 하려면?

<details>
<summary>정답 및 해설 보기</summary>

**Full Load + CDC**로 기존 데이터와 변경 데이터를 함께 적재한다.

</details>

### Q4. DMS Multi-AZ를 선택하면 원본 Oracle DB가 자동으로 Multi-AZ가 되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** DMS 복제 컴퓨팅의 가용성을 높이는 것이다.

</details>

### Q5. 데이터베이스 소프트웨어와 OS를 그대로 EC2로 옮기는 서비스는?

<details>
<summary>정답 및 해설 보기</summary>

**MGN**을 고려한다. DMS는 DB 데이터를 이전한다.

</details>

## 🇯🇵 日本語 Summary

DMS はデータの移行・継続レプリケーションを担当し、SCT / Schema Conversion は異種エンジン間のスキーマ・SQL 変換を担当する。Full Load + CDC により初期データと更新分を移行できる。Multi-AZ は DMS のレプリケーション作業の可用性を高める。

## 🇺🇸 English Summary

AWS DMS moves database data and ongoing changes. SCT or DMS Schema Conversion handles incompatible schemas across database engines. Full Load plus CDC supports low-downtime migrations; DMS Multi-AZ protects replication infrastructure, not the source database itself.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Database Migration | 데이터베이스 이전 | データベース移行 |
| Schema Conversion | 스키마 변환 | スキーマ変換 |
| Change Data Capture | 변경 데이터 캡처 | 変更データキャプチャ |
| Replication Instance | 복제 인스턴스 | レプリケーションインスタンス |
| Homogeneous | 동종 | 同種 |
| Heterogeneous | 이종 | 異種 |
