# 📘 24-07. Parameter Store Hands-On: CLI & Lambda SDK

> [!NOTE]
> **AWS SAA-C03 Study Note**  
> 핵심 키워드: `GetParameters` · `GetParametersByPath` · `WithDecryption` · `boto3` · `Lambda Execution Role` · `Environment Variables` · `IAM` · `KMS`

## 🇰🇷 1. 실습의 목적과 사전 설정
직전 강의에서는 설정값을 코드에 직접 적지 않기 위해 Parameter Store를 사용한다고 배웠다. 이번 실습은 **사람이 CLI로 읽는 것**과 **Lambda 프로그램이 AWS SDK로 읽는 것**이 같은 API 개념임을 확인한다.

```text
/my-app/dev/db-url           String       dev.database.example.com:3306
/my-app/dev/db-password      SecureString (KMS alias/tutorial)
/my-app/prod/db-url          String       prod.database.example.com:3306
/my-app/prod/db-password     SecureString (KMS alias/tutorial)
```

콘솔에서 `Create parameter` → 이름·유형·설명·값 → SecureString이면 KMS Key 선택 → 생성한다. 매개변수마다 버전, 수정 기록, 태그 등을 확인할 수 있다. 강의의 주소와 비밀번호는 실제 DB를 만들지 않은 **예시 데이터**다.

## 🇰🇷 2. CLI: 지정한 이름으로 조회
```bash
aws ssm get-parameters \
  --names /my-app/dev/db-url /my-app/dev/db-password

aws ssm get-parameters \
  --names /my-app/dev/db-url /my-app/dev/db-password \
  --with-decryption
```

`--with-decryption`은 **SecureString을 평문으로 복호화해 달라**는 요청이다. 해당 KMS Key에 대한 `kms:Decrypt` 권한 없이는 복호화할 수 없다. 단순 String 조회에는 KMS Decrypt가 필요하지 않다.

## 🇰🇷 3. CLI: 경로로 여러 매개변수 조회
```bash
aws ssm get-parameters-by-path --path /my-app/dev/ --with-decryption
aws ssm get-parameters-by-path --path /my-app/ --recursive --with-decryption
```

첫 명령은 Dev 경로의 바로 아래 항목, 둘째는 `--recursive`로 Dev/Prod 하위 항목까지 조회한다. **상위 경로 조회 권한을 광범위하게 주면 Prod 암호도 함께 노출**될 수 있으므로 실무 IAM은 경로를 좁게 관리한다.

## 🇰🇷 4. Lambda: boto3 SDK와 실행 역할
Lambda에는 Python 코드가 실행되며, AWS SDK for Python인 **boto3**로 SSM API를 호출할 수 있다. `import boto`는 강의에서 실제로 발생한 오류이며 정확한 모듈은 `boto3`다.

```python
import os
import boto3

ssm = boto3.client('ssm')

def lambda_handler(event, context):
    stage = os.environ['DEV_OR_PROD']
    base = f'/my-app/{stage}'
    res = ssm.get_parameters(
        Names=[f'{base}/db-url', f'{base}/db-password'],
        WithDecryption=True,
    )
    by_name = {item['Name']: item['Value'] for item in res['Parameters']}
    # 실제 코드에서는 비밀번호를 로그로 출력하지 않는다.
    db_url = by_name[f'{base}/db-url']
    db_password = by_name[f'{base}/db-password']
    return {'ready': bool(db_url and db_password)}
```

Lambda 실행 역할(Execution Role)은 AWS 서비스에 API를 호출할 때 쓰는 IAM Role이다. 기본 역할은 CloudWatch Logs 기록 권한만 있을 수 있다. Parameter Store 접근을 위해 **`ssm:GetParameters`**를 추가하고, SecureString 평문 조회에는 **`kms:Decrypt`** 및 키 정책의 허용이 필요하다.

## 🇰🇷 5. 실습에서 나타난 오류 3가지
1. `No module named boto` → 잘못된 Python import. **boto3 사용**.
2. `AccessDeniedException: ssm:GetParameters` → **SSM IAM 조회 권한 부족**.
3. `AccessDeniedException: KMS Decrypt` → **KMS 권한/Key Policy 부족**.

