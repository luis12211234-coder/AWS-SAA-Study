# 11-04. Amazon S3 Performance

## 🇰🇷 1. S3 Baseline Performance

Amazon S3는 높은 Request Rate에 맞춰 자동으로 확장됩니다.

S3 성능을 이해할 때 중요한 단위 중 하나가 **Prefix**입니다.

기본적으로 Prefix당 최소 다음 Request Rate를 지원합니다.

```text
PUT / COPY / POST / DELETE
→ 3,500 requests/sec

GET / HEAD
→ 5,500 requests/sec
```

간단히:

```text
Write 계열 → 3,500
Read 계열  → 5,500
```

---

## 2. Prefix란?

S3에는 실제 Directory 구조가 존재하지 않습니다.

예:

```text
s3://my-bucket/images/cat.jpg
```

실제로는:

```text
Bucket
= my-bucket

Object Key
= images/cat.jpg
```

`images/`와 같은 Object Key의 앞부분을 Prefix로 사용할 수 있습니다.

예:

```text
images/cat.jpg
images/dog.jpg
images/bird.jpg
```

위 Object들은 모두 `images/`라는 앞부분을 공유합니다.

```text
images/
├─ cat.jpg
├─ dog.jpg
└─ bird.jpg
```

콘솔에서는 Folder처럼 보일 수 있지만 실제 S3 내부에서는 Key의 앞부분 문자열입니다.

---

## 3. Prefix와 Performance

다음 Object들이 있다고 가정합니다.

```text
images/cat.jpg
images/dog.jpg
images/bird.jpg
```

이 Object들에 대한 Request가 같은 Prefix에 집중되어 있습니다.

```text
images/
  ↓
GET
GET
GET
GET
...
```

하나의 Prefix에서 최소 5,500 GET/HEAD requests/sec를 지원합니다.

중요한 점은 **Object 하나당 5,500이 아니라 Prefix에 대한 Request Rate**라는 것입니다.

---

## 4. Multiple Prefixes

여러 Prefix에 Request를 분산하면 전체 Request Rate를 더 크게 확장할 수 있습니다.

```text
images/       → 5,500 GET/HEAD/sec
videos/       → 5,500 GET/HEAD/sec
documents/    → 5,500 GET/HEAD/sec
logs/         → 5,500 GET/HEAD/sec
```

Request가 실제로 네 Prefix에 분산되어 있다면:

```text
5,500 × 4
= 22,000 GET/HEAD requests/sec
```

개념적으로:

```text
                  S3 Bucket
                      │
       ┌──────────────┼──────────────┐
       │              │              │
       ▼              ▼              ▼
    images/         videos/       documents/
     5,500           5,500          5,500
                                      │
                                      ▼
                                    logs/
                                    5,500
```

따라서 S3의 Read Performance가 Bucket 전체에서 5,500 requests/sec로 제한되는 것은 아닙니다.

### Important

Prefix가 여러 개 존재한다고 자동으로 성능이 증가하는 것은 아닙니다.

Request도 여러 Prefix에 분산되어야 합니다.

```text
Prefix A ← 모든 Request 집중
Prefix B ← 0
Prefix C ← 0
Prefix D ← 0
```

위와 같은 상황에서는 여러 Prefix를 활용한 병렬화의 이점을 얻지 못합니다.

---

## 5. Multipart Upload

대용량 Object를 효율적으로 업로드하기 위한 기능입니다.

큰 파일 하나를 여러 Part로 나눕니다.

```text
Large File
████████████████████

        ↓ Split

████   ████   ████   ████

 ↓       ↓      ↓      ↓

Part1   Part2  Part3  Part4
   \      |      |     /
          S3
```

각 Part를 병렬로 업로드할 수 있기 때문에 대역폭을 효율적으로 사용할 수 있습니다.

또한 일부 Part의 Upload가 실패한 경우 전체 파일을 처음부터 다시 전송하지 않고 실패한 Part를 다시 전송할 수 있습니다.

### Size

```text
100 MB 이상
→ Multipart Upload 사용 권장

5 GB 초과
→ Multipart Upload 필요
```

---

## 6. S3 Transfer Acceleration

Transfer Acceleration은 사용자와 S3 Bucket 사이의 장거리 데이터 전송을 가속하기 위한 기능입니다.

예를 들어 사용자는 미국에 있고 S3 Bucket은 호주 Region에 있다고 가정합니다.

일반적인 개념:

```text
User 🇺🇸
   │
   │ Long-distance Network
   │
   ▼
S3 Bucket 🇦🇺
```

Transfer Acceleration:

```text
User 🇺🇸
   │
   ▼
Nearby AWS Edge Location
   │
   │ AWS optimized network path
   ▼
S3 Bucket 🇦🇺
```

사용자는 가까운 AWS Edge Location을 진입점으로 사용하고 이후 AWS의 최적화된 네트워크 경로를 이용하여 S3 Bucket으로 데이터를 전송합니다.

대표적인 상황:

```text
Global Users
+
S3 Bucket이 멀리 있음
+
Large File Transfer

→ S3 Transfer Acceleration
```

---

## 7. Multipart Upload와 Transfer Acceleration

둘은 서로 다른 문제를 해결합니다.

