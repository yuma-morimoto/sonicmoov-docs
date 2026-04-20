
## 概要
- 現状のwordpressの構成からmicroCMS × Next.JSにリニューアルする
- [洋子さんが作成したリニューアル構想 - slack](https://sonicmoov.slack.com/archives/CAQK9SB53/p1768455953395639?thread_ts=1767957465.561049&cid=CAQK9SB53)
- 開発期間: 26年3-4月（当初は26年3月末までだったが伸びた）
- アサイン: 森本、葉田さん、阿部さん
- Backlog: https://sonicmoov.backlog.jp/projects/SMV_HP2021
- Repo: https://github.com/sonicmoov/sonicmoov-corporate-site
- 作業：
	- Next.JSの環境構築
		- アサイン：自分
	- モックアップ実装
		- アサイン：葉田さん、marizoさんあたり
		- 作業：デザイン改修、モックアップ実装

## microcmsアカウント情報
- メールアドレス: frontend@sonicmoov.com
- パスワード: d4nfpDCzA88C
## vercelアカウント情報
- メールアドレス: frontend@sonicmoov.com
- パスワード: なし（PINコード）


## 構成
- Next.JS (AppRouter)
- microcms


## ページ

- トップページ
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

### カスタムフィールド

#### SEO（カスタムフィールドID: `seo`）
各コンテンツAPIに共通フィールドとして追加する。空の場合は既存フィールドにフォールバックする。

| フィールドID          | 表示名         | 種類      | フォールバック                           |
| ---------------- | ----------- | ------- | --------------------------------- |
| `og_title`       | SEOタイトル     | テキスト    | 記事タイトル                            |
| `og_description` | メタディスクリプション | テキストエリア | 本文冒頭                              |
| `og_image`       | OGP画像       | 画像      | `thumbnail`フィールド（ない場合はデフォルトOGP画像） |

**コードで固定するもの**（CMSフィールド不要）
- `og:locale` = `ja_JP`
- `og:site_name` = `株式会社ソニックムーブ | デジタルでコミュニケーションをデザインする`
- `og:type` = コンテンツ種別に応じて固定（`website` / `article`）
- `og:url` / `canonical` = Next.jsで自動生成
- `og:image:secure_url` / `og:image:width` / `og:image:height` = 画像フィールドから自動取得
- `twitter:card` = `summary_large_image`
- `twitter:site` / `twitter:creator` = `@sonicmoov`
- `twitter:title` / `twitter:description` / `twitter:image` = og系と共通化

### スキーマ設計のポイント
- 表示場所（ページ）を定義しない、コンテンツそのものを定義する
	- 将来的な拡張性
	- コンテンツを表示できるページが固定されてしまうため
- 運用者の視点を取り入れる

---
## 議事録
### 3/17 本田さん共有MTG

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

### 3/17 メモ 原さん、安倍さん、葉田さんで共有MTG 
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
- [x] SEO（OGP）用のカスタムフィールドを作成する（SEO設定されていなければ、既存の記事情報を使う）
- [x] 再ビルド時に`/api/v1/apis/{endpoint}`からフィールドを取得して`gen:types`まで自動実行する仕組みを作る
	- [x] → productionでは型が必要ないので仕組み自体不要な可能性がある
- [ ] Next.jsの画面プレビュー方法を調査する

### 4/3 話し合いメモ
- package by featureは採用しない（過剰すぎるため）口頭で相談
- tailwindcssで置き換えるんじゃなくて、現行ページのcssをそのまま使う
---

## 技術調査

### microCMS マネジメントAPI - セレクトフィールドの取得
- **参照:** https://document.microcms.io/management-api/get-api-info
- **結論: 取得できる**
- `GET /api/v1/apis/{endpoint}` のレスポンス内、`kind: "select"` のフィールドに `selectItems` 配列が含まれる

```json
{
  "fieldId": "tags",
  "kind": "select",
  "multipleSelect": true,
  "selectItems": [
    { "id": "Hc1QzudmqV", "value": "更新情報" },
    { "id": "Fx0fDbBj7S", "value": "チュートリアル" }
  ]
}
```

- 一覧ページのフィルターUIは、ビルド時にこの `selectItems` を取得してコンポーネントに渡す方針で実装可能
- APIキーはサーバーサイドのみで使用すること（クライアントに露出しない）
- マネジメントAPIは現在 **ベータ版**

### microCMS 画面プレビュー

- **参照:** https://document.microcms.io/manual/screen-preview
- **結論: 実装可能**（ただし静的サイト構成のため、プレビューページはCSRで実装する必要がある）

#### 仕組み

1. microCMS管理画面でプレビューURLを設定する（`API設定 → 画面プレビュー`）
   - URLパターン例: `https://example.com/preview?contentId={CONTENT_ID}&draftKey={DRAFT_KEY}`
2. 管理画面の「プレビュー」ボタンを押すと、`contentId` と `draftKey` をクエリパラメータに含んだURLに遷移する
3. フロントエンド側でクエリパラメータを取得し、`draftKey` 付きでmicroCMS APIにリクエストして下書きコンテンツを取得・描画する

#### Next.js App Router での実装方針

- `app/preview/page.tsx` を `'use client'` で作成（ビルド時にコンテンツが不明なため、CSR必須）
- `useSearchParams()` で `contentId` と `draftKey` を取得（`<Suspense>` でラップが必要）
- クライアントサイドで microcms-js-sdk を使い、`draftKey` オプション付きでコンテンツを取得・表示
- プレビュー用APIキーは **GETのみの権限** に制限し、`NEXT_PUBLIC_` プレフィックスで公開する
- ローカル開発時は `http://localhost:3000/preview?contentId={CONTENT_ID}&draftKey={DRAFT_KEY}` で確認

### GitHub Actionsの設定方法
- [【コンテンツのWebhook連携】GitHub Actionsの設定方法について](https://help.microcms.io/ja/knowledge/webhook-github-actions-settings)
- [Webhookのタイミング](https://document.microcms.io/manual/webhook-setting)

---

## Next.JS環境構築
- [阿部さんとのSlackでのやりとり](https://sonicmoov.slack.com/archives/C0ABSE589/p1772176371963719)
- microCMSとVercelアカウントに使用するメールアドレス
	- frontend@sonicmoov.com

---

## 参考
- [【Next.js×microCMS×Vercel】初心者向け爆速ブログ作成ハンズオン](https://qiita.com/takakou/items/88dba056e6b391a28db6)
- [microCMS - Getting Started（App Router）](https://document.microcms.io/tutorial/next/next-app-router-getting-started)


