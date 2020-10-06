[drag=300 30, drop=center, flow=col true]

## GoによるGraphQLの実装

2020/10/6 沼田

---

[drag=50 20, drop=top, pad=0px]
## 自己紹介

[drag=100, drop=0 10, fit=1.5]
| key | value |
| --- | --- |
| 名前 | ぬまた |
| 職業 | エンジニア |
| 会社 | カミナシ |
| 言語 | JavaScript, Golang |

---
[drag=50 20, drop=top, pad=0px]
## アジェンダ

@ul[list-spaced-sm-bullets, drag=100 200, drop=0 10, fit=1.5](false)
1. GraphQL
2. Goを使うメリット
3. GraphQL + Goの実装方法
4. gqlgen
5. gqlgenの実装例
6. まとめ
@ul

---
[drag=100, drop=center, fit=1.3]

## GraphQL

---

@ul[list-spaced-sm-bullets, drag=100 200, drop=0 0, fit=1.5](false)
- Facebookが開発したWeb APIのための規格
- Alt REST API
- スキーマとクエリからなる
@ul

---
[drag=50 20, drop=top, pad=0px]
## スキーマ

@ul[list-spaced-sm-bullets, drag=100 200, drop=0 0, fit=1.5](false)
- データ、APIのインターフェースを定義
- Queryが参照、Mutationが更新API
@ul

---