```text
Multipart Upload
= 파일을 어떻게 Upload할 것인가?

Transfer Acceleration
= 먼 S3 Bucket까지 어떤 경로로 빠르게 전송할 것인가?
```

따라서 둘을 함께 사용할 수도 있습니다.

```text
Large File
   ↓
Multipart
   ↓
Part 1 ─┐
Part 2 ─┼─→ Edge Location
Part 3 ─┤        ↓
Part 4 ─┘   Optimized Network
                  ↓
              S3 Bucket
```

---

## 8. S3 Byte-Range Fetch

Byte-Range Fetch는 Object 전체가 아니라 특정 Byte Range만 GET하는 방법입니다.

예:

```text
Large Object

┌──────┬──────┬──────┬──────┐
│Range1│Range2│Range3│Range4│
└──────┴──────┴──────┴──────┘
   ↑      ↑      ↑      ↑
  GET    GET    GET    GET
```

여러 Range를 병렬로 가져오면 Large Object의 Download Throughput을 높일 수 있습니다.

---

## 9. Retry 효율

Object 전체를 하나의 Request로 다운로드하는 대신 여러 Range로 나누면 일부 Request가 실패했을 때 해당 Range만 다시 요청할 수 있습니다.

```text
Range 1 ✅
Range 2 ✅
Range 3 ❌
Range 4 ✅

→ Range 3만 Retry
```

따라서 실패 시 복구 효율도 향상될 수 있습니다.

---

## 10. Partial Object Retrieval

Byte-Range Fetch는 성능 향상뿐 아니라 Object의 일부만 필요한 경우에도 사용할 수 있습니다.

예를 들어 큰 파일의 처음 50 Byte에 Header 정보가 있다고 가정합니다.

```text
10 GB File

┌──────────┬──────────────────────────┐
│ Header   │       Main Data          │
│ 50 Bytes │                          │
└──────────┴──────────────────────────┘
```

전체 10 GB를 다운로드할 필요 없이:

```text
GET first 50 Bytes
```

만 수행할 수 있습니다.

---

## 11. Performance Mental Model

S3 Performance 문제는 어떤 병목을 해결하려는지 먼저 판단합니다.

```text
① Request가 매우 많음
→ Multiple Prefixes / Parallelization

② Large Object Upload
→ Multipart Upload

③ User와 S3 Region이 지리적으로 멂
→ Transfer Acceleration

④ Large Object Download
→ Byte-Range Fetch
```

이 네 기능은 서로 경쟁하는 기능이 아니라 **서로 다른 성능 문제를 해결하는 방법**입니다.

---

## 🔑 핵심 정리

```text
Prefix
→ 높은 Request Rate 확장

3,500
→ PUT / COPY / POST / DELETE

5,500
→ GET / HEAD

Multipart Upload
→ Large File Upload

Transfer Acceleration
→ Long-distance S3 Transfer

Byte-Range Fetch
→ Parallel / Partial Download
```

---

## 🇯🇵 日本語 Summary

Amazon S3は高いリクエストレートに自動的にスケールします。

主なパフォーマンス最適化方法：

- Prefixごとにリクエストを分散
- Multipart Uploadによる大容量ファイルの並列アップロード
- Transfer Accelerationによる長距離転送の高速化
- Byte-Range Fetchによる大容量オブジェクトの並列・部分ダウンロード

Multipart UploadとTransfer Accelerationは異なる問題を解決する機能であり、必要に応じて併用できます。

---

## 🇺🇸 English Summary

Amazon S3 automatically scales to high request rates.

Important performance concepts include:

- distributing requests across prefixes
- using Multipart Upload for large object uploads
- using Transfer Acceleration for long-distance transfers
- using Byte-Range Fetches for parallel or partial downloads

Multipart Upload and Transfer Acceleration solve different performance problems and can be used together.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Prefix | プレフィックス | 접두사 |
| Request Rate | リクエストレート | 요청 처리율 |
| Multipart Upload | マルチパートアップロード | 멀티파트 업로드 |
| Transfer Acceleration | 転送高速化 | 전송 가속 |
| Edge Location | エッジロケーション | 엣지 로케이션 |
| Byte-Range Fetch | バイト範囲取得 | 바이트 범위 가져오기 |
| Throughput | スループット | 처리량 |
| Parallelization | 並列化 | 병렬화 |
| Retry | 再試行 | 재시도 |
| Partial Retrieval | 部分取得 | 부분 가져오기 |

---

## 📝 Review Questions

<details>
<summary>Q1. Prefix당 최소 GET/HEAD Request Rate는?</summary>

초당 5,500 requests입니다.

</details>

<details>
<summary>Q2. Prefix당 최소 PUT/COPY/POST/DELETE Request Rate는?</summary>

초당 3,500 requests입니다.

</details>

<details>
<summary>Q3. 대용량 Object Upload에 사용하는 기능은?</summary>

Multipart Upload입니다.

</details>

<details>
<summary>Q4. 전 세계 사용자와 먼 Region의 S3 사이 전송을 가속하려면?</summary>

S3 Transfer Acceleration을 고려할 수 있습니다.

</details>

<details>
<summary>Q5. Large Object의 특정 부분만 가져오거나 병렬 Download하려면?</summary>

Byte-Range Fetch를 사용할 수 있습니다.

</details>
