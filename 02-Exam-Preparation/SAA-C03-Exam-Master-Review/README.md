# 🎯 SAA-C03 Exam Master Review

> [!NOTE]
> **AWS Certified Solutions Architect – Associate (SAA-C03) 시험 대비 전용 정리본**  
> **Edition:** v1.0 · **Date:** 2026-10-21 · **Status:** Draft for review → PDF after final revisions

## 📌 Overview

`01-Cloud-Basics`의 **28개 강의 섹션**을 수강한 뒤, 첫 모의고사 **56%**에서 발견된 부족한 영역을 보완하기 위해 만든 **시험 문제 해결 중심** 정리본이다.

기존 학습 노트처럼 강의 순서에 따라 서비스를 설명하는 대신, **시험 시나리오의 요구사항 → 적절한 AWS 서비스 → 그럴듯한 오답을 탈락시키는 이유**의 순서로 재구성했다. 실제 비공개 기출이나 유출된 문항을 사용하지 않는다.

## 📂 Contents

| File | Purpose |
|---|---|
| [SAA-C03-EXAM-MASTER-GUIDE.md](./SAA-C03-EXAM-MASTER-GUIDE.md) | **단일 마스터 본문**: 4개 도메인, 선택 기준, 함정 30개, 시나리오 12개, 복습 문제 |
| [01-EXAM-EMPHASIS-EVIDENCE.md](./01-EXAM-EMPHASIS-EVIDENCE.md) | **출제 우선순위 근거**: 공식 가이드, 대본에서 확인된 강사 강조, 2026년 후기와 각각의 한계 |
| [02-ERROR-LOG-TEMPLATE.md](./02-ERROR-LOG-TEMPLATE.md) | **실전 오답 기록 양식**: 56% 진단 점수에서 출발하는 보강 훈련 |

## 📊 Official Domain Weights

| Domain | Weight | Focus |
|---|---:|---|
| Secure Architectures | **30%** | IAM / VPC / KMS / S3 Security |
| Resilient Architectures | **26%** | ALB / ASG / Multi-AZ / SQS / DR |
| High-Performing Architectures | **24%** | S3 / EC2 / Database / Networking / Analytics |
| Cost-Optimized Architectures | **20%** | Pricing / Lifecycle / Data Transfer / Right-Sizing |

## 🧭 Study Method

1. 마스터 본문을 읽으며 모르는 주제는 기존 GitHub의 해당 섹션으로 돌아가 복습한다.
2. 새 모의고사 65문항을 **130분 제한 시간**으로 풀고 정답뿐 아니라 확신 없는 문제를 표시한다.
3. `02-ERROR-LOG-TEMPLATE.md`에 **조건·정답 이유·오답 이유**를 기록한다.
4. 한 번 풀었던 문제의 암기 효과를 배제하고, **처음 보는 문제**로 다시 평가한다.
5. 정리본 보완이 마무리되면 PDF로 변환한다.

## 🧪 Upcoming Original Practice Exams (not yet created)

정리본 검수 완료 후 **65문항 × 10세트 = 총 650개의 독창적인 연습 문제**를 별도로 제작할 계획이다. AWS 공식 영역 비중을 참고하되 실제 시험 기출 문제를 복원하거나 그대로 옮기지 않는다. 각 세트에는 제한 시간, 복수 정답 유형, 정답표, 상세 해설 및 오답 선택지 해설을 포함한다.

## 📖 Sources

- [AWS SAA-C03 공식 시험 가이드](https://docs.aws.amazon.com/ko_kr/aws-certification/latest/solutions-architect-associate-03/solutions-architect-associate-03.html)
- [기존 AWS 학습 저장소](https://github.com/luis12211234-coder/AWS-SAA-Study/tree/main/01-Cloud-Basics)
- 학습한 Stephane Maarek 강의 대본·슬라이드 관련 부분 및 2026년 공개 응시 후기(확정 출제 목록이 아님)

## 🇯🇵 日本語 Summary

試験対策専用の総まとめ。4つの公式領域を基準に、要件に合うサービスの選び方と誤答の除外理由を学習する。講義ノートを補完し、模擬試験と誤答分析に活用する。

## 🇺🇸 English Summary

An exam-focused SAA-C03 review guide organized by the four official domains, architectural requirements, service selection criteria, and explanations of common distractors. Review this draft before PDF export and independent practice-test generation.
