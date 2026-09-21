# 10-06. S3 Storage Classes

## 1. S3 Storage Classes란?

Amazon S3에서는 데이터의 **Access Pattern, Availability 요구사항, 보관 기간, 비용**에 따라 여러 Storage Class를 선택할 수 있다.

```text
S3 Storage Classes
│
├── S3 Standard
├── S3 Standard-IA
├── S3 One Zone-IA
├── S3 Glacier Instant Retrieval
├── S3 Glacier Flexible Retrieval
├── S3 Glacier Deep Archive
└── S3 Intelligent-Tiering
```

핵심은 단순히:

```text
비싼 Storage
vs
싼 Storage
```

를 고르는 것이 아니다.

```text
얼마나 자주 접근하는가?
얼마나 빨리 꺼내야 하는가?
얼마나 오래 보관하는가?
여러 AZ에 저장해야 하는가?
Access Pattern을 예측할 수 있는가?
```

를 보고 적절한 Storage Class를 선택한다.

---

## 2. Durability vs Availability

Storage Class를 이해하려면 먼저 **Durability**와 **Availability**를 구분해야 한다.

### Durability

데이터가 손실되지 않고 유지되는 정도이다.

```text
Durability
→ "내 데이터가 살아 있는가?"
```

S3의 주요 Storage Class는 매우 높은 Durability를 제공하도록 설계되어 있다.

```text
99.999999999%
= 11 nines
```

### Availability

필요한 순간에 데이터에 접근할 수 있는 정도이다.

```text
Availability
→ "지금 이 데이터에 접근할 수 있는가?"
```

따라서:

```text
Durability
≠
Availability
```

이다.

데이터가 안전하게 존재하는 것과 지금 즉시 서비스가 가능한 것은 서로 다른 개념이다.

---

# 3. S3 Standard

가장 일반적인 S3 Storage Class이다.

```text
자주 사용하는 데이터
→ S3 Standard
```

특징:

- Frequent Access
- Low Latency
- High Throughput
- 여러 Availability Zone에 데이터 저장
- 일반적인 S3 사용에 적합

대표적인 사용 사례:

```text
Web Content
Mobile Applications
Gaming
Big Data
Content Distribution
```

특별한 Access Pattern이 없다면 가장 기본적으로 생각할 수 있는 Storage Class이다.

---

# 4. S3 Standard-IA

IA는 **Infrequent Access**의 약자이다.

```text
자주 사용하지 않음
+
필요하면 즉시 접근해야 함

→ Standard-IA
```

S3 Standard보다 Storage 비용을 낮출 수 있지만, 데이터를 가져올 때 Retrieval 비용이 발생할 수 있다.

특징:

```text
Infrequent Access
Millisecond Retrieval
Multi-AZ
Minimum Storage Duration: 30 days
```

대표적인 사용 사례:

- Backup
- Disaster Recovery
- 자주 사용하지 않는 데이터
- 필요할 때 즉시 접근해야 하는 데이터

---

# 5. S3 One Zone-IA

Standard-IA와 비슷하게 자주 접근하지 않는 데이터를 위한 Storage Class이다.

하지만 가장 큰 차이는 **Single Availability Zone**에 저장된다는 것이다.

```text
Standard-IA

AZ A
AZ B
AZ C


One Zone-IA

AZ A
```

따라서 One Zone-IA는 해당 AZ를 상실하는 상황까지 견뎌야 하는 중요한 데이터에는 적합하지 않다.

대표적인 사용 사례:

```text
재생성 가능한 데이터
Secondary Backup
다른 위치에 원본이 존재하는 데이터
```

### 기억하기

```text
가끔 사용
+
즉시 접근
+
Multi-AZ 필요

→ Standard-IA


가끔 사용
+
즉시 접근
+
재생성 가능
+
Single AZ 허용

→ One Zone-IA
```

---

# 6. S3 Glacier

Glacier 계열은 **Archive Storage**를 위한 Storage Class이다.

```text
S3 Glacier
│
├── Glacier Instant Retrieval
├── Glacier Flexible Retrieval
└── Glacier Deep Archive
```

세 가지의 가장 큰 차이는:

```text
얼마나 자주 접근하는가?
+
얼마나 빨리 데이터를 꺼내야 하는가?
+
얼마나 장기간 보관하는가?
```

이다.

---

# 7. S3 Glacier Instant Retrieval

Archive 데이터이지만 필요할 때 **즉시 Retrieval**해야 하는 경우 사용한다.

```text
거의 사용하지 않음
+
Archive
+
그래도 필요할 때 즉시 접근

→ Glacier Instant Retrieval
```

특징:

```text
Millisecond Retrieval
Minimum Storage Duration: 90 days
```

