# Software Updates Offloading

> Section 18 | AWS Solutions Architect Associate | Lecture study note

## 1. 요구사항
기존 EC2 애플리케이션이 EFS의 소프트웨어 업데이트 파일을 사용자에게 배포한다. 새 버전 출시 직후 동일 파일 다운로드 요청이 폭증해 EC2·네트워크·EFS에 부담이 발생한다. 애플리케이션을 크게 변경하지 않고 비용과 확장성을 개선해야 한다.

## 2. 기존 구조
```text
Users ──> ELB ──> EC2 Auto Scaling Group (Multi-AZ) ──> EFS
```

## 3. 개선 구조
```text
Users ──> CloudFront ──> ELB ──> EC2 ASG (Multi-AZ) ──> EFS
              │
              └── Edge Cache: versioned update files
```

**Cache Miss:** 엣지에 파일이 없다면 CloudFront가 ELB를 통해 원본에서 파일을 받아 사용자에게 전송하고 캐싱한다.

**Cache Hit:** 동일한 캐시 키의 파일이 엣지에 유효하게 저장되어 있으면 CloudFront가 직접 응답한다. 이 요청은 EC2/EFS까지 도달하지 않는다.

업데이트 파일은 버전별로 내용이 고정되는 정적 콘텐츠이므로 CDN 캐싱에 적합하다. 원본 요청이 감소하면 ASG의 확장 필요성, EC2의 파일 전송 부담, EFS 읽기 부담이 줄어들 수 있다.

## 4. 트레이드오프와 구현 포인트
- CloudFront 자체의 요청·데이터 전송 비용은 발생한다. 총비용 절감은 트래픽과 캐시 적중률에 달려 있다.
- `updates/v1.0/file.zip`처럼 버전별 경로를 사용하면 캐시 갱신·무효화 문제를 줄이기 쉽다.
- TTL, Cache-Control, 파일 크기와 CloudFront 캐싱 동작을 점검한다.
- 원본 ALB로의 직접 접근 제한, HTTPS, 필요한 인증 정책을 고려한다.
- EC2/EFS는 그대로 남아 있으므로 전체 애플리케이션이 서버리스가 되는 것은 아니다.
- CPU 절감은 워크로드에 따라 달라진다. 이 사례의 직접적인 효과는 원본 요청과 데이터 전송 감소다.

## 🎯 Exam Notes

- 대량 정적 파일 다운로드 + 기존 EC2 유지 + 글로벌 사용자 = CloudFront 검토.
- CloudFront는 S3뿐 아니라 ALB/HTTP 원본 앞에도 배치 가능.
- Cache Hit는 원본 호출을 줄인다.
- ASG 확장과 원본 대역폭 비용을 줄일 수 있지만 CloudFront 비용을 함께 비교한다.

## 💡 Practical Example

게임 패치 `patch-v3.zip`을 전 세계에 배포할 때 CloudFront를 ELB 앞에 추가한다. 첫 요청은 EC2/EFS에서 파일을 가져오고 이후 동일 파일 요청은 엣지 캐시에서 제공한다.

## 🇯🇵 日本語 Summary

EC2とEFSで配布する静的なソフトウェア更新ファイルの前段にCloudFrontを配置する。エッジキャッシュがヒットすればオリジンサーバーへのアクセスが減り、Auto Scalingやネットワーク負荷を抑えられる。

## 🇺🇸 English Summary

Place CloudFront in front of an existing ELB, EC2 Auto Scaling group, and EFS to cache immutable software updates. Cache hits reduce origin traffic and may lower scaling and bandwidth costs.

## 📚 Vocabulary

Offloading=부하 이전, Origin=원본 서버, Edge Cache=엣지 캐시, Cache Hit/Miss=캐시 적중/미스

## 📝 Review Questions

**Q. 기존 EC2 앱을 재작성하지 않고 정적 업데이트 배포 부하를 줄이는 서비스는?**

<details>
<summary>정답 보기</summary>

Amazon CloudFront를 기존 ELB 앞에 배치한다.

</details>
