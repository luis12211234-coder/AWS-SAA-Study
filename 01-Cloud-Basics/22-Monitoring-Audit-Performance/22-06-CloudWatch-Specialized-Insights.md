# 📘 22-06. CloudWatch Specialized Insights (4 Types)

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Container Insights` · `Lambda Insights` · `Contributor Insights` · `Application Insights` · `Cold Start`

## 🇰🇷 1. 어떤 Insights를 선택할까?

| 서비스 | 모니터링 대상 / 목적 | 대표 시험 단서 |
|---|---|---|
| **Container Insights** | ECS·EKS·Kubernetes·Fargate 컨테이너 및 클러스터 | Container, Pod, ECS, EKS, Fargate |
| **Lambda Insights** | Lambda 함수의 상세 성능 분석 | Lambda 메모리·CPU·Cold Start |
| **Contributor Insights** | 로그·메트릭에서 기여도가 큰 대상을 순위화 | Top N, 상위 IP, Top Talkers |
| **Application Insights** | 애플리케이션 및 연관 AWS 리소스의 문제 탐지·대시보드 | 애플리케이션 전체 장애 조사 |

## 🇰🇷 2. CloudWatch Container Insights

- 컨테이너 환경의 CPU·메모리·네트워크 등 지표와 로그를 수집·집계·시각화한다.
- 지원 대상: Amazon ECS, Amazon EKS, Kubernetes on EC2, Fargate 환경 등.
- EKS / Kubernetes에서 실행할 때 **컨테이너화된 CloudWatch Agent** 같은 수집 구성 요소를 사용한다. 현재는 다양한 관측성 애드온과 구성 방식이 있으므로 구현에 따라 확인한다.
- **SAA 예시:** “EKS Pod마다 메모리 사용량을 확인” → **Container Insights**.

## 🇰🇷 3. CloudWatch Lambda Insights

- Lambda 함수의 기본 호출 횟수·Errors·Duration보다 상세한 시스템 수준 지표를 제공한다.
- 메모리·CPU 시간·네트워크·Cold Start 등 함수 실행 문제 조사.
- CloudWatch Lambda Insights용 확장 기능을 **Lambda Layer**로 제공할 수 있다. Layer는 Lambda 함수에 재사용 가능한 코드·라이브러리·확장 기능을 추가하는 패키지다.
- **SAA 예시:** “Lambda Cold Start와 메모리 병목 분석” → **Lambda Insights**.

## 🇰🇷 4. CloudWatch Contributor Insights

**Contributor = 특정 현상에 가장 많이 기여하는 대상(예: IP 주소, 호스트, DNS 이름)**.

```text
VPC Flow Logs → CloudWatch Logs → Contributor Insights Rules
                                   ↓
                             Top 10 Source IPs
