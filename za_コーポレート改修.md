
## 概要
- 現状のwordpressの構成からmicroCMS × Next.JSにリニューアルする
- [洋子さんが作成したリニューアル構想 - slack](https://sonicmoov.slack.com/archives/CAQK9SB53/p1768455953395639?thread_ts=1767957465.561049&cid=CAQK9SB53)
- 開発期間：26年3-4月（当初は26年3月末までだったが伸びた）
- アサイン：森本、葉田さん、（marizoさん？
- 作業：
	- Next.JSの環境構築
		- アサイン：自分
	- モックアップ実装
		- アサイン：葉田さん、marizoさんあたり
		- 作業：デザイン改修、モックアップ実装

## 構成
- Next.JS (AppRouter)
- microcms

## microcmsアカウント情報
- メールアドレス: frontend@sonicmoov.com
- パスワード: d4nfpDCzA88C
## vercelアカウント情報
- メールアドレス: frontend@sonicmoov.com
- パスワード: なし（PINコード）

## ページ

- トップページ（固定でいいかも？）
- SMVについて
- サービストップ
- 実績一覧
- 実績詳細

---

## コンテンツスキーマ設計
- 本田洋子さんが作成した[リニューアル構想](https://sonicmoov.slack.com/archives/CAQK9SB53/p1768455953395639?thread_ts=1767957465.561049&cid=CAQK9SB53)で⭐️マークが付いている箇所のみCMSで管理できるようにする
- 実績一覧のCMS化は不要（カテゴリだけ編集したいとのことなので実績詳細でやる）

### コンテンツ
- 実績詳細（作成済み）
- お知らせ詳細（作成済み）
- 採用情報（本田さんが青木さんに確認中）

### 確認事項
- 採用情報のどの要素をCMSで管理できるようにするか？
	- [ ] 本田さんが青木さんに確認中

### 今後やること
- zodスキーマ等の定義

### スキーマ設計のポイント
- 表示場所（ページ）を定義しない、コンテンツそのものを定義する
	- 将来的な拡張性
	- コンテンツを表示できるページが固定されてしまうため
- 運用者の視点を取り入れる

---

## メモ 本田さん共有MTG 3/17

- **実装着手の流れ**
	- 最初はグレーのページからやっていく
	- その次はLINEマーケティングに取り掛かっていく
	- sonicmoovについて
	- 下層ページ
	- 提供サービス
	- トップ
- **グレーのページ**
	- 来週着手で今月中に完成する
- 質問事項
	- 実績一覧で管理画面から操作したいことは？
	- → タグの追加削除のみ

## メモ 3/17
- select itemsのフィールドがapiで取ってこれるか？
	- https://document.microcms.io/management-api/get-api-info
- SEO OGPの設定をフィールドで入れないといけないか確認する？
	- いや、一旦既存のフィールドを活用していいかも、画像もthumbnailフィールドを使う
- お知らせの場合は、thumbnailがないからOGP用の画像のフィールドが必要かも
- 管理画面だけだから、検証しなくて良さそうだからzodなくていいかも、型だけでいい
- 再ビルドするときに、`/api/v1/apis/{endpoint}` からフィールド取得してgen:typesまで自動的にやるようなスクリプトを書く
	- https://document.microcms.io/management-api/get-api-info
- 画面プレビューもnext.js表示できる
	- 実際の実行は阿部さんと合流してから
- vercel microcms nextjsでちゃんと動くかもテストする
	- これは阿部さんがベース作ってくれてから着手する

### TODO（メモ 3/17）
- [ ] SEO（OGP）用のカスタムフィールドを作成する（SEO設定されていなければ、既存の記事情報を使う）
- [ ] 再ビルド時に`/api/v1/apis/{endpoint}`からフィールドを取得して`gen:types`まで自動実行する仕組みを作る
- [ ] Next.jsの画面プレビュー方法を調査する
- [ ] 阿部さんのベース準備後に`Vercel × microCMS × Next.js`の動作確認をする

---

## Next.JS環境構築
- [阿部さんとのSlackでのやりとり](https://sonicmoov.slack.com/archives/C0ABSE589/p1772176371963719)
- [x] microcmsアカウントの作成（指定されたメールアドレスで作成）
- [x] vercelアカウントの作成（指定されたメールアドレスで作成）
- [x] Next.JS初期環境構築
- [x] Next.JS x microCMSでSSGの構成が作れるか可能性を調査する（SSGの方がパフォーマンスが高いので）
	- [x] microCMSの記事追加・更新をフックして再ビルド可能か（これはたぶん余裕でできる）
	- [x] microCMS側の追加・更新分だけの差分ビルドは可能か（こちらは無理な可能性が高い）
		- [x] microCMSのwebhookを使って更新分だけビルドするのは難しそう。ただ、ページまたはfetch単位でrevalidateを設定すれば更新分だけビルドは可能そう。
- microCMSとVercelアカウントに使用するメールアドレス
	- frontend@sonicmoov.com

---

## 参考
- [【Next.js×microCMS×Vercel】初心者向け爆速ブログ作成ハンズオン](https://qiita.com/takakou/items/88dba056e6b391a28db6)
- [microCMS - Getting Started（App Router）](https://document.microcms.io/tutorial/next/next-app-router-getting-started)


