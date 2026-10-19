# 📘 26-06. On-Premises VM Migration & Legacy Services

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `Amazon Linux 2` · `VM Import/Export` · `Application Discovery Service` · `Migration Hub` · `Server Migration Service` · `AWS Transform`

## 🇰🇷 1. 무엇을 옮기는가: VM 전체 vs DB 데이터
온프레미스 서버의 OS·앱·설정·디스크를 새 EC2에서 최대한 재현하려면 **VM/서버 마이그레이션**, 기존 DB의 데이터만 새로운 RDS/Aurora로 이동하려면 **DMS**가 알맞다. 하나의 프로젝트에서 두 방식을 함께 쓸 수도 있다.

## 🇰🇷 2. Amazon Linux 2를 온프레미스에서도 실행
Amazon Linux는 EC2에 설치 가능한 Linux 기반 운영체제다. Amazon Linux 2의 온프레미스용 VM 이미지는 VMware, KVM, VirtualBox, Hyper-V 등의 가상화 플랫폼에서 사용할 수 있었다. 강의의 'ISO' 표현은 일반화가 부정확하다. 배포 형식은 플랫폼에 맞는 **VMDK/VDI/VHD 등 가상 디스크 이미지**인 경우가 많다. VM 환경의 `user-data` 같은 초기 구성 메커니즘도 고려할 수 있다.

## 🇰🇷 3. VM Import/Export
```text
온프레미스 VM Image ── VM Import ─▶ EC2에서 실행
EC2(지원되는 VM 이미지) ─ VM Export ▶ 온프레미스 가상화 환경
```
기존 VM 이미지를 가져와 EC2로 실행하거나, 지원 조건 아래 AWS 측 VM을 다시 내보내는 도구다. **모든 AMI나 모든 라이선스의 VM이 자유롭게 내보내지는 것은 아니다.**

## 🇰🇷 4. 서버를 옮기기 전 조사와 진행 관리(과거 명칭)
- **AWS Application Discovery Service**: 서버 수, CPU/메모리/디스크 사용량, 네트워크 연결 및 종속성을 조사. '웹 서버만 옮겨도 되는가? 그 뒤의 DB는?' 같은 질문에 필요한 자료를 준다.
- **AWS Migration Hub**: 여러 마이그레이션 프로젝트의 상태를 통합 조회·추적.
- **AWS Server Migration Service(SMS)**: 예전의 온프레미스 VM 증분 복제/서버 이전 서비스. **2023-04-01 종료**.

이들은 성격이 다르다. **Discovery는 조사, Hub는 관리, MGN은 실제 서버 복제**를 담당한다. Discovery가 DB를 반드시 DMS로 옮기라고 자동 결정하는 것은 아니다.

## 🇰🇷 5. 2026년 기준 정리
**Application Discovery Service와 Migration Hub는 2025-11-07부터 신규 고객이 이용할 수 없으며**, AWS는 **AWS Transform**을 조사·계획·추적에 권장한다. 기존 Discovery Connector는 2025-11-17부터 새 데이터 수집을 받지 않는다고 안내됐다. 기존 학습용 서비스 이름을 알아두되 실제 신규 프로젝트에서는 최신 가용성을 확인한다.

**MGN은 2026년 6월 AWS Transform MGN으로 명칭이 변경**됐으며 서버 리호스팅 기능은 유지된다. 이는 다음 노트에서 자세히 다룬다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- VM Import/Export = VM 이미지 가져오기/내보내기, 지원 조건 확인.
- Application Discovery Service = 자원·종속성 조사(기존 서비스).
- Migration Hub = 여러 이전 작업 추적(기존 서비스).
- SMS는 2023-04 종료, 서버 이전은 MGN/Transform MGN.
- DB 서버 전체 이전(MGN)과 DB 데이터 이전(DMS)은 서로 다른 선택이며 함께 사용 가능.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. 온프레미스 VM 이미지를 AWS EC2로 가져오는 기능은?

<details>
<summary>정답 및 해설 보기</summary>

**VM Import/Export의 Import** 기능이다.

</details>

### Q2. 기존 서버의 통신 종속성 지도를 만들기 위한 기존 서비스는?

<details>
<summary>정답 및 해설 보기</summary>

**Application Discovery Service**이다. 신규 프로젝트는 AWS Transform도 검토한다.

</details>

### Q3. 여러 서버의 이전 진행 상태를 추적하던 서비스는?

<details>
<summary>정답 및 해설 보기</summary>

**Migration Hub.** 2025-11부터 신규 고객은 이용할 수 없다.

</details>

### Q4. SMS(Server Migration Service)는 2026년에도 신규로 사용할 수 있나?

<details>
<summary>정답 및 해설 보기</summary>

**아니다. 2023-04-01 종료**됐다.

</details>

### Q5. 회사 서버의 MySQL을 OS와 함께 EC2로 그대로 복제하려면 DMS가 필수인가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 서버 전체 리호스팅은 **MGN**을 활용할 수 있다.

</details>

## 🇯🇵 日本語 Summary

VM Import/Export は VM イメージと EC2 間の移行機能。旧 Application Discovery Service はサーバー・依存関係の調査、Migration Hub は進捗管理、SMS は旧サーバー移行サービスだった。2026 年の新規計画では AWS Transform と Transform MGN を区別する。

## 🇺🇸 English Summary

VM Import/Export moves supported VM images to and from EC2. Legacy Application Discovery Service assessed servers, Migration Hub tracked projects, and SMS handled older server migrations. In 2026, AWS Transform and Transform MGN are the current planning and rehosting paths.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| Virtual Machine | 가상 머신 | 仮想マシン |
| VM Import/Export | VM 가져오기/내보내기 | VM のインポート・エクスポート |
| Dependency Mapping | 의존성 매핑 | 依存関係マッピング |
| Inventory | 자산 목록·인벤토리 | インベントリ |
| Server Migration | 서버 이전 | サーバー移行 |

## 🔗 Official References

- https://docs.aws.amazon.com/application-discovery/latest/userguide/application-discovery-service-availability-change.html
- https://docs.aws.amazon.com/migrationhub/latest/ug/migrationhub-availability-change.html
- https://docs.aws.amazon.com/general/latest/gr/full_shutdown_services.html
