
## 構成
- Next.JS (AppRouter)
- microcms

## やること

### 🔧 環境セットアップ
- [ ] sonicmoovのGitHubにコーポレートのリポジトリを作成する
- [ ] Next.jsプロジェクト初期化（App Router / TypeScript）
- [ ] ESLint / Prettier 設定
- [ ] Vercelアカウントを作成する
- [ ] VercelにGitHubリポジトリを連携・プロジェクト作成
- [ ] microCMSアカウントを作成する
- [ ] microCMS サービスを作成する
- [ ] `microcms-js-sdk` インストール・型定義設定

### 📐 microCMS 設計・設定
- [ ] コンテンツスキーマ設計（ニュース・サービス・会社概要・メンバー・採用情報 など）
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



コーポレートサイト改修: 1~2名予定  

- モックアップは2月後半〜3月にかけて作業（デザイン改修）
- CMSはMicroCMS想定（設計・多分実装も2月から先行で着手できそう）
- FW: Next.js

すごい余裕あればで大丈夫なんだけど、SGの可能性探るためにも  
- microCMSの記事追加・更新をフックして再ビルド可能か（これはたぶん余裕でできる）
- microCMS側の追加・更新分だけの差分ビルドは可能か
みたいなとこ調査できたら嬉しい、たぶん2個目がむりだけど （編集済み） 

  

[19:28]

可能ならSGのほうがパフォーマンスいいはずなので


---
## 質問事項

お疲れ様です。
コーポレートサイトのnextjs環境構築について質問があります。
お聞きしたいこと：
- githubのリポジトリ名
- microcmsとvercelのアカウント作成に使用するメールアドレス
- 以下のリポジトリは参考にできそうでしょうか
	- https://github.com/sonicmoov/reloclub-common-platform-api
お手数ですが、お願いいたします。