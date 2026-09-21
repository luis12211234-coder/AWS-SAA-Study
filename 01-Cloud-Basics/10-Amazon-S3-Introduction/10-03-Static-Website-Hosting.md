# 10-03. S3 Static Website Hosting

## 1. Static Website Hosting

Amazon S3는 Object Storage이지만, Bucket에 저장된 HTML, CSS, JavaScript, Image 등의 파일을 이용하여 **Static Website**를 Hosting할 수도 있다.

```text
S3 Bucket
│
├── index.html
├── style.css
├── script.js
└── images/
    └── beach.jpg
```

Static Website Hosting을 활성화하면 S3가 해당 Bucket에 대한 **Website Endpoint**를 제공한다.

```text
Browser
   │
   │ HTTP Request
   ▼
S3 Website Endpoint
   │
   ▼
index.html
```

---

## 2. Static Website란?

Static Website는 미리 만들어진 파일을 그대로 Client에게 전달하는 Website이다.

S3에서 제공할 수 있는 대표적인 파일은 다음과 같다.

```text
HTML
CSS
JavaScript
Images
Videos
Static Assets
```

반면 S3 자체에서 Server-side Application을 실행하는 것은 아니다.

예:

```text
PHP
Spring Boot
Django
Node.js Server
```

즉:

```text
S3
→ 파일 저장 및 전달

Application Server
→ Server-side Code 실행
```

이다.

---

## 3. Website Endpoint

Static Website Hosting을 활성화하면 일반적인 S3 Object 접근과 별도로 **Website Endpoint**를 사용할 수 있다.

개념적으로:

```text
S3 Bucket
      │
      │ Static Website Hosting ON
      ▼
Website Endpoint
      │
      ▼
Browser
```

사용자는 Website Endpoint를 통해 Bucket을 하나의 Static Website처럼 이용할 수 있다.

---

## 4. Index Document

Static Website Hosting을 설정할 때 **Index Document**를 지정한다.

대표적으로:

```text
index.html
```

을 사용한다.

사용자가 Website의 Root에 접근하면:

```text
GET /
```

S3는 설정된 Index Document를 반환한다.

```text
Browser
   │
   │ GET /
   ▼
Website Endpoint
   │
   ▼
index.html
```

즉 사용자가 직접:

```text
/index.html
```

을 입력하지 않아도 Root URL에 접근하면 기본 페이지가 표시될 수 있다.

---

## 5. Error Document

Static Website Hosting에서는 필요하다면 Error Document도 지정할 수 있다.

예:

```text
error.html
```

Website 요청 처리 중 Error가 발생했을 때 사용자에게 보여줄 페이지로 사용할 수 있다.

```text
Request
   │
   ├── Success
   │      ↓
   │   index.html
   │
   └── Error
          ↓
       error.html
```

---

## 6. HTML과 다른 Object의 관계

`index.html` 안에서 Image를 사용하는 경우를 생각해 보자.

```html
<img src="beach.jpg">
```

Browser가 Website에 접속하면 먼저 `index.html`을 요청한다.

```text
GET /
→ index.html
```

그 후 Browser가 HTML을 해석하면서 `beach.jpg`가 필요하다는 것을 확인한다.

그러면 별도의 요청을 보낸다.

```text
GET /beach.jpg
→ beach.jpg
```

전체 흐름은 다음과 같다.

```text
Browser
   │
   ├── GET /
   │      ↓
   │   index.html
   │
   └── GET /beach.jpg
          ↓
       beach.jpg
```

즉 HTML 파일 안에 Image 자체가 들어 있는 것이 아니라, Browser가 필요한 Object를 추가로 요청한다.

---

## 7. URL Path와 Object Key

Website URL의 Path는 S3의 Object Key와 연결해서 이해할 수 있다.

Bucket이 다음과 같다고 하자.

```text
my-bucket
│
├── index.html
├── beach.jpg
└── images/
    └── coffee.jpg
```

Website에서:

```text
/beach.jpg
```

에 접근하면 다음 Object를 요청한다.

```text
Key = beach.jpg
```

그리고:

```text
/images/coffee.jpg
```

에 접근하면:

```text
Key = images/coffee.jpg
```

를 요청한다.

즉:

```text
URL Path
        ↓
Object Key
```

로 연결해서 생각하면 된다.

