# 📘 25-07. NAT Instance & Source/Destination Check

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `NAT` · `NAT Instance` · `PAT` · `Source/Destination Check` · `IP Forwarding` · `Blackhole`

## 🇰🇷 1. NAT가 필요한 이유
Private EC2 `10.0.16.20`은 외부 인터넷에 자신의 사설 IPv4를 그대로 노출해 통신할 수 없다. **NAT(Network Address Translation)**는 출발지 주소를 변환하고, 여러 연결을 구분하기 위해 포트도 이용(PAT)하여 응답을 원래 EC2에 되돌린다.

```text
Private EC2 10.0.16.20
  → NAT Instance (사설→공인 주소/포트 변환)
  → IGW → 인터넷
응답은 NAT 상태 테이블을 통해 다시 원본 EC2로 반환
```

기존 연결의 응답은 돌아오지만 외부가 임의로 새로운 연결을 시작하는 접근은 기본 NAT 경로로 허용하지 않는다.

## 🇰🇷 2. NAT Instance 구성
**NAT Instance**는 NAT 소프트웨어/IP Forwarding을 설정한 일반 EC2다. 보통 Public Subnet에 배치하고, 공인 IP 또는 EIP가 있어야 한다.

| Subnet | Route |
|---|---|
| Private | `0.0.0.0/0 → NAT Instance(또는 ENI)` |
| NAT Instance의 Public | `0.0.0.0/0 → IGW` |

NAT Instance의 SG에는 Private Subnet에서 오는 필요한 TCP/UDP 트래픽, 응답 전송을 위한 규칙 등이 필요하다.

## 🇰🇷 3. Source/Destination Check를 왜 꺼야 하는가
EC2 기본 **Source/Destination Check(출발지/목적지 확인)**는 자신의 ENI를 송신자 또는 수신자로 가지지 않는 패킷 전달을 제한한다. NAT Instance는 **다른 EC2의 패킷을 중계**하므로 검사 기능을 끄고 IP Forwarding을 활성화해야 한다. '오래된 방식이라서 끈다'가 아니라 **패킷을 대신 전달하는 역할 때문에** 끄는 것이다.

## 🇰🇷 4. 실습 관찰과 한계
강의는 Community NAT AMI로 EC2를 만들고 소스/목적지 검사를 해제한 뒤 Private Route Table에 경로를 추가했다. ICMP 허용 전에는 `ping`이 실패했지만 NAT Instance SG에 ICMP가 추가되자 성공했다. `curl`로 HTTP 외부 통신을 확인했다.

NAT Instance가 중지되거나 네트워크 인터페이스 경로가 유효하지 않으면 라우팅 대상이 **Blackhole(블랙홀)**이 될 수 있다. 성능과 가용성이 인스턴스 크기·유지보수에 의존하며 구형 강의 AMI를 실무에 재사용하면 안 된다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- NAT Instance는 관리자가 운영하는 EC2 기반 NAT.
- Source/Destination Check OFF + IP Forwarding이 필수.
- Private Route `0.0.0.0/0 → NAT Instance`.
- 인스턴스 장애 시 경로 Blackhole 가능.
- NAT Instance는 관리형 NAT Gateway보다 운영 부담이 큼.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. NAT Instance에서 Source/Destination Check를 끄는 이유는?

<details>
<summary>정답 및 해설 보기</summary>

**다른 EC2의 패킷을 전달해야 하기 때문.**

</details>

### Q2. NAT Instance는 어느 Subnet에 주로 배치하는가?

<details>
<summary>정답 및 해설 보기</summary>

**Public Subnet.** 공인 주소와 IGW 경로 필요.

</details>

### Q3. Private EC2의 기본 IPv4 경로는 어디로?

<details>
<summary>정답 및 해설 보기</summary>

**NAT Instance 또는 해당 ENI.**

</details>

### Q4. NAT Instance가 정지되어 라우트 대상이 무효라면?

<details>
<summary>정답 및 해설 보기</summary>

**Blackhole 경로**가 되어 통신 불가할 수 있다.

</details>

### Q5. NAT Instance와 Gateway 중 OS 보안 패치가 필요한 쪽은?

<details>
<summary>정답 및 해설 보기</summary>

**NAT Instance.** EC2 자체를 운영해야 한다.

</details>

## 🇯🇵 日本語 Summary

NAT Instance は Public Subnet の EC2 を NAT ルータとして使う方法。Private Subnet のデフォルトルートを NAT Instance に向け、Source/Destination Check を無効化する。EC2 の障害と運用負荷に注意する。

## 🇺🇸 English Summary

A NAT instance is a self-managed EC2-based IPv4 egress router. It needs a public subnet, internet access, IP forwarding, and disabled source/destination checks because it forwards traffic for other instances. Instance failure can create a blackhole route.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Network Address Translation | 네트워크 주소 변환 | ネットワークアドレス変換 |
| Source/Destination Check | 출발지/목적지 확인 | 送信元/宛先チェック |
| IP Forwarding | IP 전달 | IP フォワーディング |
| Blackhole Route | 무효 경로 | ブラックホールルート |
