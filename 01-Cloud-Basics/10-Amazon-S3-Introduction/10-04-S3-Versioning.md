# 10-04. S3 Versioning

## 1. S3 Versioning이란?

S3 Versioning은 **같은 Object Key의 여러 Version을 보관하는 기능**이다.

예를 들어 `index.html`을 수정해서 같은 이름으로 다시 Upload하면:

```text
index.html

v3  ← Current Version
v2
v1
```

기존 Object를 덮어써서 없애는 것이 아니라 새로운 Version을 생성한다.

각 Version은 **Version ID**로 구분된다.

---

## 2. Versioning을 사용하는 이유

Versioning의 대표적인 목적은 **실수로 인한 Object 삭제 또는 덮어쓰기에서 복구하는 것**이다.

예를 들어:

```text
index.html v1
      ↓
실수로 잘못된 파일 Upload
      ↓
index.html v2
```

Versioning이 없다면 기존 Object가 덮어써질 수 있다.

Versioning이 활성화되어 있다면:

```text
index.html

v2  ← 잘못 Upload한 현재 Version
v1  ← 이전 정상 Version
```

이전 Version이 남아 있기 때문에 복구할 수 있다.

---

## 3. Versioning은 Bucket 단위

Versioning은 개별 Object마다 설정하는 기능이 아니라 **Bucket 단위로 활성화**한다.

```text
S3 Bucket
│
│ Versioning Enabled
│
├── coffee.jpg
├── index.html
└── beach.jpg
```

Versioning이 활성화된 이후 동일한 Key의 Object를 다시 Upload하면 새로운 Version ID가 생성된다.

---

## 4. Version ID

Versioning이 활성화된 Bucket에서는 Object Version마다 고유한 **Version ID**가 생성된다.

```text
coffee.jpg

Version ID: abc123
Version ID: def456
Version ID: ghi789
```

Object Key는 모두:

```text
coffee.jpg
```

로 동일하지만 Version ID가 다르다.

따라서 S3는:

```text
Key + Version ID
```

를 이용하여 특정 Object Version을 구분할 수 있다.

---

## 5. Null Version

Versioning을 활성화하기 **전에 존재했던 Object**가 있을 수 있다.

예:

```text
Versioning OFF

coffee.jpg
```

이 Object는 Version ID를 가지지 않았기 때문에 Versioning 관점에서:

```text
Version ID = null
```

로 표시된다.

이후 Versioning을 활성화하고 같은 `coffee.jpg`를 다시 Upload하면:

```text
coffee.jpg

v2    ← 새로운 Version
null  ← 기존 Object
```

가 된다.

여기서 중요한 점은 **null version도 실제 Object Version**이라는 것이다.

```text
null
≠ 아무것도 없음

null version
= Versioning 활성화 이전의 Object Version
```

따라서 필요하면 null version의 데이터도 다시 사용할 수 있다.

---

## 6. 같은 Key를 다시 Upload하면?

Versioning이 활성화된 상태에서 같은 Key의 Object를 다시 Upload하면 기존 Version을 삭제하지 않는다.

```text
Upload
coffee.jpg
      ↓
Version v1

Upload
coffee.jpg
      ↓
Version v2

Upload
coffee.jpg
      ↓
Version v3
```

결과:

```text
coffee.jpg

v3 ← Current
v2
v1
```

일반적인 요청에서는 **Current Version**이 반환된다.

---

## 7. Versioning과 Storage Cost

Versioning은 이전 데이터를 보존하지만, 그만큼 Storage도 사용한다.

중요한 점은 Version이 단순히 변경된 부분만 저장하는 것이 아니라는 것이다.

예를 들어 1GB Object를 여러 번 Upload한다면:

```text
file.zip

v1 → 1 GB
v2 → 1 GB
v3 → 1 GB
```

각 Version이 실제 Object 데이터로 저장된다.

따라서 Versioning을 사용할 경우 오래된 Version들이 계속 쌓이면서 Storage Cost가 증가할 수 있다.

이후 Lifecycle Rule 등을 이용하여 오래된 Version을 관리할 수 있다.

---

## 8. Object를 삭제하면?

