# 📘 26-08. AWS Backup Console Lab: Tags, Jobs & Cleanup

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Backup Plan Template` · `Daily Backup` · `Monthly Backup` · `IAM Role` · `Tag Assignment` · `Backup Jobs` · `Recovery Points`

## 🇰🇷 1. 실습 목표와 준비
강사는 AWS Backup 콘솔에서 **일별 + 월별 자동 백업 정책**을 만들고, 태그로 EBS 볼륨을 자동 선택하는 과정을 시연했다. 별도의 EBS 볼륨을 만들었지만 백업이 완료될 때까지 하루를 기다리는 실습은 필수로 수행하지 않았다. 아래 내용은 **콘솔을 보지 않고 이해할 수 있도록 화면 순서와 설정 의미**를 재구성한 것이다.

## 🇰🇷 2. Step 1. Create Backup Plan
```text
AWS Console → AWS Backup → Backup plans → Create backup plan
                ├─ Start with template
                ├─ Build a new plan
                └─ Define using JSON
```
강사는 **템플릿을 이용하는 방식**으로 `Daily-Monthly-1yr-Retention` 유형을 고르고 계획 이름을 `TestPlan`처럼 정했다. 계획 생성 전에 Rule의 기본값을 확인·수정할 수 있다.

## 🇰🇷 3. Step 2. 두 가지 Backup Rule
| Rule | 주기 | Vault | Lifecycle |
|---|---|---|---|
| **DailyBackups** | 매일, UTC 05:00 근처 시작 | 기본 Vault | 예시: 5주(35일) 보존 |
| **MonthlyBackups** | 매달 첫날 | 기본 Vault | 예시: 1개월 후 Cold로 이전, 1년 보존 |

**주의:** 월별 백업의 Cold 이전은 **해당 리소스가 Cold Tier를 지원하고 수명 주기 제약을 만족**한다는 전제에서만 가능하다. 시작 시각을 정해도 즉시 그 순간 백업이 끝나는 것은 아니다.

## 🇰🇷 4. Step 3. Assign Resources
- Plan 생성 후 **Assign resources** 클릭. Assignment 이름 예: `TestAssignment`.
- **IAM Role**: Default Role(권한이 있는 경우 자동 생성/사용) 또는 기존 역할 선택. AWS Backup이 EBS 등을 백업할 권한이 필요하다.
- **Resource Selection**: 전체 리소스 유형 또는 특정 유형(DynamoDB 등) 선택.
- **Tags**: `Key=Environment`, `Value=Production` 조건을 지정. 태그가 일치하는 지원 리소스가 자동 선택된다.

```text
Plan(TestPlan) ─ Rule(Daily/Monthly) ─ Assignment[Environment=Production]
                                                 │
                                  EBS 볼륨(1 GiB, 같은 태그)
                                                 │
                                           예약 시 Backup
```

## 🇰🇷 5. Step 4. EBS 생성 및 태그 확인
EC2 콘솔 → Volumes → Create Volume → 예시 **1 GiB EBS** 생성 → 태그 `Environment=Production` 설정. 별도의 수동 백업 연결 없이, 태그 기반 Assignment 대상에 포함될 수 있다는 점이 실습 포인트다. 볼륨은 AZ 리소스이며 리전과 AZ, 생성 비용을 고려한다.

## 🇰🇷 6. Step 5. Jobs와 Vault 확인
- **Backup Jobs**: 예약 작업의 `Pending/Running/Completed/Failed` 등 상태 확인.
- **Restore Jobs**: 백업 데이터로 리소스를 재생성한 작업.
- **Copy Jobs**: 다른 Vault/리전/계정으로 백업 사본을 만드는 작업.
- **Backup Vault → Recovery Points**: 실제 생성된 백업 확인.

## 🇰🇷 7. Step 6. 비용 발생 리소스 정리
1. 테스트용 EBS 볼륨이 더 이상 필요 없다면 삭제한다.
2. **Resource Assignment** 제거.
3. **Backup Plan** 제거. 필요하다면 각 Backup Rule의 설정도 먼저 검토한다.
4. **이미 생성된 Recovery Point는 계획 삭제 후에도 남을 수 있으므로** Vault와 보존 정책을 확인한다. Vault Lock이 있으면 임의 삭제가 제한될 수 있다.

> [!WARNING]
> 콘솔 메뉴명·기본 템플릿 이름은 AWS UI 개정으로 다를 수 있다. 실습은 선택지의 역할과 비용 정리 순서를 배우는 것이 목적이다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- 순서: Plan/Rule → Assignment → 태그 있는 EBS → Jobs/Vault 확인 → 리소스 정리.
- Backup Window는 시작 허용 시간이며 보존 기간이 아니다.
- 태그 선택 조건은 `Environment=Production`을 사용한 예시.
- 기본 IAM Role에도 실제 백업 권한이 필요하다.
- Plan을 삭제해도 과거 Recovery Point가 남을 수 있어 비용 확인 필요.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. AWS Backup에서 템플릿 방식으로 시작할 때 선택하는 메뉴는?

<details>
<summary>정답 및 해설 보기</summary>

**Create backup plan → Start with template**이다.

</details>

### Q2. 매일과 매월 다른 보존기간을 적용하려면 몇 개의 Backup Plan이 필요한가?

<details>
<summary>정답 및 해설 보기</summary>

**하나의 Plan에 Rule 둘**로 구성할 수 있다.

</details>

### Q3. EBS 볼륨을 생성할 때 어떤 태그를 주면 강의의 선택 조건에 맞는가?

<details>
<summary>정답 및 해설 보기</summary>

**Environment=Production**이다.

</details>

### Q4. 백업 복사 작업의 실행 상태는 어디서 보는가?

<details>
<summary>정답 및 해설 보기</summary>

**Copy Jobs**에서 본다.

</details>

### Q5. 실습 후 Backup Plan 삭제만 하면 모든 백업 비용이 즉시 사라지나?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 기존 Recovery Point와 Vault의 보존 정책을 별도로 확인한다.

</details>

## 🇯🇵 日本語 Summary

コンソール実習ではテンプレートで毎日・毎月の Rule を持つ Backup Plan を作成し、Environment=Production タグで EBS ボリュームを割り当てた。Jobs で状態、Vault で Recovery Point を確認する。Plan 削除後も既存バックアップが残る点に注意。

## 🇺🇸 English Summary

The lab creates a template-based AWS Backup plan with daily and monthly rules, assigns resources through an Environment=Production tag, and checks jobs and vault recovery points. Cleanup must account for existing recovery points after deleting a plan.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Template | 템플릿 | テンプレート |
| Resource Assignment | 리소스 할당 | リソース割り当て |
| Tag Selection | 태그 기준 선택 | タグによる選択 |
| Backup Window | 백업 작업 시간 창 | バックアップウィンドウ |
| Restore Job | 복원 작업 | 復元ジョブ |
