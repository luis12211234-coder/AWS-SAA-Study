# 13-06. CloudFront Cache Invalidation

## 🇰🇷 1. Cache와 TTL

CloudFront Edge에 저장된 콘텐츠에는 TTL(Time To Live)이 존재합니다.

TTL이 만료되기 전까지 CloudFront는 기존 Cache를 계속 사용할 수 있습니다.

```text
Origin
 ↓
CloudFront Cache
 ↓
TTL
 ↓
Expiration
 ↓
Origin에서 다시 가져오기
```

---

## 2. Origin을 수정하면?

S3 Origin의 `index.html`을 수정했다고 가정합니다.

```text
S3 Origin
Version 1
   ↓
Version 2로 수정
```

하지만 CloudFront Edge에는 Version 1이 Cache되어 있을 수 있습니다.

```text
Origin
Version 2

CloudFront Cache
Version 1
```

따라서 TTL이 남아 있다면 사용자에게 기존 콘텐츠가 제공될 수 있습니다.

---

## 3. Cache Invalidation

변경 사항을 즉시 반영하고 싶다면 Cache Invalidation을 실행합니다.

```text
CloudFront Cache
      ↓
Invalidation
      ↓
Cached Object 제거
```

이후 사용자가 다시 요청하면:

```text
User Request
 ↓
Cache Miss
 ↓
Origin
 ↓
Latest Content
 ↓
New Cache
```

가 됩니다.

---

## 4. Path 지정

특정 Object만 무효화할 수 있습니다.

```text
/index.html
```

특정 경로의 여러 Object를 한꺼번에 무효화할 수도 있습니다.

```text
/images/*
```

---

## 5. S3 Update와 Invalidation은 별개

중요한 점:

```text
S3 Object Update
≠
Automatic CloudFront Invalidation
```

S3 파일을 수정한다고 CloudFront Cache가 자동으로 제거되는 것은 아닙니다.

필요하다면 관리자가 직접 Invalidation을 실행하거나 배포 Pipeline에서 자동화할 수 있습니다.

---

## 🔑 핵심 정리

```text
TTL
= Cache 유효 시간

Origin Update
≠ Cache 즉시 갱신

Invalidation
= Cache 강제 제거
```

```text
/index.html
→ 특정 파일

/images/*
→ 특정 경로 전체
```

한 줄 정리:

> **TTL을 기다리지 않고 CloudFront Cache를 즉시 제거한다 = Cache Invalidation**

---

## 🇯🇵 日本語 Summary

CloudFrontのCacheにはTTLがあります。

Originのファイルを更新しても、TTLが残っている場合は古いCacheが配信される可能性があります。

更新内容をすぐに反映したい場合はCache Invalidationを実行します。

特定のファイルや `/images/*` のようなPathを指定できます。

---

## 🇺🇸 English Summary

CloudFront cached objects have a TTL.

Updating the origin does not immediately remove existing cached content.

Cache Invalidation can remove selected cached objects before their TTL expires.

Paths such as `/index.html` or `/images/*` can be invalidated.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Invalidation | 無効化 | 무효화 |
| TTL | 有効期限 | 캐시 유효 시간 |
| Cache | キャッシュ | 캐시 |
| Path | パス | 경로 |
| Expiration | 有効期限切れ | 만료 |

---

## 📝 Review Questions

<details>
<summary>Q1. Origin의 파일을 수정하면 CloudFront Cache도 즉시 바뀌는가?</summary>

아닙니다. 기존 Cache는 TTL이 만료될 때까지 남아 있을 수 있습니다.

</details>

<details>
<summary>Q2. 변경된 콘텐츠를 즉시 반영하기 위해 사용할 수 있는 기능은?</summary>

CloudFront Cache Invalidation입니다.

</details>

<details>
<summary>Q3. 모든 이미지 Cache를 제거하기 위한 Path 예시는?</summary>

`/images/*`와 같은 Path를 사용할 수 있습니다.

</details>
