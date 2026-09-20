# Elastic Load Balancer Advanced Features

## Overview

Elastic Load Balancer의 주요 고급 기능:

- Sticky Sessions
- Cross-Zone Load Balancing
- SSL/TLS Certificates & SNI
- Deregistration Delay

---

# 1. Sticky Sessions

**Sticky Session (Session Affinity)**은 동일한 Client의 요청을 일정 시간 동안 **같은 Backend Target**으로 전달하는 기능이다.

```text
Client A
   ↓
Load Balancer
   ↓
EC2 A ← 반복 요청도 같은 Target
```

### How It Works

- Cookie를 이용해 Client와 Target의 연결 관계를 유지
- Cookie가 만료되면 다른 Target으로 전달될 수 있음
- Session 정보를 특정 Backend에서 유지해야 할 때 유용

### Cookie Types

**Application-based Cookie**
- 애플리케이션 또는 Load Balancer가 생성
- 애플리케이션 요구사항에 맞는 Session 관리에 사용

**Duration-based Cookie**
- Load Balancer가 생성
- 설정된 기간 동안 동일 Target 유지

### Trade-off

Sticky Session은 특정 Target에 Traffic이 집중되어 **부하가 불균형해질 수 있다.**

### Exam Point

```text
Same Client → Same Target
        ↓
Sticky Session
```

---

# 2. Cross-Zone Load Balancing

Cross-Zone Load Balancing은 Load Balancer Node가 **다른 AZ의 Target에도 Traffic을 전달할 수 있도록 하는 기능**이다.

### Enabled

```text
AZ-A LB ─┬→ AZ-A Targets
         └→ AZ-B Targets

AZ-B LB ─┬→ AZ-A Targets
         └→ AZ-B Targets
```

모든 AZ의 Target을 대상으로 Traffic을 분산한다.

### Disabled

```text
AZ-A LB → AZ-A Targets only
AZ-B LB → AZ-B Targets only
```

각 Load Balancer Node가 자신의 AZ에 있는 Target에만 Traffic을 전달한다.

AZ별 Target 수가 다르면 Instance당 부하가 불균형해질 수 있다.

### Defaults

| Load Balancer | Cross-Zone Default |
|---|---|
| ALB | Enabled |
| NLB | Disabled |
| GWLB | Disabled |

ALB는 Target Group 수준에서 Cross-Zone 동작을 제어할 수 있다.

### Exam Point

```text
Cross-Zone ON
→ AZ를 넘어 전체 Target에 분산

Cross-Zone OFF
→ 각 AZ 내부 Target에 분산
```

---

# 3. SSL/TLS Certificates

SSL/TLS는 Client와 Load Balancer 사이의 Traffic을 **전송 중 암호화(In-flight Encryption)**한다.

```text
Client
   │
 HTTPS 🔒
   ▼
Load Balancer
```

현재는 TLS가 사용되지만 관습적으로 SSL이라는 표현도 사용한다.

### ACM

**AWS Certificate Manager (ACM)**을 사용해 SSL/TLS Certificate을 관리할 수 있다.

Certificate에는 만료 기간이 있으며 갱신이 필요하다.

### TLS Termination

Load Balancer에서 TLS 연결을 종료하고 Backend로 요청을 전달할 수 있다.

```text
Client
   │ HTTPS
   ▼
Load Balancer
   │
   │ HTTP
   ▼
Backend EC2
```

> Backend 연결이 반드시 HTTP여야 하는 것은 아니다. 위 구조는 강의의 TLS Termination 예시이다.

### Listener

**ALB**

```text
HTTPS :443
→ SSL/TLS Certificate
→ Target Group
```

**NLB**

```text
TLS :443
→ SSL/TLS Certificate
→ Target Group
```

HTTPS/TLS Listener에서는 Certificate와 Security Policy를 설정할 수 있다.

---

# 4. SNI

**SNI (Server Name Indication)**는 Client가 TLS 연결 과정에서 **접속하려는 Hostname을 서버에 알려주는 기능**이다.

이를 통해 하나의 Load Balancer가 여러 Domain의 SSL/TLS Certificate을 사용할 수 있다.

```text
Client
   │
   │ SNI = www.example.com
   ▼
ALB
   │
   └→ example.com Certificate 선택
```

예:

```text
                 ALB
          ┌───────┴───────┐
          ↓               ↓
Certificate A       Certificate B
   a.com               b.com
```

