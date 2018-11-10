## CloudFormationを使ってみた

2018/11/11  
@keinuma

---

### 自己紹介

| key | value |
| --- | --- |
| 氏名 | keinuma |
| 年齢 | 23 |
| 趣味 | 映画・読書 |
| 言語 | Python,JavaScript,Go |

---

### あるサーバーチームの話

---

### 開発内容
- モバイルアプリ開発
- 責務はアプリ向けAPI開発
- 同時に5案件くらいやってる

---

### メンバー
- バックエンドエンジニア x 5名
- インフラエンジニア兼リーダー x 1名

---

### 開発環境
- GitLab CE
- AWS
  - ECS

---

リーダー以外「要件が決まってきたからAWS環境が欲しい」  
  
リーダー「同時には無理」

---

### 課題
- インフラ構築できる人が1人
- バス係数が低すぎる 
- 環境をアップデートすることもままならない
- だいたい同じ構成になることが多い

---

### Infrastructure as Code

---

### CloudFormationを導入
- チームでテンプレートを用意
- 案件に合わせて修正

---

### つらいこと
- デバッグ
- パラメータを調べること
- YAMLの読み書き

---

### 解決策
- Pythonで書いてみる
- [sceptre](https://github.com/cloudreach/sceptre): PythonによるCLIツール
- [troposphere](https://github.com/cloudtools/troposphere): CloudFormationのPython記法

---

### Troposphereの例

---
- ACMを作ってみる
- テンプレートの初期化、パラメータの注入

```python
from troposphere import Parameter
from troposphere import Template

class CertificateManager:
    def __init__(self):
        self.template = Template()
        self.template.add_description('Certificate Manager')

    def add_parameters(self):
        self.domain_name = self.template.add_parameter(
            Parameter(
                'DomainName',
                Type='String',
                Description='DomainName',
            ))
```

---

- 作成するリソースの設定

```python
    def add_resources(self):
        self.certificate = self.template.add_resource(
            Certificate(
                'Cert',
                DomainName=self.cert_domain,
                ValidationMethod='DNS',
                DomainValidationOptions=[
                    DomainValidationOption(
                        DomainName=self.cert_domain,
                        ValidationDomain=Ref(self.domain_name)
                    )
                ],
            )
        )
```
---

- 他リソースへ変数として受け渡す

```python
    def add_outputs(self):
        self.template.add_output([
            Output(
                'CertificateArn',
                Value=Ref(self.certificate),
            ),
            Output(
                'CertDomain',
                Value=self.cert_domain,
            )
        ])

```

---

### Sceptre
- 開発環境を構築してみる

```bash
$ sceptre delete-env dev
```

---

### よかったこと
- ライブラリのコードを見ればパラメータがわかる
- Sceptreは環境変数やHookなどのオプション多彩
- Pythonの文法を使える

---

### つらいところ
- デバッグ(CloudFormation)
- ドキュメントを細部までみないといけない
  - RDSのパラメータがわかってもAuroraの変更点は調べないと…

---

おわり

