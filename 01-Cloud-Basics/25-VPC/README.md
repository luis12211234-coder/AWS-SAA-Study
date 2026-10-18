# 📚 25. VPC & Networking

> [!NOTE]
> **AWS SAA-C03 Section Guide** · `GitHub Section 25` · `Udemy Section 26: Networking - VPC`

## 📌 Overview

AWS VPC의 IP 주소·서브넷·라우팅부터 IGW/NAT/Bastion, SG/NACL, VPC Peering/Endpoint, Flow Logs, Site-to-Site VPN/Direct Connect/Transit Gateway, IPv6, 비용 최적화, Network Firewall까지 **네트워크 패킷이 왜 그 경로를 선택하고 어디에서 허용·차단되는지** 중심으로 정리한다. 기존 강의의 설명과 현재 AWS 기능 차이도 각 노트에 함께 기록했다.

## 🎯 Learning Objectives

1. CIDR, AZ/Subnet, 예약 IP, 라우트 테이블을 활용해 VPC 네트워크를 설계한다.
2. IGW, NAT Instance/Gateway, Bastion, Egress-only IGW의 목적과 패킷 흐름을 설명한다.
3. SG(Stateful), NACL(Stateless), 임시 포트, Flow Logs, Traffic Mirroring, Network Firewall을 구분한다.
4. VPC Peering, Endpoint, VPN, Direct Connect/VIF, Transit Gateway의 연결 요건과 경로 설정을 선택한다.
5. IPv6 Dual Stack, 교차 AZ/리전 전송 및 NAT/S3 비용을 고려해 SAA-C03 아키텍처를 판단한다.

## 📂 Contents

| File | Topic |
|---|---|
| [25-01-VPC-Overview-and-Default-VPC.md](./25-01-VPC-Overview-and-Default-VPC.md) | VPC Overview & Default VPC |
| [25-02-CIDR-Private-IP-Ranges-and-Longest-Prefix-Match.md](./25-02-CIDR-Private-IP-Ranges-and-Longest-Prefix-Match.md) | CIDR, Private IP Ranges & Longest Prefix Match |
| [25-03-VPC-Tenancy-and-CIDR-Quotas.md](./25-03-VPC-Tenancy-and-CIDR-Quotas.md) | VPC Tenancy, CIDR Quotas & Secondary CIDR |
| [25-04-Subnet-Planning-Reserved-IPs-and-Hands-On.md](./25-04-Subnet-Planning-Reserved-IPs-and-Hands-On.md) | Subnet Planning, Reserved IPs & Console Lab |
| [25-05-Internet-Gateway-and-Public-Private-Routing.md](./25-05-Internet-Gateway-and-Public-Private-Routing.md) | Internet Gateway & Public/Private Routing |
| [25-06-Bastion-Host-and-Private-EC2-SSH.md](./25-06-Bastion-Host-and-Private-EC2-SSH.md) | Bastion Host & SSH into Private EC2 |
| [25-07-NAT-Instance-and-Source-Destination-Check.md](./25-07-NAT-Instance-and-Source-Destination-Check.md) | NAT Instance & Source/Destination Check |
| [25-08-NAT-Gateway-High-Availability-and-Console-Lab.md](./25-08-NAT-Gateway-High-Availability-and-Console-Lab.md) | NAT Gateway: High Availability & Console Lab |
| [25-09-Security-Groups-NACLs-and-Ephemeral-Ports.md](./25-09-Security-Groups-NACLs-and-Ephemeral-Ports.md) | Security Groups, NACLs & Ephemeral Ports |
| [25-10-NACL-and-Security-Group-Console-Lab.md](./25-10-NACL-and-Security-Group-Console-Lab.md) | NACL & Security Group Console Troubleshooting |
| [25-11-VPC-Peering-CIDRs-and-Bidirectional-Routes.md](./25-11-VPC-Peering-CIDRs-and-Bidirectional-Routes.md) | VPC Peering, CIDRs & Bidirectional Routes |
| [25-12-VPC-Endpoints-Gateway-Interface-and-PrivateLink.md](./25-12-VPC-Endpoints-Gateway-Interface-and-PrivateLink.md) | VPC Endpoints: Gateway vs Interface & PrivateLink |
| [25-13-S3-Gateway-Endpoint-Console-Lab.md](./25-13-S3-Gateway-Endpoint-Console-Lab.md) | S3 Gateway Endpoint: NAT Removal & CLI Lab |
| [25-14-VPC-Flow-Logs-Fields-and-Troubleshooting.md](./25-14-VPC-Flow-Logs-Fields-and-Troubleshooting.md) | VPC Flow Logs: Fields & Network Troubleshooting |
| [25-15-VPC-Flow-Logs-S3-CloudWatch-and-Athena-Lab.md](./25-15-VPC-Flow-Logs-S3-CloudWatch-and-Athena-Lab.md) | Flow Logs Lab: S3, CloudWatch & Athena |
| [25-16-Site-to-Site-VPN-CGW-VGW-and-CloudHub.md](./25-16-Site-to-Site-VPN-CGW-VGW-and-CloudHub.md) | Site-to-Site VPN: CGW, VGW & VPN CloudHub |
| [25-17-Site-to-Site-VPN-Console-Configuration.md](./25-17-Site-to-Site-VPN-Console-Configuration.md) | Site-to-Site VPN: Console Configuration Steps |
| [25-18-Direct-Connect-Location-and-Virtual-Interfaces.md](./25-18-Direct-Connect-Location-and-Virtual-Interfaces.md) | Direct Connect: Location, Physical Link & VIFs |
| [25-19-Direct-Connect-Gateway-Types-Resiliency-and-VPN-Backup.md](./25-19-Direct-Connect-Gateway-Types-Resiliency-and-VPN-Backup.md) | DX Gateway, Connection Types, Resiliency & VPN Backup |
| [25-20-Transit-Gateway-Attachments-Routing-RAM-ECMP.md](./25-20-Transit-Gateway-Attachments-Routing-RAM-ECMP.md) | Transit Gateway: Attachments, Routing, RAM & ECMP |
| [25-21-VPC-Traffic-Mirroring-and-Packet-Inspection.md](./25-21-VPC-Traffic-Mirroring-and-Packet-Inspection.md) | VPC Traffic Mirroring & Packet Inspection |
| [25-22-IPv6-Dual-Stack-CIDRs-and-Console-Lab.md](./25-22-IPv6-Dual-Stack-CIDRs-and-Console-Lab.md) | IPv6, Dual Stack & IPv6 Console Lab |
| [25-23-Egress-Only-Internet-Gateway-and-IPv6-Routing.md](./25-23-Egress-Only-Internet-Gateway-and-IPv6-Routing.md) | Egress-only Internet Gateway & IPv6 Routing |
| [25-24-AWS-Network-Data-Transfer-Cost-Optimization.md](./25-24-AWS-Network-Data-Transfer-Cost-Optimization.md) | AWS Network Data Transfer & Cost Optimization |
| [25-25-AWS-Network-Firewall-and-Deep-Packet-Inspection.md](./25-25-AWS-Network-Firewall-and-Deep-Packet-Inspection.md) | AWS Network Firewall & Deep Packet Inspection |
| [25-26-VPC-Final-Comparison-and-SAA-Exam-Scenarios.md](./25-26-VPC-Final-Comparison-and-SAA-Exam-Scenarios.md) | VPC Final Review & SAA-C03 Scenarios |

