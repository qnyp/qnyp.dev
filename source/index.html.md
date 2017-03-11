---
title: qnyp API Reference

language_tabs:
  - plaintext: GraphQL
  - ruby: Ruby
  - javascript: JavaScript
  - shell: Shell

includes:
  - graphql_queries
  - graphql_types

toc_footers:
 - <a href="./">APIリファレンス</a>
 - <a href="./ruby.html">Rubyチュートリアル</a>
 - <a href="./javascript.html">JavaScriptチュートリアル</a>

search: false
---

# APIリファレンス

このページでは、qnyp APIの仕様について解説します。

qnyp APIを利用することで、qnypに登録されているオープンな情報を取得したり、ユーザーの代わりにアニメについての感想を投稿することができます。

<aside class="notice">
qnyp APIはベータ版として提供されています。現時点では仕様の安定性や稼働率についての保証はありません。
</aside>

各プログラミング言語用のチュートリアルは以下を参照してください。

- [Ruby](ruby.html)
- [JavaScript](javascript.html)

# Overview (概要)

## エンドポイント

APIのエンドポイントは `api.qnyp.com/graphql` です。通信には常にHTTPSを利用します。

## OAuth2

権限の認可には [OAuth2](https://ja.wikipedia.org/wiki/OAuth) を利用します。

以下の2種類の認可フロー(Grant Types)をサポートしています。

- 認可コード (Authorization Code Grant Flow)
  - オープンな情報の取得およびユーザーの認可を得た感想の投稿が可能
- クライアントクレデンシャル (Client Credentials)
  - オープンな情報の取得のみが可能

## GraphQL

APIへの問い合わせ言語として[GraphQL](http://graphql.org/)を利用します。

### GraphQLクライアント
- [GraphiQL.app](https://github.com/skevy/graphiql-app) - A light, Electron-based wrapper around GraphiQL
- [GraphiQL](https://github.com/graphql/graphiql) - An in-browser IDE for exploring GraphQL

### GraphQLクライアントライブラリ
- [github/graphql-client](https://github.com/github/graphql-client) - Ruby
- [Apollo Client](http://dev.apollodata.com/) - JavaScript

この他のクライアントやライブラリについては
[chentsulin/awesome\-graphql](https://github.com/chentsulin/awesome-graphql)
を参照してください。

# Errors (エラー)

qnyp APIはエラーが発生した際に以下のステータスコードを返します。

ステータスコード | 意味
---------- | -------
400 | Bad Request -- Your request sucks
401 | Unauthorized -- Your API key is wrong
403 | Forbidden -- The kitten requested is hidden for administrators only
404 | Not Found -- The specified kitten could not be found
405 | Method Not Allowed -- You tried to access a kitten with an invalid method
406 | Not Acceptable -- You requested a format that isn't json
429 | Too Many Requests -- You're requesting too many kittens! Slow down!
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.

# Rate limiting (利用制限)

(WIP)

# Authorization (認可)

> 認可を行うには以下のコードを使います:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>
