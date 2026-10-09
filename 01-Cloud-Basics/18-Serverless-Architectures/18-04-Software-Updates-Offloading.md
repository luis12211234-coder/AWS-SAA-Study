# 18-04. Software Updates Offloading

## 1. Overview

기존 EC2 기반 애플리케이션에서 소프트웨어 업데이트 파일을 대량 배포할 때 발생하는 비용과 서버 부하를 줄이는 아키텍처를 학습한다.

핵심은 기존 애플리케이션을 크게 변경하지 않고 CloudFront를 도입하는 것이다.

## 2. Requirements

- 기존 EC2 애플리케이션 유지
- 여러 Availability Zone에서 서비스 운영
- 소프트웨어 업데이트 파일 제공
- 신규 업데이트 출시 시 대량 다운로드 요청 처리
- CPU 및 네트워크 부하 감소
- 인프라 비용 절감
- 글로벌 사용자에 대한 확장성 개선

## 3. Initial Architecture

```text
             Users
               |
               v
       Elastic Load Balancer
               |
               v
        Auto Scaling Group
         /      |      \
        v       v       v
      EC2      EC2     EC2
      AZ-A     AZ-B    AZ-C
         \      |      /
                v
            Amazon EFS
          Software Files
```

### EC2 Auto Scaling Group

사용자 요청량이 증가하면 추가 EC2 인스턴스를 실행할 수 있다.

### Elastic Load Balancer

사용자 요청을 여러 EC2 인스턴스에 분산한다.

### Amazon EFS

여러 EC2 인스턴스가 동일한 소프트웨어 업데이트 파일에 접근할 수 있는 공유 파일 시스템이다.

## 4. Architecture Problem

새로운 소프트웨어 업데이트가 출시되면 수많은 사용자가 동일한 파일을 다운로드한다.

```text
10,000 Users
      |
      v
     ELB
      |
      v
   EC2 Fleet
      |
      v
     EFS
```

동일한 정적 파일을 반복적으로 전송하기 때문에 원본 인프라에 부담이 발생한다.

### Potential Problems

- 높은 원본 요청량
- EC2 네트워크 처리 부담 증가
- EC2 Auto Scaling 확장 가능성
- EFS 읽기 작업 증가
- 데이터 전송 및 컴퓨팅 비용 증가

## 5. Improved Architecture

CloudFront를 기존 Load Balancer 앞에 배치한다.

```text
             Global Users
                  |
                  v
           Amazon CloudFront
              Edge Cache
                  |
                  | Cache Miss
                  v
        Elastic Load Balancer
                  |
                  v
           Auto Scaling Group
            /      |      \
           v       v       v
         EC2      EC2     EC2
           \       |      /
                   v
               Amazon EFS
```

## 6. Cache Hit & Cache Miss

### Cache Miss

CloudFront에 파일이 없으면 Origin에서 가져온다.

```text
User
  |
  v
CloudFront
  |
  | Cache Miss
  v
ELB
  |
  v
EC2
  |
  v
EFS
  |
  v
Software Update
  |
  v
CloudFront Cache
  |
  v
User
```

### Cache Hit

CloudFront에 파일이 있으면 Origin에 요청하지 않고 전달한다.

```text
User
  |
  v
CloudFront
  |
  | Cache Hit
  v
Cached Software Update
  |
  v
User
```

이 경우 ELB, EC2, EFS에 해당 다운로드 요청이 전달되지 않는다.

## 7. Why CloudFront?

### Static Content

소프트웨어 업데이트 파일은 일반적으로 버전별로 고정된 정적 콘텐츠다.

예시:

```text
updates/
├── app-v1.0.zip
├── app-v1.1.zip
└── app-v2.0.zip
```

버전별 파일명을 사용하면 캐시 관리에 유리하다.

### Reduced Origin Load

반복적인 파일 요청을 CloudFront가 처리하므로 원본 요청이 감소한다.

### Improved Scalability

CloudFront는 관리형 CDN으로 대규모 글로벌 트래픽을 처리하도록 설계되어 있다.

### Cost Optimization

원본 서버의 처리량과 확장 요구가 감소하면 EC2 및 관련 네트워크 비용을 절감할 가능성이 있다.