예를 들어 평소에는 거의 열어보지 않지만, 갑자기 필요할 경우 즉시 접근해야 하는 Archive 데이터에 적합하다.

---

# 8. Standard-IA vs Glacier Instant Retrieval

둘 다 **Millisecond 단위로 빠르게 Retrieval할 수 있기 때문에** 헷갈리기 쉽다.

하지만 Access Pattern이 다르다.

| 항목 | Standard-IA | Glacier Instant Retrieval |
|---|---|---|
| 접근 속도 | Millisecond | Millisecond |
| 접근 빈도 | 가끔 | 매우 드물게 |
| 주요 목적 | Infrequent Data | Archive |
| 최소 보관 기간 | 30일 | 90일 |
| Storage 비용 | 상대적으로 높음 | 상대적으로 낮음 |
| Retrieval 비용 | 발생 가능 | Rare Access를 전제로 고려 |

핵심은 Retrieval 속도가 아니다.

```text
Standard-IA

"가끔 쓰긴 하는 데이터야.
하지만 필요하면 바로 줘."


Glacier Instant Retrieval

"거의 안 보는 Archive야.
근데 정말 필요할 때는 바로 줘."
```

### 시험용 기억법

```text
가끔 사용 + 즉시 접근
→ Standard-IA

거의 안 봄 + Archive + 즉시 접근
→ Glacier Instant Retrieval
```

---

# 9. S3 Glacier Flexible Retrieval

Archive 데이터이며 즉시 Retrieval할 필요가 없는 경우 사용한다.

```text
Archive
+
데이터를 꺼내는 데 기다릴 수 있음

→ Glacier Flexible Retrieval
```

Retrieval 방식에 따라 몇 분에서 몇 시간 정도 기다릴 수 있다.

최소 Storage Duration은:

```text
90 days
```

이다.

즉:

```text
Instant Retrieval
→ 바로 필요

Flexible Retrieval
→ 기다릴 수 있음
```

으로 구분하면 쉽다.

---

# 10. S3 Glacier Deep Archive

Glacier 계열 중에서도 **매우 장기간 보관하고 거의 접근하지 않는 데이터**를 위한 Storage Class이다.

```text
거의 절대 안 봄
+
오랫동안 보관
+
꺼내는 데 오래 걸려도 괜찮음

→ Glacier Deep Archive
```

대표적인 사용 사례:

- 장기 Backup
- Compliance Archive
- 수년간 보존해야 하는 기록

최소 Storage Duration은:

```text
180 days
```

이다.

---

# 11. Glacier 3종 비교

```text
Glacier Instant Retrieval
│
│ 바로 꺼낼 수 있음
│
▼
Glacier Flexible Retrieval
│
│ 기다릴 수 있음
│
▼
Glacier Deep Archive

가장 장기적인 Archive
```

| Storage Class | Access Pattern | Retrieval |
|---|---|---|
| Glacier Instant Retrieval | 매우 드문 접근 | Millisecond |
| Glacier Flexible Retrieval | Archive | Minutes ~ Hours |
| Glacier Deep Archive | 매우 장기 Archive | Hours |

시험에서는 정확한 Retrieval 시간 숫자만 외우기보다 **상황의 차이**를 먼저 잡는 것이 중요하다.

---

# 12. S3 Intelligent-Tiering

데이터의 Access Pattern을 예측하기 어려운 경우 사용할 수 있다.

예를 들어 어떤 Object가:

```text
이번 달에는 자주 사용됨
다음 달에는 거의 사용 안 됨
몇 달 뒤 다시 사용됨
```

처럼 움직인다면 처음부터 적절한 Storage Class를 선택하기 어렵다.

이때 **S3 Intelligent-Tiering**을 사용할 수 있다.

```text
Object
   │
   ▼
S3 Intelligent-Tiering
   │
   ▼
Access Pattern Monitoring
   │
   ▼
적절한 Access Tier로 자동 이동
```

---

# 13. Intelligent-Tiering의 Access Tiers

대표적인 자동 Access Tier는 다음과 같다.

```text
Frequent Access
      │
      │ 일정 기간 Access 없음
      ▼
Infrequent Access
      │
      │ 더 오랫동안 Access 없음
      ▼
Archive Instant Access
```

대표적인 흐름:

```text
Frequent Access

     ↓ 30일 동안 Access 없음

Infrequent Access

     ↓ 90일 동안 Access 없음

Archive Instant Access
```

추가적인 Archive Tier를 선택적으로 구성할 수도 있다.

```text
Archive Access

Deep Archive Access
```

---

# 14. Storage Class와 Access Tier는 다르다

여기서 중요한 함정이 있다.

Intelligent-Tiering에서:

