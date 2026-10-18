# 📘 25-14. VPC Flow Logs: Fields & Network Troubleshooting

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `VPC Flow Logs` · `ENI` · `ACCEPT` · `REJECT` · `Metadata` · `CloudWatch` · `S3`

## 🇰🇷 1. Flow Logs는 어떤 정보를 기록하는가
**VPC Flow Logs**는 VPC 네트워크 인터페이스에 오가는 **IP 트래픽의 메타데이터**를 일정 시간 단위로 집계해 저장한다. VPC 전체, Subnet 또는 특정 **ENI**를 대상으로 생성할 수 있고 EC2뿐 아니라 ELB/RDS/NAT Gateway 등 ENI를 사용하는 서비스 트래픽 조사에도 활용한다.

실제 HTTP 본문이나 비밀번호 같은 **패킷 페이로드를 저장하는 서비스는 아니다**. 일부 특수 트래픽은 로그 대상에서 제외될 수 있다.

## 🇰🇷 2. 한 줄의 로그 구조
기본 형식에는 `version`, `account-id`, `interface-id`, `srcaddr`, `dstaddr`, `srcport`, `dstport`, `protocol`, `packets`, `bytes`, `start`, `end`, `action`, `log-status` 필드가 있다.

```text
... eni-abc 203.0.113.50 10.0.0.72 50105 22 6 10 840 ... REJECT OK
```

이는 외부 IP가 EC2의 TCP **22 SSH**에 접속하려 했지만 REJECT됐다는 의미다. `protocol=6`은 TCP, `OK`는 로그 기록 상태이며 연결 성공 여부가 아니다. `REJECT`는 반드시 공격 성공을 뜻하지 않는다.

## 🇰🇷 3. SG/NACL 문제에 이용하기
**Inbound REJECT**는 SG 또는 NACL이 거부했을 가능성이 있다. **Inbound ACCEPT + 같은 연결의 응답 Outbound REJECT**라면 SG는 Stateful이므로 NACL outbound 설정을 우선 의심한다. 반대로 EC2에서 보낸 요청은 ACCEPT인데 돌아오는 Inbound 응답이 REJECT라면 NACL inbound 임시 포트를 의심할 수 있다.

다만 Flow Logs는 집계된 데이터이며 정확한 연결 대조, 라우팅 및 기타 거부 원인을 함께 조사해야 한다. 단독 로그만으로 SG와 NACL 중 원인을 확정할 수는 없다.

## 🇰🇷 4. 출력 대상과 사용 사례
로그를 **S3**, **CloudWatch Logs**, **Amazon Data Firehose** 등에 전송한다. 로그에서 문제가 많은 출발지 IP, 거부 포트, 트래픽 양, 접속 패턴을 확인한다. CloudWatch Logs Insights는 운영 시 빠른 쿼리에, S3+Athena는 누적 데이터의 SQL 분석에 유용하다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Flow Logs = ENI IP 흐름의 메타데이터, 패킷 본문 저장 아님.
- VPC / Subnet / ENI 단위 생성.
- `ACCEPT` / `REJECT`와 출발지·목적지 IP/Port 분석.
- REJECT가 반드시 침입 성공이나 실제 공격 확정은 아님.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. HTTP 메시지 본문을 분석해야 할 때 Flow Logs가 충분한가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 메타데이터 중심으로 기록한다.

</details>

### Q2. 특정 EC2만 조사하려면 어느 범위의 Flow Logs가 적합한가?

<details>
<summary>정답 및 해설 보기</summary>

**해당 ENI 수준.**

</details>

### Q3. `protocol=6`은 무슨 프로토콜인가?

<details>
<summary>정답 및 해설 보기</summary>

**TCP.**

</details>

### Q4. Flow Log의 `REJECT OK`에서 OK는 연결 성공인가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 로그 기록 상태가 정상이라는 의미.

</details>

### Q5. AWS 내부 IP 거부 기록을 SQL로 대량 분석하려면?

<details>
<summary>정답 및 해설 보기</summary>

**S3 + Athena.**

</details>

## 🇯🇵 日本語 Summary

VPC Flow Logs は VPC、Subnet、ENI 単位で IP 通信メタデータを記録する。送信元・宛先 IP、ポート、ACCEPT/REJECT などを調べて障害や不審な通信を分析する。ペイロードの内容は取得しない。

## 🇺🇸 English Summary

VPC Flow Logs capture aggregated IP-flow metadata at VPC, subnet, or ENI scope. Source/destination addresses, ports, and ACCEPT/REJECT actions help troubleshoot connectivity and detect suspicious patterns. They are not full packet captures.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Flow Log | 흐름 로그 | フローログ |
| Network Metadata | 네트워크 메타데이터 | ネットワークメタデータ |
| ACCEPT/REJECT | 허용/거부 | 許可/拒否 |
| Log Status | 로그 기록 상태 | ログ状態 |
