# EC2 Public IP, Private IP and Elastic IP

## Overview

EC2 Instance에서는 네트워크 통신을 위해 Public IP와 Private IP를 사용할 수 있다.

Private IP는 AWS 내부의 Private Network에서 통신하기 위해 사용하며,
Public IP는 인터넷을 통해 Instance에 접근하기 위해 사용한다.

또한 일반 Public IPv4 Address는 EC2 Instance를 Stop 후 Start하면 변경될 수 있기 때문에,
고정된 Public IPv4 Address가 필요한 경우 Elastic IP를 사용할 수 있다.

---

## IPv4 and IPv6

IP Address에는 대표적으로 IPv4와 IPv6가 있다.

### IPv4

IPv4는 현재 널리 사용되는 IP Address 형식이다.

```text
192.168.1.10
54.180.10.20
```

각 숫자는 0~255 범위를 사용한다.

```text
[0-255].[0-255].[0-255].[0-255]
```

IPv4 Address의 수가 제한되어 있기 때문에 Public IPv4 Address는 제한된 자원이다.

### IPv6

IPv6는 IPv4의 Address 부족 문제를 해결하기 위해 훨씬 큰 Address Space를 제공한다.

AWS는 IPv4와 IPv6를 모두 지원한다.

---

## Public IP

Public IP는 인터넷에서 Resource를 식별할 수 있는 IP Address이다.

```text
Internet
   │
   │ Public IP
   ▼
EC2 Instance
```

Public IP의 특징:

- 인터넷에서 식별 가능
- 인터넷 전체에서 Unique해야 함
- 외부에서 EC2 Instance에 접근할 때 사용할 수 있음

예를 들어 Local PC에서 인터넷을 통해 EC2에 SSH 접속할 경우 Public IP를 사용할 수 있다.

```text
My PC
↓
Internet
↓
Public IPv4
↓
EC2
```

---

## Private IP

Private IP는 Private Network 내부에서 Resource를 식별하기 위해 사용한다.

```text
VPC

EC2-A
10.0.0.10
   │
   │ Private Network
   ▼
EC2-B
10.0.0.20
```

Private IP의 특징:

- Private Network 내부에서 사용
- 해당 Private Network 안에서 Unique해야 함
- 서로 다른 Private Network에서는 동일한 Private IP를 사용할 수 있음
- 인터넷에서 직접 접근하기 위한 주소가 아님

예:

```text
Company A
192.168.1.10

Company B
192.168.1.10

→ 서로 다른 Private Network이므로 사용 가능
```

---

## Public IP vs Private IP

| 특징 | Public IP | Private IP |
|---|---|---|
| 사용 범위 | Internet | Private Network |
| Internet에서 직접 식별 | 가능 | 불가능 |
| Unique 범위 | 전 세계 | 해당 Private Network |
| 서로 다른 Network에서 중복 사용 | 불가능 | 가능 |
| EC2 외부 접근 | 사용 가능 | 같은 Network 또는 연결된 Network 필요 |

핵심:

```text
Public IP
→ Internet

Private IP
→ Internal Network
```

---

## EC2 and IP Addresses

강의의 EC2 환경에서는 Instance에 다음 Address가 존재한다.

```text
EC2 Instance

Private IPv4
→ AWS Internal Network

Public IPv4
→ Internet Access
```

Local PC가 AWS의 Private Network에 직접 연결되어 있지 않은 상태라면
EC2의 Private IP를 사용하여 직접 SSH 접속할 수 없다.

```text
My PC
    │
    │ Internet
    ▼
Public IP
    │
    ▼
EC2
```

Private IP로 직접 접근하려면 동일한 Network 또는 VPN 등 Private Network에 접근할 수 있는 연결이 필요하다.

---

## Public IP after Stop and Start

일반적인 EC2 Public IPv4 Address는 Instance를 Stop한 후 다시 Start하면 변경될 수 있다.

```text
EC2 Running

Public IP
54.x.x.x

↓

Stop

↓

Start

↓

New Public IP
3.x.x.x
```

따라서 기존 Public IP에 의존하는 Application에서는 문제가 발생할 수 있다.

반면 EC2 Instance의 Primary Private IPv4 Address는 일반적인 Stop / Start 과정에서 유지된다.

```text
Stop → Start

Public IPv4
→ 변경될 수 있음

Primary Private IPv4
→ 유지
```

주의:

```text
Reboot
≠
Stop + Start
```

강의에서 Public IP 변경을 확인하기 위해서는 Instance를 Stop한 후 다시 Start한다.

---

# Elastic IP

## What is an Elastic IP?

Elastic IP(EIP)는 AWS에서 사용할 수 있는 **고정된 Public IPv4 Address**이다.

일반 Public IPv4와 달리 EC2 Instance를 Stop하고 다시 Start하더라도
Elastic IP를 계속 연결해 두면 동일한 Public IPv4 Address를 사용할 수 있다.

```text
Normal Public IPv4

Stop
↓
Start
↓
IP 변경 가능
```

반면:

```text
Elastic IP

Stop
↓
Start
↓
Same Public IPv4
```

