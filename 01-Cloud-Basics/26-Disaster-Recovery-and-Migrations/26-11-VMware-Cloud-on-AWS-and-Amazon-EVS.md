# 📘 26-11. VMware Cloud on AWS & Amazon Elastic VMware Service

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `VMware` · `vSphere` · `vSAN` · `NSX` · `VMware Cloud on AWS` · `Amazon EVS` · `MGN` · `Hybrid Cloud`

## 🇰🇷 1. VMware는 EC2와 어떤 관계인가?
VMware는 물리 서버 위에 **가상 머신(VM)**을 만들어 실행하고 관리하는 가상화 플랫폼이다. VMware 기반 회사는 가상 머신과 운영 도구를 이미 사용하므로, 단순히 일반 EC2로 옮기면 운영 관리 방식이 달라질 수 있다.

| VMware 기술 | 담당하는 영역 |
|---|---|
| **vSphere / ESXi** | 서버 가상화와 가상 머신 관리 |
| **vSAN** | 가상화된 스토리지 통합·관리 |
| **NSX** | 소프트웨어 정의 네트워크·네트워크 보안 |

## 🇰🇷 2. VMware Cloud on AWS의 핵심
AWS의 물리적 인프라 위에서 **VMware SDDC(Software-Defined Data Center)**를 실행하여 **기존 vSphere 운영 방식과 VM 워크로드를 AWS에서도 유지·확장**하는 관리형 서비스다.

```text
온프레미스 데이터센터               AWS 물리 인프라
VMware vSphere                      VMware Cloud on AWS
 ├─ VM(웹 서버)                        ├─ VMware VM(앱 서버)
 ├─ VM(DB 서버)       연계/마이그레이션 ├─ VMware VM(기타)
 └─ vSAN / NSX ─────────────────────▶ └─ vSAN / NSX
```

중요: 오른쪽의 VM이 자동으로 일반 **EC2 인스턴스로 변환되는 것이 아니다**. AWS 인프라 위에서도 VMware 스택 안의 VM으로 운영한다. 이를 통해 AWS 서비스(Direct Connect, S3, FSx, RDS, EC2, Redshift 등)와 연동할 수 있지만 VMware VM 자체에 RDS의 관리형 DB 기능이 자동 생기지는 않는다.

## 🇰🇷 3. 사용 사례 네 가지
1. **Capacity Expansion**: 회사의 컴퓨팅/스토리지 부족을 AWS 기반 VMware 확장으로 대응.
2. **VMware Migration**: 기존 VMware 워크로드를 플랫폼을 크게 바꾸지 않고 AWS에서 실행.
3. **Hybrid/Multi-Data-Center**: 온프레미스와 AWS에 VMware 환경을 두고 함께 운영.
4. **Disaster Recovery**: 원본 사이트 장애 시 AWS 기반 VMware 복구 사이트를 활용. 별도의 복제·전환 계획이 필요하다.

## 🇰🇷 4. MGN과의 구분
| 비교 | VMware Cloud on AWS | AWS Transform MGN |
|---|---|---|
| 이전 후 실행 형태 | VMware SDDC 내부의 VMware VM | **일반 EC2** |
| 주요 장점 | 기존 VMware 툴과 운영 모델 유지 | OS/앱/디스크를 EC2로 Rehosting |
| 적합한 조건 | 'vSphere를 계속 쓰겠다' | 'EC2로 Lift-and-Shift 하겠다' |

## 🇰🇷 5. 2026년 변화와 Amazon EVS
**VMware Cloud on AWS**는 **2024-04-30부터 AWS가 직접 재판매하지 않으며, Broadcom 경로로 제공**된다. 별도 서비스 **Amazon Elastic VMware Service(EVS)**는 고객 AWS VPC의 베어메탈 인프라에서 VMware Cloud Foundation을 운영하는 AWS 서비스다. 목적은 비슷해도 상품·운영 모델은 다르므로 같은 이름처럼 취급하지 않는다.

강의 SAA 핵심은 '기존 VMware 도구 그대로 AWS 확장 = VMware Cloud on AWS'라는 **아키텍처 아이디어**다. 실제 2026년 신규 구축에서는 Amazon EVS 등 현재 가능한 서비스를 검토한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- VMware vSphere(가상화) / vSAN(스토리지) / NSX(네트워크).
- VMware Cloud on AWS = VMware 운영 환경 자체를 AWS로 확장.
- MGN = 일반 EC2로 서버 Rehosting. 두 서비스는 이전 후 실행 환경이 다르다.
- 사용 사례: 용량 확장, VMware VM 이전, 하이브리드, DR.
- 2024년 이후 VMC on AWS의 판매 경로 변경, Amazon EVS는 별도 AWS 서비스.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. AWS로 옮긴 뒤에도 VMware vSphere로 VM을 운영하려면?

<details>
<summary>정답 및 해설 보기</summary>

강의 기준 **VMware Cloud on AWS**가 대표적인 답이다.

</details>

### Q2. vSAN이 담당하는 것은 서버 CPU인가 네트워크인가 스토리지인가?

<details>
<summary>정답 및 해설 보기</summary>

**스토리지**다.

</details>

### Q3. NSX는 무엇을 관리하는가?

<details>
<summary>정답 및 해설 보기</summary>

**소프트웨어 정의 네트워크 및 네트워크 보안**이다.

</details>

### Q4. MGN의 이전 후 실행 형태와 VMware Cloud on AWS의 차이는?

<details>
<summary>정답 및 해설 보기</summary>

MGN은 **일반 EC2**, VMC on AWS는 **VMware 환경의 VM**이다.

</details>

### Q5. Amazon EVS는 VMware Cloud on AWS의 단순 새 이름인가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 고객 VPC에서 VMware Cloud Foundation을 운영하는 **별도 AWS 서비스**다.

</details>

## 🇯🇵 日本語 Summary

VMware Cloud on AWS は AWS 上でも vSphere・vSAN・NSX と既存の VM 運用を維持する。MGN の一般 EC2 への Rehosting とは異なる。用途は容量拡張、移行、ハイブリッド運用、DR。2026 年の別選択肢として Amazon EVS がある。

## 🇺🇸 English Summary

VMware Cloud on AWS extends VMware-managed virtual machines and familiar vSphere, vSAN, and NSX operations onto AWS infrastructure. Unlike MGN, it does not convert workloads to ordinary EC2 instances. Amazon EVS is a separate AWS service for VMware Cloud Foundation.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| vSphere | VMware 가상화 관리 | vSphere |
| vSAN | VMware 가상 스토리지 | vSAN |
| NSX | VMware 가상 네트워크 | NSX |
| Software-Defined Data Center | 소프트웨어 정의 데이터센터 | ソフトウェア定義データセンター |
| Hybrid Cloud | 하이브리드 클라우드 | ハイブリッドクラウド |

## 🔗 Official References

- https://aws.amazon.com/vmware/vmwarecloudonaws/
- https://docs.aws.amazon.com/prescriptive-guidance/latest/transitioning-from-vmware-to-the-aws-cloud/introduction.html
