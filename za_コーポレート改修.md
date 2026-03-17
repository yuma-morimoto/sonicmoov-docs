
## 【非生産稼働】コーポレートサイト改修の概要
- 概要：
	- 現状の構成からNext.JSに切り替える作業だと思う
	- [洋子さんが作成したリニューアル構想 - slack](https://sonicmoov.slack.com/archives/CAQK9SB53/p1768455953395639?thread_ts=1767957465.561049&cid=CAQK9SB53)
- 期間：
	- コーポリニューアル対応は26年3月末まで（予定）ギリギリ
- アサイン：1~2名予定
- 作業：
	- Next.JSの環境構築
		- アサイン：自分
	- 
	- モックアップ実装
		- アサイン：葉田さん、marizoさんあたり
		- 作業：デザイン改修、モックアップ実装

## 1. Next.JS環境の構築
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
- [x] Vercelアカウントを作成する
- [x] VercelにGitHubリポジトリを連携・プロジェクト作成
- [x] microCMSアカウントを作成する
- [x] codecov導入する

### コンテンツスキーマ設計 (これは確定でやる)
- [ ] 本田洋子さんが作成した[リニューアル構想](https://sonicmoov.slack.com/archives/CAQK9SB53/p1768455953395639?thread_ts=1767957465.561049&cid=CAQK9SB53)を把握する
- [ ] スキーマ設計する


## ページ

- トップページ（固定でいいかも？）
- SMVについて
- サービストップ
- 実績一覧
- 実績詳細

## スキーマ設計

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