---

## Why Use an Elastic IP?

고정된 Public IPv4 Address가 반드시 필요한 경우 Elastic IP를 사용할 수 있다.

예:

```text
Application
↓
Fixed Public IPv4 Required
↓
Elastic IP
```

Elastic IP는 다른 Instance로 다시 연결할 수도 있다.

예를 들어 Instance A에 문제가 발생한 경우:

```text
Elastic IP
     │
     ▼
Instance A
   ERROR

     ↓ Remap

Elastic IP
     │
     ▼
Instance B
```

같은 Public IP를 다른 Instance로 이동시켜 장애를 숨기는 방식으로 사용할 수 있다.

---

## Elastic IP Characteristics

Elastic IP의 핵심 특징:

- Static Public IPv4 Address
- 계정에 할당하여 사용
- EC2 Instance에 연결 가능
- Stop / Start 후에도 동일한 IP 유지 가능
- 다른 Instance로 Remap 가능
- Public IPv4이므로 비용에 주의해야 함

---

## Elastic IP Lifecycle

Elastic IP 사용 흐름:

```text
Allocate Elastic IP
↓
Elastic IP 생성
↓
Associate
↓
EC2 Instance에 연결
↓
고정 Public IPv4로 사용
```

더 이상 필요하지 않다면:

```text
Disassociate
↓
EC2에서 연결 해제
↓
Release
↓
Elastic IP 반환
```

### Disassociate vs Release

```text
Disassociate
→ Instance와 EIP의 연결만 해제

Release
→ Elastic IP 자체를 AWS에 반환
```

사용하지 않는 Elastic IP를 불필요하게 유지하지 않는 것이 중요하다.

---

## Elastic IP Architecture Consideration

강의에서는 가능하면 Elastic IP에 의존하는 Architecture를 피하는 것을 권장한다.

고정 IP를 직접 관리하는 대신 다음과 같은 방법을 사용할 수 있다.

```text
DNS
→ Route 53

또는

Load Balancer
→ 여러 EC2 Instance 앞에서 Traffic 처리
```

즉:

```text
Fixed Public IP 직접 의존

        ↓ 가능하면

DNS / Load Balancer 기반 Architecture
```

Elastic IP가 나쁜 서비스라는 뜻이 아니라,
Application Architecture를 하나의 고정 IP에 지나치게 의존하도록 만드는 것을 피하는 것이 좋다는 의미이다.

---

## Public IPv4 Cost

AWS에서는 Public IPv4 Address가 유료 Resource가 될 수 있다.

따라서 실습 후에는 사용하지 않는 EC2 Instance 및 Elastic IP 등의 Public IPv4 Resource를 확인하고 정리하는 것이 중요하다.

```text
Lab Finished
↓
Check EC2
↓
Check Elastic IP
↓
Terminate / Release unused resources
```

> Public IPv4의 정확한 가격 및 Free Tier 조건은 시점과 계정 조건에 따라 변경될 수 있으므로 AWS의 현재 Pricing 정보를 확인한다.

---

## Summary

- IPv4와 IPv6는 IP Address 형식이다.
- Public IP는 Internet에서 Resource를 식별하기 위해 사용한다.
- Public IP는 인터넷 전체에서 Unique해야 한다.
- Private IP는 Private Network 내부에서 사용한다.
- 서로 다른 Private Network에서는 동일한 Private IP를 사용할 수 있다.
- Local PC에서 일반적으로 EC2에 SSH할 때 Public IP를 사용할 수 있다.
- 일반 Public IPv4는 EC2 Stop / Start 후 변경될 수 있다.
- Primary Private IPv4는 일반적인 Stop / Start에서 유지된다.
- Elastic IP는 Static Public IPv4 Address이다.
- Elastic IP는 Stop / Start 이후에도 동일한 Public IP를 유지할 수 있다.
- Elastic IP는 다른 Instance로 Remap할 수 있다.
- Elastic IP가 필요하지 않다면 Disassociate 후 Release하여 정리한다.
- 가능하면 DNS 또는 Load Balancer 기반 Architecture를 고려한다.

---

## Exam Notes

### Public vs Private IP

```text
Public IP
→ Internet에서 식별
→ Globally Unique

Private IP
→ Private Network에서 식별
→ 다른 Private Network와 중복 가능
```

### EC2 Stop / Start

```text
EC2 Stop
↓
Start

Public IPv4
→ 변경 가능

Primary Private IPv4
→ 유지
```

### Elastic IP

```text
Need Fixed Public IPv4?
↓
Elastic IP
```

```text
Elastic IP
= Static Public IPv4
```

```text
Stop → Start
↓
Elastic IP 유지
```

### Elastic IP Failure Remapping

```text
EIP
↓
EC2-A Failure

EIP
↓ Remap

EC2-B
```

### Elastic IP Cleanup

```text
Disassociate
→ Instance에서 연결 해제

Release
→ Elastic IP 자체 반환
```

---

## Practical Example

웹 서버에 고정 Public IPv4 Address가 필요하다고 가정한다.

일반 Public IPv4를 사용하는 경우:

