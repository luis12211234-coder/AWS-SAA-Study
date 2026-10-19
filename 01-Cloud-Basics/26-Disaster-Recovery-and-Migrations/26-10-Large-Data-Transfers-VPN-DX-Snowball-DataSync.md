# 📘 26-10. Large Data Transfer: Internet, DX, Snowball & DataSync

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `200TB` · `100Mbps` · `Site-to-Site VPN` · `Direct Connect` · `Snowball` · `DataSync` · `DMS CDC`

## 🇰🇷 1. 대용량 전송은 네트워크 속도부터 계산한다
강의 예시: **200TB** 데이터를 AWS로 옮겨야 하고 기존 인터넷 회선은 **100Mbps**. 계산할 때 Byte와 bit를 구분한다. `1 Byte = 8 bits`이며, 십진 단위 기준 `200 TB = 200×10^12 Bytes`다.

```text
200 TB × 8 ÷ 100 Mbps
= (200 × 10^12 × 8) / (100 × 10^6) 초
= 16,000,000초 ≒ 185.2일

1 Gbps = 100 Mbps의 10배 속도
→ 이론적으로 ≒ 18.5일 (별도의 DX 구축 기간은 제외)
```

이는 **회선 대역폭을 100% 전송에 쓰는 이론상 하한**이다. 실제 TCP·암호화 오버헤드, 파일 수, 디스크 IO, 경쟁 트래픽 때문에 더 오래 걸릴 수 있다.

## 🇰🇷 2. 인터넷과 Site-to-Site VPN
둘 다 비교적 빠르게 구축할 수 있다. VPN은 인터넷 위 **IPsec 암호화 터널**을 추가하는 것이므로 100Mbps 회선을 1Gbps로 만들어 주지 않는다. 초기 데이터량이 작거나 빠른 회선이 이미 있으면 적합하다.

## 🇰🇷 3. Direct Connect
AWS까지 **공용 인터넷을 통과하지 않는 전용 네트워크 경로**를 활용한다. 예시 1Gbps 회선은 100Mbps 대비 약 10배의 전송 처리량을 제공할 수 있다. 하지만 신규 회선은 DX Location/제공업체 연결 등의 구축 시간이 걸려서 **회선 개통 + 전송시간**을 합쳐 비교해야 한다. 이미 DX가 있다면 판단이 달라진다.

## 🇰🇷 4. 물리적 오프라인 대용량 전송: 강의의 Snowball
```text
온프레미스 200TB
       │
       ├─ 네트워크 100Mbps → 약 185일
       │
       └─ 여러 대의 물리 전송 장치에 복사
                      ↓ 물류 배송
                    AWS 데이터 적재
```
강의에서는 **Snowball 2~3대 병렬 주문 시 약 1주일**을 예시로 사용했다. 이는 보장된 배송/수집 시간이 아니라 설명을 위한 가정이다. 장치 용량, 입출력, 배송 상황에 좌우된다.

**현재 서비스 주의:** 과거 SAA 문제의 '수백 TB, 느린 인터넷, 빠른 1회성 이전'에는 **Snowball**이 대표 답이었다. 하지만 Snowball Edge는 **2025년 11월부터 신규 고객 주문이 제한/종료**되므로 지금 실제 프로젝트는 **AWS Data Transfer Terminal 또는 사용 가능한 다른 물리적 전송 옵션**과 온라인 전송을 검토해야 한다.

## 🇰🇷 5. 초기 대량 이전과 지속 복제를 분리한다
**초기 전송(Initial Load)**: 매우 많은 기존 데이터 → 적합한 오프라인 장치·대역폭으로 전송. **변경분(Delta)**: 이후 새로 바뀌는 DB 정보 → DMS+CDC 등으로 따라잡는다.

```text
[초기 200TB]  오프라인/고속 링크 ─────▶ AWS 최초 적재
[이후 변경]   DMS + CDC ──────────▶ AWS 대상 DB 동기화
[최종 전환]   지연·정합성 확인 ──────▶ AWS로 Cutover
```