## 🧠 Key Concept

**주소 범위(CIDR/Subnet) → 경로(Route Table/IGW/NAT) → 접근 제어(SG/NACL) → 서비스·네트워크 연결(Endpoint/Peering/VPN/DX/TGW) → 관측·방어(Flow Logs/Mirroring/Network Firewall) → 비용 및 IPv6 최적화** 순서로 학습한다.

## 🛠️ Study Notes

- **학습 방식:** 강의의 콘솔 시연은 네트워크 패킷 흐름과 필요한 설정 조건으로 재구성했다.
- **함정 주의:** Peering은 비전이적이며, TGW는 Attachment/Association/Propagation으로 전이적 연결을 제어한다.
- **최신 AWS 변경:** IPv6-only Subnet, Regional NAT Gateway, DX 용량 등 구버전 강의와 달라진 기능은 본문에서 명시했다.
- **요금:** GB당 금액은 리전·시점별로 바뀌므로 고정 수치보다 상대 비용과 경로 최적화 원칙을 우선한다.
- **복습 구성:** 각 노트는 SAA-C03 Exam Notes, 정답·해설 접기형 질문 5개, 일본어/영어 요약과 Vocabulary를 포함한다.

## 🇯🇵 日本語 Summary

VPC の CIDR・Subnet・Route Table を土台として IGW、NAT、Bastion、SG/NACL、Peering、Endpoint、Flow Logs を学ぶ。オンプレミス接続は Site-to-Site VPN と Direct Connect、複数ネットワークの中継は Transit Gateway を使う。IPv6、Traffic Mirroring、Network Firewall、データ転送料金まで設計判断を整理する。

## 🇺🇸 English Summary

This section covers AWS VPC addressing, subnet and route design, internet connectivity, stateful versus stateless security, VPC peering and endpoints, flow monitoring, hybrid networking with VPN and Direct Connect, centralized Transit Gateway routing, IPv6, network security, and cost-aware architecture choices.
