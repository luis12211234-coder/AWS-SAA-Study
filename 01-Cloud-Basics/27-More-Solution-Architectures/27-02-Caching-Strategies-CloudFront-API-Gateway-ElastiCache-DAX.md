# 📘 27-02. Caching Strategies

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `캐시의 원리`, `CloudFront`, `API Gateway 캐시`, `ElastiCache와 DAX`

## 🇰🇷 1. 캐시의 원리

캐시는 자주 읽는 데이터를 원본보다 가까운 곳에 저장해 지연 시간과 원본 부하를 줄인다. Cache Hit는 캐시에서 응답, Miss는 원본 조회 후 저장한다. TTL과 무효화(Invalidation)는 신선도와 성능의 균형이다.

## 🇰🇷 2. CloudFront

전 세계 엣지 로케이션에서 정적 및 캐시 가능한 동적 HTTP 콘텐츠를 제공한다. S3, ALB 등의 Origin과 조합하며 캐시 키와 TTL, Invalidation을 설계한다.

## 🇰🇷 3. API Gateway 캐시

API Gateway **REST API**의 스테이지 캐시를 통해 응답을 캐시할 수 있다. 모든 HTTP API에서 동일한 네이티브 캐시 기능을 제공하는 것은 아니다. 요청 인증·캐시 키·TTL에 주의한다.

## 🇰🇷 4. ElastiCache와 DAX

ElastiCache for Redis/Valkey 또는 Memcached는 애플리케이션이 사용하는 범용 인메모리 캐시다. DynamoDB Accelerator(DAX)는 DynamoDB에 특화된 인메모리 캐시로 읽기 지연을 줄인다. 캐시 일관성·TTL을 고려한다.

## 🇰🇷 5. 엔드 투 엔드 구조

Client → CloudFront → API Gateway → Lambda/EC2 → ElastiCache/DAX → RDS/DynamoDB 같은 계층별 캐시 구조가 가능하다. API는 프로그램 간 기능·데이터 접근 계약이며 REST API는 주로 HTTP 메서드와 리소스를 사용한다. DB 자체에도 버퍼 캐시가 있으므로 DB가 캐시를 전혀 못한다는 뜻은 아니다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- **캐시의 원리:** 캐시는 자주 읽는 데이터를 원본보다 가까운 곳에 저장해 지연 시간과 원본 부하를 줄인다.
- **CloudFront:** 전 세계 엣지 로케이션에서 정적 및 캐시 가능한 동적 HTTP 콘텐츠를 제공한다.
- **API Gateway 캐시:** API Gateway **REST API**의 스테이지 캐시를 통해 응답을 캐시할 수 있다.
- **ElastiCache와 DAX:** ElastiCache for Redis/Valkey 또는 Memcached는 애플리케이션이 사용하는 범용 인메모리 캐시다.
- **엔드 투 엔드 구조:** Client → CloudFront → API Gateway → Lambda/EC2 → ElastiCache/DAX → RDS/DynamoDB 같은 계층별 캐시 구조가 가능하다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. Cache Hit와 Cache Miss 차이는?

<details>
<summary>정답 및 해설 보기</summary>

Hit는 캐시에서 바로 응답, Miss는 원본 조회가 필요하다.

</details>

### Q2. 글로벌 엣지 캐시 서비스는?

<details>
<summary>정답 및 해설 보기</summary>

Amazon CloudFront.

</details>

### Q3. API Gateway 네이티브 스테이지 캐시가 대표적으로 제공되는 API 유형은?

<details>
<summary>정답 및 해설 보기</summary>

REST API. HTTP API와 혼동하지 않는다.

</details>

### Q4. DynamoDB 읽기 성능 최적화용 전용 캐시는?

<details>
<summary>정답 및 해설 보기</summary>

DAX.

</details>

### Q5. TTL을 지나치게 길게 잡으면 생길 수 있는 문제는?

<details>
<summary>정답 및 해설 보기</summary>

오래된 데이터를 반환할 가능성이 커진다.

</details>

## 🇯🇵 日本語 Summary

Caching Strategies：AWS SAA-C03 では、要件に応じてサービスを組み合わせ、可用性・性能・運用自動化を設計する。主要な選択基準と障害時の動作を理解する。

## 🇺🇸 English Summary

Caching Strategies: Understand the architecture, trade-offs, failure handling, and SAA-C03 service selection criteria. Combine managed AWS services based on performance, availability, and operational requirements.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| 캐시의 원리 | 캐시의 원리 | 캐시의 원리 |
| CloudFront | CloudFront | CloudFront |
| API Gateway 캐시 | API Gateway 캐시 | API Gateway 캐시 |
| ElastiCache와 DAX | ElastiCache와 DAX | ElastiCache와 DAX |
| 엔드 투 엔드 구조 | 엔드 투 엔드 구조 | 엔드 투 엔드 구조 |
