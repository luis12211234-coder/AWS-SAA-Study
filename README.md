# AWS SAA-C03 Study Notes

AWS Certified Solutions Architect – Associate(SAA-C03)를 준비하며 작성하는 학습 기록입니다.

강의 내용을 그대로 옮기기보다, 직접 이해한 내용을 한국어로 정리하고 일본어와 영어로 핵심을 다시 설명합니다. 자격증 취득뿐 아니라 AWS 기초 지식, 실습 과정, 문제 해결 과정과 꾸준한 학습 흔적을 남기는 것이 목적입니다.

> Status: In progress — currently studying IAM

## Objectives

- Understand the core services and architecture patterns covered by SAA-C03.
- Record hands-on work, mistakes, and lessons learned.
- Practice explaining cloud concepts in Korean, Japanese, and English.
- Build a public learning portfolio while following AWS security best practices.

## Study Progress

| Section | Status | Notes |
|---|---|---|
| Cloud fundamentals and global infrastructure | In progress | [Open notes](./01-Cloud-Basics/01-AWS-Global-Infrastructure.md) |
| IAM overview | In progress | [Open notes](./01-Cloud-Basics/02-1-IAM-Overview.md) |
| IAM users and groups | In progress | [Open notes](./01-Cloud-Basics/02-2-IAM-Users-and-Groups.md) |
| IAM policies | In progress | [Open notes](./01-Cloud-Basics/02-3-IAM-Policies.md) |
| Shared Responsibility Model | Planned | — |
| EC2 | Planned | — |

## Repository Structure

```text
AWS-SAA-Study/
├─ README.md
└─ 01-Cloud-Basics/
   ├─ README.md
   ├─ 01-AWS-Global-Infrastructure.md
   ├─ 02-1-IAM-Overview.md
   ├─ 02-2-IAM-Users-and-Groups.md
   └─ 02-3-IAM-Policies.md
```

## Documentation Format

Each note generally contains:

1. Korean explanation
2. Exam notes
3. Practical example or hands-on result
4. Japanese summary
5. English summary
6. Vocabulary
7. Review questions

Japanese and English sections are intentionally concise. Their purpose is to practice explaining the key concept clearly rather than translating every sentence.

## Security Rules

- Never commit passwords, access keys, secret keys, session tokens, or MFA information.
- Do not expose personal email addresses, AWS account IDs, or unnecessary resource identifiers.
- Use placeholders in policy examples and screenshots.
- Prefer temporary credentials, MFA, and least-privilege permissions.
- Review files before every commit.

## Learning Resources

- Stephane Maarek, *AWS Certified Solutions Architect Associate SAA-C03*
- [AWS Documentation](https://docs.aws.amazon.com/)
- [AWS Architecture Center](https://aws.amazon.com/architecture/)

## Disclaimer

This is a personal study repository. The notes may be revised as my understanding improves. For production decisions, refer to the latest official AWS documentation.