```text
SNI = a.com
→ Certificate A

SNI = b.com
→ Certificate B
```

### SNI vs ALB Routing

두 기능의 역할을 구분한다.

```text
SNI
→ 어떤 Certificate을 사용할 것인가?

ALB Listener Rule
→ 어떤 Target Group으로 보낼 것인가?
```

### Exam Point

```text
Multiple Domains
+ Multiple Certificates
+ One Load Balancer
        ↓
       SNI
```

ALB와 NLB는 여러 SSL/TLS Certificate과 SNI를 지원한다.

---

# 5. Deregistration Delay

ALB/NLB에서는 **Deregistration Delay**, Classic Load Balancer에서는 **Connection Draining**이라고 부른다.

Target을 제거할 때 현재 처리 중인 Request가 완료될 시간을 제공한다.

```text
EC2 A = Draining

Existing Request → 완료 허용
New Request      → 다른 Target으로 전달
```

즉:

```text
새 Request 차단
      ↓
기존 Request 완료
      ↓
Target 제거
```

### Duration

강의 기준:

- Default: **300 seconds**
- Range: **1–3600 seconds**
- `0`: Disabled

짧은 Request가 대부분이면 짧게, Upload 등 오래 지속되는 Request가 있다면 길게 설정할 수 있다.

### Exam Point

```text
Target 제거 예정
+
진행 중인 Request를 중단하면 안 됨
        ↓
Deregistration Delay
```

---

# Exam Notes

| Requirement | Feature |
|---|---|
| Same Client → Same Target | Sticky Session |
| Traffic across AZs | Cross-Zone Load Balancing |
| Encrypt traffic in transit | SSL/TLS |
| Manage AWS certificates | ACM |
| Multiple domains/certificates on one LB | SNI |
| Finish existing requests before removing Target | Deregistration Delay |

### Quick Memory

```text
Sticky
→ Client를 Target에 붙인다

Cross-Zone
→ AZ 경계를 넘는다

SSL/TLS
→ Traffic을 암호화한다

SNI
→ Certificate을 고른다

Deregistration Delay
→ 기존 Request를 끝내고 Target을 뺀다
```

---

# 日本語まとめ

- **Sticky Session**: 同じクライアントのリクエストを同じターゲットに送る
- **Cross-Zone Load Balancing**: AZをまたいでターゲットにトラフィックを分散する
- **SSL/TLS**: 通信中のデータを暗号化する
- **ACM**: SSL/TLS証明書を管理するAWSサービス
- **SNI**: 接続先ホスト名に応じて適切な証明書を選択する
- **Deregistration Delay**: ターゲット削除前に処理中のリクエストを完了させる

---

# English Summary

- **Sticky Sessions** keep a client connected to the same target.
- **Cross-Zone Load Balancing** distributes traffic across targets in multiple AZs.
- **SSL/TLS** encrypts traffic in transit.
- **ACM** manages SSL/TLS certificates.
- **SNI** allows a load balancer to select the appropriate certificate for a hostname.
- **Deregistration Delay** allows in-flight requests to finish before a target is removed.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Sticky Session | スティッキーセッション | 고정 세션 |
| Session Affinity | セッションアフィニティ | 세션 고정 |
| Cross-Zone Load Balancing | クロスゾーン負荷分散 | 교차 영역 로드 밸런싱 |
| SSL/TLS Certificate | SSL/TLS証明書 | SSL/TLS 인증서 |
| In-flight Encryption | 転送中の暗号化 | 전송 중 암호화 |
| Certificate Authority (CA) | 認証局 | 인증 기관 |
| Server Name Indication (SNI) | サーバー名表示 | 서버 이름 지정 |
| Security Policy | セキュリティポリシー | 보안 정책 |
| Deregistration Delay | 登録解除の遅延 | 등록 취소 지연 |
| Connection Draining | コネクションドレイニング | 연결 드레이닝 |

---

# Review Questions

1. Sticky Session은 어떤 문제를 해결하는가?
2. Cross-Zone Load Balancing이 비활성화되면 Traffic은 어떻게 분산되는가?
3. ALB와 NLB의 Cross-Zone 기본 설정은 어떻게 다른가?
4. ACM의 역할은 무엇인가?
5. SNI와 ALB Host-based Routing의 역할 차이는 무엇인가?
6. ALB와 NLB에서 TLS를 사용하는 Listener는 각각 어떤 프로토콜을 사용하는가?
7. Deregistration Delay가 필요한 이유는 무엇인가?