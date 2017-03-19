# 6. Queries (情報の取得)

APIから情報を取得する場合は、GraphQLのクエリルートが持つ以下のフィールドに対するクエリをAPIに送ります。

フィールド | 機能
---- | ----
`title` | タイトルの取得
`titles` | タイトルの検索


## タイトルの取得

```plaintext
query {
  title(id:3501) {
    id
    name
  }
}
```

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

> 以下のような構造のJSONが返されます。

```json
{
  "data": {
    "title": {
      "id": "3501",
      "name": "けものフレンズ"
    }
  }
}
```

`title (Title)`

IDを指定してアニメタイトルを取得します。

引数 | 型 | 説明
---- | ---- | ----
id | Int | タイトルID
