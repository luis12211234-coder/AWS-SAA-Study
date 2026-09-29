# S3 Access Points

## 🇰🇷 1. S3 Access Point란?

하나의 S3 Bucket을 여러 Team이나 Application이 사용할 경우 Bucket Policy가 매우 복잡해질 수 있습니다.

```text
S3 Bucket
│
├─ finance/
├─ sales/
└─ analytics/
```

모든 권한을 하나의 Bucket Policy에 계속 추가하면 관리가 어려워집니다.

S3 Access Point는 Bucket으로 들어가는 별도의 접근 지점을 만들어 이러한 접근 관리를 단순화합니다.

```text
             S3 Bucket
                 ↑
       ┌─────────┼─────────┐
       │         │         │
 Finance AP   Sales AP  Analytics AP
```

---

## 2. Access Point Policy

각 Access Point는 별도의 Access Point Policy를 가질 수 있습니다.

예:

```text
Finance Access Point
→ /finance
→ Read / Write

Sales Access Point
→ /sales
→ Read / Write

Analytics Access Point
→ /finance + /sales
→ Read Only
```

따라서 거대한 하나의 Bucket Policy 대신 접근 목적별로 Policy를 분리할 수 있습니다.

---

## 3. Access Point는 독립적인 Endpoint를 가진다

각 Access Point에는 고유한 DNS Name이 존재합니다.

Application은 Bucket에 직접 접근하는 대신 특정 Access Point를 통해 접근할 수 있습니다.

```text
Application
     ↓
Access Point
     ↓
S3 Bucket
```

---

## 4. Bucket Policy가 사라지는 것은 아니다

Access Point Policy를 사용한다고 해서 Bucket Policy가 무시되는 것은 아닙니다.

```text
Request
   ↓
Access Point Policy
   +
Bucket Permissions
   ↓
S3 Object
```

실무에서는 Bucket이 Access Point를 통한 접근을 허용하도록 구성하고 세부 접근 권한을 Access Point Policy에 위임하는 구조를 사용할 수 있습니다.

즉:

```text
Bucket
→ Access Point를 통한 접근 허용

Access Point
→ 실제 세부 권한 관리
```

라고 이해할 수 있습니다.

---

## 5. Network Origin

Access Point는 Network Origin을 지정할 수 있습니다.

```text
S3 Access Point
│
├─ Internet Origin
└─ VPC Origin
```

---

## 6. Internet Origin

Internet Origin은 해당 Access Point가 특정 VPC에서 오는 요청만으로 제한되지 않았다는 의미입니다.

중요:

```text
Internet Origin
≠
Public Access
```

실제 접근은 여전히 IAM, Access Point Policy, Bucket Policy, Public Access Block 등의 영향을 받습니다.

---

## 7. VPC Origin

VPC Origin Access Point는 지정된 VPC에서 오는 요청만 허용합니다.

```text
VPC A
 │
 ▼
Access Point
Origin = VPC A
 │
 ▼
S3 Bucket
```

다른 VPC에서 해당 Access Point로 접근하는 것은 허용되지 않습니다.

---

## 8. VPC Endpoint

S3는 사용자의 VPC 내부에 존재하는 서비스가 아닙니다.

VPC 내부 Resource가 S3에 Private하게 접근하기 위해 VPC Endpoint를 사용할 수 있습니다.

```text
EC2
 │
 ▼
VPC Endpoint
 │
 ▼
S3 Access Point
 │
 ▼
S3 Bucket
```

쉽게 구분하면:

```text
VPC Endpoint
= Private 통로

Access Point
= S3 접근 입구
```

입니다.

---

## 9. 여러 Policy Layer

VPC Origin Access Point 구조에서는 여러 Policy Layer가 존재할 수 있습니다.

```text
EC2
 ↓
VPC Endpoint
 │
 └─ Endpoint Policy
 ↓
Access Point
 │
 └─ Access Point Policy
 ↓
S3 Bucket
 │
 └─ Bucket Permissions
```

