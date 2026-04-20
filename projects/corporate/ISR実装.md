
## パターン
1. revalidateを使う
2. microcms webhookとrevalidateTagを使う


## microcms webhookとrevalidateTagを使う
- microcms更新されたらwebhookで`/api/revalidate`を叩いてrevalidateTagのキャッシュ削除