# 📘 25-06. Bastion Host & SSH into Private EC2

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Bastion Host` · `SSH` · `Public Subnet` · `Private Subnet` · `Security Group Reference` · `ProxyJump` · `Session Manager`

## 🇰🇷 1. Bastion Host의 목적
**Bastion Host(배스천 호스트)**는 외부 관리자가 인터넷에서 직접 접근할 수 없는 **Private EC2**에 SSH로 접근하기 위한 중간 관리 서버다. 특별한 AWS 전용 인스턴스 유형이 아니라 **Public Subnet에 둔 일반 EC2**다.

```text
관리자 PC --SSH 22--> Public Bastion EC2
                       --SSH 22--> Private EC2
```

Bastion을 통해 프라이빗 EC2에 로그인한다고 해서 프라이빗 EC2가 인터넷으로 나가는 NAT 기능을 자동으로 얻는 것은 아니다.

## 🇰🇷 2. 보안 그룹 구성
- **Bastion SG inbound SSH 22:** 관리자 공인 IPv4 `/32`로 제한.
- **Private EC2 SG inbound SSH 22:** Bastion의 SG ID를 Source로 참조하면 Bastion의 사설 IP가 바뀌어도 관리에 유리하다.

SSH 키 인증은 퍼블릭 키를 EC2에 두고 개인 키는 클라이언트 측에서 관리한다. 개인 키 파일 권한을 제한하는 명령은 `chmod 400 DemoKeyPair.pem` 같은 형식이다.

## 🇰🇷 3. 강의 실습과 운영 환경의 차이
강의는 배스천 호스트에서 `ssh -i DemoKeyPair.pem ec2-user@10.0.19.99`로 PrivateInstance에 접속했다. 개인 키를 배스천 호스트에 복사하는 데모는 학습용으로 볼 수 있지만, 실무에서는 **개인 키를 Bastion에 남기지 않는 SSH ProxyJump/에이전트 포워딩의 안전한 사용** 또는 **SSM Session Manager / EC2 Instance Connect Endpoint**를 우선 검토한다.

## 🇰🇷 4. 실패할 때 점검
퍼블릭 Bastion에 공인 IP와 IGW 경로가 있는지, 관리자 IP가 TCP 22로 허용되는지, 두 EC2 간 `local` 라우트가 있는지, Private SG에 Bastion발 22 포트가 허용되는지 확인한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Bastion Host는 SSH 점프 호스트이며 NAT Gateway 대체재가 아님.
- Bastion SG: 관리자 IP만 SSH 허용, Private SG: Bastion SG 참조.
- 개인 키를 퍼블릭 서버에 장기 저장하지 않는 것이 권장됨.
- 관리 편의·보안을 위해 Session Manager 등 대안 가능.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Bastion Host는 AWS의 별도 전용 서버 상품인가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** SSH 경유지 역할을 하는 일반 EC2다.

</details>

### Q2. Bastion의 SSH 22를 0.0.0.0/0로 열어 두는 것이 최선인가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 관리자 `/32`로 제한하는 것이 바람직하다.

</details>

### Q3. Private EC2 SG의 SSH 출발지로 유용한 것은?

<details>
<summary>정답 및 해설 보기</summary>

**Bastion의 Security Group ID.**

</details>

### Q4. Bastion을 만들어 두면 Private EC2에서 패키지 업데이트가 자동 가능한가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 별도 NAT나 VPC Endpoint 등이 필요하다.

</details>

### Q5. 개인 키를 Bastion에 저장하지 않고 경유 SSH하려면?

<details>
<summary>정답 및 해설 보기</summary>

**SSH ProxyJump**, 또는 Session Manager/EC2 Instance Connect Endpoint를 고려한다.

</details>

## 🇯🇵 日本語 Summary

Bastion Host は Public Subnet の管理用 EC2 で、Private Subnet の EC2 に SSH 接続するための中継点。Bastion の SSH は管理者 IP に制限し、Private EC2 は Bastion の Security Group を許可する。NAT の代わりではない。

## 🇺🇸 English Summary

A bastion host is a public EC2 jump server for SSH access to private EC2 instances. Restrict inbound SSH to administrators and reference the bastion security group from the private instance. A bastion is not a NAT solution; ProxyJump and Session Manager are safer alternatives.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Bastion Host | 점프 호스트 | 踏み台サーバー |
| SSH | 보안 셸 접속 | SSH 接続 |
| Security Group Reference | 보안 그룹 참조 | セキュリティグループ参照 |
| Session Manager | 세션 관리자 | セッションマネージャー |