각각 서로 다른 수준에서 접근을 제어합니다.

---

## 10. 왜 Access Point를 사용하는가?

핵심은 **Access Management의 확장성**입니다.

기존:

```text
Huge Bucket Policy 👹
├─ Finance Rules
├─ Sales Rules
├─ Analytics Rules
├─ App A Rules
└─ App B Rules
```

Access Point 사용:

```text
S3 Bucket
│
├─ Finance AP
│   └─ Finance Policy
│
├─ Sales AP
│   └─ Sales Policy
│
└─ Analytics AP
    └─ Analytics Policy
```

각 Team이나 Application의 접근 정책을 분리하여 관리하기 쉬워집니다.

---

## 🎯 Exam Scenario

다음과 같은 문제가 나오면 S3 Access Points를 생각합니다.

```text
One Large S3 Dataset
+
Many Teams / Applications
+
Different Access Requirements
+
Bucket Policy becoming complex
        ↓
S3 Access Points
```

또한:

```text
Access Point
+
Only accessible from specific VPC
        ↓
VPC Origin Access Point
```

---

## 🔑 핵심 정리

```text
S3 Access Point
= S3 Bucket으로 들어가는 전용 입구

각 Access Point
= Own DNS Name + Policy

목적
= 복잡한 S3 Access Management 단순화

Internet Origin
= 특정 VPC로 제한하지 않음
≠ Public

VPC Origin
= 지정 VPC에서만 접근

VPC Endpoint
= VPC → S3 Private 통로
```

한 줄 정리:

> **하나의 대규모 S3 Bucket에 여러 Team과 Application의 접근을 분리해 관리한다 = S3 Access Points**

---

## 🇯🇵 日本語 Summary

S3 Access Pointsは、複数のTeamやApplicationが同じS3 Bucketを利用する場合のAccess Managementを簡単にする機能です。

各Access Pointは独自のDNS NameとAccess Point Policyを持つことができます。

VPC Originを使用すると、指定されたVPCからのRequestだけにAccess Pointを制限できます。

---

## 🇺🇸 English Summary

S3 Access Points simplify access management for shared S3 datasets.

Each Access Point has its own DNS name and Access Point policy.

Access Points can use an Internet origin or be restricted to a specific VPC using a VPC origin.

A VPC endpoint can provide private connectivity from resources inside a VPC to S3.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Access Point | アクセスポイント | 액세스 포인트 |
| Access Point Policy | アクセスポイントポリシー | 액세스 포인트 정책 |
| Network Origin | ネットワークオリジン | 네트워크 오리진 |
| Internet Origin | インターネットオリジン | 인터넷 오리진 |
| VPC Origin | VPCオリジン | VPC 오리진 |
| VPC Endpoint | VPCエンドポイント | VPC 엔드포인트 |
| Private Access | プライベートアクセス | 프라이빗 접근 |

---

## 📝 Review Questions

<details>
<summary>Q1. 하나의 S3 Bucket을 여러 Team이 사용해 Bucket Policy가 지나치게 복잡해졌다면?</summary>

S3 Access Points를 사용할 수 있습니다.

</details>

<details>
<summary>Q2. Internet Origin은 Access Point가 Public이라는 의미인가?</summary>

아닙니다. 특정 VPC에서 오는 요청만으로 제한하지 않았다는 의미입니다.

</details>

<details>
<summary>Q3. 특정 VPC에서만 Access Point를 사용하도록 제한하려면?</summary>

VPC Origin Access Point를 사용합니다.

</details>

<details>
<summary>Q4. VPC Endpoint와 Access Point의 역할 차이는?</summary>

VPC Endpoint는 VPC에서 S3로 Private하게 연결하는 통로이고, Access Point는 S3 Bucket으로 들어가는 접근 지점입니다.

</details>
