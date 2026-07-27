# AWS SAA-C03 Study Notes

AWS Certified Solutions Architect – Associate(SAA-C03)를 준비하며 작성하는 학습 기록입니다.

강의 내용을 그대로 옮기기보다, 직접 이해한 내용을 한국어로 정리하고 일본어와 영어로 핵심을 다시 설명합니다. 자격증 취득뿐 아니라 AWS 기초 지식, 실습 과정, 문제 해결 과정과 꾸준한 학습 흔적을 남기는 것이 목적입니다.

> Status: IAM section completed — next: Amazon EC2

## Objectives

- Understand the core services and architecture patterns covered by SAA-C03.
- Record hands-on work, mistakes, and lessons learned.
- Practice explaining cloud concepts in Korean, Japanese, and English.
- Build a public learning portfolio while following AWS security best practices.

## Study Progress

| Section | Status | Notes |
|---|---|---|
| Cloud fundamentals and global infrastructure | Completed | [Open notes](./01-Cloud-Basics/01-AWS-Global-Infrastructure.md) |
| IAM overview | Completed | [Open notes](./01-Cloud-Basics/02-1-IAM-Overview.md) |
| IAM users and groups | Completed | [Open notes](./01-Cloud-Basics/02-2-IAM-Users-and-Groups.md) |
| IAM policies | Completed | [Open notes](./01-Cloud-Basics/02-3-IAM-Policies.md) |
| IAM password policy and MFA | Completed | [Open notes](./01-Cloud-Basics/02-4-IAM-Password-Policy-and-MFA.md) |
| IAM access keys, CLI, SDK, and CloudShell | Completed | [Open notes](./01-Cloud-Basics/02-5-IAM-Access-Keys-CLI-SDK-and-CloudShell.md) |
| IAM roles | Completed | [Open notes](./01-Cloud-Basics/02-6-IAM-Roles.md) |
| IAM security tools and best practices | Completed | [Open notes](./01-Cloud-Basics/02-7-IAM-Security-Tools-and-Best-Practices.md) |
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
   ├─ 02-3-IAM-Policies.md
   ├─ 02-4-IAM-Password-Policy-and-MFA.md
   ├─ 02-5-IAM-Access-Keys-CLI-SDK-and-CloudShell.md
   ├─ 02-6-IAM-Roles.md
   └─ 02-7-IAM-Security-Tools-and-Best-Practices.md
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
