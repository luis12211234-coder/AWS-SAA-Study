# 📘 22-04. CloudWatch Alarms, Composite Alarms & EC2 Recovery

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Alarm` · `Threshold` · `Period` · `Evaluation Periods` · `Datapoints to Alarm`

## 🇰🇷 1. CloudWatch Alarm이란?

지표(Metric)를 지속적으로 평가하여 임계 조건을 만족하면 상태를 변경하고 알림이나 자동 작업을 실행한다.

```text
EC2 CPUUtilization → CloudWatch Metric → CloudWatch Alarm → SNS / EC2 Action / Auto Scaling
```

### 세 가지 상태

| 상태 | 의미 |
|---|---|
| **OK** | 평가한 지표가 경보 조건을 충족하지 않음 |
| **ALARM** | 경보 조건 충족 |
| **INSUFFICIENT_DATA** | 데이터가 부족하거나 사용할 수 없어 정상 평가가 어려움 |

누락된 지표를 어떻게 처리할지는 Missing Data Treatment 설정에도 영향을 받는다. **ALARM 상태 = SNS 알림 발송 완료**가 아니다. SNS 등의 Action을 연결해야 한다.

## 🇰🇷 2. Alarm 평가 방법

| 항목 | 의미 | 예시 |
|---|---|---|
| Metric | 감시할 숫자 지표 | `AWS/EC2`의 `CPUUtilization` |
| Statistic | 데이터를 요약할 통계 | `Average`, `Sum`, `Maximum`, p95 |
| Threshold | 허용 기준 | `> 95%` |
| Period | 데이터 한 개의 평가 기간 | 5분 |
| Evaluation Periods | 최근 검사 기간 개수(N) | 3 |
| Datapoints to Alarm | N개 중 위반해야 하는 최소 개수(M) | 3 |

### 실제 강의 실습: 3 out of 3

5분 주기에서 최근 CPU 평균 97%, 98%, 99%라면 세 구간 모두 95% 초과이므로 ALARM이 될 수 있다.

```text
Period 1 (5분): 97% > 95% ✓
Period 2 (5분): 98% > 95% ✓
Period 3 (5분): 99% > 95% ✓
→ 3/3 임계값 초과 → ALARM
```

- `3 out of 3`: 최근 3개 전부 위반.
- `2 out of 3`: 최근 3개 중 2개 이상 위반.
- 기본 EC2 CPU 지표는 대개 5분 간격, Detailed Monitoring 시 1분 간격.
- **High-Resolution Alarm:** 고해상도 지표라면 10초·30초 등 짧은 Period 선택 가능(요금·해상도 확인).

## 🇰🇷 3. Alarm Actions

| 작업 | 사용 사례 |
|---|---|
| **Amazon SNS** | 이메일·메시지 알림 |
| **AWS Lambda** | 사용자 지정 자동 대응 (직접 호출 지원) |
| **Auto Scaling** | 정책 기반 Scale Out / Scale In |
| **EC2 Stop** | 유휴 인스턴스 중지 등 |
| **EC2 Terminate** | 더 이상 필요 없는 인스턴스 종료·삭제 |
| **EC2 Reboot** | 인스턴스 재부팅 |
| **EC2 Recover** | 지원되는 인스턴스의 호스트 장애 복구 |

> **실습 주의:** 강사는 CPU >95%, 3/3 Alarm에 EC2 Terminate를 연결했지만, CPU 부하는 정상 트래픽 때문에 증가할 수도 있다. **실무에서 높은 CPU를 이유로 곧바로 Terminate하면 데이터 손실과 장애를 유발할 수 있다.** 실습용에는 SNS 알림을 권장한다.

## 🇰🇷 4. Composite Alarm

개별 Metric Alarm들의 **상태**를 논리식으로 조합한다. 원본 Metrics를 직접 여러 개 비교하는 기능으로만 이해하지 말 것.

```text
Alarm A: CPUHigh = ALARM
Alarm B: NetworkHigh = OK
Composite: ALARM(A) AND NOT ALARM(B)
→ ALARM (CPU 높고 네트워크는 높지 않을 때)
```

- `AND`, `OR`, `NOT` 조건 활용.
- 여러 개의 기본 Alarm을 묶어 불필요한 경보(Alarm Noise)를 줄일 수 있다.
- 단일 Metric Alarm도 Metric Math 등을 지원한다. **Composite는 다른 Alarm의 상태를 조합한다**는 점이 결정적 차이.

## 🇰🇷 5. EC2 Status Checks & Recovery

| EC2 상태 검사 | 감시 대상 | 대표 대응 |
|---|---|---|
| **Instance Status Check** | EC2 가상 머신/OS 내부 | OS 조사·Reboot |
| **System Status Check** | AWS 물리 호스트 및 기반 인프라 | 지원 시 EC2 Recover |
| **Attached EBS Status Check** | 연결된 EBS 스토리지 상태 | 스토리지 조사 |

**시험 공식:** `StatusCheckFailed_System` → EC2 Recovery를 고려.

EC2 Recovery는 호스트 장애에서 인스턴스를 복구하며 기존 Instance ID, Private IP, Elastic IP 등 주요 정보를 유지할 수 있다. 지원 조건을 확인해야 하며 RAM이나 임시 Instance Store 데이터는 보장되지 않는다. Reboot와 Recover를 동일시하지 않는다.

## 🇰🇷 6. SetAlarmState 테스트

CloudWatch Alarm을 실제 부하 없이 일시적으로 ALARM 상태로 바꿀 수 있다.

```bash
aws cloudwatch set-alarm-state \
  --alarm-name "my-test-alarm" \
  --state-value ALARM \
  --state-reason "testing"