```text
Frequent Access
→ Infrequent Access
```

로 이동했다고 해서 Storage Class가:

```text
S3 Standard
→ S3 Standard-IA
```

로 변경되는 것이 아니다.

Object의 Storage Class는 계속:

```text
S3 Intelligent-Tiering
```

이다.

그 내부에서 **Access Tier**가 바뀌는 것이다.

```text
Storage Class
S3 Intelligent-Tiering
│
├── Frequent Access Tier
├── Infrequent Access Tier
├── Archive Instant Access Tier
├── Archive Access Tier
└── Deep Archive Access Tier
```

따라서:

```text
Storage Class
≠
Access Tier
```

이다.

---

# 15. Lifecycle Rule

S3에서는 Object의 수명에 따라 Storage Class를 자동으로 변경하도록 **Lifecycle Rule**을 구성할 수도 있다.

예:

```text
Object 생성
    │
    ▼
S3 Standard
    │
    │ 30 days
    ▼
Standard-IA
    │
    │ 일정 기간 후
    ▼
Glacier
```

이러한 Storage Class 변경을 **Lifecycle Transition**이라고 한다.

Lifecycle을 사용하면 오래된 Object를 더 저렴한 Storage Class로 이동시켜 비용을 최적화할 수 있다.

---

# 16. Lifecycle vs Intelligent-Tiering

둘 다 Storage 비용을 최적화할 수 있지만 방식이 다르다.

### Lifecycle Rule

사용자가 규칙을 미리 정의한다.

```text
30일 후
→ Standard-IA

90일 후
→ Glacier
```

즉:

```text
내가 규칙을 정함
→ AWS가 규칙대로 이동
```

### Intelligent-Tiering

AWS가 Object의 Access Pattern을 확인하여 Access Tier를 자동으로 변경한다.

```text
Access Pattern
      │
      ▼
AWS Monitoring
      │
      ▼
Automatic Tiering
```

### 비교

```text
Lifecycle
→ "30일 지나면 여기로 옮겨."

Intelligent-Tiering
→ "언제 다시 쓸지 모르겠으니까
   접근 패턴 보고 알아서 관리해."
```

---

# 17. Storage Class 선택 흐름

시험 문제에서는 다음처럼 상황을 읽으면 된다.

```text
자주 사용?
│
├── YES
│    └── S3 Standard
│
└── NO
     │
     ├── 가끔 사용 + 즉시 접근?
     │    └── Standard-IA
     │
     ├── 재생성 가능 + Single AZ 가능?
     │    └── One Zone-IA
     │
     ├── Archive지만 즉시 접근?
     │    └── Glacier Instant Retrieval
     │
     ├── Archive + 기다릴 수 있음?
     │    └── Glacier Flexible Retrieval
     │
     ├── 초장기 Archive?
     │    └── Glacier Deep Archive
     │
     └── Access Pattern 예측 불가?
          └── Intelligent-Tiering
```

---

# 핵심 정리

```text
S3 Standard
→ 자주 사용


Standard-IA
→ 가끔 사용
→ 즉시 접근
→ Multi-AZ
→ 최소 30일


One Zone-IA
→ 가끔 사용
→ Single AZ
→ 재생성 가능한 데이터


Glacier Instant Retrieval
→ Archive
→ 매우 드문 접근
→ 즉시 Retrieval
→ 최소 90일


Glacier Flexible Retrieval
→ Archive
→ Retrieval을 기다릴 수 있음
→ 최소 90일


Glacier Deep Archive
→ 초장기 Archive
→ Retrieval이 오래 걸려도 됨
→ 최소 180일


Intelligent-Tiering
→ Access Pattern 예측 어려움
→ Access Tier 자동 이동


Standard-IA vs Glacier Instant

둘 다 빠른 Retrieval

Standard-IA
→ "가끔 사용"

Glacier Instant
→ "거의 안 보는 Archive"


Lifecycle
→ 사용자가 이동 규칙 지정

Intelligent-Tiering
→ AWS가 Access Pattern을 보고 자동 관리
```

---

# 🇯🇵 日本語まとめ

Amazon S3 には、アクセス頻度や保存期間に応じた複数の Storage Class があります。

```text
S3 Standard
→ 頻繁にアクセス

Standard-IA
→ 低頻度アクセス + 即時アクセス

One Zone-IA
→ 低頻度アクセス + Single AZ

Glacier Instant Retrieval
→ Archive + 即時アクセス

Glacier Flexible Retrieval
→ Retrieval を待てる Archive

Glacier Deep Archive
→ 長期 Archive

Intelligent-Tiering
→ Access Pattern に応じて自動管理
```