---

## 8. Object URL vs Website Endpoint

S3에서는 **Object URL**과 **Website Endpoint**를 구분해야 한다.

### Object URL

특정 Object에 직접 접근하기 위한 URL이다.

```text
Object URL
     ↓
coffee.jpg
```

즉 목적지가 하나의 Object이다.

### Website Endpoint

Bucket을 Website로 사용할 때 제공되는 Endpoint이다.

```text
Website Endpoint
      │
      │ GET /
      ▼
Index Document
      │
      ▼
index.html
```

따라서:

```text
Object URL
→ 특정 Object에 직접 접근

Website Endpoint
→ Bucket을 Static Website처럼 접근
```

이라는 차이가 있다.

---

## 9. Static Website와 Public Access

S3 Static Website Endpoint를 이용해 일반 Internet 사용자에게 Website를 공개하려면 사용자가 Website의 Object를 읽을 수 있어야 한다.

예를 들어:

```text
Browser
   │
   │ GET index.html
   ▼
S3
```

여기서 `index.html`에 대한 Read Permission이 없다면 S3는 Object를 전달할 수 없다.

```text
Private Object
      ↓
Public User Request
      ↓
Access Denied
```

따라서 Public S3 Website를 구성하는 경우 Object에 필요한 Public Read Permission을 구성해야 한다.

예를 들어 Bucket Policy에서:

```json
{
  "Effect": "Allow",
  "Principal": "*",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::example-bucket/*"
}
```

와 같은 형태로 Object Read를 허용할 수 있다.

이 경우 Block Public Access 설정 역시 함께 고려해야 한다.

---

## 10. Block Public Access와 Static Website

Public Static Website를 구성한다고 가정하면 다음 두 개념을 구분해야 한다.

```text
Block Public Access
→ Public Access를 막는 안전장치

Bucket Policy
→ 실제 Object Read Permission
```

따라서 단순히:

```text
Block Public Access OFF
```

만 했다고 Website가 자동으로 Public이 되는 것은 아니다.

개념적인 흐름은:

```text
Block Public Access 설정 확인
          ↓
Bucket Policy에서
s3:GetObject 허용
          ↓
Public User가 Object Read 가능
          ↓
Static Website 표시
```

이다.

---

## 11. Access Denied

Object URL이나 Website Endpoint를 알고 있더라도 Object를 읽을 Permission이 없다면 접근할 수 없다.

```text
URL을 알고 있음
≠
접근 권한이 있음
```

예:

```text
User
 │
 │ Object URL
 ▼
Private S3 Object
 │
 ▼
Access Denied
```

S3 Security에서 중요한 것은 **URL의 존재와 Permission은 별개**라는 점이다.

---

## 12. S3 Website Endpoint와 HTTPS

S3의 **Static Website Endpoint 자체는 HTTPS를 지원하지 않는다.**

```text
S3 Website Endpoint
→ HTTP
```

HTTPS를 사용하는 실제 Website Architecture가 필요하다면 CloudFront 등을 S3 앞에 구성할 수 있다.

개념적으로:

```text
Internet
   │
   │ HTTPS
   ▼
CloudFront
   │
   ▼
Amazon S3
```

이러한 Architecture에서는 S3를 직접 Public Website로 노출하는 방식보다 S3를 Private하게 유지하면서 CloudFront를 통해 Content를 제공하는 구성을 사용할 수 있다.

CloudFront는 이후 관련 섹션에서 자세히 학습한다.

---

# 핵심 정리

```text
S3 Static Website Hosting
→ HTML / CSS / JS / Image 등
→ Static Content 제공


Static Website Hosting ON
        ↓
Website Endpoint 제공


GET /
↓
Index Document
↓
index.html


HTML에서 Image 참조

index.html
   ↓
Browser가 별도로 요청
   ↓
beach.jpg


URL Path
→ Object Key

/beach.jpg
→ beach.jpg

/images/beach.jpg
→ images/beach.jpg


Object URL
→ 특정 Object 직접 접근

Website Endpoint
→ Bucket을 Website처럼 접근


URL을 알고 있음
≠
Object 접근 권한이 있음


Public Static Website
→ Object Read Permission 필요


S3 Website Endpoint
→ HTTPS 지원 X

HTTPS가 필요한 Architecture
→ CloudFront 등을 앞에 구성
```

