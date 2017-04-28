---
title: qnyp GraphQL API Reference

language_tabs:
  - graphql: GraphQL
  - ruby: Ruby
  - javascript: JavaScript
  - shell: Shell

includes:
  - graphql_queries

toc_footers:
 - <a href="./">APIリファレンス</a>
 - <a href="./explorer/">GraphQL Explorer</a>
 - <a href="./graphql_schema/">GraphQL Schema</a>
 - <a href="./ruby.html">Rubyチュートリアル</a>
 - <a href="./javascript.html">JavaScriptチュートリアル</a>

search: false
---

# APIリファレンス

このページでは、qnyp GraphQL API(以下API)の仕様について解説します。

APIを利用することで、qnypに登録されているオープンな情報を取得したり、ユーザーとして感想を投稿することができます。

<aside class="notice">
APIはベータ版として提供されています。現時点では仕様の安定性や稼働率についての保証はありません。
</aside>

各プログラミング言語用のチュートリアルは以下を参照してください。

- [Ruby](ruby.html)
- [JavaScript](javascript.html)


# 1. Overview (概要)

APIはGraphQLをベースとしています。
すべてのリクエストはエンドポイント `https://api.qnyp.com/graphql` に対して送信します。

リクエストは`https`プロトコルを使ったセキュアなものである必要があります。


# 2. Authorization (認可)

APIを利用するにはアクセストークンが必ず必要となります。

<aside class="notice">
OAuth2によるアクセストークンの発行についても対応予定ですが、現時点では各ユーザーのアカウントに紐付いたものを設定ページにて生成する方式のみとなります。
</aside>

## 2.1. アクセストークンの生成

qnypのユーザーは、[API関連の設定](https://qnyp.com/settings/api)ページにて自分のアカウントに紐づくアクセストークンを生成することができます。

このアクセストークンを利用するにあたって有効期限はありませんが、ユーザーはいつでも設定ページから自分のアクセストークンを無効化することができます。


# 3. GraphQL

APIへの問い合わせには[GraphQL](http://graphql.org/)を利用します。

## GraphQLクライアント
- [GraphiQL.app](https://github.com/skevy/graphiql-app) - A light, Electron-based wrapper around GraphiQL
- [GraphiQL](https://github.com/graphql/graphiql) - An in-browser IDE for exploring GraphQL

## GraphQLクライアントライブラリ
- [github/graphql-client](https://github.com/github/graphql-client) - Ruby
- [Apollo Client](http://dev.apollodata.com/) - JavaScript

この他のクライアントやライブラリについては
[chentsulin/awesome\-graphql](https://github.com/chentsulin/awesome-graphql)
を参照してください。


# 4. Errors (エラー)

APIは、エラーが発生した際に以下のステータスコードを返します。

コード | 意味
---------- | -------
400 | リクエスト内容が不正である
401 | アクセストークンが無効である
403 | アクセストークンがAPIリクエストに必要な権限を持っていない
404 | 無効なエンドポイントをリクエストした
405 | Method Not Allowed
406 | Not Acceptable
429 | リクエスト数が上限に達している
500 | サーバー側でエラーが発生した
503 | APIがメンテナンス中である


# 5. Rate limiting (利用制限)

APIへのリクエスト数の上限は、アクセストークン毎に1時間あたり1,000回までとなっています。

リクエスト数の残数や回数がリセットされる日時などの情報は、APIのレスポンスに以下のような形で含まれています。

```text
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: Tue, 14 Mar 2017 16:16:00 GMT
```

リクエスト数が上限に達した場合は、以下のようにステータスコード429のレスポンスが返されます。

```text
429 Too Many Requests

Retry-After: 3600
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 0
X-RateLimit-Reset: Tue, 14 Mar 2017 16:16:00 GMT
```

`Retry-After`レスポンスヘッダには、回数がリセットされるまでの秒数が格納されています。
