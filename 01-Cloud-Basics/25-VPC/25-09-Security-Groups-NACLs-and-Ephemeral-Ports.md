# 📘 25-09. Security Groups, NACLs & Ephemeral Ports

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Security Group` · `NACL` · `Stateful` · `Stateless` · `Ephemeral Port` · `Rule Number`

## 🇰🇷 1. SG와 NACL은 어디에 적용되는가
| 항목 | Security Group(SG) | Network ACL(NACL) |
|---|---|---|
| 적용 단위 | ENI(주로 EC2 인스턴스) | Subnet 경계 |
| 규칙 | Allow만 | Allow / Deny |
| 상태 | **Stateful** | **Stateless** |
| 규칙 평가 | 일치하는 Allow 하나면 허용 | 낮은 번호부터 첫 일치 규칙으로 결정 |
| 범위 | SG를 연결한 네트워크 인터페이스 | 해당 Subnet의 인터페이스 |

NACL은 **서브넷 경계를 넘는** 트래픽을 검사하며, 같은 Subnet 내부 패킷을 모두 검사하는 장치가 아니다.

## 🇰🇷 2. Stateful과 Stateless의 요청/응답 차이
```text
외부 → EC2로 새 HTTP 요청
  NACL inbound 검사 → SG inbound 검사 → EC2 도착
EC2 → 외부로 HTTP 응답
  SG: 허용된 연결의 응답이므로 자동 허용
  NACL outbound: 별도 규칙 검사

EC2 → 외부로 새 HTTPS 요청
  SG outbound 검사 → NACL outbound 검사
외부 → EC2로 그 응답
  NACL inbound 별도 검사 → SG 응답 자동 허용
```

SG의 상태 저장은 **모든 신규 outbound/inbound 자동 허용**을 뜻하지 않는다. 이미 허용된 연결의 응답만 허용된다.

## 🇰🇷 3. NACL Rule Number와 초기 상태
규칙 번호 `1~32766`에서 **낮은 번호가 우선**. 예를 들어 `80 DENY HTTP`와 `100 ALLOW ALL`이면 HTTP는 먼저 80에서 차단된다. `140 DENY HTTP`로 바꾸면 100 Allow에서 먼저 통과한다. 맨 마지막 `*`는 어떤 규칙에도 맞지 않는 트래픽을 거부한다.

**Default NACL**은 IPv4/IPv6에 대해 기본적으로 모두 허용하는 규칙을 가진다. 새 **Custom NACL**은 허용 규칙이 없어 처음에는 모든 트래픽을 거부한다.

## 🇰🇷 4. Ephemeral Port가 필요한 이유
클라이언트는 서버의 고정 포트(HTTPS 443, MySQL 3306)에 접속할 때 자신의 **임시 포트(Ephemeral Port)**를 선택한다.

```text
요청: 클라이언트 10.0.0.10:50105 → DB 10.0.16.20:3306
응답: DB 10.0.16.20:3306 → 클라이언트 10.0.0.10:50105
```

NACL의 Stateless 성격 때문에 **DB 측 outbound 임시 포트**와 **웹 측 inbound 임시 포트**도 허용해야 한다. 운영체제마다 임시 포트 범위가 다르며 `1024-65535` 같은 넓은 범위를 쓰더라도 출발지/목적지 CIDR로 제한할 수 있다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- SG = Stateful/Allow only/ENI; NACL = Stateless/Allow+Deny/Subnet.
- NACL은 낮은 규칙 번호부터 첫 일치 결과 적용.
- 허용된 연결의 응답은 SG에서 자동 통과, NACL은 반대 방향도 별도 허용.
- Ephemeral Port는 클라이언트 쪽 반환 목적지 포트.
- Default NACL Allow All, 신규 Custom NACL Deny All.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 특정 IP를 명시적으로 차단할 수 있는 것은 SG와 NACL 중?

<details>
<summary>정답 및 해설 보기</summary>

**NACL.** Deny 규칙 지원.

</details>

### Q2. 들어온 HTTP 요청이 SG에서 허용되면 응답 outbound는?

<details>
<summary>정답 및 해설 보기</summary>

**자동 허용.** 허용된 연결에 대한 Stateful 응답이다.

</details>

### Q3. NACL의 80 DENY와 100 ALLOW ALL 중 누가 먼저 적용되는가?

<details>
<summary>정답 및 해설 보기</summary>

**80 DENY.** 낮은 번호가 높은 우선순위다.

</details>

### Q4. MySQL 3306으로 연결한 클라이언트가 응답받는 포트는?

<details>
<summary>정답 및 해설 보기</summary>

**클라이언트가 선택한 임시 포트**(예: 50105).

</details>

### Q5. 새 사용자 정의 NACL은 기본적으로 어떤 트래픽을 허용하는가?

<details>
<summary>정답 및 해설 보기</summary>

**없다.** 허용 규칙을 직접 추가해야 한다.

</details>

## 🇯🇵 日本語 Summary

Security Group は ENI 単位の Stateful/Allow-only ファイアウォール。NACL は Subnet 境界の Stateless/Allow-Deny 制御で、番号が小さいルールを先に適用する。戻り通信の Ephemeral Port 許可が重要。

## 🇺🇸 English Summary

Security groups are stateful allow-only controls on ENIs; NACLs are stateless subnet-boundary filters with ordered allow/deny rules. Responses to SG-approved connections are automatically allowed, but NACLs require explicit rules for return traffic and ephemeral ports.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Security Group | 보안 그룹 | セキュリティグループ |
| Network ACL | 네트워크 접근 제어 목록 | ネットワーク ACL |
| Stateful | 상태 저장 | ステートフル |
| Stateless | 무상태 | ステートレス |
| Ephemeral Port | 임시 포트 | エフェメラルポート |
