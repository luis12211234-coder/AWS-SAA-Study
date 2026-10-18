# 📘 25-13. S3 Gateway Endpoint: NAT Removal & CLI Lab

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `S3 Gateway Endpoint` · `IAM Role` · `S3ReadOnly` · `Route Table` · `CLI Region` · `AWS CLI`

## 🇰🇷 1. 실습 목표와 IAM 역할
목표는 **Private EC2의 일반 인터넷 경로를 삭제한 뒤 S3만 접근**할 수 있도록 만드는 것이다. 기존 BastionHost를 통해 PrivateInstance에 SSH 접속한다. EC2가 S3를 읽을 수 있도록 `AmazonS3ReadOnlyAccess` 정책을 가진 **IAM Role**을 생성하고 EC2에 연결한다.

**역할/정책 = API 접근 권한, 엔드포인트/라우팅 = 네트워크 경로**이므로 둘 모두 충족해야 한다.

## 🇰🇷 2. NAT 제거 전후 확인
```bash
aws s3 ls
curl https://example.com
```

NAT Gateway로 향하는 `0.0.0.0/0` 경로가 있으면 둘 다 접근 가능할 수 있다. PrivateRouteTable에서 해당 경로를 제거하면 일반 인터넷과 AWS S3 퍼블릭 엔드포인트로 향하는 기본 경로가 사라져 둘 다 실패한다.

## 🇰🇷 3. S3 Gateway Endpoint 구성
VPC → Endpoints → Create Endpoint → AWS Services에서 **S3/Gateway**를 선택한다. `DemoVPC`와 **PrivateRouteTable**을 연결하고 Endpoint Policy를 실습용 `Full access`로 둔다. 생성 후 Route Table에 `pl-...(S3) → vpce-...`가 자동으로 추가된다. NAT 기본 경로는 여전히 없으므로 일반 인터넷은 불가능해도 S3 통신은 가능하다.

## 🇰🇷 4. CLI Region으로 발생한 실습 오류
강의에서는 `aws s3 ls`가 계속 실패했다. CLI의 기본 AWS Region이 `us-east-1`이어서 실제 설치된 `eu-central-1` S3 Gateway Endpoint 경로와 맞지 않았기 때문이다.

```bash
aws s3 ls --region eu-central-1
```

이 명령으로 버킷 목록 조회에 성공했다. `--region`은 **API 호출 엔드포인트 리전**을 지정하는 설정이며 단순히 해당 리전 버킷만 나열하는 필터가 아니다. Endpoint 사용 조건으로 DNS·Region·SG/NACL·IAM 정책 및 Endpoint Policy를 함께 점검한다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- IAM 권한과 네트워크 연결은 분리된다.
- S3 Gateway Endpoint 생성 시 연결 Route Table에 Prefix List 경로 자동 추가.
- NAT 없더라도 같은 리전 S3 접근 가능, 일반 인터넷은 불가.
- CLI Region 불일치로 S3 Endpoint 경로를 사용하지 못할 수 있다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. EC2에서 S3 버킷 목록을 보려면 네트워크 경로만 있으면 되는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** 적절한 IAM 권한도 필요하다.

</details>

### Q2. NAT를 삭제한 뒤 S3만 접근시키는 가장 비용 효율적인 방법은?

<details>
<summary>정답 및 해설 보기</summary>

**S3 Gateway Endpoint**를 Private Route Table에 연결.

</details>

### Q3. S3 Gateway Endpoint 생성 후 Route Table에 추가되는 목적지는?

<details>
<summary>정답 및 해설 보기</summary>

**S3 AWS Managed Prefix List(pl-...).**

</details>

### Q4. CLI가 us-east-1로 호출하는데 엔드포인트는 eu-central-1에 있다. 해결은?

<details>
<summary>정답 및 해설 보기</summary>

**`--region eu-central-1`** 등 올바른 API Region 설정.

</details>

### Q5. Endpoint Policy Full access면 EC2의 IAM 권한을 무시하는가?

<details>
<summary>정답 및 해설 보기</summary>

**아니다.** IAM과 버킷 정책이 계속 평가된다.

</details>

## 🇯🇵 日本語 Summary

実習では Private EC2 に S3 読み取り用 IAM Role を付与し、NAT のデフォルトルートを削除した。その後 S3 Gateway Endpoint と Private Route Table の Prefix List ルートを追加し、正しいリージョンで `aws s3 ls` を実行して接続を確認した。

## 🇺🇸 English Summary

The lab attaches an S3-read IAM role to private EC2, removes the NAT default route, and adds an S3 gateway endpoint to the private route table. S3 works without internet egress when the CLI uses the matching regional service endpoint and authorization permits access.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| AWS CLI | AWS 명령줄 인터페이스 | AWS CLI |
| IAM Role | IAM 역할 | IAM ロール |
| Gateway Route | 엔드포인트 라우트 | エンドポイントルート |
| Region Override | 리전 명시 | リージョン指定 |
