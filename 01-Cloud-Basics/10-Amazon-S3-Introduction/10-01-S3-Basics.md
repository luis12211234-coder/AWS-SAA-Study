# 10-01. Amazon S3 Basics

## 1. Amazon S3란?

Amazon S3(Simple Storage Service)는 AWS의 대표적인 **Object Storage Service**이다.

AWS에서 자주 사용하는 Storage Service를 비교하면 다음과 같다.

| Service | Storage Type | 특징 |
|---|---|---|
| Amazon EBS | Block Storage | EC2에서 사용하는 Block 단위 Storage |
| Amazon EFS | File Storage | 여러 EC2에서 공유 가능한 File System |
| Amazon S3 | Object Storage | 데이터를 Object 단위로 저장 |

S3에서는 데이터를 일반적인 File System의 파일이 아니라 **Object**라는 단위로 저장한다.

```text
Amazon S3
└── Bucket
    ├── Object
    ├── Object
    └── Object
```

---

## 2. S3 Bucket

**Bucket**은 S3 Object를 저장하는 최상위 Container이다.

```text
Amazon S3
└── my-bucket
    ├── coffee.jpg
    ├── index.html
    └── images/beach.jpg
```

각 Bucket은 특정 AWS Region에 생성된다.

```text
Amazon S3
│
├── ap-northeast-1
│   └── Bucket A
│
└── us-east-1
    └── Bucket B
```

S3 Console에서는 여러 Region에 존재하는 Bucket을 한 화면에서 관리할 수 있기 때문에 Global Service처럼 보일 수 있다.

하지만 실제로는 **각 Bucket이 특정 Region에 존재**한다.

> S3 Console은 여러 Region의 Bucket을 통합하여 관리할 수 있지만, 각 Bucket과 그 데이터는 특정 Region에 존재한다.

또한 Bucket의 데이터가 다른 Region으로 자동 복제되는 것은 아니다.

다른 Region으로 Object를 복제하려면 **S3 Replication** 등의 기능을 별도로 구성해야 한다.

---

## 3. S3 Object

S3에 실제로 저장되는 데이터 단위를 **Object**라고 한다.

Object는 대표적으로 다음 요소를 가진다.

```text
Object
├── Key
├── Value
├── Metadata
├── Tags
└── Version ID (Versioning 사용 시)
```

### Key

Object를 식별하는 전체 이름이다.

```text
coffee.jpg
images/beach.jpg
users/profile/avatar.png
```

### Value

Object의 실제 데이터이다.

예를 들어 `coffee.jpg`라는 Object라면 실제 이미지 데이터가 Value에 해당한다.

### Metadata

Object에 대한 부가 정보이다.

### Tags

Object를 분류하거나 관리하기 위한 Key-Value 형태의 Tag를 지정할 수 있다.

### Version ID

S3 Versioning을 사용하는 경우 동일한 Key의 여러 Object Version을 구분하기 위해 사용된다.

Versioning은 별도의 문서에서 자세히 다룬다.

---

## 4. S3에는 실제 Folder가 없다

S3는 일반적인 File System처럼 실제 Directory 계층 구조를 사용하지 않는다.

예를 들어 다음 Object가 있다고 하자.

```text
images/vacation/beach.jpg
```

File System처럼 생각하면:

```text
images
└── vacation
    └── beach.jpg
```

처럼 보이지만 S3에서 실제 Object Key는 다음 문자열 전체이다.

```text
images/vacation/beach.jpg
```

S3 Console이 `/` 문자를 기준으로 이를 Folder처럼 보여주는 것이다.

---

## 5. Prefix

**Prefix**는 Object Key의 앞부분을 의미한다.

예:

```text
Object Key

images/vacation/beach.jpg
```

여기서 다음과 같은 문자열을 Prefix로 볼 수 있다.

```text
images/
images/vacation/
```

따라서 S3의 Folder 구조를 이해할 때는 다음과 같이 생각하면 된다.

