# 13-05. CloudFront Price Classes

## 🇰🇷 1. Price Class란?

CloudFront Edge Location은 전 세계에 분산되어 있으며 지역에 따라 데이터 전송 비용이 다릅니다.

Price Class를 사용하면 CloudFront가 사용할 Edge Location의 범위를 조절하여 비용과 Global Coverage 사이의 균형을 맞출 수 있습니다.

---

## 2. Price Class 종류

```text
Price Class 100
→ 가장 제한된 Edge 범위
→ 비용 절감 중심

Price Class 200
→ 더 넓은 Edge 범위

Price Class All
→ 모든 Edge Location 사용
→ 가장 넓은 Global Coverage
```

---

## 3. Cost vs Performance

가장 저렴한 Price Class가 항상 가장 좋은 것은 아닙니다.

Edge 범위를 줄이면 특정 지역의 사용자가 더 먼 Edge Location을 이용해야 할 수 있기 때문입니다.

따라서 다음 요소를 함께 고려합니다.

```text
Cost
+
User Location
+
Latency
+
Global Coverage
```

---

## 4. Exam Scenario

다음과 같은 키워드가 나오면 Price Class를 생각합니다.

```text
CloudFront
+
Reduce Cost
+
Limit Expensive Edge Locations
```

---

## 🔑 핵심 정리

```text
Price Class
= CloudFront가 사용할 Edge 범위 조절

Class 100
→ 제한적 / 비용 절감

Class 200
→ 더 넓은 범위

Class All
→ 전체 범위
```

한 줄 정리:

> **CloudFront의 Edge Coverage와 비용 사이의 균형을 조절한다 = Price Class**

---

## 🇯🇵 日本語 Summary

CloudFront Price Classを利用すると、使用するEdge Locationの範囲を制限してコストを調整できます。

Price Class 100、200、Allがあり、利用範囲が広いほどGlobal Coverageも広くなります。

コストだけでなく、ユーザーのLocationやLatencyも考慮する必要があります。

---

## 🇺🇸 English Summary

CloudFront Price Classes control the range of Edge Locations used by a distribution.

Price Class 100 uses a more limited set of locations, Price Class 200 provides broader coverage, and Price Class All uses all available locations.

The choice balances cost, latency, and global coverage.

---

## 📚 Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Price Class | 料金クラス | 가격 등급 |
| Edge Location | エッジロケーション | 엣지 로케이션 |
| Coverage | カバレッジ | 서비스 범위 |
| Cost | コスト | 비용 |
| Latency | レイテンシー | 지연 시간 |

---

## 📝 Review Questions

<details>
<summary>Q1. CloudFront에서 사용할 Edge Location의 범위를 조절하는 기능은?</summary>

Price Class입니다.

</details>

<details>
<summary>Q2. 가장 넓은 Edge Location 범위를 사용하는 것은?</summary>

Price Class All입니다.

</details>

<details>
<summary>Q3. 가장 저렴한 Price Class가 항상 최선인가?</summary>

아닙니다. 비용뿐 아니라 사용자 위치, Latency, 필요한 Global Coverage도 함께 고려해야 합니다.

</details>