**DataSync**는 파일·객체 기반 저장소의 전송 및 동기화, **DMS**는 데이터베이스 데이터와 변경 로그 기반 복제에 적합하다. **VPN/DX는 데이터 서비스가 타고 가는 네트워크 연결 경로**다. 따라서 `DX + DataSync`, `VPN + DMS`처럼 함께 조합한다.

## 🇰🇷 6. 시험 판단식
'200TB + 100Mbps + 신속한 1회성 이전' → **강의 기준 Snowball 같은 물리적 오프라인 전송**. '이미 빠른 DX가 있고 반복 파일 동기화' → DX + DataSync 검토. 'DB를 계속 사용하면서 이전' → DMS + CDC 검토. 항상 **설치 리드 타임/비용/최종 다운타임/지속성**을 같이 읽는다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- 200TB / 100Mbps → 이론상 약 185일, 1Gbps → 약 18.5일.
- VPN은 암호화 연결이고 회선 속도를 올리지 않는다.
- DX는 구축 리드 타임이 있으며 이미 개통된 경우 유리할 수 있다.
- 기존 시험 Snowball = 대량 오프라인 초기 이전. 신규 주문 여부는 현재 공식 제공 상태 확인.
- DataSync(파일·객체) vs DMS(DB·CDC) vs VPN/DX(네트워크 회선).

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 200TB를 100Mbps로 연속 전송한다면 대략 며칠인가?

<details>
<summary>정답 및 해설 보기</summary>

약 **185일**. 계산은 `200×10^12×8/(100×10^6)`초이다.

</details>

### Q2. 100Mbps 인터넷에 Site-to-Site VPN을 붙이면 데이터가 10배 빨라지나?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** VPN은 공용 인터넷을 통한 암호화 터널이지 속도 증가 서비스가 아니다.

</details>

### Q3. 신규 Direct Connect 1Gbps로 200TB 전송 시 무엇을 추가 고려해야 하는가?

<details>
<summary>정답 및 해설 보기</summary>

전송 자체는 이론상 **18.5일**이지만 **회선 개통 기간**이 따로 든다.

</details>

### Q4. 파일 공유 데이터의 반복 전송에 적합한 AWS 서비스는?

<details>
<summary>정답 및 해설 보기</summary>

**AWS DataSync**. 경로로 VPN/DX를 조합할 수 있다.

</details>

### Q5. 거대한 초기 DB를 옮긴 후 새로운 주문 내역을 복제하려면?

<details>
<summary>정답 및 해설 보기</summary>

초기 데이터 적재 후 **DMS+CDC**로 변경 사항을 복제하는 전략을 검토한다.

</details>

## 🇯🇵 日本語 Summary

200 TB を 100 Mbps で送ると理論上約185日、1 Gbps なら約18.5日。VPN は暗号化するが回線速度を上げない。Direct Connect は開通時間が必要。旧講義では Snowball が大量の初期オフライン移行、DataSync はファイル同期、DMS+CDC は DB 更新分の複製。

## 🇺🇸 English Summary

At full utilization, moving 200 TB over 100 Mbps takes about 185 days; 1 Gbps takes 18.5 days, excluding setup. VPN encrypts over the internet, Direct Connect provides a dedicated path, Snowball was the classic offline bulk-transfer answer, DataSync synchronizes files, and DMS with CDC handles ongoing database changes.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Bandwidth | 대역폭 | 帯域幅 |
| Transfer Time | 전송 시간 | 転送時間 |
| Offline Transfer | 오프라인 전송 | オフライン転送 |
| Initial Load | 초기 적재 | 初期ロード |
| Continuous Replication | 지속 복제 | 継続レプリケーション |
| Cutover | 최종 운영 전환 | カットオーバー |

## 🔗 Official References

- https://docs.aws.amazon.com/snowball/latest/developer-guide/doc-history.html
