# 6. GraphQL

## 6.1. Objects

GraphQL APIではqnyp上の主要なデータが以下のようなオブジェクトとして表現されます。

| 型 | 意味 |
| ---- | ---- |
| `Title` | タイトル。<br/>TVシリーズやOVAなどのアニメ作品。 |
| `Episode` | エピソード。<br/>タイトルに属する1つのエピソード。 |
| `User` | ユーザー。<br/>qnypのユーザーアカウント。 |
| `Log` | 視聴ログ。<br/>ユーザーがエピソードに対して記録した感想。 |

各オブジェクトの関係はおおまかに以下のようになります。

- `Title` は0個以上の `Episode` を持つ
- `Episode` は0個以上の `Log` を持つ
- `User` は0個以上の `Log` を持つ

各オブジェクトの詳細な仕様に関しては [GraphQL Explorer](https://graphql-explorer.qnyp.com/) の Docs および [GraphQL Schema document](./graphql_schema/) を参照してください。

## 6.2. ID

GraphQL APIにおける各オブジェクトは識別子として `id (String)` と `databaseId (Int)` の2種類のIDを持ちます。

| 属性 | 説明 |
| ---- | ---- |
| `id` | すべてのオブジェクトにおいて一意な文字列<br/>例: `"VGl0bGUtMzUwMQ"` |
| `databaseId` | データベースにおけるプライマリキー<br/>同一のオブジェクトにおいて一意な数値<br/>例: `3501` |

GraphQL APIの操作においては基本的に `id` を使います。`databaseId` は「既にqnypのサイト上でIDがわかっているオブジェクトを取得する」場合の利便性のために用意されています。


## 6.2. Queries

情報を取得する場合は、 `Query` ルートタイプが持つ以下のフィールドに対するクエリをAPIに送ります。

| フィールド | 機能 |
| ---- | ---- |
| `episode` | idでエピソードを取得 |
| `episodeByDatabaseId` | databaseIdでエピソードを取得 |
| `log` | idで視聴ログを取得 |
| `logByDatabaseId` | databaseIdで視聴ログを取得 |
| `node` | idでオブジェクトを取得 |
| `nodes` | idでオブジェクトを一括取得 |
| `searchTitles` | タイトルを検索 |
| `title` | idでタイトルを取得 |
| `titleByDatabaseId` | databaseIdでタイトルを取得 |
| `user` | ユーザー名でユーザーを取得 |
| `viewer` | 認証したユーザーを取得 |


## 6.2.1 Query の例

```graphql
query {
  viewer {
    id
    databaseId
    username
    profileImageURL
    recentlyWatchedTitles(first: 1) {
      edges {
        node {
          name
        }
      }
    }
    logs(first: 1) {
      edges {
        node {
          id
          channel
          rating
          episode {
            numberText
            title {
              name
            }
          }
        }
      }
    }
  }
}
```

```json
{
  "data": {
    "viewer": {
      "id": "VXNlci0x",
      "databaseId": 1,
      "username": "junya",
      "profileImageURL": "//qnyp.global.ssl.fastly.net/attachments/05da1bf0b79769073ad0a2543f411521da298fa0/store/fill/80/80/1a63fa09379efe7e52335cfa74ecda8106585f6cb4ca6a91641f8ef6df0c/profile_image.png",
      "recentlyWatchedTitles": {
        "edges": [
          {
            "node": {
              "name": "月刊少女野崎くん"
            }
          }
        ]
      },
      "logs": {
        "edges": [
          {
            "node": {
              "id": "TG9nLTIxNDk2",
              "channel": "NET",
              "rating": "GREAT",
              "episode": {
                "numberText": "最終号",
                "title": {
                  "name": "月刊少女野崎くん"
                }
              }
            }
          }
        ]
      }
    }
  }
}
```

`viewer` フィールドから、「アクセストークンで認証したユーザー」の

- ユーザー情報
- 最近見たタイトル1件の情報
- 最近作成した視聴ログ1件の情報

を取得する例です。


## 6.2.2. Connection

一部のオブジェクトは `Connection` で終わる型名のフィールドを持っています(例えば `User` オブジェクトの `logs` フィールドは `LogConnection` 型)。

これらのコネクション表現はオブジェクト間の1対多関係を表現しており、カーソルを使ったページングをシンプルに実現するためのものとなっています。

その実装は Relay Cursor Connections Specification に沿ったものとなっていますので、詳細についてはそれらのドキュメントを参照してください。

- [Relay Cursor Connections Specification](https://facebook.github.io/relay/graphql/connections.htm)
- [Connection \- Relay Docs](https://facebook.github.io/relay/docs/graphql-connections.html)


## 6.3. Mutations

情報を更新する場合は、 `Mutation` ルートタイプが持つ以下のmutationフィールドに対するクエリをAPIに送ります。

| フィールド | 機能 |
| ---- | ---- |
| `createLog` | 視聴ログを作成する |
| `deleteLog` | 視聴ログを削除する |
| `updateLog` | 視聴ログを更新する |

Mutation を実行するには、アクセストークンに `public` および `write` スコープが与えられている必要があります。


## 6.3.1 Mutation の例

```graphql
mutation ($input: CreateLogInput!) {
  createLog(input: $input) {
    log {
      id
      databaseId
      createdAt
      url
    }
  }
}
```

```json
// Query Variables
{
  "input": {
    "body": "楽しめるエピソードでした。",
    "channel": "TV",
    "episodeId": "RXBpc29kZS03NTY1OQ",
    "rating": "GREAT",
    "spoiler": false
  }
}
```

```json
// Response
{
  "data": {
    "createLog": {
      "log": {
        "id": "TG9nLTIxMjE3",
        "databaseId": 99999,
        "createdAt": "2017-04-29T11:17:22Z",
        "url": "https://qnyp.com/junya/logs/99999"
      }
    }
  }
}
```

`createLog` フィールドで、エピソードに対する視聴ログを作成し、作成された視聴ログの情報を取得する例です。
