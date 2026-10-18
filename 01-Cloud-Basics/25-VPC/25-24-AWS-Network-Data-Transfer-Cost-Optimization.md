# 📘 25-24. AWS Network Data Transfer & Cost Optimization

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Data Transfer` · `Ingress` · `Egress` · `AZ` · `Region` · `CloudFront` · `S3` · `Gateway Endpoint`

## 🇰🇷 1. 송신 비용과 수신 비용
AWS 네트워크 요금에서는 **Ingress(외부 → AWS)**는 일반적으로 무료인 반면, **Egress(AWS → 외부)**는 데이터 송신 요금이 발생할 수 있다. 서비스·지역·무료 사용량·특수 경로에 따라 예외가 있으므로 `GB당 몇 센트`를 절대값으로 외우지 않는다.

## 🇰🇷 2. AZ/리전 간 전송의 비용
일반적으로 **같은 AZ에서 사설 IPv4로 직접 통신하는 EC2**는 데이터 전송 비용이 낮거나 무료이고, **AZ 간/리전 간**은 별도 전송 요금이 발생할 수 있다. 다른 AZ의 EC2끼리는 퍼블릭 IP를 쓰는 것보다 **사설 IP 경로**가 비용 면에서 유리한 경우가 많다. 비용만 보고 한 AZ에 다 몰아넣으면 AZ 장애 대비 고가용성이 약해지므로 절충해야 한다.

## 🇰🇷 3. 데이터를 계산하는 위치가 비용을 바꾼다
```text
비효율: AWS DB → 100MB를 회사 데이터센터로 전송 → 50KB 결과
개선:   AWS DB → 같은 AWS의 EC2에서 100MB 처리 → 50KB만 송신
```

데이터가 크고 최종 결과만 작다면 **데이터 가까이 연산을 이동**해 외부 송신 트래픽을 줄이는 게 유리하다. 같은 AZ 배치도 고려한다.

## 🇰🇷 4. S3, CloudFront, Transfer Acceleration
**CloudFront**는 S3 콘텐츠를 엣지에 캐싱하여 지연시간과 원본 S3 요청 수를 줄일 수 있다. S3에서 CloudFront로의 적격 원본 데이터 전송은 무료인 반면 CloudFront의 인터넷 전송과 요청에는 요금이 적용된다. **S3 Transfer Acceleration**은 먼 거리의 전송 성능 향상 가능성과 추가 비용을 함께 고려한다. **Cross-Region Replication**에는 리전 간 전송 및 복제 관련 비용이 발생할 수 있다.

## 🇰🇷 5. NAT Gateway vs S3 Gateway Endpoint 비용
Private EC2 → NAT GW → S3 경로는 NAT Gateway의 운영 시간·GB 처리량 요금이 발생한다. **S3 Gateway Endpoint는 엔드포인트 자체 비용이 무료**이며 NAT 경로를 사용하지 않아 NAT 처리 비용을 줄인다. 강의의 고정 `$0.045` 등의 숫자는 시점/리전별로 변할 수 있다. 같은 리전 S3와 EC2의 표준 전송은 대체로 무료이며 **Gateway Endpoint를 쓴다고 항상 GB당 $0.01이 청구되는 것은 아니다**.

**강의 보정:** 같은 리전의 RDS 원본 → Read Replica의 복제 전송은 AZ가 달라도 별도 데이터 전송 요금이 없는 일반적인 RDS 복제 특례가 있다. EC2 AZ 간 일반 전송 비용과 혼동하지 않는다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- Egress 송신 비용을 줄이려면 데이터 처리를 AWS 내부로 이동.
- 가능하면 사설 IP·같은 AZ로 비용 절약, 고가용성과 균형.
- S3 Gateway Endpoint로 NAT GW 처리 비용 제거 가능.
- CloudFront 캐싱은 S3 요청 수·지연시간 최적화.
- 단가 숫자는 리전/서비스/시점에 따라 변하므로 원칙 중심 학습.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. AWS로 데이터 100GB 업로드와 다운로드 중 일반적으로 송신 요금이 더 문제되는 것은?

<details>
<summary>정답 및 해설 보기</summary>

**AWS → 외부 다운로드(Egress).**

</details>

### Q2. 100MB DB 데이터에서 50KB 결과만 필요하다면 처리 위치는?

<details>
<summary>정답 및 해설 보기</summary>

**데이터가 있는 AWS 내부에 앱을 배치**해 Egress 절감.

</details>

### Q3. Private EC2가 S3에 매일 대량 접근할 때 NAT 비용을 줄이는 방법은?

<details>
<summary>정답 및 해설 보기</summary>

**S3 Gateway Endpoint.**

</details>

### Q4. 한 AZ에 모든 서버를 두면 얻는 이점과 단점은?

<details>
<summary>정답 및 해설 보기</summary>

**AZ 간 전송 비용 절약**, 대신 **AZ 장애 가용성 저하**.

</details>

### Q5. 같은 리전 RDS Read Replica의 AZ 간 복제 요금은 일반 EC2 AZ 간 전송과 동일한가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 일반적으로 RDS 복제 전송에 별도 요금이 없다.

</details>

## 🇯🇵 日本語 Summary

ネットワーク料金は主に送信方向、AZ/リージョン境界、利用サービスで変わる。アプリ処理をデータの近くに置くと Egress を減らせる。S3 Gateway Endpoint は NAT 処理料金を回避できる。CloudFront のキャッシュは遅延と S3 リクエストを削減する。

## 🇺🇸 English Summary

Network transfer costs depend on ingress/egress, AZ and Region boundaries, and the services involved. Process data near its storage to reduce outbound transfer. An S3 gateway endpoint avoids NAT processing costs, while CloudFront caching reduces origin requests and latency. Treat lecture prices as time- and Region-specific.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Ingress | 수신 트래픽 | インバウンド転送 |
| Egress | 송신 트래픽 | アウトバウンド転送 |
| Data Transfer | 데이터 전송 | データ転送 |
| Transfer Acceleration | 전송 가속화 | 転送高速化 |
| Cost Optimization | 비용 최적화 | コスト最適化 |
