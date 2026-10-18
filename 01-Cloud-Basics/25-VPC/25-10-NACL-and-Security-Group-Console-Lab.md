# 📘 25-10. NACL & Security Group Console Troubleshooting

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `NACL Lab` · `httpd` · `Rule Priority` · `Inbound` · `Outbound` · `Stateful`

## 🇰🇷 1. HTTP 테스트 서버 준비
강의는 기존 **BastionHost EC2**를 임시 HTTP 서버로 재사용했다. Bastion은 원래 SSH 점프 호스트지만 일반 EC2이므로 `httpd`를 설치할 수 있다. 이는 NACL 동작 실험을 위한 재사용일 뿐 운영 모범 사례는 아니다.

```bash
sudo yum install -y httpd
sudo systemctl enable --now httpd
echo 'hello world' | sudo tee /var/www/html/index.html
```

SG 인바운드 TCP 80 허용 후 공인 IPv4의 `http://...`로 접속하여 `hello world`를 확인한다.

## 🇰🇷 2. NACL 우선순위 실험
Default NACL의 규칙은 보통 `100 Allow All`이다. 여기에 `80 Deny TCP 80`을 추가하면 **80이 먼저 일치**하므로 HTTP 연결이 실패한다.

다시 Deny 규칙 번호를 `140`으로 바꾸면 `100 Allow All`에 먼저 일치하여 웹페이지가 다시 나타난다. NACL은 **첫 번째 일치 규칙으로 결론** 내린다.

## 🇰🇷 3. 무상태 실험
Inbound를 허용한 상태에서 NACL Outbound를 Deny로 바꾸면 브라우저의 HTTP 요청은 EC2에 도착해도 **응답이 나가지 못한다**. 이는 NACL의 Stateless 속성을 증명한다.

반대로 NACL을 Allow로 되돌리고 SG의 **모든 outbound 규칙을 삭제**해도 외부에서 인바운드로 시작된 HTTP 요청의 응답은 계속 반환된다. 다만 EC2가 새롭게 외부로 접속하려는 연결은 SG outbound가 없으면 거부된다.

## 🇰🇷 4. 실습 후 복구 및 점검
실습 종료 후 NACL 규칙을 Allow로 복구하고, SG의 의도한 outbound 규칙을 되살린다. `All TCP`와 `All traffic`은 다르므로 필요한 프로토콜이 TCP인지 ICMP/UDP도 포함하는지 확인한다. 네트워크 연결 장애 시 **Route Table → NACL → SG → OS 서비스** 순서로 검사하면 유용하다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- NACL 80 Deny HTTP → 100 Allow All: 차단.
- NACL 100 Allow All → 140 Deny HTTP: 허용.
- NACL outbound Deny는 반환 응답을 차단할 수 있음.
- SG outbound를 지워도 허용된 inbound 연결의 응답은 통과.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 기존 BastionHost에서 httpd를 사용한 이유는?

<details>
<summary>정답 및 해설 보기</summary>

**NACL 실험용으로 일반 EC2를 재사용한 것.**

</details>

### Q2. NACL 80 DENY HTTP가 100 ALLOW ALL보다 먼저면?

<details>
<summary>정답 및 해설 보기</summary>

**HTTP 접속 차단.**

</details>

### Q3. NACL outbound 전면 DENY 상태에서 inbound HTTP 허용만으로 응답 가능한가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 응답이 NACL outbound에 막힌다.

</details>

### Q4. SG outbound 규칙을 모두 지우면 외부에서 허용된 HTTP 요청의 응답도 차단되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** Stateful 반환 트래픽은 허용된다.

</details>

### Q5. SG의 All TCP는 ICMP Ping까지 허용하는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** ICMP는 TCP가 아닌 별도 프로토콜이다.

</details>

## 🇯🇵 日本語 Summary

既存の Bastion EC2 に httpd を起動し、NACL のルール番号と Stateless 性を確認する。HTTP の Deny 80 は Allow 100 より優先される。NACL Outbound を拒否すると返信が失敗するが、SG の Outbound が空でも許可された Inbound の返信は通る。

## 🇺🇸 English Summary

The lab installs httpd on an existing bastion EC2, blocks HTTP using a lower-numbered NACL deny rule, and confirms stateless return-traffic filtering. Removing SG outbound rules does not block replies to already approved inbound connections.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Rule Priority | 규칙 우선순위 | ルール優先度 |
| Inbound Rule | 인바운드 규칙 | インバウンドルール |
| Outbound Rule | 아웃바운드 규칙 | アウトバウンドルール |
| HTTP Server | HTTP 서버 | HTTP サーバー |
