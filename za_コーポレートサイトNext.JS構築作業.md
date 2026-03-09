
## 【非生産稼働】コーポレートサイト改修の概要
- 概要：
	- 現状の構成からNext.JSに切り替える作業だと思う
	- 洋子さんが作成したリニューアル構想がある
- 期間：
	- 3月末まで（予定）ギリギリ
	- 当初の予定では：モックアップ
- アサイン：1~2名予定
- 作業：
	- 1. Next.JSの環境構築
		- アサイン：自分
	- 2. モックアップ実装
		- アサイン：葉田さん、marizoさんあたり
		- 作業：デザイン改修、モックアップ実装

## 1. Next.JS環境の構築
- [阿部さんとのSlackでのやりとり](https://sonicmoov.slack.com/archives/C0ABSE589/p1772176371963719)
- [x] microcmsアカウントの作成（指定されたメールアドレスで作成）
- [x] vercelアカウントの作成（指定されたメールアドレスで作成）
- [x] Next.JS初期環境構築
- [ ] microCMSの調査（Next.JSへ連携までするかは原さん阿部さんに確認）
- [ ] Next.JS x microCMSでSSGの構成が作れるか可能性を調査する（SSGの方がパフォーマンスが高いので）
	- [ ] microCMSの記事追加・更新をフックして再ビルド可能か（これはたぶん余裕でできる）
	- [ ] microCMS側の追加・更新分だけの差分ビルドは可能か（こちらは無理な可能性が高い）
		- [x] microCMSのwebhookを使って更新分だけビルドするのは難しそう。ただ、ページまたはfetch単位でrevalidateを設定すれば更新分だけビルドは可能そう。
- microCMSとVercelアカウントに使用するメールアドレス
	- frontend@sonicmoov.com

## 不確定事項(原さん阿部さんあたりに確認)
- githubのリポジトリ名
	- こちらの確認はローカルで構築できてからでもいい
- microcmsでテスト用のプロジェクトを作って動作テストをしてもいいか

---
## microcmsアカウント情報
- メールアドレス: frontend@sonicmoov.com
- パスワード: d4nfpDCzA88C
## vercelアカウント情報
- メールアドレス: frontend@sonicmoov.com
- パスワード: 

## 参考
- [【Next.js×microCMS×Vercel】初心者向け爆速ブログ作成ハンズオン](https://qiita.com/takakou/items/88dba056e6b391a28db6)
- [microCMS - Getting Started（App Router）](https://document.microcms.io/tutorial/next/next-app-router-getting-started)

## 構成
- Next.JS (AppRouter)
- microcms

## やること

### 🔧 環境セットアップ
- [x] sonicmoovのGitHubにコーポレートのリポジトリを作成する
- [x] Next.jsプロジェクト初期化（App Router / TypeScript）
- [ ] ESLint / Prettier 設定
- [x] Vercelアカウントを作成する
- [x] VercelにGitHubリポジトリを連携・プロジェクト作成
- [x] microCMSアカウントを作成する
- [ ] microCMS サービスを作成する
- [ ] `microcms-js-sdk` インストール・型定義設定
- [ ] codecov導入する

### コンテンツスキーマ設計 (これは確定でやる)
- [ ] 洋子さんが作成したリニューアル構想を把握する
- [ ] スキーマ設計する

### 📐 microCMS 設計・設定
- [ ] 各コンテンツのAPI作成（リスト形式 / オブジェクト形式を判断）
- [ ] 画像管理方針の決定（microCMS側 or 別途CDN）
- [ ] APIキーを環境変数で管理（`.env.local`, Vercel環境変数）
- [ ] Webhook設定（コンテンツ更新時にVercelの再ビルドをトリガー）

### 🏗️ Next.js 実装
- [ ] ディレクトリ構成の設計（`app/`, `components/`, `lib/`, `types/` など）
- [ ] microCMS SDK初期化・型定義ファイル作成（`lib/microcms.ts`）
- [ ] 各ページのルーティング設計（App Router）
- [ ] 共通レイアウト作成（ヘッダー・フッター・ナビゲーション）
- [ ] 各ページ実装（トップ・会社概要・サービス・ニュース一覧・ニュース詳細・採用・お問い合わせ）
- [ ] ISR設定（`revalidate` によるキャッシュ更新）
- [ ] 動的ルート実装（`[slug]` ページのSSG / `generateStaticParams`）
- [ ] SEO対応（`metadata` API によるtitle・description・OGP設定）
- [ ] サイトマップ生成（`sitemap.ts`）
- [ ] `robots.txt` 設定
- [ ] お問い合わせフォーム実装（送信先の確認）

### 🚀 デプロイ・インフラ
- [ ] Vercel 本番・ステージング・プレビュー環境の整備
- [ ] 環境変数をVercelに設定（`MICROCMS_SERVICE_DOMAIN`, `MICROCMS_API_KEY`）
- [ ] カスタムドメイン設定・DNS設定
- [ ] HTTPS確認

### 📊 計測・品質
- [ ] Google Analytics / GTM の設定
- [ ] Lighthouseでパフォーマンス・アクセシビリティ確認
- [ ] `next/image` による画像最適化
- [ ] OGP画像の確認（SNSシェア時の表示）
---
