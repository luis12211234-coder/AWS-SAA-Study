# EC2 Security Groups

## Overview

Security Group은 AWS에서 EC2 Instance의 네트워크 트래픽을 제어하는 기본적인 방화벽이다.

EC2 Instance로 들어오는 Inbound Traffic과 EC2 Instance에서 외부로 나가는 Outbound Traffic을 규칙을 통해 제어한다.

Security Group에는 Allow Rule만 정의한다.

---

## Security Group Architecture

Security Group은 EC2 Instance 외부에서 동작하는 방화벽이다.

```text
Internet
   ↓
Security Group
   ↓
EC2 Instance
```

Security Group에서 Traffic이 차단되면 EC2 Instance는 해당 Traffic을 받지 못한다.

---

## Inbound and Outbound Traffic

### Inbound Traffic

외부에서 EC2 Instance로 들어오는 Traffic이다.

```text
Client
↓
EC2 Instance
```

예시

- SSH 접속
- HTTP 요청
- HTTPS 요청

### Outbound Traffic

EC2 Instance에서 외부로 나가는 Traffic이다.

```text
EC2 Instance
↓
Internet
```

예를 들어 EC2 Instance가 인터넷에서 Software Package를 다운로드하는 경우 Outbound Traffic이 발생한다.

---

## Security Group Rules

Security Group Rule에서는 주로 다음 요소를 설정한다.

```text
Type
Protocol
Port
Source
```

예시

```text
Type: SSH
Protocol: TCP
Port: 22
Source: My IP
```

이는 지정된 IP Address에서 Port 22를 통한 SSH 접근을 허용한다는 의미이다.

---

## IP Address Rules

Security Group에서는 IPv4 또는 IPv6 주소 범위를 이용하여 Traffic을 허용할 수 있다.

예시

```text
0.0.0.0/0
```

`0.0.0.0/0`은 모든 IPv4 Address를 의미한다.

예를 들어:

```text
HTTP
Port 80
Source 0.0.0.0/0
```

으로 설정하면 인터넷의 모든 IPv4 Address에서 Web Server에 접근할 수 있다.

---

## Default Traffic Behavior

강의에서 설명하는 기본적인 Security Group 동작은 다음과 같다.

```text
Inbound Traffic
→ Blocked by default

Outbound Traffic
→ Allowed by default
```

필요한 Inbound Traffic만 Rule을 추가하여 허용한다.

---

## Security Groups and EC2 Instances

Security Group과 EC2 Instance는 1:1 관계가 아니다.

하나의 Security Group을 여러 EC2 Instance에 연결할 수 있다.

```text
Security Group
├─ EC2 Instance A
├─ EC2 Instance B
└─ EC2 Instance C
```

또한 하나의 EC2 Instance에 여러 Security Group을 연결할 수도 있다.

---

## Region and VPC

Security Group은 특정 Region과 VPC에 연결된다.

따라서 다른 Region 또는 다른 VPC에서는 별도의 Security Group이 필요하다.

```text
Security Group
↓
Region + VPC
```

---

## Security Groups Live Outside EC2

Security Group은 EC2 Instance 내부에서 실행되는 Application이 아니다.

EC2 Instance 외부에서 Traffic을 필터링한다.

```text
Client
↓
Security Group
↓
EC2
↓
Application
```

따라서 Security Group에서 차단된 Traffic은 EC2 Instance까지 도달하지 않는다.

---

## SSH Security Group

강의에서는 SSH 접근을 위한 별도의 Security Group을 관리하는 방식을 소개한다.

```text
SSH Security Group
↓
Port 22
↓
Allowed IP
```

SSH는 관리 목적의 원격 접속에 사용되기 때문에 접근 범위를 적절히 제한하는 것이 중요하다.

---

## Timeout vs Connection Refused

네트워크 문제를 진단할 때 두 가지 상황을 구분할 수 있다.

### Timeout

```text
Connection
↓
Waiting...
↓
Timeout
```

강의에서는 Security Group Rule 문제를 의심할 수 있다고 설명한다.

Traffic이 Security Group에서 차단되면 EC2 Instance에 도달하지 못하기 때문이다.

### Connection Refused