단, CloudFront 사용 비용도 발생하므로 실제 비용 절감 여부는 측정해야 한다.

## 8. Important Design Considerations

### Cache Policy

정적 파일의 Cache-Control 및 TTL을 적절히 설정한다.

### Versioned Files

기존 파일을 덮어쓰는 대신 버전별 파일명을 사용하면 오래된 캐시로 인한 문제를 줄일 수 있다.

### Origin Protection

사용자가 CloudFront를 우회해 원본 ALB에 직접 접근하는 문제를 고려해야 한다.

### Cost Evaluation

도입 전후 다음 지표를 비교한다.

- CloudFront Cache Hit Ratio
- Origin Request Count
- EC2 Network Throughput
- EC2 CPU Utilization
- Auto Scaling Activity
- Total Data Transfer Cost

## 9. Before vs After

| Item | Before | After |
|---|---|---|
| Entry Point | ELB | CloudFront |
| Static File Delivery | EC2 | CloudFront Cache |
| Shared Storage | EFS | EFS |
| Compute | EC2 ASG | EC2 ASG |
| Origin Requests | High | Potentially Lower |
| Global Scalability | Origin-Dependent | CDN-Assisted |
| Application Rewrite | Not Required | Usually Minimal |

CloudFront를 도입해도 EC2와 EFS가 사라지는 것은 아니다.

기존 아키텍처 앞에 캐시 계층을 추가하는 방식이다.

## 🎯 Exam Notes

- Static Content Distribution: CloudFront
- Edge Caching: CloudFront
- Existing EC2 Application Optimization: CloudFront in Front of ELB
- Shared File Storage: Amazon EFS
- Automatic EC2 Scaling: Auto Scaling Group
- Reduced Origin Traffic: Cache Hit
- Cache-Friendly Software Distribution: Versioned Static Files

## 💡 Practical Example

게임 회사가 2GB 크기의 업데이트 파일을 전 세계 사용자에게 배포한다.

기존에는 사용자가 업데이트를 다운로드할 때마다 EC2가 파일을 전달했다.

CloudFront 도입 후에는 캐싱된 업데이트 파일을 엣지에서 제공하므로 원본 서버가 처리해야 하는 반복 다운로드 요청을 줄일 수 있다.

## 🇯🇵 日本語 Summary

Software Updates Offloading は、既存の EC2 アプリケーションの前段に CloudFront を配置し、静的な更新ファイルをエッジでキャッシュする設計例である。これにより、オリジンサーバーへのリクエストを減らし、EC2 の負荷とネットワークコストを削減できる可能性がある。

## 🇺🇸 English Summary

Software Updates Offloading demonstrates how CloudFront can reduce the load on an existing EC2-based application. By caching static software update files at edge locations, CloudFront reduces repeated origin requests and can improve global scalability while potentially lowering infrastructure costs.

## 📚 Vocabulary

| Term | Meaning |
|---|---|
| Offloading | 기존 시스템의 작업 부담을 다른 계층으로 이전 |
| Origin | 원본 서버 |
| Edge Cache | 엣지 캐시 |
| Cache Hit Ratio | 캐시 적중률 |
| Static Content | 정적 콘텐츠 |
| Auto Scaling | 자동 확장 |
| Bandwidth | 네트워크 대역폭 |
| Cache Invalidation | 캐시 무효화 |
| Immutable File | 변경되지 않는 파일 |
| Cost Optimization | 비용 최적화 |

## 📝 Review Questions

### Q1. 기존 EC2 애플리케이션에서 대량의 정적 파일 다운로드를 최적화하려면?

<details>
<summary>정답 보기</summary>

CloudFront를 기존 ELB 앞에 배치한다.

</details>

### Q2. CloudFront Cache Hit가 발생하면 EC2와 EFS는 어떻게 되는가?

<details>
<summary>정답 보기</summary>

CloudFront가 캐싱된 파일을 직접 반환하므로 해당 요청은 원본 EC2와 EFS에 전달되지 않는다.

</details>

### Q3. CloudFront를 도입하면 EC2가 완전히 필요 없어지는가?

<details>
<summary>정답 보기</summary>

아니다.

기존 EC2 애플리케이션은 유지되며 CloudFront가 정적 파일 전달 부담을 줄여준다.

</details>