IAM 변경은 전파 시간이 걸려 즉시 반영되지 않을 수 있다. `DEV_OR_PROD=dev` 또는 `prod` 환경 변수로 같은 코드를 다른 매개변수 경로에 연결할 수 있다. 하지만 실무에서는 Dev 함수에 Prod 비밀번호 조회 권한까지 부여하지 않는다.

## 🎯 Exam Notes

> [!IMPORTANT]
> SAA-C03에서 구분해야 할 핵심 포인트입니다.

- CLI와 boto3는 같은 SSM API를 서로 다른 방식으로 호출한다.
- SecureString 평문 조회 시 `WithDecryption=True`와 KMS Decrypt 권한이 핵심.
- Lambda는 Execution Role의 AWS API 권한으로 SSM/KMS에 접근한다.
- `GetParametersByPath --recursive`는 부모 경로 허용 범위의 하위 값을 읽는다.
- Lambda CloudWatch Logs에 비밀번호를 `print()`하지 않는다.

## 📝 Review Questions

> [!TIP]
> 먼저 답을 생각한 뒤 **정답 및 해설**을 펼쳐 확인하세요.

### Q1. CLI에서 일반 String과 SecureString을 모두 평문으로 읽으려면 추가하는 플래그는?

<details>
<summary>정답 및 해설 보기</summary>

**--with-decryption.** 단 SecureString은 KMS Decrypt 권한이 있어야 복호화된다.

</details>

### Q2. Lambda에서 SSM GetParameters를 호출했지만 AccessDenied가 나온다. 어디를 먼저 확인해야 하나?

<details>
<summary>정답 및 해설 보기</summary>

**Lambda Execution Role의 ssm:GetParameters 허용.** 실행 역할이 Lambda의 AWS API 권한을 결정한다.

</details>

### Q3. SSM 조회 성공 이후 SecureString의 복호화에서 AccessDenied가 발생했다. 해결책은?

<details>
<summary>정답 및 해설 보기</summary>

**KMS Decrypt 권한과 Key Policy**를 확인한다. SSM 접근 권한과는 별개다.

</details>

### Q4. `--path /my-app/ --recursive`는 무엇을 조회하는가?

<details>
<summary>정답 및 해설 보기</summary>

**/my-app 하위의 Dev와 Prod 등 모든 하위 경로의 접근 가능한 값.** 좁은 IAM 권한이 중요하다.

</details>

### Q5. DEV_OR_PROD 환경 변수를 dev에서 prod로 바꾸면 어떤 변화가 발생하는가?

<details>
<summary>정답 및 해설 보기</summary>

**조회하는 Parameter Store 이름의 경로가 바뀐다.** 프로그램 코드를 일일이 수정하지 않아도 된다.

</details>

## 🇯🇵 日本語 Summary

CLI の GetParameters と Lambda の boto3 SDK は同じ SSM API を利用する。SecureString の復号には WithDecryption と KMS Decrypt 権限が必要。Lambda Execution Role で SSM と KMS のアクセスを制御する。GetParametersByPath と `--recursive` による上位パスの過剰許可に注意。

## 🇺🇸 English Summary

The CLI and boto3 call SSM APIs in different ways. Decrypting SecureString requires WithDecryption and permission to decrypt using the KMS key. Lambda uses its execution role. Hierarchical GetParametersByPath with recursive traversal must be scoped carefully; never log secrets.

## 📚 Vocabulary

| English | 한국어 | 日本語 |
|---|---|---|
| AWS CLI | AWS 명령줄 인터페이스 | AWS コマンドラインインターフェイス |
| SDK | 소프트웨어 개발 키트 | ソフトウェア開発キット |
| boto3 | Python용 AWS SDK | Python 向け AWS SDK |
| Execution Role | 실행 역할 | 実行ロール |
| GetParametersByPath | 경로 기준 조회 API | パス指定取得 API |
| WithDecryption | 복호화 옵션 | 復号オプション |
| Environment Variable | 환경 변수 | 環境変数 |