```text
Traffic
↓
Security Group
↓
EC2 Instance
↓
Connection Refused
```

Security Group을 통과했지만 Application이 실행되지 않았거나 해당 Port를 사용하고 있지 않을 가능성이 있다.

---

## Referencing Security Groups

Security Group Rule에서는 IP Address뿐만 아니라 다른 Security Group을 Source로 참조할 수 있다.

예시

```text
EC2 Instance A
Security Group A

Inbound Rule:
Allow Security Group B
```

Security Group B가 연결된 EC2 Instance는 Security Group A가 연결된 Instance에 접근할 수 있다.

```text
EC2 B
[Security Group B]
        ↓
     Allowed
        ↓
[Security Group A]
EC2 A
```

반면 허용되지 않은 Security Group이 연결된 Instance는 접근할 수 없다.

```text
EC2 C
[Security Group C]
        ↓
     Denied
```

이 방법을 사용하면 Instance의 IP Address를 개별적으로 관리하지 않고 Security Group을 기준으로 접근을 제어할 수 있다.

---

## Classic Ports

AWS 시험과 EC2 사용에서 자주 등장하는 Port는 다음과 같다.

| Port | Protocol / Service | Purpose |
|---:|---|---|
| 21 | FTP | File Transfer |
| 22 | SSH | Linux Remote Login |
| 22 | SFTP | Secure File Transfer using SSH |
| 80 | HTTP | Unsecured Website |
| 443 | HTTPS | Secured Website |
| 3389 | RDP | Windows Remote Desktop |

---

## SSH vs RDP

Linux Instance에 원격 접속할 때는 SSH를 사용한다.

```text
Linux
→ SSH
→ Port 22
```

Windows Instance에 원격 접속할 때는 RDP를 사용한다.

```text
Windows
→ RDP
→ Port 3389
```

---

## Summary

- Security Group은 EC2 Instance의 방화벽이다.
- Inbound와 Outbound Traffic을 제어한다.
- Security Group에는 Allow Rule을 정의한다.
- Rule은 IP Address 또는 다른 Security Group을 기준으로 설정할 수 있다.
- 기본적으로 Inbound Traffic은 차단되고 Outbound Traffic은 허용된다.
- Security Group은 EC2 Instance 외부에서 동작한다.
- 하나의 Security Group을 여러 Instance에 연결할 수 있다.
- 하나의 Instance에도 여러 Security Group을 연결할 수 있다.
- Security Group은 특정 Region과 VPC에 연결된다.
- Security Group끼리 서로 참조하여 접근을 허용할 수 있다.

---

## Exam Notes

- Security Group = EC2 Firewall
- Inbound = Outside → EC2
- Outbound = EC2 → Outside
- Security Group Rule은 Allow 기반
- `0.0.0.0/0` = 모든 IPv4 Address
- Default Inbound = Blocked
- Default Outbound = Allowed
- Security Group은 EC2 외부에서 동작
- Security Group은 여러 EC2에 연결 가능
- EC2 하나에 여러 Security Group 연결 가능
- Security Group은 Region / VPC에 종속
- Security Group을 다른 Security Group의 Source로 참조 가능

### Ports

```text
FTP   → 21
SSH   → 22
SFTP  → 22
HTTP  → 80
HTTPS → 443
RDP   → 3389
```

---

## Practical Example

Web Server EC2 Instance를 운영한다고 가정한다.

```text
Internet
   │
   ├─ HTTP : 80
   │
   ▼
Security Group
   │
   ▼
EC2 Web Server
```

Web Traffic은 모든 사용자가 접근해야 하므로 다음과 같이 설정할 수 있다.

```text
HTTP
Port 80
Source 0.0.0.0/0
```

SSH는 관리자만 사용할 경우 특정 IP Address만 허용할 수 있다.

```text
SSH
Port 22
Source My IP
```

따라서 Web Traffic은 공개하면서 관리용 SSH 접근은 제한할 수 있다.

---

# 🇯🇵 日本語

## EC2 Security Groups

Security GroupはEC2 InstanceのFirewallとして動作する。

EC2 InstanceへのInbound Trafficと、Instanceから外部へのOutbound Trafficを制御する。

---

## Inbound / Outbound

```text
Inbound
外部 → EC2

Outbound
EC2 → 外部
```