```text
실제 Directory
X

Object Key + Prefix
O
```

---

## 6. Object URL

S3 Object는 URL을 통해 특정 Object를 가리킬 수 있다.

예를 들어 Bucket에 다음 Object들이 존재한다고 하자.

```text
my-bucket

├── coffee.jpg
├── beach.jpg
└── images/cat.jpg
```

URL의 Path와 Object Key를 연결해서 생각할 수 있다.

```text
/coffee.jpg
→ Key: coffee.jpg

/beach.jpg
→ Key: beach.jpg

/images/cat.jpg
→ Key: images/cat.jpg
```

단, Object URL이 존재한다고 해서 누구나 해당 Object를 읽을 수 있는 것은 아니다.

Object가 Private이라면 적절한 Permission 없이 접근할 경우 `Access Denied`가 발생할 수 있다.

S3 Permission은 다음 Security 문서에서 자세히 다룬다.

---

## 7. S3 Object Size

S3는 매우 큰 Object를 저장할 수 있다.

여기서 반드시 구분해야 하는 것은:

```text
Object 자체의 최대 크기

vs

한 번의 PUT 요청으로
Upload할 수 있는 최대 크기
```

이다.

하나의 PUT 요청으로 Upload할 수 있는 최대 크기는:

```text
5 GB
```

이다.

따라서 다음과 같은 25GB Object 자체를 S3에 저장할 수 없는 것이 아니다.

```text
25 GB Object
→ S3 저장 가능
```

문제는 25GB 전체를 **한 번의 PUT 요청으로 Upload할 수 없다는 것**이다.

---

## 8. Multipart Upload

큰 Object는 **Multipart Upload**를 사용하여 여러 Part로 나누어 Upload할 수 있다.

예:

```text
25 GB File

      ↓ Split

Part 1
Part 2
Part 3
Part 4
Part 5

      ↓ Upload

Amazon S3

      ↓ Complete

25 GB Object
```

각 Part를 별도로 Upload한 후 모든 Part가 준비되면 S3에서 하나의 Object로 완성한다.

---

## 9. Multipart Upload의 장점

Multipart Upload는 단순히 큰 Object를 Upload하기 위한 기능만은 아니다.

대용량 Upload 중 네트워크 문제가 발생했다고 가정해 보자.

하나의 거대한 요청이라면 전체 Upload를 다시 시작해야 할 수 있다.

Multipart Upload에서는:

```text
Part 1  ✅
Part 2  ✅
Part 3  ✅
Part 4  ❌
Part 5

        ↓

실패한 Part를 다시 Upload
```

처럼 처리할 수 있다.

또한 여러 Part를 병렬로 Upload하여 대용량 Object의 Upload 성능을 향상시킬 수도 있다.

---

## 10. Multipart Upload 기준

시험에서 다음 두 기준을 구분하는 것이 중요하다.

```text
약 100 MB 이상
→ Multipart Upload 사용 권장

5 GB 초과
→ Single PUT 불가능
→ Multipart Upload 필요
```

즉 다음 문제를 만났다고 하자.

> 25GB 크기의 Object를 하나의 PUT 요청으로 S3에 Upload하려고 했지만 실패했다.

이유는:

```text
S3 Object 최대 크기가 5GB이기 때문
X

Single PUT 최대 크기가 5GB이기 때문
O
```

이다.

---

# 핵심 정리

```text
Amazon S3
= Object Storage

Bucket
= Object를 저장하는 Container
= 특정 Region에 생성

Object
├── Key
├── Value
├── Metadata
├── Tags
└── Version ID

S3 Folder
= 실제 Directory가 아님
= Object Key와 Prefix를 Folder처럼 표현

Single PUT
= 최대 5 GB

Multipart Upload
= 큰 Object를 여러 Part로 나누어 Upload

약 100 MB 이상
→ Multipart Upload 권장

5 GB 초과
→ Multipart Upload 필요
```

