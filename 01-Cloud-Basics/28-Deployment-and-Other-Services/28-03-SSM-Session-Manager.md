
# 📘 28-03. AWS Systems Manager Session Manager

> [!NOTE]
> **AWS SAA-C03 Study Note** · `GitHub Section 28` · `Udemy Section 30`
> **핵심 키워드:** Session Manager, SSM Agent, IAM, Instance Profile, VPC Interface Endpoint, HTTPS 443, No SSH 22

## 🇰🇷 1. 왜 Session Manager가 필요한가?

EC2 Linux 서버에 접속할 때 일반적인 방법은 **SSH(TCP 22)** 다. 하지만 서버가 많아질수록 SSH 키 관리, Bastion Host 운영, 인바운드 포트 통제에 부담이 생긴다.

**Session Manager**는 AWS Systems Manager의 관리형 셸 접속 기능이다. **SSH 키와 Bastion Host 없이, 대상 EC2의 인바운드 22번 포트를 열지 않고도** 관리형 인스턴스에 접속할 수 있다.

> **핵심:** Session Manager는 'SSH를 다른 방식으로 뚫는 서비스'가 아니라 **SSM Agent와 AWS 서비스의 통신을 이용한 관리형 세션**이다.

## 🇰🇷 2. 실제 접속 구조

```text
운영자 (AWS Console / CLI)
   │ IAM: ssm:StartSession + 대상 리소스 접근 권한
   ▼
AWS Systems Manager - Session Manager
   ▲
   │ TLS/HTTPS 세션 통신 (ssmmessages)
   │
EC2 인스턴스 안의 SSM Agent ── OS Shell 실행
   │
   └─ EC2 권한: AmazonSSMManagedInstanceCore 등
```

**필요 조건은 크게 세 가지다.**

1. **사용자 권한:** `ssm:StartSession` 등 세션과 대상 리소스에 대한 IAM 허용.
2. **인스턴스 상태·권한:** 실행 중인 지원 버전 **SSM Agent**, Systems Manager 등록 상태, 필요한 인스턴스 측 권한.
3. **네트워크:** 인스턴스가 관련 Systems Manager 엔드포인트에 **아웃바운드 HTTPS 443**으로 도달 가능해야 한다.

일반적인 EC2 구성은 **IAM Instance Profile에 `AmazonSSMManagedInstanceCore` 정책을 부착**한다. 별도 구성으로 **Default Host Management Configuration**을 사용할 수도 있지만 에이전트 버전, IMDSv2 등 전제 조건이 있다.

## 🇰🇷 3. Private Subnet에서도 되는 이유

'Private Subnet에는 인터넷 게이트웨이가 없으니 접속할 수 없다'고 생각하기 쉽지만, **Session Manager는 인바운드 SSH 연결을 요구하지 않는다.**

### 방식 A: NAT 등을 통한 퍼블릭 엔드포인트 접근

```text
Private EC2 + SSM Agent
          ↓ HTTPS 443
      NAT Gateway
          ↓
AWS Systems Manager 공개 엔드포인트
```

### 방식 B: VPC Interface Endpoint를 통한 사설 접근

```text
운영자 → Systems Manager 서비스
                      ▲
                      │ AWS 네트워크
    VPC Interface Endpoints
    - com.amazonaws.<region>.ssm
    - com.amazonaws.<region>.ssmmessages
    - 일부 기존 Region/환경의 ec2messages
                      ▲ HTTPS 443
                      │
             Private EC2 + SSM Agent
        (NAT / Public IP / Bastion 불필요)
```

VPC Endpoint 방식을 사용할 때는 **Private DNS, VPC DNS 설정, Endpoint Security Group의 인바운드 443, EC2에서 Endpoint로 나가는 443 통신** 등이 올바르게 설정돼야 한다. 로그 또는 KMS 기능을 사용하면 S3, Logs, KMS 등 추가 서비스로의 네트워크 경로도 필요할 수 있다.

`ec2messages`의 필요 여부는 Region과 SSM Agent 조건에 따라 달라진다. 최근 Region에서는 `ssmmessages` 사용이 중심이므로 **모든 환경에서 세 엔드포인트가 반드시 필요하다고 외우지 않는다.**

## 🇰🇷 4. IAM을 두 방향으로 구분하기

| 주체 | 왜 IAM 권한이 필요한가? | 예시 |
|---|---|---|
| **운영자 IAM User/Role** | 누가 어떤 서버에 접속할 수 있는지 결정 | `ssm:StartSession` |
| **EC2 Instance Profile** | EC2 내부 Agent가 SSM과 통신 | `AmazonSSMManagedInstanceCore` |

**운영자에게 `ssm:StartSession`만 주고 EC2의 SSM 권한·통신 경로를 준비하지 않으면 접속에 실패**할 수 있다. 반대로 EC2가 관리형 노드로 등록되었다고 모든 사용자가 접속 가능한 것도 아니다.

## 🇰🇷 5. SSH, EC2 Instance Connect, Session Manager 비교

| 항목 | 일반 SSH | EC2 Instance Connect | Session Manager |
|---|---|---|---|
| 원격 접속 방식 | SSH | SSH + 임시 키 전달 | SSM Agent 기반 관리형 세션 |
| 대상의 SSH 22번 포트 | 필요 | **필요** | **불필요** |
| Bastion Host | Private EC2 접근 시 구성 가능 | 구성 방식에 따라 대안 존재 | 불필요 |
| 인스턴스 측 조건 | SSH 서버·인증 | SSH 서버·지원 구성 | SSM Agent·IAM·443 경로 |
| 흔한 시험 표현 | SSH 관리 | 임시 SSH 공개키 | No inbound ports / No SSH keys |