```

- 상위 N개 기여자, 고유 기여자 수, 자주 발생하는 값 등을 시간 흐름으로 분석한다.
- VPC Flow Logs에서 가장 많은 요청을 발생시키는 IP, DNS 로그에서 오류가 잦은 도메인 등을 파악하는 데 사용.
- **상위 트래픽 발생자 = 공격자**라는 뜻은 아니다. 별도의 해석과 보안 조사가 필요하다.
- CloudWatch Logs용 Rule 또는 일부 AWS 서비스의 내장 규칙을 사용할 수 있다.
- **SAA 예시:** “Top 10 IP, Top Talkers” → **Contributor Insights**.

## 🇰🇷 5. CloudWatch Application Insights

- 애플리케이션과 연관된 리소스(EC2, RDS, ALB, Lambda 등)의 관측 데이터를 종합해 **애플리케이션 관점의 문제 탐지 및 조사**를 지원한다.
- 기술 스택에 맞춘 모니터링과 대시보드 구성을 자동화한다.
- 일부 문제 이벤트를 **EventBridge / Systems Manager OpsCenter**와 연동할 수 있다.
- 예: 웹 사이트에서 응답 지연 발생 → ALB·EC2·RDS 지표 및 문제 정보를 함께 분석.
- 자동 분석이 문제를 반드시 정확하게 진단·수정한다는 뜻은 아니다.

## 🇰🇷 6. SAA 선택 연습

| 문제 | 서비스 |
|---|---|
| Fargate Task와 EKS Pod 성능 | Container Insights |
| Lambda Cold Start와 메모리 분석 | Lambda Insights |
| 트래픽 발생량 상위 10개 IP | Contributor Insights |
| 애플리케이션 관련 AWS 자원의 종합적 장애 가시성 | Application Insights |

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- 컨테이너 환경의 CPU·메모리·네트워크 등 지표와 로그를 수집·집계·시각화한다.
- 지원 대상: Amazon ECS, Amazon EKS, Kubernetes on EC2, Fargate 환경 등.
- EKS / Kubernetes에서 실행할 때 **컨테이너화된 CloudWatch Agent** 같은 수집 구성 요소를 사용한다. 현재는 다양한 관측성 애드온과 구성 방식이 있으므로 구현에 따라 확인한다.
- **SAA 예시:** “EKS Pod마다 메모리 사용량을 확인” → **Container Insights**.
- Lambda 함수의 기본 호출 횟수·Errors·Duration보다 상세한 시스템 수준 지표를 제공한다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. EKS Pod별 메모리 지표와 컨테이너 성능을 분석하려면?

<details>
<summary>정답 및 해설 보기</summary>

**정답:** CloudWatch Container Insights

Container Insights는 ECS/EKS/Kubernetes/Fargate의 컨테이너·클러스터 수준 지표와 로그를 분석한다.

</details>

### Q2. VPC Flow Logs를 분석해 트래픽을 가장 많이 발생시킨 IP 상위 10개를 보고 싶다. 무엇을 사용하나?

<details>
<summary>정답 및 해설 보기</summary>

**정답:** CloudWatch Contributor Insights

Contributor Insights는 로그 등에서 Top N 기여자를 찾는 기능이다. Top IP가 무조건 악성이라는 뜻은 아니다.

</details>

### Q3. Lambda의 Cold Start와 상세 메모리 사용량을 조사하고 싶다. 어떤 기능인가?

<details>
<summary>정답 및 해설 보기</summary>

**정답:** Lambda Insights

Lambda Insights는 Lambda 런타임의 상세 시스템 성능을 보여 준다.

</details>

### Q4. 어떤 Insights를 선택할까?의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

컨테이너 환경의 CPU·메모리·네트워크 등 지표와 로그를 수집·집계·시각화한다.

</details>

### Q5. CloudWatch Container Insights의 핵심 개념은 무엇인가?

<details>
<summary>정답 및 해설 보기</summary>

- 컨테이너 환경의 CPU·메모리·네트워크 등 지표와 로그를 수집·집계·시각화한다.
- 지원 대상: Amazon ECS, Amazon EKS, Kubernetes on EC2, Fargate 환경 등.
- EKS / Kubernetes에서 실행할 때 **컨테이너화된 CloudWatch Agent** 같은 수집 구성 요소를 사용한다. 현재는 다양한 관측성 애드온과 구성 방식이 있으므로 구현에 따라 확인한다.
- **SAA 예시:** “EKS Pod마다 메모리 사용량을 확인” → **Container Insights**.

</details>

## 🇯🇵 日本語 Summary

Container Insights は ECS・EKS・Fargate などのコンテナ環境を監視する。Lambda Insights は Lambda 関数の詳細な性能指標とコールドスタートを分析する。Contributor Insights はログから上位 N 件の送信元 IP など主要な寄与要因を識別する。Application Insights はアプリケーション全体の障害分析を支援する。

**試験の注意点:** コンテナ・Lambda・上位 N 件・アプリ全体という対象を見分ければ適切な Insights 機能を選択できる。

## 🇺🇸 English Summary

Container Insights monitors containerized workloads. Lambda Insights provides detailed Lambda function performance metrics. Contributor Insights identifies top contributors such as source IPs and hosts. Application Insights helps detect and troubleshoot problems across application resources. For SAA, match the monitoring requirement to the correct Insights feature.

**Exam tip:** Match the question keyword to the workload: containers, Lambda runtimes, top contributors, or application-wide troubleshooting.

## 📚 Vocabulary

> GitHub 검색 시 영어 표제어 그대로 검색하면 관련 설명으로 이동하기 쉽다.

| English | 한국어 | 日本語 |
|---|---|---|
| Container Insights | 컨테이너 모니터링 | コンテナ監視 |
| Lambda Insights | Lambda 상세 모니터링 | Lambda 詳細監視 |
| Contributor Insights | 상위 기여자 분석 | コントリビューター分析 |
| Application Insights | 앱 전체 문제 탐지 | アプリケーション監視 |
| Cold Start | 초기 실행 지연 | コールドスタート |
| Top N Contributors | 상위 N개 기여 대상 | 上位 N 件の寄与要因 |
| Containerized Agent | 컨테이너형 에이전트 | コンテナ化エージェント |
