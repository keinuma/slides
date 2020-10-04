## GoによるGraphQLの実装

2020/10/6
沼田

---

### 自己紹介

| key | value |
| --- | --- |
| 名前 | ぬまた |
| 職業 | エンジニア |
| 年齢 | 25 |
| 言語 | JavaScript, Golang |

---

### アジェンダ
- GraphQLとは
- Goを使うメリット
- GraphQL + Goの実装方法
- gqlgenのいいところ
- gqlgenを使った実装例
- 今回の設計のPros Cons

---

### GraphQLとは
- Facebookが開発したWeb APIのための規格
- Alt REST API
- スキーマとクエリからなる

---

### スキーマ
- データ、APIのインターフェースを定義
- Queryが参照、Mutationが更新API

```graphql
type Todo {
    id: ID!
    status: Int!
    text: String
}

type Query {
    getTodos: [Todo!]!
}

type Mutation {
    createTodo(status: Int): Todo!
}
```

---

### クエリ
- スキーマ定義をもとに定義
- レスポンスに必要なデータを選択

```graphql
query GetTodos {
    getTodos {
        id
    }
}
```

---

### GraphQLの特徴
- エンドポイントが一つ
  - POST `/graphql`
- クライアント（GraphiQL）が提供されている
- スキーマ定義、拡張が豊富
- クエリの柔軟性が高い

---

### Goを使うメリット
- Goの構造体とGraphQLのスキーマのマッピング
- リゾルバーに依存性逆転の法則を適用

---

### （他の言語の例）
- Ruby
  - [graphql-ruby](https://github.com/rmosolgo/graphql-ruby)
  - GitHub API v4に使用されている
- Node
  - [apollo-server](https://github.com/apollographql/apollo-server)
  - Apolloのコアシステムを利用できる

---

### GraphQL + Goの実装方法
- BaaS or 独力で実装
- BaaSはGraphQLのコア部分の実装が提供されている
- GraphQLの実装がシンプルな場合、BaaSがよさそう

---

### BaaSの例
- [AppSync](https://aws.amazon.com/jp/appsync/)
  - AWS提供のマネージドサービス
  DynamoDB, LambdaなどAWSのサービスと連携できる
- [Hasura](https://hasura.io/)
  - Heroku＋PostgresQLによるマネージドサービス
  - データベースのテーブルを定義するとAPIを生成できる
- [Prisma](https://www.prisma.io/)
  - 1系はRDBによるGraphQLビルダー
  - 2系はGraphQLかかわらずTypeScriptによるORM + Clientを提供している

---

### 独力で実装する場合
- スキーマファーストかコードファーストを選択
- スキーマファースト
  - クライアントとバックエンドの実装を並列に進めやすい
- コードファースト
  - 開発フローが一方向になる

---

### コードファーストのライブラリ
- [graphql-go](https://github.com/graphql-go/graphql)
  - スキーマとリゾルバーをコードで表現し、スキーマを出力

- [thunder](samsarahq/thunder https://github.com/samsarahq/thunder)
  - Goのstructからスキーマを生成する

---

### スキーマファーストのライブラリ
- [graph-gophers/graphql-go](https://github.com/graph-gophers/graphql-go)
  - ランタイム時にスキーマとコードをチェック
- [gqlgen](https://github.com/99designs/gqlgen/)
  - CLIからスキーマをコードに変換

---

### gqlgenのいいところ
- GraphQLのサポート範囲が広い
- 型安全
- 自動生成を利用しやすい

---

### GraphQLのサポート範囲が広い
[![Image from Gyazo](https://i.gyazo.com/7caa91278032fe19c98080300c374d1f.png)](https://gyazo.com/7caa91278032fe19c98080300c374d1f)

---

### 型安全
- CLI実行時に型の整合性を取れる
- `interface{}` は発生しない

---

### 自動生成を利用しやすい
- 自動生成は二種類
  - 基本的に開発者が変更しないファイル
  - 開発者が実装を積み重ねるファイル
- スキーマとGoの差分がある場合は差分を埋めるためのコードが出力される

---

- Goのプロパティが不足している状態でコードを出力する

```graphql
type Todo {
    id: ID!
    text: String!
    done: Boolean!
}      
```

```go
type Todo struct {
    ID   string
    Text string
}
```

---

- コードに不足している `Done` を埋めるためのリゾルバーが出力される

```go
func (t *TodoResolver) Done(ctx context.Context, *model.Todo) (bool, error) {
    // 編集する
    panic("panic")
}
```

---

### gqlgenの実装例
- [サンプルリポジトリ](https://github.com/keinuma/tech-story)
- 使用技術
  - APIフレームワーク: echo
  - ORM: GORM
  - DB: MySQL

---

### 設計トピック
- ドメイン3層 + GraphQL
- GraphQLのResolverをControllerとして利用
- スキーマとのマッピング

---

### ドメイン3層 + GraphQL
- model + service + repository
- modelにドメインを実装
- serviceにロジックを実装
- repositoryはinterfaceのみ
- 実態はinfra層に実装

---

### GraphQLのResolverをControllerとして利用
- Resolverからドメインのpresenter層を実行する
- presenterは必要なservice層を保持している

```go
func (r *mutationResolver) CreateStory(ctx context.Context, input request.NewStory) (*model.Story, error) {
    storyPresenter := presenter.NewStory(*service.NewStory(gateway.NewStory(ctx, r.DB)))
    story, err := storyPresenter.CreateStory(input)
    if err != nil {
        return nil, err
    }
    return story, nil
}
```

---

### スキーマとのマッピング
- GraphQLのtypeとmodel層をマッピング
- GraphQLのinputとpresenter層をマッピング

---

### 実装のユースケース
- 認証
- 権限
- エラーハンドリング

---

### 認証
- firebase authを利用
- echoのmiddlewareでトークンの検証を実行
- トークン取得後はユーザー情報をResolverに格納して各APIに渡している

---

### 権限
- Schemaにディレクティブを設定
- 権限のチェック処理をディレクティブのResolverに実装する

```graphql
directive @hasRole(role: Role!) on FIELD_DEFINITION

enum Role {
    ADMIN
    USER
}
```

---

### エラーハンドリング
- Resolverのcontextに対してエラーを詰めていく
- GraphQLはエラーコードを持たないので拡張して実装する

```go
import (
     	"context"
     
     	"github.com/vektah/gqlparser/v2/gqlerror"
     	"github.com/99designs/gqlgen/graphql"
     )
     
func (r Query) DoThings(ctx context.Context) (bool, error) {
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

### 今回の設計のPros Cons
- Pros
  - 責務ごとに分離して実装できる
  - データ取得部分をinfra層で管理しているためN+1問題が発生しにくい
- Cons
  - スキーマが複雑になるとリゾルバーが肥大化する
