# 📘 06-04. Gateway Load Balancer (GWLB)

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Gateway Load Balancer` · `Virtual Appliance` · `Firewall` · `IDS` · `IPS`

## 🇰🇷 1. Overview

**Gateway Load Balancer (GWLB)**는
Third-Party Network Virtual Appliance를
배포하고 확장하기 위한 Load Balancer이다.

```text
GWLB
→ Layer 3
→ Network Layer
→ IP Packets
```

대표적인 Virtual Appliance:

```text
Firewall
IDS / IPS
Deep Packet Inspection
```

## 🇰🇷 2. How GWLB Works

GWLB는 Application에 도달하기 전
Network Traffic을 보안 Appliance로 전달한다.

```text
User
↓
GWLB
↓
Security Virtual Appliance
↓
GWLB
↓
Application
```

Security Appliance가 Traffic을 검사한다.

```text
Safe Traffic
→ Application으로 전달

Malicious Traffic
→ Drop
```

## 🇰🇷 3. Two Functions

GWLB는 두 가지 역할을 결합한다.

### Transparent Network Gateway

Network Traffic이
GWLB를 통과하도록 구성한다.

```text
Traffic
↓
GWLB
↓
Security Appliance
```

### Load Balancer

여러 Virtual Appliance에
Traffic을 분산한다.

```text
GWLB
├─ Firewall A
├─ Firewall B
└─ Firewall C
```

즉:

```text
Gateway
+
Load Balancer
=
GWLB
```

## 🇰🇷 4. Target Groups

GWLB의 Target은
주로 Third-Party Network Virtual Appliance이다.

Target Group에 등록 가능한 대상:

```text
EC2 Instances
Private IP Addresses
```

예:

```text
GWLB
↓
Target Group
├─ Firewall A
├─ Firewall B
└─ Firewall C
```

Private IP를 이용하여
연결 가능한 외부 Network의 Appliance도 등록할 수 있다.

## 🇰🇷 5. GENEVE

GWLB는 다음 Protocol과 Port를 사용한다.

```text
GENEVE
Port 6081
```

시험에서:

```text
GENEVE
Port 6081
→ GWLB
```

## 🇰🇷 6. Load Balancer Comparison

```text
ALB
→ Layer 7
→ HTTP / HTTPS
→ HTTP Routing

NLB
→ Layer 4
→ TCP / UDP
→ High Performance / Static IP

GWLB
→ Layer 3
→ IP Packets
→ Security Appliances
```

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

```text
GWLB
→ Layer 3
→ IP Packets
```

주요 사용 사례:

```text
Firewall
IDS / IPS
Deep Packet Inspection
Third-Party Security Appliance
```

두 가지 역할:

```text
Transparent Network Gateway
+
Load Balancer
```

Target:

```text
EC2
Private IP
```

시험 핵심:

```text
GENEVE
Port 6081
→ GWLB
```

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. GWLB는 OSI 몇 계층에서 동작하는가?

<details>
<summary>정답 및 해설 보기</summary>

```text
Layer 3
Network Layer
```

</details>

### Q2. GWLB의 주요 사용 사례는?

<details>
<summary>정답 및 해설 보기</summary>

```text
Firewall
IDS / IPS
Deep Packet Inspection
```

</details>

### Q3. GWLB가 결합한 두 가지 기능은?

<details>
<summary>정답 및 해설 보기</summary>

```text
Transparent Network Gateway
+
Load Balancer
```

</details>

### Q4. GWLB의 Target은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

주로 Network Virtual Appliance이며,
EC2 Instance 또는 Private IP로 등록할 수 있다.

</details>

### Q5. GWLB가 사용하는 Protocol과 Port는?

<details>
<summary>정답 및 해설 보기</summary>

```text
GENEVE
Port 6081
```

</details>

## 🇯🇵 日本語 Summary

**Gateway Load Balancer (GWLB)**は、
Layer 3で動作するLoad Balancerです。

Firewall、IDS/IPSなどの
Network Virtual ApplianceにTrafficを分散します。

```text
Gateway
+
Load Balancer
=
GWLB
```

GWLBは**GENEVE Protocol / Port 6081**を使用します。

## 🇺🇸 English Summary

**Gateway Load Balancer (GWLB)** operates at Layer 3
and distributes network traffic across
third-party virtual appliances.

Typical use cases include:

```text
Firewall
IDS / IPS
Deep Packet Inspection
```

GWLB combines:

```text
Transparent Network Gateway
+
Load Balancer
```

It uses the **GENEVE protocol on port 6081**.

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Gateway Load Balancer | ゲートウェイロードバランサー | 보안 Appliance용 Layer 3 Load Balancer |
| Virtual Appliance | 仮想アプライアンス | 소프트웨어 형태의 가상 네트워크 장비 |
| Firewall | ファイアウォール | 네트워크 트래픽을 허용/차단하는 보안 시스템 |
| IDS | 侵入検知システム | 침입 탐지 시스템 |
| IPS | 侵入防止システム | 침입 방지 시스템 |
| Deep Packet Inspection | ディープパケットインスペクション | 패킷 내용을 상세 분석하는 기술 |
| Transparent Network Gateway | 透過型ネットワークゲートウェイ | 트래픽이 통과하는 투명한 네트워크 관문 |
| GENEVE | GENEVE | GWLB가 사용하는 네트워크 캡슐화 프로토콜 |
| Target Group | ターゲットグループ | Traffic을 전달할 Target들의 그룹 |