Versioning이 활성화된 Bucket에서 Object를 **일반적으로 Delete**하면 기존 Object Version이 즉시 영구 삭제되는 것이 아니다.

대신 **Delete Marker**가 생성된다.

삭제 전:

```text
coffee.jpg

v3 ← Current
v2
v1
```

일반 Delete 실행:

```text
DELETE coffee.jpg
```

결과:

```text
coffee.jpg

Delete Marker ← Current
v3
v2
v1
```

기존 Version은 그대로 남아 있다.

---

## 9. Delete Marker

Delete Marker는 실제 Object 데이터가 아니라:

> 이 Object는 현재 삭제된 것으로 취급한다

는 것을 나타내는 Marker이다.

```text
Delete Marker
     ↓
Current 상태에서 Object가
삭제된 것처럼 보이게 함
```

따라서 일반적인 GET 요청:

```text
GET coffee.jpg
```

에서는 Delete Marker가 Current이므로 Object를 가져올 수 없다.

하지만 아래에는 이전 Version들이 여전히 존재한다.

```text
Delete Marker ← Current
v3
v2
v1
```

---

## 10. 삭제된 Object 복구

Delete Marker를 제거하면 이전 Version이 다시 Current 상태로 드러날 수 있다.

삭제된 상태:

```text
Delete Marker ← Current
v3
v2
v1
```

Delete Marker 삭제:

```text
Delete Marker ❌

v3 ← 다시 Current
v2
v1
```

따라서 Versioning은 실수로 Object를 삭제했을 때 복구하는 데 유용하다.

---

## 11. 특정 Version 삭제

일반적인 Object Delete와 **특정 Version 삭제**는 다르다.

### 일반 Delete

```text
DELETE coffee.jpg
```

결과:

```text
Delete Marker 생성

기존 Version 유지
```

### Version ID를 지정한 Delete

```text
DELETE coffee.jpg
VersionId=v2
```

결과:

```text
v2 자체가 삭제
```

즉 특정 Version ID를 지정하여 삭제하면 해당 Version을 영구적으로 제거할 수 있다.

### 반드시 구분

```text
일반 DELETE
→ Delete Marker 생성
→ 이전 Version 유지

특정 Version DELETE
→ 해당 Version 자체 삭제
```

---

## 12. Versioning Suspend

한 번 Versioning을 활성화한 Bucket은 이후 **Suspend**할 수 있다.

```text
Versioning
Enabled
   ↓
Suspended
```

중요한 점은 Suspend한다고 기존 Version들이 삭제되는 것이 아니라는 것이다.

```text
Before Suspend

coffee.jpg
├── v3
├── v2
└── v1


After Suspend

기존 Version
→ 그대로 유지
```

즉:

```text
Suspend
≠ 기존 Version 삭제
```

이다.

Versioning을 Suspend한 이후 새롭게 저장되는 Object의 Version 처리 방식은 Enabled 상태와 달라진다.

---

## 13. Versioning 전체 흐름

처음 Bucket에 Object가 있다고 하자.

```text
Versioning OFF

coffee.jpg
└── null
```

Versioning을 활성화한다.

```text
Versioning ON
```

같은 Key의 새로운 Object를 Upload한다.

```text
coffee.jpg

v2   ← Current
null
```

한 번 더 Upload한다.

```text
coffee.jpg

v3   ← Current
v2
null
```

Object를 일반 Delete한다.

```text
coffee.jpg

Delete Marker ← Current
v3
v2
null
```

Delete Marker를 제거한다.

```text
coffee.jpg

v3 ← Current
v2
null
```

이 흐름을 이해하면 S3 Versioning의 핵심은 거의 끝이다.

---

# 핵심 정리

```text
S3 Versioning
→ 같은 Key의 여러 Version 보관


Versioning
→ Bucket 단위 설정


Same Key Upload

coffee.jpg
├── v3 ← Current
├── v2
└── v1


Version ID
→ 각 Object Version을 구분


null version
→ Versioning 활성화 이전 등에 존재한 Object Version
→ 실제 데이터가 존재하는 Version


Normal DELETE

coffee.jpg
├── Delete Marker ← Current
├── v3
├── v2
└── v1


Delete Marker 삭제

coffee.jpg
├── v3 ← Current
├── v2
└── v1


특정 Version ID DELETE
→ 해당 Version 자체 삭제


Versioning Suspend
→ 기존 Version 삭제 X


각 Version은 실제 Object 데이터를 저장
→ Storage Cost 증가 가능
```