デフォルトではInbound Trafficはブロックされ、Outbound Trafficは許可される。

---

## Security Group Rules

Security Groupでは主に以下を設定する。

- Protocol
- Port
- Source

SourceにはIP Addressだけでなく、別のSecurity Groupを指定することもできる。

---

## Security Group Reference

別のSecurity GroupをSourceとして指定すると、そのSecurity Groupが接続されているInstanceからの通信を許可できる。

これにより、各InstanceのIP Addressを個別に管理する必要がなくなる。

---

## Classic Ports

```text
FTP   → 21
SSH   → 22
SFTP  → 22
HTTP  → 80
HTTPS → 443
RDP   → 3389
```

---

## Summary

- Security GroupはEC2のFirewall。
- Inbound / Outbound Trafficを制御する。
- IP AddressまたはSecurity GroupをRuleで指定できる。
- LinuxへのRemote LoginはSSH / Port 22。
- WindowsへのRemote LoginはRDP / Port 3389。

---

# 🇺🇸 English

## EC2 Security Groups

A Security Group acts as a firewall for an EC2 instance.

It controls inbound traffic entering the instance and outbound traffic leaving the instance.

---

## Inbound and Outbound

```text
Inbound
Outside → EC2

Outbound
EC2 → Outside
```

Inbound traffic is blocked by default, while outbound traffic is allowed by default.

---

## Security Group Rules

Security Group rules can define:

- Protocol
- Port
- Source

A source can be an IP address or another Security Group.

---

## Security Group References

A Security Group can reference another Security Group as a source.

Instances associated with the referenced Security Group can then communicate according to the configured rule.

This avoids managing individual IP addresses.

---

## Classic Ports

```text
FTP   → 21
SSH   → 22
SFTP  → 22
HTTP  → 80
HTTPS → 443
RDP   → 3389
```

---

## Summary

- Security Groups are EC2 firewalls.
- They control inbound and outbound traffic.
- Rules can reference IP addresses or other Security Groups.
- SSH uses port 22.
- HTTP uses port 80.
- HTTPS uses port 443.
- RDP uses port 3389.

---

## Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Security Group | 보안 그룹 | セキュリティグループ |
| Firewall | 방화벽 | ファイアウォール |
| Inbound Traffic | 인바운드 트래픽 | インバウンドトラフィック |
| Outbound Traffic | 아웃바운드 트래픽 | アウトバウンドトラフィック |
| Source | 출발지 | ソース |
| Port | 포트 | ポート |
| Protocol | 프로토콜 | プロトコル |
| IPv4 | IPv4 주소 | IPv4 |
| IPv6 | IPv6 주소 | IPv6 |
| SSH | 보안 원격 접속 | SSH |
| HTTP | HTTP | HTTP |
| HTTPS | HTTPS | HTTPS |
| FTP | 파일 전송 프로토콜 | FTP |
| SFTP | 보안 파일 전송 | SFTP |
| RDP | 원격 데스크톱 프로토콜 | RDP |
| Timeout | 시간 초과 | タイムアウト |
| Connection Refused | 연결 거부 | 接続拒否 |

---

## Review Questions

1. Security Group의 역할은 무엇인가?

2. Inbound Traffic과 Outbound Traffic의 차이는 무엇인가?

3. Security Group Rule의 Source에는 무엇을 지정할 수 있는가?

4. `0.0.0.0/0`은 무엇을 의미하는가?

5. 기본적으로 Inbound와 Outbound Traffic은 각각 어떻게 처리되는가?

6. Security Group은 EC2 내부와 외부 중 어디에서 동작하는가?

7. 하나의 Security Group을 여러 EC2 Instance에 연결할 수 있는가?

8. Security Group이 다른 Security Group을 Source로 참조한다는 것은 무슨 의미인가?

9. Timeout이 발생하면 강의에서는 어떤 문제를 우선 의심하는가?

10. Connection Refused가 발생하면 어떤 문제를 의심할 수 있는가?

11. SSH, HTTP, HTTPS, RDP의 Port 번호는 각각 무엇인가?

12. Linux EC2와 Windows EC2에 원격 접속할 때 사용하는 Protocol은 각각 무엇인가?