![drag=50 80, drop=center, fit=1.0, stretch=true](https://i.gyazo.com/2c11116910aef4ca45fcfc37d8f9118d.png)

---
[drag=50 20, drop=top, pad=0px]
## クエリ

@ul[list-spaced-sm-bullets, drag=100 200, drop=0 0, fit=1.5](false)
- スキーマ定義をもとに定義
- レスポンスに必要なデータを選択
@ul

---

![drag=30 35, drop=10 30, fit=0.7, stretch=true](https://i.gyazo.com/fa660c9143e58a578b45328cfc8aa972.png)

[drag=30 35, drop=35 30, fit=1.5, stretch=true]
=>

![drag=25 80, drop=60 10, fit=0.7, stretch=true](https://i.gyazo.com/1fa89e6ddc219f4c7ec6ad63667c11dc.png)

---
[drag=50 20, drop=top, pad=0px]
## GraphQLの特徴

@ul[list-spaced-sm-bullets, drag=100 100, drop=0 0, fit=1.5](false)
- エンドポイントが一つ (`/graphql`)
- APIクライアント（GraphiQL）が提供されている
- スキーマ定義、拡張が豊富
- クエリの柔軟性が高い
@ul

---
[drag=100, drop=center, fit=1.3]

## Goを使うメリット

---

@ul[list-spaced-sm-bullets, drag=100 200, drop=0 0, fit=1.5](false)
- Goの構造体とGraphQLのスキーマをマッピング可能
- リゾルバーをダックタイピングで実装
- GraphQLの複雑性を構造化する
@ul

---
[drag=100, drop=center, fit=1.3]

## GraphQL + Goの実装方法

---

@ul[list-spaced-sm-bullets, drag=100 200, drop=0 0, fit=1.4](false)
- BaaS or 独力で実装
- BaaSはGraphQLのコア部分の実装が提供されている
- GraphQLの実装がシンプルな場合、BaaSがよさそう
@ul

---
[drag=50 20, drop=top, pad=0px]

## BaaSの例

@ul[list-spaced-sm-bullets, drag=90 100, drop=5 5, fit=1.3](false)
- [AppSync](https://aws.amazon.com/jp/appsync/)
  - AWS提供のマネージドサービス
  - DynamoDB, LambdaなどAWSのサービスと連携できる
- [Hasura](https://hasura.io/)
  - Heroku＋PostgresQLによるマネージドサービス
  - データベースのテーブルを定義するとAPIを生成できる
@ul

---
[drag=80 20, drop=top, pad=0px]
## 独力で実装する場合

@ul[list-spaced-sm-bullets, drag=80 60, drop=10 30, fit=1.3, pad=0px](false)
- スキーマファーストかコードファーストを選択
- スキーマファースト
  - クライアントとバックエンドの実装を並列に進めやすい
- コードファースト
  - 開発フローが一方向になる
@ul

---
[drag=80 20, drop=top, pad=0px]
## コードファーストのライブラリ

@ul[list-spaced-sm-bullets, drag=80 100, drop=center, fit=1.3](false)
- [graphql-go](https://github.com/graphql-go/graphql)
  - スキーマとリゾルバーをコードで表現し、スキーマを出力
- [thunder](samsarahq/thunder https://github.com/samsarahq/thunder)
  - Goのstructからスキーマを生成する
@ul

---
[drag=80 30, drop=top, pad=0px]
## スキーマファースト
## のライブラリ

@ul[list-spaced-sm-bullets, drag=80 100, drop=10 5, fit=1.3](false)
- [graph-gophers/graphql-go](https://github.com/graph-gophers/graphql-go)
  - ランタイム時にスキーマとコードをチェック
- [gqlgen](https://github.com/99designs/gqlgen/)
  - CLIからスキーマをコードに変換
@ul

---
[drag=100, drop=center, fit=1.3]
## gqlgen

---

[drag=80 20, drop=top, pad=0px]
## gqlgenの使い方

@ul[list-spaced-sm-bullets, drag=80 100, drop=center, fit=1.5](false)
- スキーマを定義
- `gqlgen` コマンドを実行
@ul


---

[drag=80 20, drop=top, pad=0px]
## 生成されるファイル

@ul[list-spaced-sm-bullets, drag=80 100, drop=10 10, fit=1.5](false)
- `model.go` : スキーマに定義されているデータファイル
- `resolver.go`: GraphQLの返り値にデータをマッピング
- `generated.go`: リクエストパラメータからモデルを生成（編集しない）
@ul

---

[drag=80 20, drop=top, pad=0px]
## gqlgenの利点

@ul[list-spaced-sm-bullets, drag=80 100, drop=center, fit=1.5](false)
- GraphQLのサポート範囲が広い
- 型安全
- 自動生成を利用しやすい
@ul

---
[drag=80 20, drop=top, pad=0px]
## GraphQLのサポート範囲が広い

[drag=100 80, drop=bottom, fit=1.4]
[![Image from Gyazo](https://i.gyazo.com/7caa91278032fe19c98080300c374d1f.png)](https://gyazo.com/7caa91278032fe19c98080300c374d1f)

---
[drag=80 20, drop=top, pad=0px]
## 型安全

@ul[list-spaced-sm-bullets, drag=80 100, drop=center, fit=1.5](false)
- CLI実行時に型の整合性を取れる
- `interface{}` は発生しない
@ul

---
[drag=80 20, drop=top, pad=0px]
## 自動生成を利用しやすい

@ul[list-spaced-sm-bullets, drag=80 100, drop=10 5, fit=1.3](false)
- スキーマとGoの差分がある場合は差分を埋めるためのコードが出力される
@ul

---
[drag=100 10, drop=top, pad=10px, fit=scale]

- Goのプロパティが不足している状態

[drag=80 40, drop=10 15, fit=1.1]
```graphql
type Todo {
    id: ID!
    text: String!
    done: Boolean!
}      
```
GraphQL

[drag=80 40, drop=10 60, fit=1.1]
```go
type Todo struct {
    ID   string
    Text string
}
```
Go

---
[drag=80 20, drop=10 0, fit=1.1]

- コードに不足している `Done` を埋めるためのリゾルバーが出力される

[drag=80 30, drop=center, fit=1.1]
```go
func (t *TodoResolver) Done(ctx context.Context, *model.Todo) (bool, error) {
    // 編集する
    panic("panic")
}
```

---
[drag=100, drop=center, fit=1.3]
## gqlgenの実装例

---

@ul[list-spaced-sm-bullets, drag=80 100, drop=center, fit=1.5](false)
- [サンプルリポジトリ](https://github.com/keinuma/tech-story)
- 使用技術
  - APIフレームワーク: echo
  - ORM: GORM
  - DB: MySQL
@ul

---
[drag=80 20, drop=top, pad=0px]
## 設計トピック

@ul[list-spaced-sm-bullets, drag=80 100, drop=center, fit=1.5](false)
- ドメイン3層 + GraphQL
- GraphQLのResolverをControllerにする
- スキーマとのマッピング
@ul

---
[drag=80 20, drop=top, pad=0px]
## ドメイン3層 + GraphQL

@ul[list-spaced-sm-bullets, drag=90 100, drop=0 5, fit=1.3](false)
- model + service + repository
- modelにドメインを実装
- serviceにロジックを実装
- repositoryはinterfaceのみ
- 実態はinfra層に実装
@ul

---

![drag=40 90, drop=0 5, fit=1.0, stretch=true](20201006_gqlgen/image/domain-architecture.png)

@ul[list-spaced-sm-bullets, drag=90 100, drop=25 5, fit=1.3](false)
- APIリクエスト時にControllerを実行
- PresenterはResponseを生成
- Repositoryはインターフェースのみ
- InfraでDBの処理を実行
@ul

---

![drag=40 90, drop=0 5, fit=1.0, stretch=true](20201006_gqlgen/image/domain-architecture_graphql.png)

@ul[list-spaced-sm-bullets, drag=90 100, drop=25 5, fit=1.3](false)
- Controller→Resolver
- Model→GraphQL Model
@ul


---
[drag=80 20, drop=top, pad=0px]
## 実装のユースケース

@ul[list-spaced-sm-bullets, drag=80 100, drop=center, fit=1.5](false)
- 認証
- 権限
- エラーハンドリング
@ul

---
[drag=80 20, drop=top, pad=0px]
## 認証

@ul[list-spaced-sm-bullets, drag=80 100, drop=center, fit=1.5](false)
- firebase authを利用
- echoのmiddlewareでトークンの検証を実行
- トークン取得後はユーザー情報をResolverに格納して各APIに渡している
@ul

---
[drag=80 20, drop=top, pad=0px]
## 権限

@ul[drag=80 30, drop=10 20, fit=1.2](false)
- Schemaにディレクティブを設定
- 権限のチェック処理をディレクティブのResolverに実装する
@ul

[drag=90 99, drop=5 20, fit=1.1]
```graphql
directive @hasRole(role: Role!) on FIELD_DEFINITION

enum Role {
    ADMIN
    USER
}
```

---
[drag=80 20, drop=top, pad=0px]
## エラーハンドリング

@ul[list-spaced-sm-bullets, drag=80 30, drop=10 30, fit=1.2](false)
- Resolverのcontextに対してエラーを詰めていく
- GraphQLはエラーコードを持たないので拡張して実装する
@ul

---
[drag=90 90, drop=center, fit=1.1]
エラーを追加するヘルパー関数
```go
func AddError(ctx context.Context) (bool, error) {
    graphql.AddError(ctx, &gqlerror.Error{
        Path:       graphql.GetPath(ctx),
        Message:    "A descriptive error message",
        Extensions: map[string]interface{}{
           "code": "4",
        },
    })

    return false, gqlerror.Errorf("BOOM! Headshot")
}
```

---
[drag=80 20, drop=top, pad=0px]
## まとめ

@ul[list-spaced-sm-bullets, drag=80 50, drop=center, fit=1.2](false)
- GraphQLを実装するときはコードファーストかスキーマファーストを選択する
- gqlgenを利用して責務ごとに分離してGraphQLを実装できる
- スキーマが複雑になるとリゾルバーが肥大化しそう
@ul
