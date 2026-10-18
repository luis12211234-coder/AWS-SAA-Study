# 📘 25-18. Direct Connect: Location, Physical Link & VIFs

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Direct Connect` · `DX Location` · `Private VIF` · `Public VIF` · `Transit VIF` · `BGP` · `VLAN`

## 🇰🇷 1. Direct Connect는 무엇을 연결하는가
**AWS Direct Connect(DX)**는 기업 네트워크와 AWS를 **공용 인터넷을 거치지 않는 네트워크 연결**로 잇는다. Site-to-Site VPN과 달리 물리 회선/파트너 작업이 필요할 수 있어 구축 시간이 길어질 수 있다. 지연시간의 일관성·대량 데이터 전송·하이브리드 환경에 유용하다. **DX 기본 연결은 암호화를 제공하지 않는다.**

## 🇰🇷 2. Location / 물리 회선 / VIF의 정확한 위치
```text
[회사 데이터센터의 라우터]
           │
      통신사 회선 등
           │
┌── Direct Connect Location (물리 시설) ───────┐
│ 회사/통신사 측 네트워크 접속 포트              │
│            │ 크로스 커넥트(물리 연결)           │
│ AWS Direct Connect 라우터                    │
│         └─ 이 DX 연결 위에서 VIF 설정 ────     │
└──────────────────────────────────────────────┘
           │
      AWS 네트워크
```

**DX Location은 시설(장소)**, **DX Connection은 AWS 포트와의 물리적 연결**, **VIF(Virtual Interface)는 그 연결 위에 설정되는 논리 인터페이스**다. VIF가 별도의 물리적 중간 장비는 아니다. 실제로 VLAN과 BGP를 사용하여 연결을 구분한다.

## 🇰🇷 3. Private/Public/Transit VIF
| VIF | 대상 |
|---|---|
| **Private VIF** | VGW 또는 DX Gateway를 통해 VPC의 사설 IP 접근 |
| **Public VIF** | Amazon S3 등 AWS 서비스의 **공인 주소** 접근 |
| **Transit VIF** | DX Gateway를 경유해 **Transit Gateway**에 접근 |

`Public VIF`의 Public은 **AWS 서비스의 공인 IP를 대상으로 한다**는 뜻이지, 트래픽이 일반 공용 인터넷을 통과한다는 뜻은 아니다. 일반 인터넷 전체 접속을 위한 회선도 아니다.

## 🇰🇷 4. VIF는 물리 회선 위의 논리 분기
한 DX 물리 연결 위에 VIF를 구성하여 **Private VIF → VPC** 또는 **Public VIF → AWS Public Service**로 통신할 수 있다. CCNA에서 물리 인터페이스 위에 VLAN별 논리 서브인터페이스를 구성하던 개념과 비슷하다. 단 AWS VIF는 별도 AWS 리소스로 생성·관리된다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- DX Location = 물리 시설; DX Connection = 물리 포트 연결; VIF = 논리 인터페이스.
- Private VIF는 VPC 사설 IP, Public VIF는 AWS 퍼블릭 IP 서비스.
- Public VIF는 공용 인터넷 회선이라는 뜻이 아니다.
- TGW 연결에는 Transit VIF + DX Gateway 사용.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Direct Connect Location은 VIF 장치의 다른 이름인가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** AWS와 회사 네트워크가 연결되는 물리적 시설.

</details>

### Q2. VIF가 따로 놓인 물리적 스위치인가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** DX 연결 위의 논리 인터페이스다.

</details>

### Q3. 회사에서 EC2의 사설 IP로 접속할 때 대표적인 VIF는?

<details>
<summary>정답 및 해설 보기</summary>

**Private VIF.**

</details>

### Q4. 회사에서 S3의 AWS 퍼블릭 서비스 주소로 DX 연결을 활용하려면?

<details>
<summary>정답 및 해설 보기</summary>

**Public VIF.**

</details>

### Q5. Direct Connect와 Transit Gateway 연결에 사용하는 VIF는?

<details>
<summary>정답 및 해설 보기</summary>

**Transit VIF.**

</details>

## 🇯🇵 日本語 Summary

Direct Connect はオンプレミスから AWS への専用ネットワーク接続。DX Location は物理施設、DX Connection は物理リンク、VIF は VLAN/BGP を用いた論理インターフェイス。Private VIF は VPC のプライベート IP、Public VIF は AWS のパブリックサービス、Transit VIF は TGW 連携に使用する。

## 🇺🇸 English Summary

Direct Connect provides dedicated connectivity to AWS. A DX Location is the physical facility; a DX connection is the physical link, while a VIF is a logical interface over that link. Private VIF reaches VPC private addresses, public VIF reaches AWS public service addresses, and transit VIF reaches a DX gateway linked to TGW.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Direct Connect Location | DX 접속 시설 | Direct Connect ロケーション |
| Virtual Interface | 가상 인터페이스 | 仮想インターフェイス |
| Private VIF | 프라이빗 VIF | プライベート VIF |
| Public VIF | 퍼블릭 VIF | パブリック VIF |
| Transit VIF | 트랜짓 VIF | トランジット VIF |