Standard-IA と Glacier Instant Retrieval はどちらも高速な Retrieval が可能ですが、Standard-IA は「たまに利用するデータ」、Glacier Instant Retrieval は「ほとんど利用しない Archive データ」に適しています。

Lifecycle Rule ではユーザーが Storage Class の Transition Rule を設定します。

Intelligent-Tiering では AWS が Access Pattern に応じて Access Tier を自動的に変更します。

---

# 🇺🇸 English Summary

Amazon S3 provides multiple storage classes designed for different access patterns and retention requirements.

```text
S3 Standard
→ Frequently accessed data

Standard-IA
→ Infrequently accessed data requiring immediate retrieval

One Zone-IA
→ Infrequently accessed, recreatable data in a single AZ

Glacier Instant Retrieval
→ Archive data requiring millisecond retrieval

Glacier Flexible Retrieval
→ Archive data where retrieval can wait

Glacier Deep Archive
→ Long-term archival data

Intelligent-Tiering
→ Unpredictable access patterns
```

Standard-IA and Glacier Instant Retrieval both provide fast retrieval, but they target different access patterns.

Standard-IA is designed for data that is accessed occasionally, while Glacier Instant Retrieval is designed for archive data that is accessed very rarely but still requires immediate retrieval.

Lifecycle Rules transition objects according to predefined rules, while Intelligent-Tiering automatically adjusts access tiers based on access patterns.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Storage Class | ストレージクラス | 스토리지 클래스 |
| Durability | 耐久性 | 내구성 |
| Availability | 可用性 | 가용성 |
| Infrequent Access | 低頻度アクセス | 낮은 빈도 접근 |
| Retrieval | 取り出し | 데이터 인출 |
| Archive | アーカイブ | 아카이브 |
| Standard-IA | Standard-IA | Standard-IA |
| One Zone-IA | One Zone-IA | One Zone-IA |
| Glacier Instant Retrieval | Glacier Instant Retrieval | Glacier 즉시 검색 |
| Glacier Flexible Retrieval | Glacier Flexible Retrieval | Glacier 유연한 검색 |
| Glacier Deep Archive | Glacier Deep Archive | Glacier 딥 아카이브 |
| Intelligent-Tiering | Intelligent-Tiering | 지능형 티어링 |
| Access Tier | アクセスティア | 액세스 티어 |
| Lifecycle Rule | ライフサイクルルール | 수명 주기 규칙 |
| Lifecycle Transition | ライフサイクル移行 | 수명 주기 전환 |

---

# Review Questions

<details>
<summary>1. 자주 사용하는 일반적인 데이터에는 어떤 Storage Class가 적합한가?</summary>

S3 Standard가 적합하다.

</details>

<details>
<summary>2. 가끔 사용하지만 필요할 때 즉시 접근해야 하는 데이터에는?</summary>

S3 Standard-IA가 적합하다.

</details>

<details>
<summary>3. Standard-IA와 One Zone-IA의 중요한 차이는?</summary>

Standard-IA는 여러 Availability Zone을 사용하지만 One Zone-IA는 하나의 Availability Zone에 데이터를 저장한다.

따라서 One Zone-IA는 재생성 가능한 데이터 등에 적합하다.

</details>

<details>
<summary>4. Standard-IA와 Glacier Instant Retrieval은 둘 다 빠른데 무엇이 다른가?</summary>

둘 다 Millisecond Retrieval이 가능하지만 Access Pattern이 다르다.

```text
Standard-IA
→ 가끔 사용하는 데이터

Glacier Instant Retrieval
→ 거의 접근하지 않는 Archive 데이터
```

</details>

<details>
<summary>5. Archive 데이터인데 필요할 때 즉시 접근해야 한다면?</summary>

S3 Glacier Instant Retrieval을 고려할 수 있다.

</details>

<details>
<summary>6. 매우 장기간 보관하고 거의 접근하지 않는 데이터에는?</summary>

S3 Glacier Deep Archive가 적합하다.

</details>

<details>
<summary>7. Access Pattern을 예측하기 어렵다면?</summary>

S3 Intelligent-Tiering을 고려할 수 있다.

</details>

<details>
<summary>8. Intelligent-Tiering에서 Infrequent Access Tier로 이동하면 Storage Class가 Standard-IA로 바뀌는가?</summary>

아니다.

Storage Class는 계속 `S3 Intelligent-Tiering`이며, 그 내부의 Access Tier가 변경되는 것이다.

</details>

<details>
<summary>9. Lifecycle Rule과 Intelligent-Tiering의 핵심 차이는?</summary>

Lifecycle Rule은 사용자가 미리 정한 규칙에 따라 Storage Class를 Transition한다.

Intelligent-Tiering은 실제 Access Pattern을 기반으로 Access Tier를 자동으로 조정한다.

</details>