---

# 🇯🇵 日本語まとめ

Amazon S3 は AWS の代表的な **オブジェクトストレージサービス**です。

データは Bucket の中に Object として保存されます。

各 Bucket は特定の AWS Region に作成されます。

Object は Key、Value、Metadata、Tags、Version ID などの情報を持ちます。

S3 には一般的なファイルシステムのような実際の Directory 構造はありません。

```text
images/vacation/beach.jpg
```

は一つの Object Key であり、S3 Console が Prefix を Folder のように表示しています。

大きな Object を Upload する場合は **Multipart Upload** を利用できます。

```text
100 MB 以上
→ Multipart Upload 推奨

5 GB 超過
→ Multipart Upload が必要
```

---

# 🇺🇸 English Summary

Amazon S3 is AWS's **object storage service**.

Objects are stored inside buckets, and each bucket is created in a specific AWS Region.

An S3 object contains information such as its key, value, metadata, tags, and optionally a version ID.

S3 does not use a traditional directory hierarchy. Paths such as:

```text
images/vacation/beach.jpg
```

are object keys, while prefixes are presented as folders in the S3 console.

For large objects, S3 supports Multipart Upload.

```text
Around 100 MB or larger
→ Multipart Upload is recommended

Larger than 5 GB
→ Multipart Upload is required
```

Multipart Upload divides a large object into multiple parts that can be uploaded independently and then assembled into a single S3 object.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Object Storage | オブジェクトストレージ | 객체 스토리지 |
| Bucket | バケット | 버킷 |
| Object | オブジェクト | 객체 |
| Key | キー | 키 |
| Value | 値 | 값 / 실제 데이터 |
| Prefix | プレフィックス | 접두사 |
| Metadata | メタデータ | 메타데이터 |
| Tag | タグ | 태그 |
| Version ID | バージョンID | 버전 ID |
| Multipart Upload | マルチパートアップロード | 멀티파트 업로드 |
| PUT Request | PUTリクエスト | PUT 요청 |

---

# Review Questions

<details>
<summary>1. Amazon S3는 어떤 종류의 Storage Service인가?</summary>

Amazon S3는 **Object Storage Service**이다.

```text
EBS → Block Storage
EFS → File Storage
S3  → Object Storage
```

</details>

<details>
<summary>2. S3 Bucket은 모든 Region에 동시에 존재하는가?</summary>

아니다.

각 Bucket은 특정 AWS Region에 생성된다.

S3 Console에서 여러 Region의 Bucket을 통합 관리할 수 있지만, 각 Bucket 자체는 특정 Region에 존재한다.

</details>

<details>
<summary>3. images/vacation/beach.jpg에서 images와 vacation은 실제 Directory인가?</summary>

아니다.

S3는 전통적인 Directory 구조를 사용하지 않는다.

`images/vacation/beach.jpg` 전체가 Object Key이며, S3 Console이 Prefix를 이용하여 Folder처럼 표현한다.

</details>

<details>
<summary>4. 25GB Object를 S3에 저장할 수 있는가?</summary>

저장할 수 있다.

다만 25GB 전체를 하나의 PUT 요청으로 Upload할 수 없으므로 Multipart Upload를 사용해야 한다.

</details>

<details>
<summary>5. S3에서 5GB라는 숫자는 무엇을 의미하는가?</summary>

하나의 PUT 요청으로 Upload할 수 있는 최대 Object 크기이다.

5GB보다 큰 Object는 Multipart Upload가 필요하다.

</details>

<details>
<summary>6. Multipart Upload를 사용하는 이유는 무엇인가?</summary>

큰 Object를 여러 Part로 나누어 Upload할 수 있다.

또한 일부 Part의 Upload가 실패했을 때 전체 Object를 처음부터 다시 전송하지 않고 실패한 Part를 다시 Upload할 수 있으며, 병렬 Upload도 가능하다.

</details>
