￼Enter file contents here# S3 Presigned URLs

## 🇰🇷 1. Presigned URL이란?

Presigned URL은 Private S3 Object에 제한된 시간 동안 접근할 수 있도록 서명된 URL을 생성하는 기능입니다.

```text
Private S3 Object 🔒
        ↓
Presigned URL 생성
        ↓
Temporary URL
        ↓
External User
```

Bucket이나 Object 자체를 Public으로 만들 필요가 없습니다.

---

## 2. 권한은 URL 생성자의 권한을 기반으로 한다

Presigned URL은 URL을 생성한 IAM Principal의 권한을 기반으로 생성됩니다.

```text
IAM User / Role
      │
      │ Presign
      ▼
Temporary URL
      ↓
User
```

즉 URL을 생성하는 Principal이 해당 작업을 수행할 권한을 가지고 있어야 합니다.

---

## 3. GET과 PUT

Presigned URL은 대표적으로 임시 Download 또는 Upload에 사용할 수 있습니다.

### GET

```text
Private File
    ↓
Presigned GET URL
    ↓
Temporary Download
```

### PUT

```text
External User
    ↓
Presigned PUT URL
    ↓
S3 Upload
```

따라서 Bucket을 Public으로 공개하지 않고 특정 Object 작업만 임시로 허용할 수 있습니다.

---

## 4. Expiration

Presigned URL에는 만료 시간이 존재합니다.

```text
URL Created
   ↓
Valid
   ↓
Expiration
   ↓
Access Denied
```

강의 기준으로 Console에서는 최대 12시간, CLI에서는 최대 168시간(7일)의 예시를 다룹니다.

시험에서는 숫자 자체보다 **Temporary Access + Expiration** 개념을 우선적으로 기억합니다.

---

## 5. 대표 사용 사례

### Premium Content

```text
Authenticated User
        ↓
Application
        ↓
Presigned URL 생성
        ↓
Private Premium Video
```

### Temporary Upload

```text
User
 ↓
Presigned PUT URL
 ↓
Private S3 Bucket
```

---

## 🎯 Exam Scenario

문제에서 다음 표현이 나오면 Presigned URL을 생각합니다.

```text
Private S3 Object
+
Temporary Access
+
External User
+
Do not make bucket public
```

---

## 🔑 핵심 정리

```text
Presigned URL
= Private Object 임시 접근

권한
= URL 생성자의 권한 기반

가능한 사용
= Download / Upload

특징
= Expiration

Bucket Public
= 필요 없음
```

한 줄 정리:

> **Private S3 Object를 Public으로 바꾸지 않고 제한된 시간 동안 공유한다 = Presigned URL**

---

## 🇯🇵 日本語 Summary

Presigned URLは、PrivateなS3 Objectに一時的なアクセスを提供する署名付きURLです。

BucketやObjectをPublicにする必要はありません。

URLには有効期限があり、DownloadやUploadなどの一時的なアクセスに利用できます。

---

## 🇺🇸 English Summary

A presigned URL provides temporary access to a private S3 object without making the bucket or object public.

The URL is created using the permissions of the principal that generates it and has an expiration time.

Common use cases include temporary downloads and uploads.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Presigned URL | 署名付きURL | 미리 서명된 URL |
| Temporary Access | 一時アクセス | 임시 접근 |
| Expiration | 有効期限 | 만료 |
| Private Object | プライベートオブジェクト | 비공개 객체 |
| Download | ダウンロード | 다운로드 |
| Upload | アップロード | 업로드 |

---

## 📝 Review Questions

<details>
<summary>Q1. Private S3 Object를 Public으로 만들지 않고 외부 사용자에게 임시로 공유하려면?</summary>

Presigned URL을 사용합니다.

</details>

<details>
<summary>Q2. Presigned URL에는 무엇이 존재하는가?</summary>

Expiration Time이 존재합니다.

</details>

<details>
<summary>Q3. Presigned URL은 Download에만 사용할 수 있는가?</summary>

아닙니다. PUT 등을 이용한 Temporary Upload에도 사용할 수 있습니다.

</details>