```text
Client
↓
54.1.1.1
↓
EC2

EC2 Stop → Start

Client
↓
54.1.1.1
↓
X

New EC2 Public IP
3.2.2.2
```

Elastic IP를 사용하는 경우:

```text
Client
↓
Elastic IP
54.1.1.1
↓
EC2

EC2 Stop → Start

Client
↓
Elastic IP
54.1.1.1
↓
EC2
```

따라서 고정 Public IPv4가 반드시 필요한 경우 Elastic IP를 사용할 수 있다.

---

# 🇯🇵 日本語

## Public IP and Private IP

Public IPはインターネット上でResourceを識別するためのIP Addressである。

Private IPはPrivate Network内部でResourceを識別するために使用する。

```text
Public IP
→ Internet

Private IP
→ Private Network
```

Public IPはインターネット全体でUniqueである必要がある。

Private IPはPrivate Network内でUniqueであればよいため、
異なるPrivate Networkでは同じPrivate IPを使用できる。

---

## EC2 IP Behavior

EC2 Instanceの通常のPublic IPv4 Addressは、
InstanceをStopして再びStartすると変更される可能性がある。

一方、Primary Private IPv4 Addressは通常維持される。

```text
Stop → Start

Public IPv4
→ Change possible

Private IPv4
→ Remains
```

---

## Elastic IP

Elastic IPは固定されたPublic IPv4 Addressである。

```text
Elastic IP
→ Static Public IPv4
```

EC2 InstanceをStopして再びStartしても、
Elastic IPを使用することで同じPublic IPv4 Addressを維持できる。

また、Elastic IPを別のInstanceにRemapすることもできる。

不要になったElastic IPはReleaseする。

---

## Summary

- Public IPはInternetで使用する。
- Private IPはPrivate Network内部で使用する。
- EC2の通常のPublic IPv4はStop / Startで変更される可能性がある。
- Primary Private IPv4は通常維持される。
- Elastic IPは固定Public IPv4 Addressである。
- Elastic IPは別のInstanceにRemapできる。
- 不要なElastic IPはReleaseする。

---

# 🇺🇸 English

## Public IP and Private IP

A Public IP identifies a resource on the public Internet.

A Private IP identifies a resource within a private network.

```text
Public IP
→ Internet

Private IP
→ Private Network
```

A Public IP must be unique across the Internet.

Private IP addresses only need to be unique within their private network, so different private networks can use the same private IP ranges.

---

## EC2 IP Behavior

A normal EC2 public IPv4 address can change when an instance is stopped and started again.

The primary private IPv4 address normally remains associated with the instance.

```text
Stop → Start

Public IPv4
→ May change

Private IPv4
→ Remains
```

---

## Elastic IP

An Elastic IP is a static public IPv4 address.

```text
Elastic IP
→ Static Public IPv4
```

It can remain associated with an EC2 instance across stop and start operations.

An Elastic IP can also be remapped to another instance when necessary.

Unused Elastic IP addresses should be released.

---

## Summary

- Public IP addresses are used for Internet communication.
- Private IP addresses are used inside private networks.
- A normal EC2 public IPv4 can change after Stop / Start.
- The primary private IPv4 normally remains.
- Elastic IP provides a static public IPv4 address.
- Elastic IP can be remapped to another instance.
- Unused Elastic IP addresses should be released.

---

## Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Public IP | 공용 IP | パブリックIP |
| Private IP | 사설 IP | プライベートIP |
| IPv4 | IPv4 | IPv4 |
| IPv6 | IPv6 | IPv6 |
| Elastic IP | 탄력적 IP | Elastic IP |
| Static IP | 고정 IP | 固定IP |
| Private Network | 사설 네트워크 | プライベートネットワーク |
| Internet Gateway | 인터넷 게이트웨이 | インターネットゲートウェイ |
| Associate | 연결 | 関連付け |
| Disassociate | 연결 해제 | 関連付け解除 |
| Release | 반환 / 해제 | 解放 |
| Remap | 재연결 | 再マッピング |
| DNS | 도메인 이름 시스템 | DNS |
| Load Balancer | 로드 밸런서 | ロードバランサー |

---

## Review Questions

1. Public IP와 Private IP의 가장 큰 차이는 무엇인가?
2. 서로 다른 Private Network에서 같은 Private IP를 사용할 수 있는 이유는 무엇인가?
3. Local PC에서 EC2의 Private IP로 직접 SSH할 수 없는 이유는 무엇인가?
4. EC2 Instance를 Stop 후 Start하면 일반 Public IPv4는 어떻게 될 수 있는가?
5. Primary Private IPv4는 Stop / Start 후 일반적으로 어떻게 되는가?
6. Elastic IP란 무엇인가?
7. 고정 Public IPv4가 필요한 경우 어떤 AWS 기능을 사용할 수 있는가?
8. Elastic IP를 다른 EC2 Instance로 Remap할 수 있는 이유는 무엇인가?
9. Disassociate와 Release의 차이는 무엇인가?
10. AWS Architecture에서 Elastic IP에 지나치게 의존하는 것을 피하는 이유는 무엇인가?