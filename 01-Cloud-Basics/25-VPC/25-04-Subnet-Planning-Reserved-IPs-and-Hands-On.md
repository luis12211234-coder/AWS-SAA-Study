# 📘 25-04. Subnet Planning, Reserved IPs & Console Lab

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Subnet` · `Availability Zone` · `IPv4 Reservations` · `CIDR Alignment` · `Public Subnet` · `Private Subnet`

## 🇰🇷 1. VPC와 Subnet 관계
VPC CIDR가 `10.0.0.0/16`이어도 Subnet은 **한 AZ에만 존재**한다. 가용성을 높이려면 여러 AZ에 각각 Subnet을 만든다.

```text
AZ-a: PublicSubnetA  10.0.0.0/24
AZ-b: PublicSubnetB  10.0.1.0/24
AZ-a: PrivateSubnetA 10.0.16.0/20
AZ-b: PrivateSubnetB 10.0.32.0/20
```

CIDR 블록은 중복되면 안 되며, `/20` 경계는 16 단위로 맞는다. 예를 들어 `10.0.16.0/20` 범위는 `10.0.16.0`~`10.0.31.255`다.

## 🇰🇷 2. AWS가 예약하는 IPv4 주소 5개
예를 들어 `10.0.0.0/24`에는 총 256개 IP가 있지만 **251개만 EC2 등에 할당 가능**하다.

| 주소 | 예약 이유 |
|---|---|
| `10.0.0.0` | 네트워크 주소 |
| `10.0.0.1` | VPC Router |
| `10.0.0.2` | DNS용 예약 주소(실제 Resolver IP는 VPC 기본 CIDR 기준) |
| `10.0.0.3` | 미래 사용 예약 |
| `10.0.0.255` | 브로드캐스트 주소 예약(AWS VPC는 브로드캐스트 미지원) |

## 🇰🇷 3. 필요한 IP에서 CIDR 선택
29개 사설 IP가 필요하다면 `/27`의 총 32개에서 예약 5개를 뺀 **27개**는 부족하다. `/26`은 총 64개, 사용 가능 59개라 적합하다. 서브넷 내 리소스 확장 여유도 함께 계산한다.

## 🇰🇷 4. 실습 시 확인할 설정
VPC → Subnets → Create Subnet에서 VPC, AZ, CIDR를 선택한다. IPv4 **Auto-assign public IP** 옵션은 새 ENI에 공인 IP를 자동 배정할지에 관한 설정이다. 이것만 활성화해도 퍼블릭 서브넷이 되지는 않는다. **IGW 기본 경로**가 연결된 Route Table이 필요하다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Subnet은 한 AZ에 속하고 다른 Subnet과 CIDR가 겹치면 안 됨.
- AWS IPv4 Subnet 예약 주소 5개.
- `/27` 사용 가능 27개, `/26` 사용 가능 59개.
- Public 여부는 공인 IP 자동 할당만으로 결정되지 않는다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. AWS `/24` Subnet에서 사용 가능한 IPv4 수는?

<details>
<summary>정답 및 해설 보기</summary>

**251개.** 전체 256개에서 5개 예약.

</details>

### Q2. `10.0.16.0/20`의 마지막 IPv4는?

<details>
<summary>정답 및 해설 보기</summary>

**10.0.31.255.** 다음 `/20`은 10.0.32.0에서 시작.

</details>

### Q3. 29개 IP가 필요할 때 `/27`이면 충분한가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 27개만 할당 가능하므로 `/26` 이상 필요.

</details>

### Q4. 하나의 Subnet을 AZ-a와 AZ-b에 걸쳐 만들 수 있는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** Subnet은 단일 AZ에 속한다.

</details>

### Q5. 공인 IP 자동 할당 옵션만 켜면 인터넷 연결되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** IGW 경로, 보안 설정 등이 필요하다.

</details>

## 🇯🇵 日本語 Summary

Subnet は必ず一つの AZ に属し、CIDR は重複できない。AWS は各 IPv4 Subnet で 5 個を予約する。`/24` の利用可能アドレスは 251 個。Public Subnet かどうかは IGW への経路で決まる。

## 🇺🇸 English Summary

A subnet resides in exactly one AZ. AWS reserves five IPv4 addresses per subnet; a /24 has 251 usable addresses. Plan CIDR boundaries to avoid overlaps. A public subnet requires a route to an internet gateway, not merely auto-assigned public IPs.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Subnet | 서브넷 | サブネット |
| Reserved IP | 예약 IP | 予約 IP |
| CIDR Alignment | CIDR 경계 정렬 | CIDR 境界 |
| Public IP Auto Assignment | 공인 IP 자동 할당 | パブリック IP 自動割当 |
