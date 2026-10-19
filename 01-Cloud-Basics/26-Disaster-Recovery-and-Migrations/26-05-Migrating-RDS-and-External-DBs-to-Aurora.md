# 📘 26-05. Migrate MySQL & PostgreSQL to Amazon Aurora

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Aurora MySQL` · `Aurora PostgreSQL` · `Snapshot` · `Aurora Read Replica` · `Percona XtraBackup` · `mysqldump` · `aws_s3` · `AWS DMS`

## 🇰🇷 1. 먼저 DB 명칭을 구분한다
**MySQL, PostgreSQL, Oracle**은 서로 다른 DB 엔진(제품/기술)이고, **RDS**는 이런 엔진을 AWS가 관리하도록 제공하는 플랫폼이다. **Aurora**는 AWS의 관리형 DB로 MySQL 호환 및 PostgreSQL 호환 버전을 제공한다. **Percona XtraBackup**은 MySQL을 백업하는 도구이지 DB 엔진이 아니다.

## 🇰🇷 2. RDS MySQL → Aurora MySQL
| 방법 | 작동 방식 | 특징 |
|---|---|---|
| **Snapshot 복원** | RDS MySQL Snapshot 생성 → 새 Aurora MySQL 클러스터 복원 | 단순하지만 Snapshot 이후의 쓰기는 자동 동기화되지 않음 |
| **Aurora Read Replica** | RDS MySQL 원본 → Aurora Replica 복제 → Lag 확인 → 독립 클러스터로 Promote | 쓰기 중단 기간을 줄이기 유리 |

```text
RDS MySQL ───── Snapshot ──▶ 신규 Aurora MySQL
RDS MySQL ── 지속 복제 ──▶ Aurora Read Replica ── Promote ──▶ Aurora Primary
```

**Lag 0**은 특정 시점에 복제 지연이 없다는 의미에 가깝다. 최종 Cutover에서는 원본의 새 쓰기를 통제하고 완전 반영을 재확인하며 앱 접속 주소를 바꿔야 할 수 있다. 엔진 버전·리전·기능 지원 여부는 확인한다.

## 🇰🇷 3. 외부 MySQL(온프레미스/직접 설치) → Aurora
- **Percona XtraBackup + S3**: MySQL의 **물리적 데이터 파일**을 백업 → S3에 저장 → 지원되는 Aurora MySQL 버전으로 **새 클러스터**를 생성하며 가져옴. 큰 DB를 효율적으로 이전하는 사례. 호환 엔진/버전 조건이 있다.
- **mysqldump**: 테이블 정의와 행 데이터를 **SQL 문장으로 논리적 덤프** → Aurora MySQL에서 SQL 재실행. 기존 클러스터로 가져오는 경우에도 쓰고 S3는 필수가 아니다. 대규모 DB에서는 오래 걸릴 수 있다.
- **AWS DMS + CDC**: 초기 데이터 적재와 이후 변경 사항 지속 반영으로 중단 시간을 줄인다.

```text
XtraBackup : 원본 MySQL ─ 물리 백업 ─ S3 ─ 신규 Aurora 클러스터
mysqldump  : 원본 MySQL ─ SQL 덤프 ───────▶ Aurora에 Import
DMS        : 원본 MySQL ─ Full Load + CDC ▶ Aurora
```

## 🇰🇷 4. PostgreSQL → Aurora PostgreSQL
RDS PostgreSQL이라면 **Snapshot 복원** 또는 지원되는 **Aurora Read Replica + Promote** 전략을 검토한다. 외부 PostgreSQL이라면 `pg_dump/pg_restore` 같은 네이티브 도구, **DMS+CDC**를 활용할 수 있다.

강의에서 나온 **`aws_s3` 확장**은 S3의 CSV/텍스트 등 데이터 파일을 **기존 Aurora PostgreSQL 테이블로 가져오는 기능**이다. MySQL의 Percona 물리 백업을 S3에서 새 클러스터로 복원하는 기능과 혼동하지 않는다.

## 🇰🇷 5. 시험에서의 결정 기준
'간단한 1회성 RDS → Aurora'면 Snapshot. 'RDS MySQL/PostgreSQL에서 중단 시간 단축'이면 Aurora 읽기 복제본·승격. '외부 MySQL 대용량 백업으로 새 Aurora 생성'이면 XtraBackup+S3. '외부 DB의 계속 바뀌는 데이터를 복제'는 DMS+CDC. 특정 경로가 모든 버전에서 항상 지원되는 것은 아니다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- RDS MySQL/PostgreSQL → Aurora: Snapshot 또는 지원되는 Aurora Read Replica → Promote.
- Percona XtraBackup: 외부 **MySQL 물리 백업** → S3 → **새 Aurora MySQL**.
- mysqldump: SQL 논리 덤프, 기존 DB Import에 사용 가능.
- `aws_s3`: **Aurora PostgreSQL 테이블로 S3 데이터 가져오기**, 전체 DB 물리 복원 아님.
- CDC는 이전 중 원본 DB 사용을 지속할 때 중요하다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. RDS MySQL을 간단하게 Aurora MySQL로 옮기되 다운타임을 허용한다면?

<details>
<summary>정답 및 해설 보기</summary>

**Snapshot → 신규 Aurora MySQL 복원**이 대표 경로다.

</details>

### Q2. 원본 RDS를 계속 쓰면서 Aurora에 복제한 뒤 최종 승격하려면?

<details>
<summary>정답 및 해설 보기</summary>

지원되는 **Aurora Read Replica를 생성하고 복제 지연 확인 후 Promote**한다.

</details>

### Q3. 온프레미스 MySQL 물리 백업을 S3에 올려 새 Aurora를 만들려면?

<details>
<summary>정답 및 해설 보기</summary>

**Percona XtraBackup + S3** 방식이다.

</details>

### Q4. mysqldump는 DB 엔진의 이름인가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** MySQL 데이터를 SQL 구문으로 추출하는 백업/이전 도구다.

</details>

### Q5. Aurora PostgreSQL의 aws_s3 확장은 무엇을 하는가?

<details>
<summary>정답 및 해설 보기</summary>

**S3의 데이터 파일을 Aurora PostgreSQL 테이블로 가져오는 기능**이다.

</details>

## 🇯🇵 日本語 Summary

RDS MySQL / PostgreSQL から Aurora へは Snapshot 復元または対応する Read Replica の昇格を利用できる。外部 MySQL からは Percona XtraBackup + S3、mysqldump、DMS + CDC などを選ぶ。Aurora PostgreSQL の aws_s3 は S3 データをテーブルへ取り込む拡張機能。

## 🇺🇸 English Summary

Migration paths to Aurora include RDS snapshots, supported Aurora read replicas promoted after synchronization, Percona XtraBackup and S3 for physical MySQL imports, mysqldump for logical SQL imports, and DMS with CDC. The aws_s3 extension imports data into Aurora PostgreSQL tables.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Snapshot | 스냅샷 | スナップショット |
| Read Replica | 읽기 복제본 | リードレプリカ |
| Promotion | 승격 | 昇格 |
| Physical Backup | 물리 백업 | 物理バックアップ |
| Logical Dump | 논리 덤프 | 論理ダンプ |
| Replication Lag | 복제 지연 | レプリケーション遅延 |
