# 📘 25-15. Flow Logs Lab: S3, CloudWatch & Athena

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Flow Logs Lab` · `CloudWatch Logs` · `Metric Filter` · `SNS` · `S3` · `Athena` · `Partitions` · `QuickSight`

## 🇰🇷 1. Flow Logs 생성과 필터
VPC → Flow Logs → Create에서 **Accept / Reject / All** 필터를 선택한다. 최대 집계 간격은 보통 1분 또는 10분 옵션이 있고, 로그가 집계되는 시간과 저장소에 실제 도착하는 시점은 다를 수 있다. Nitro 인스턴스에서 10분을 설정해도 1분 이하로 집계될 수 있다. 실습에선 1분+All을 사용해 결과를 빨리 확인했다.

## 🇰🇷 2. S3 대상 설정
S3 버킷 생성 → **Bucket ARN** 복사 → Flow Logs의 S3 Destination 설정. 전달 서비스가 기록할 수 있도록 버킷 정책이 필요하다. 고객 계정의 데이터/보안 정책을 확인하며 실제 환경에서는 최소 권한과 보관 주기를 설정한다.

## 🇰🇷 3. CloudWatch Logs 대상 설정
CloudWatch Logs **Log Group**을 생성한다. `vpc-flow-logs.amazonaws.com` 서비스가 맡을 IAM Role을 만들고 특정 로그 그룹에 기록할 권한을 준다. 강의는 `CloudWatchLogsFullAccess`를 붙였으나 실무에서는 범위가 과도하므로 최소 권한이 바람직하다. CloudWatch Logs 안에서 ENI별 **Log Stream**에 기록이 생성되는 모습을 확인했다.

## 🇰🇷 4. S3 + Athena SQL 분석 흐름
```text
VPC Flow Logs → S3 원본 로그 버킷
                   ↓
            Athena External Table
              + 날짜 Partition
                   ↓
            SELECT ... WHERE action='REJECT'
                   ↓
               QuickSight
```

Athena의 **쿼리 결과 저장 위치**는 원본 로그 버킷과 목적이 다르다. `CREATE EXTERNAL TABLE`의 `LOCATION 's3://.../AWSLogs/<account>/vpcflowlogs/<region>/'`로 실제 로그 위치를 지정한다. `ALTER TABLE ADD PARTITION`은 특정 날짜 파티션의 위치를 등록한다. Glue/Partition Projection으로 자동화할 수 있다.

## 🇰🇷 5. 운영 모니터링과 비용
CloudWatch **Contributor Insights**는 빈번한 IP 등의 상위 기여자를 분석한다. **Metric Filter → CloudWatch Alarm → SNS**로 SSH/RDP 거부가 급증했을 때 알림을 보낼 수 있다. Athena는 그룹별 거부 건수 SQL 집계에 적합하다. 다만 `COUNT(*)`는 로그 **레코드 수**이며 실제 패킷 개수와 다르다.

Flow Log 삭제 후에도 S3 객체, CloudWatch 로그 그룹, Athena 결과 버킷은 남을 수 있다. 수집·저장·쿼리 비용을 확인한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- 최근 로그 분석/알람 → CloudWatch Logs, 대량 SQL 분석 → S3 + Athena.
- `CREATE EXTERNAL TABLE`은 파일 구조와 S3 경로를 정의한다.
- Partition은 날짜별 읽기 범위를 제한해 스캔을 줄인다.
- Metric Filter → Alarm → SNS는 실시간 경보 파이프라인.
- Role과 Bucket Policy는 로그 서비스의 쓰기 권한을 다룬다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. CloudWatch에서 거부 SSH 횟수 임계값 경보를 보내려면?

<details>
<summary>정답 및 해설 보기</summary>

**Flow Logs → CloudWatch Logs → Metric Filter → Alarm → SNS.**

</details>

### Q2. Athena의 Query Result S3 경로와 External Table LOCATION은 같은 역할인가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 전자는 SQL 결과 저장, 후자는 원본 로그 위치.

</details>

### Q3. S3 로그를 날짜별로 효율적으로 읽도록 등록하는 것은?

<details>
<summary>정답 및 해설 보기</summary>

**Partition.**

</details>

### Q4. CloudWatch 로그 그룹에 Flow Logs를 쓰려면 필요한 것은?

<details>
<summary>정답 및 해설 보기</summary>

**로그 그룹과 VPC Flow Logs 서비스용 IAM Role/권한.**

</details>

### Q5. Flow Log를 삭제하면 과거 S3 로그 파일도 자동 삭제되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 저장된 객체와 관련 리소스를 따로 정리해야 한다.

</details>

## 🇯🇵 日本語 Summary

Flow Logs を S3 または CloudWatch Logs へ配信する。CloudWatch では Metric Filter → Alarm → SNS による通知、S3 では Athena External Table/Partition による SQL 分析ができる。出力先と権限、保存費用に注意。

## 🇺🇸 English Summary

The lab delivers VPC Flow Logs to S3 and CloudWatch Logs. CloudWatch supports filter-based alarms via SNS; Athena reads S3 logs using external table schemas and partitions. Query-results storage differs from source-log storage. Costs persist while logs accumulate.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Log Group | 로그 그룹 | ロググループ |
| Log Stream | 로그 스트림 | ログストリーム |
| Metric Filter | 메트릭 필터 | メトリクスフィルター |
| External Table | 외부 테이블 | 外部テーブル |
| Partition | 파티션 | パーティション |