---

# 🇯🇵 日本語まとめ

S3 Versioning を有効にすると、同じ Object Key に対して複数の Version を保存できます。

```text
index.html

v3 ← Current
v2
v1
```

Versioning を有効にする前から存在していた Object は `null version` として扱われる場合があります。

Versioning が有効な Bucket で通常の Delete を実行すると、既存 Version がすぐに削除されるのではなく **Delete Marker** が作成されます。

```text
Delete Marker ← Current
v3
v2
v1
```

Delete Marker を削除すると以前の Version を再び利用できます。

一方、Version ID を指定して削除すると、その Version 自体を削除できます。

Versioning を Suspend しても既存の Version は削除されません。

---

# 🇺🇸 English Summary

S3 Versioning allows multiple versions of an object with the same key to be stored.

```text
index.html

v3 ← Current
v2
v1
```

Each version is identified by a Version ID.

Objects that existed before versioning was enabled may appear as a `null version`.

When an object is deleted normally from a versioned bucket, S3 creates a **Delete Marker** instead of immediately removing previous versions.

```text
Delete Marker ← Current
v3
v2
v1
```

Removing the Delete Marker can make the previous version accessible again.

Deleting a specific Version ID removes that particular version.

Suspending versioning does not delete versions that already exist.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Versioning | バージョニング | 버전 관리 |
| Version | バージョン | 버전 |
| Version ID | バージョンID | 버전 ID |
| Current Version | 現在のバージョン | 현재 버전 |
| Null Version | Nullバージョン | Null 버전 |
| Delete Marker | 削除マーカー | 삭제 마커 |
| Permanent Delete | 完全削除 | 영구 삭제 |
| Suspend | 一時停止 | 중지 |
| Recovery | 復元 | 복구 |
| Overwrite | 上書き | 덮어쓰기 |

---

# Review Questions

<details>
<summary>1. S3 Versioning의 주요 목적은?</summary>

동일한 Object Key의 여러 Version을 보관하여 실수로 발생한 덮어쓰기나 삭제로부터 데이터를 복구할 수 있게 하는 것이다.

</details>

<details>
<summary>2. Versioning은 Object 단위로 활성화하는가?</summary>

아니다.

Versioning은 Bucket 단위로 활성화한다.

</details>

<details>
<summary>3. null version은 데이터가 없는 Version인가?</summary>

아니다.

Versioning 활성화 이전 등에 존재했던 Object가 `null` Version ID를 가질 수 있으며 실제 Object 데이터를 가지고 있다.

</details>

<details>
<summary>4. Versioning된 Bucket에서 Object를 일반 Delete하면 기존 Version이 즉시 영구 삭제되는가?</summary>

아니다.

Delete Marker가 생성되어 Current 상태가 되고 기존 Version들은 남아 있다.

</details>

<details>
<summary>5. Delete Marker를 제거하면 어떻게 되는가?</summary>

Delete Marker 아래에 있던 이전 Version이 다시 Current Version으로 나타날 수 있다.

</details>

<details>
<summary>6. 일반 Delete와 특정 Version Delete의 차이는?</summary>

일반 Delete는 Delete Marker를 생성한다.

Version ID를 지정한 Delete는 해당 Object Version 자체를 삭제한다.

</details>

<details>
<summary>7. Versioning을 Suspend하면 기존 Version들이 삭제되는가?</summary>

아니다.

기존 Version은 그대로 유지된다.

</details>

<details>
<summary>8. Versioning을 사용하면 Storage Cost가 증가할 수 있는 이유는?</summary>

각 Object Version이 실제 데이터를 저장하기 때문이다.

여러 Version이 계속 누적되면 그만큼 Storage 사용량도 증가할 수 있다.

</details>
