---
title: qnyp API Ruby Tutorial

language_tabs:
  - ruby: Ruby

includes:
  - graphql_types

toc_footers:
 - <a href="./">APIリファレンス</a>
 - <a href="./ruby.html">Rubyチュートリアル</a>
 - <a href="./javascript.html">JavaScriptチュートリアル</a>

search: false
---

# Rubyチュートリアル

このページでは、qnyp APIをRubyから利用する方法について解説します。

# GraphQLクライアント

```ruby
require 'graphql/client'
require 'graphql/client/http'

# qnyp API wrapper
module QnypAPI
  # Configure GraphQL endpoint using the basic HTTP network adapter.
  HTTP = GraphQL::Client::HTTP.new('https://api.qnyp.com/graphql') do
    def headers(context)
      { 'Authorization': 'Bearer XXX' }
    end
  end

  # Fetch latest schema on init, this will make a network request
  Schema = GraphQL::Client.load_schema(HTTP)

  # However, its smart to dump this to a JSON file and load from disk
  #
  # Run it from a script or rake task
  #   GraphQL::Client.dump_schema(QnypAPI::HTTP, 'path/to/schema.json')
  #
  # Schema = GraphQL::Client.load_schema('path/to/schema.json')

  Client = GraphQL::Client.new(schema: Schema, execute: HTTP)  
end
```

GraphQLクライアントとして [github/graphql-client](https://github.com/github/graphql-client) を利用するので、 `Gemfile` に以下のように記述して `bundle install` を実行します。

`gem 'graphql-client'`

APIにGraphQLクエリを送るために、あらかじめGraphQLクライアントを作成しておきます。

# タイトルの取得

```ruby
module QnypTitle
  Query = QnypAPI::Client.parse <<-'GRAPHQL'
    query {
      title(id:3501) {
        id
        name
      }
    }
  GRAPHQL
end

result = QnypAPI::Client.query(QnypTitle::Query)
result["data"]["title"]["name"] # => 'けものフレンズ'
result.data.title.name # => 'けものフレンズ'
```

タイトルを取得するには、クエリルートの `title` フィールドにIDを与えて呼び出します。