---

# 🇯🇵 日本語まとめ

Amazon S3 では HTML、CSS、JavaScript、画像などを保存して **Static Website Hosting** を利用できます。

Static Website Hosting を有効にすると、S3 は Website Endpoint を提供します。

Index Document に `index.html` を設定した場合:

```text
GET /
→ index.html
```

として基本ページが表示されます。

HTML 内で画像を参照している場合、Browser はその Object を別の HTTP Request で取得します。

```text
GET /
→ index.html

GET /beach.jpg
→ beach.jpg
```

Website URL の Path は S3 Object Key と対応します。

また、URL を知っているだけでは Private Object にアクセスできません。Public Static Website として公開する場合は、Object に必要な Read Permission を設定する必要があります。

S3 Website Endpoint 自体は HTTPS をサポートしていません。HTTPS が必要な構成では CloudFront などを利用できます。

---

# 🇺🇸 English Summary

Amazon S3 can host static websites containing HTML, CSS, JavaScript, images, and other static content.

When Static Website Hosting is enabled, S3 provides a Website Endpoint.

An Index Document such as `index.html` can be configured so that a request to the root path returns the website's main page.

```text
GET /
→ index.html
```

If the HTML references another object, such as an image, the browser sends an additional request for that object.

```text
GET /beach.jpg
→ beach.jpg
```

The URL path maps to an S3 object key.

Knowing an object's URL does not automatically provide permission to access it. A public S3 website requires appropriate read permissions.

S3 Website Endpoints do not support HTTPS directly. CloudFront can be used in architectures that require HTTPS delivery.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Static Website | 静的ウェブサイト | 정적 웹사이트 |
| Static Website Hosting | 静的ウェブサイトホスティング | 정적 웹사이트 호스팅 |
| Website Endpoint | ウェブサイトエンドポイント | 웹사이트 엔드포인트 |
| Index Document | インデックスドキュメント | 인덱스 문서 |
| Error Document | エラードキュメント | 오류 문서 |
| Static Content | 静的コンテンツ | 정적 콘텐츠 |
| Object URL | オブジェクトURL | 객체 URL |
| Public Access | パブリックアクセス | 퍼블릭 액세스 |
| Access Denied | アクセス拒否 | 접근 거부 |
| HTTP Request | HTTPリクエスト | HTTP 요청 |

---

# Review Questions

<details>
<summary>1. S3에서 Static Website를 Hosting할 수 있는가?</summary>

가능하다.

HTML, CSS, JavaScript, Image 등의 Static Content를 S3에 저장하고 Static Website Hosting을 활성화할 수 있다.

</details>

<details>
<summary>2. Website Endpoint의 Root에 접근하면 어떤 Object가 반환되는가?</summary>

Static Website Hosting 설정에서 지정한 Index Document가 반환된다.

예:

```text
GET /
→ index.html
```

</details>

<details>
<summary>3. index.html에 beach.jpg가 포함되어 있다면 S3가 한 번에 둘을 반환하는가?</summary>

아니다.

Browser가 먼저 `index.html`을 받은 뒤 HTML을 해석하고 `beach.jpg`를 별도의 Request로 요청한다.

```text
GET /
→ index.html

GET /beach.jpg
→ beach.jpg
```

</details>

<details>
<summary>4. /images/beach.jpg라는 URL Path는 S3에서 무엇과 연결되는가?</summary>

다음 Object Key와 연결된다.

```text
images/beach.jpg
```

</details>

<details>
<summary>5. Object URL을 알고 있다면 Private Object에도 접근할 수 있는가?</summary>

아니다.

URL의 존재와 접근 Permission은 별개의 문제이다.

Permission이 없다면 `Access Denied`가 발생할 수 있다.

</details>

<details>
<summary>6. Block Public Access를 OFF하면 Static Website가 자동으로 Public이 되는가?</summary>

아니다.

Block Public Access는 Public Access를 막는 안전장치이며, 실제 Public Read Permission은 Bucket Policy 등을 통해 별도로 허용해야 한다.

</details>

<details>
<summary>7. S3 Static Website Endpoint는 HTTPS를 직접 지원하는가?</summary>

지원하지 않는다.

HTTPS가 필요한 Architecture에서는 CloudFront 등을 S3 앞에 구성할 수 있다.

</details>