```

- 경보 이름, 강제로 설정할 상태, 이유를 전달한다.
- **CPU 사용률을 실제로 높이는 명령이 아니다.** 상태 전환 시 연결된 EC2 종료 등 실제 작업이 실행될 수 있다.
- 다음 정상 평가 시 원래 상태로 돌아갈 수 있다.
- Alarm History에서 상태 변경 및 동작 기록을 확인한다.
- 반드시 **SNS-only 테스트 Alarm**부터 사용하고, Terminate 같은 파괴적 Action에는 함부로 사용하지 않는다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- CPU 80% 초과 시 이메일 → CloudWatch Alarm → SNS.
- 2 out of 3 데이터 포인트 → `Datapoints to Alarm=2`, `EvaluationPeriods=3`.
- 여러 Alarm을 AND/OR → Composite Alarm.
- 물리 호스트 장애 → `StatusCheckFailed_System` / EC2 Recovery.
- 경보 동작만 테스트 → `SetAlarmState`.
- 로그의 ERROR 횟수 기반 경보 → [Metric Filter](./22-03-CloudWatch-Logs-Analysis-and-Delivery.md) + Alarm.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. CloudWatch Alarm의 상태 세 가지가 아닌 것은?

<details>
<summary>정답 및 해설 보기</summary>

**정답:** COMPLIANT

COMPLIANT는 AWS Config 규칙 평가 상태다. CloudWatch Alarm 상태는 OK/ALARM/INSUFFICIENT_DATA이다.

</details>

### Q2. EC2 CPU가 95%를 초과하는 5분짜리 데이터 포인트 3개가 연속으로 발생할 때 ALARM으로 설정하려면?

<details>
<summary>정답 및 해설 보기</summary>

**정답:** Period 5분, 3/3

5분 Period, EvaluationPeriods=3, DatapointsToAlarm=3. 세 5분 구간이 모두 위반해야 한다.

</details>

### Q3. CPUHigh Alarm과 DiskHigh Alarm이 둘 다 ALARM일 때만 관리자에게 알리려 한다. 무엇을 사용하나?

<details>
<summary>정답 및 해설 보기</summary>

**정답:** Composite Alarm (AND)

Composite Alarm은 하위 Alarm의 상태를 AND/OR/NOT으로 조합하여 불필요한 알림을 줄인다.

</details>

### Q4. EC2의 AWS 하드웨어/호스트 장애에 대응하는 대표적인 상태 검사 및 조치는?

<details>
<summary>정답 및 해설 보기</summary>

**정답:** System Status Check / EC2 Recovery

System Status Check는 AWS 호스트 인프라를 감시한다. 지원되는 인스턴스에서 Recovery가 가능하며 일부 임시 데이터 손실에 주의한다.

</details>

### Q5. CloudWatch Alarm을 실제로 CPU를 높이지 않고 임시로 ALARM 상태로 전환해 테스트하려면?

<details>
<summary>정답 및 해설 보기</summary>

**정답:** SetAlarmState

SetAlarmState는 지표를 조작하지 않고 경보 상태만 바꾼다. 연결된 실제 EC2 Terminate 같은 Action이 실행될 수 있으므로 주의한다.

</details>

## 🇯🇵 日本語 Summary

CloudWatch Alarm は Metric をしきい値と評価期間に基づいて監視し、OK / ALARM / INSUFFICIENT_DATA のいずれかの状態を取る。Composite Alarm は複数の Alarm の状態を AND/OR/NOT で組み合わせる。EC2 の System Status Check の失敗には、対応するインスタンスで Recovery を利用できる。`SetAlarmState` はテスト用だが、実際のアクションが実行される可能性がある。

**試験の注意点:** 「5分 × 3期間、3/3」は3回連続でしきい値を超える判定。Composite Alarm は他のアラームの状態を組み合わせる。

## 🇺🇸 English Summary

CloudWatch Alarms evaluate metrics using statistics, thresholds, periods, and M-out-of-N data points. Alarm states are OK, ALARM, and INSUFFICIENT_DATA. Composite Alarms combine the states of other alarms. EC2 system status failures may trigger recovery on eligible instances. `SetAlarmState` simulates a state transition and may execute real alarm actions; never test destructive actions carelessly.

**Exam tip:** Distinguish a metric alarm from a composite alarm. Test SetAlarmState carefully because the configured EC2 actions can execute for real.

## 📚 Vocabulary

> GitHub 검색 시 영어 표제어 그대로 검색하면 관련 설명으로 이동하기 쉽다.

| English | 한국어 | 日本語 |
|---|---|---|
| Alarm | 경보 | アラーム |
| Threshold | 임계값 | しきい値 |
| Period | 평가 시간 구간 | 評価期間 |
| Evaluation Periods | 평가 기간 수 | 評価期間数 |
| Datapoints to Alarm | 경보에 필요한 위반 데이터 수 | アラーム判定に必要なデータポイント数 |
| Insufficient Data | 데이터 부족 | データ不足 |
| Composite Alarm | 복합 경보 | 複合アラーム |
| Alarm Noise | 불필요한 반복 경보 | アラームノイズ |
| Status Check | 상태 검사 | ステータスチェック |
| Instance Recovery | 인스턴스 복구 | インスタンス復旧 |
| SetAlarmState | 경보 상태 강제 설정 | アラーム状態の設定 |
| Scale Out | 수평 확장 | スケールアウト |
| Scale In | 수평 축소 | スケールイン |
