# 📘 25-03. VPC Tenancy, CIDR Quotas & Secondary CIDR

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Tenancy` · `Default` · `Dedicated Instance` · `Dedicated Host` · `VPC Quota` · `Secondary CIDR`

## 🇰🇷 1. Tenancy가 의미하는 것
**Tenancy(테넌시)**는 EC2가 사용하는 물리 호스트의 공유 범위를 결정하는 설정이다.

| 방식 | 특징 |
|---|---|
| Default | 여러 AWS 고객의 인스턴스가 물리 호스트를 공유할 수 있음 |
| Dedicated Instance | 다른 고객과 물리 서버를 공유하지 않지만 호스트 선택을 AWS가 관리 |
| Dedicated Host | 고객에게 물리 호스트가 할당되고 호스트 단위 가시성/라이선스 관리 가능 |

비용은 일반적으로 공유형보다 Dedicated 계열이 높다. 전용 VPC IP 주소와 **Dedicated 물리 호스트는 다른 개념**이다.

## 🇰🇷 2. VPC 수와 CIDR의 기본 제한
리전당 VPC 수와 VPC당 IPv4 CIDR 개수에는 기본 할당량이 있다(일반적으로 **5 VPC/리전**, **5 IPv4 CIDR/VPC**; 증가 요청 가능). 각 IPv4 CIDR은 통상 `/16`~`/28`이다. 실제 환경의 할당량은 Service Quotas를 확인한다.

## 🇰🇷 3. Secondary CIDR 추가
VPC `10.0.0.0/16`에서 주소가 부족하면 중복되지 않는 **보조 IPv4 CIDR**(예: `10.1.0.0/16`)을 추가할 수 있다. 이 경우 VPC Route Table에 각 CIDR에 대응하는 `local` 경로가 생긴다.

```text
VPC primary   10.0.0.0/16  → local
VPC secondary 10.1.0.0/16  → local
```

보조 CIDR을 추가하는 것만으로 기존 Subnet의 크기가 커지는 것은 아니다. 새 CIDR에 해당하는 **새 서브넷**을 설계해서 사용한다.

## 🇰🇷 4. 콘솔 실습 관점
VPC 콘솔의 **Edit CIDRs**에서 추가 CIDR를 연결하고, Route Table의 자동 `local` 경로를 확인한다. 별도 VPC의 동일한 주소 공간을 피어링하려 한다면 CIDR 중복이 문제가 되므로 먼저 주소 계획을 세워야 한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Default/Dedicated Instance/Dedicated Host는 **물리 호스트 공유와 관리** 차이.
- 전용 호스트 요구(소프트웨어 라이선스 등) → Dedicated Host 검토.
- VPC 수·CIDR 수는 기본 할당량이며 증가 가능.
- Secondary CIDR는 기존 Subnet을 확장하지 않는다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 물리적 서버를 직접 가시적으로 관리해야 하는 라이선스 요구는?

<details>
<summary>정답 및 해설 보기</summary>

**Dedicated Host.** 물리 호스트 할당과 가시성 제공.

</details>

### Q2. Dedicated Instance가 호스트 ID 제어까지 보장하는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 다른 고객과 물리적 격리를 제공하지만 호스트 지정은 AWS가 관리한다.

</details>

### Q3. 리전당 VPC 수 5는 절대 한계인가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 기본 할당량이며 증가 요청 가능하다.

</details>

### Q4. 보조 CIDR을 VPC에 추가하면 기존 서브넷이 자동 확대되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 별도 CIDR의 새 서브넷을 생성해야 한다.

</details>

### Q5. VPC CIDR 하나를 더 추가하면 Route Table에는?

<details>
<summary>정답 및 해설 보기</summary>

**해당 CIDR에 대한 local 경로**가 자동으로 생성된다.

</details>

## 🇯🇵 日本語 Summary

Tenancy は物理ホストの共有方式を意味する。Default、Dedicated Instance、Dedicated Host を区別する。VPC 数と CIDR 数には変更可能なクォータがある。Secondary CIDR を追加しても既存 Subnet のサイズは変わらない。

## 🇺🇸 English Summary

Tenancy determines physical host sharing: Default, Dedicated Instances, and Dedicated Hosts. VPC count and CIDR count have adjustable quotas. A secondary CIDR adds a new VPC address block, not extra addresses to an existing subnet.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Tenancy | 호스트 점유 방식 | テナンシー |
| Dedicated Instance | 전용 인스턴스 | 専有インスタンス |
| Dedicated Host | 전용 호스트 | 専有ホスト |
| Secondary CIDR | 추가 CIDR | セカンダリ CIDR |