**EC2 Instance Connect는 임시 키를 쓰더라도 SSH 기반**이라는 점이 함정이다. Instance Connect Endpoint를 사용하는 변형도 있지만 Session Manager와 동일한 동작 원리는 아니다.

## 🇰🇷 6. 감사 로그와 제한 사항

- **CloudTrail:** `StartSession` 등 **API 호출 이력**을 확인하는 데 사용.
- **S3 / CloudWatch Logs:** 지원되는 세션의 **명령 및 출력 기록**을 보관하도록 별도 설정 가능.
- **주의:** Session Manager를 활성화했다고 모든 명령이 자동으로 저장되는 것은 아니다. **포트 포워딩·SSH 세션 등은 세션 내용 로깅이 제한**된다.

### 문제 해결 순서

1. 해당 EC2가 Systems Manager에서 **Managed Node/온라인** 상태인지 확인.
2. SSM Agent 실행 상태와 버전 확인.
3. EC2에 SSM 관리 권한이 있는지 확인.
4. `ssm`·`ssmmessages` 등 필요한 엔드포인트로 HTTPS 443이 통하는지 확인.
5. 사용자 IAM의 Session Manager 접근 정책 확인.

## 🎯 Exam Notes

> [!IMPORTANT]
> **Private EC2에 SSH 키·Bastion·인바운드 22 없이 보안 접속 → Session Manager.**

- Agent는 **EC2 내부**에 설치·실행된다.
- 사용자 권한과 EC2 권한은 **별도**다.
- NAT 대신 **Interface VPC Endpoint**로 인터넷 없는 접근이 가능하다.
- Session Manager와 Run Command의 차이: **대화형 셸 접속 vs 원격 명령 실행**.
- CloudTrail의 세션 API 기록과 셸 내부 콘텐츠 로그는 구분한다.

## 📝 Review Questions

> [!TIP]
> 답을 먼저 떠올리고 펼쳐 확인한다.

### Q1. Session Manager로 EC2에 접속할 때 보안 그룹에 TCP 22를 열어야 하는가?

<details>
<summary>정답 및 해설 보기</summary>

아니다. Session Manager는 EC2의 SSM Agent가 Systems Manager 엔드포인트와 통신하며 인바운드 SSH 22번 포트를 요구하지 않는다.

</details>

### Q2. 운영자와 EC2에 각각 필요한 대표 IAM 권한은?

<details>
<summary>정답 및 해설 보기</summary>

운영자는 `ssm:StartSession` 등 대상 접근 권한, EC2는 일반적으로 `AmazonSSMManagedInstanceCore` 정책이 포함된 Instance Profile 등이 필요하다.

</details>

### Q3. NAT가 없는 Private Subnet EC2에 접속하려면 어떤 네트워크 구성이 필요한가?

<details>
<summary>정답 및 해설 보기</summary>

필요한 Systems Manager용 Interface VPC Endpoint(대표적으로 `ssm`, `ssmmessages`) 및 DNS·443 통신을 준비한다. 추가 서비스에 따라 Endpoint가 더 필요할 수 있다.

</details>

### Q4. EC2 Instance Connect는 포트 22 없이 작동하는가?

<details>
<summary>정답 및 해설 보기</summary>

일반적으로 아니다. EC2 Instance Connect는 임시 키를 이용하는 SSH 기반 접근이고 SSH 22번 통신이 필요하다. Session Manager는 별개 방식이다.

</details>

### Q5. CloudTrail에 `StartSession`이 보이면 사용자가 입력한 모든 명령도 저장된 것인가?

<details>
<summary>정답 및 해설 보기</summary>

아니다. CloudTrail의 API 감사 이벤트와 세션 명령·출력 로깅은 별개이며, 세션 콘텐츠 로깅은 별도 설정과 세션 유형별 제한을 확인해야 한다.

</details>

## 🇯🇵 日本語 Summary

**Session Manager** は SSM Agent を使い、SSH キーやインバウンドの **22 番ポートなし**で EC2 にシェル接続する機能である。利用者には `ssm:StartSession`、EC2 側には SSM 用 IAM 権限が必要。Private Subnet でも NAT または **Interface VPC Endpoint** 経由で接続できる。CloudTrail の API 監査ログと、セッション内のコマンドログは区別する。

## 🇺🇸 English Summary

Session Manager enables secure shell access without inbound SSH port 22 or bastion hosts. The managed node runs SSM Agent, the caller needs session permissions, and the instance needs SSM permissions plus outbound HTTPS connectivity. Private VPC endpoints can replace internet/NAT access. API audit events and session-content logging are separate.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| SSM Agent | 관리형 노드 에이전트 | SSM エージェント |
| Session Manager | 관리형 세션 접속 | セッションマネージャー |
| Instance Profile | EC2에 부착하는 IAM 역할 연결 | インスタンスプロファイル |
| Interface VPC Endpoint | 사설 VPC 서비스 연결 | インターフェ이스 VPC エンドポイント |
| Inbound / Outbound | 인바운드 / 아웃바운드 | インバウンド / アウトバウンド |
| Bastion Host | 중계 접속 서버 | 踏み台サーバー |
| Session Logging | 세션 내용 기록 | セッションログ |

## 📖 References

- [Session Manager overview](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)
- [Instance permissions for Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-instance-permissions.html)
- [Session Manager and AWS PrivateLink](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-getting-started-privatelink.html)
