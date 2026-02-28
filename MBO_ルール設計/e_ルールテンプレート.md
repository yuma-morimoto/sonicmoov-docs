
  ## プロジェクト概要

  

ナビパークが運営する駐車場をオンラインで決済できるモバイルアプリケーションサービスのフロントエンド。

Nuxt3（SPA）で構築されたWebアプリケーションをFlutterアプリのWebview内で表示している。

  

## 使用可能なコマンド

  

- 型チェック実行: `npm run typecheck`

- Prettierチェック: `npm run lint:prettier`

- ESLintチェック: `npm run lint:script`

- Stylelintチェック: `npm run lint:style`

- ユニットテスト実行: `npm run test`

- ユニットテストをウォッチモードで実行: `npm run test:watch`

- CI用テスト実行: `npm run test:ci`

- カバレッジ計測付きテスト実行: `npm run coverage`

- E2Eテスト実行: `npm run e2e`

  

## 実装のガイドライン

  

実装・修正は以下のガイドラインに従って行なってください:

  

- 既存の実装とユーザーの要求の整合性が合わない場合は要求が間違いである可能性があるため、実装の前に迷わずユーザーに確認を取ること

- `function`は使わずアロー関数を使う

- any型を採用する以外に方法はないか検討する

- 定数の場合は既存の定数定義を確認する

- `AGENTS.md`/`CLAUDE.md`の内容を参照した場合はそれを参照したことがわかるように言葉を発してください（例：`AGENTS.md`のコミットルールには新機能のコミットである場合、`feat`という接頭辞をつけると書いてある）

  

## 単体テストのルール

  

テストコードは以下のルールに従って作成してください:

  

- テストファイルは常に `__test__/ファイル名.spec.ts` の形式で作成する

- テストファイルはソースファイルと同じディレクトリ内に `__test__` ディレクトリを作成して配置する

- 例: `src/utils/useHelper.ts` のテストは `src/utils/__test__/useHelper.spec.ts`

  

## E2Eテストのルール

  

- テストは一連の処理をストーリー形式でテストする

- selectorは使わずにgetByTextやgetByRole等を使って実際にアプリで見える要素（表示テキストなど）を指定すること

- テストを実行する場合は`yarn e2e`とすること

  
  

## ドメイン用語

  

このプロジェクトでは、一般的な意味とは異なる定義で使われる用語が存在します。

**用語の意味は必ずここを正としてください。**

  

- **時間貸し**

- 意味: 駐車場を時間単位で貸し出すこと（関連: 時間貸駐車場）

- 英語: normal

- 識別子: `normal`

- **月極**

- 意味: 駐車場を月単位で貸し出すこと（関連: 月極駐車場）

- 英語: monthly

- 識別子: `monthly`

- **通知（お知らせ）**

- 意味: アプリ内で配信される情報提供機能。

- 英語: notice

- 識別子: `notice`

- **プッシュ通知**

- 意味: プッシュ通知機能。開封追跡機能あり

- 英語: push, notification

- 識別子: `push`

- **仮申込**

- 意味: 月極駐車場の最初の簡易的な申込。本申込の前段階

- 英語: pre application

- 識別子: `preapplication`

- **本申込**

- 意味: 仮申込後の正式な申込。署名・支払い・契約締結へと進む

- 英語: application

- 識別子: `application`

- **区画 / 車室**

- 意味: 駐車場内の個別の駐車スペース

- 英語: garage

- 識別子: `garage`, `garageNumber`

- **定期利用券（パス）**

- 意味: 一定期間特定の駐車場が使い放題になる券

- 英語: Pass

- 識別子: `Pass`, `UsersPass`

- 参照: [features/pass/](src/features/pass/)

- **出庫決済**

- 意味: 時間貸し駐車場から車を出す際の精算

- 英語: Exit Parking Payment

- 識別子: `EXIT_PARKING`

- **初期費用**

- 意味: 月極契約開始時に支払う初回費用（契約手数料・保証金・日割り料金などの合計）

- 英語: Initial Payment

- 識別子: `InitialPayment`

- **夢なびポイント**

- 意味: 夢なびサービス共通のポイント。決済に充当可能

- 英語: Yumenavi Point

- 識別子: `YumenaviPoint`

- 参照: [features/yumenavi/](src/features/yumenavi/)

- **アカウント種別**

- 意味: 個人会員か法人会員かの区分

- 英語: Account Type

- 識別子: `ACCOUNT_TYPES: PERSONAL / CORPORATION`

- **再提出**

- 意味: 月極契約の仮申込や本申込の内容が正しくなかった場合に再提出が行われる

- 英語: Resubmission

- 識別子: `resubmission`

- **再提出依頼**

- 意味: 仮申込や本申込、更新申請の内容に不備があった場合に再提出の依頼が来ることをいう。依頼が来たら再提出画面から正しい情報を送らなければならない。

- **更新申請**

- 意味: 月極契約の更新を申請する

- 英語: Applicant

- 識別子: `applicant`

  

## 技術スタック

  

### コアフレームワーク・コアライブラリ

  

- [Nuxt3](https://nuxt.com/docs/3.x/)

- [TypeScript](https://www.typescriptlang.org/docs/)

- [Node.js 20.x](https://nodejs.org/docs/latest-v20.x/api/)

- [Vite](https://vitejs.dev/guide/)

- [Tailwind CSS](https://tailwindcss.com/docs)

- [yup](https://github.com/jquense/yup)

- [vee-validate](https://vee-validate.logaretm.com/v4/)

- [AWS Amplify](https://docs.amplify.aws/)

- [MSW](https://mswjs.io/)

  

### テスト系

  

- [Vitest 4](https://vitest.dev/)

- [Playwright](https://playwright.dev/)

  

## ディレクトリ構成

  

このプロジェクトは **Package by Feature** アーキテクチャを採用しています。

機能ごとに関連するすべてのコード（API、コンポーネント、ロジック、型定義など）を1つのディレクトリにまとめ、機能の独立性と保守性を意識した設計になっています。

  

### 全体

  

```

src/

├── api/ # API層の共通実装

├── assets/ # スタイルシート（Tailwind CSS）

├── components/ # 再利用可能なUIコンポーネント

├── composables/ # Vue Composables（ロジック・状態管理）

├── config/ # アプリケーション設定

├── constants/ # グローバル定数

├── features/ # 機能別モジュール（Package by Feature）

├── layouts/ # Nuxtレイアウト

├── middleware/ # Nuxtミドルウェア

├── mocks/ # MSWモック設定

├── pages/ # Nuxtページ（ファイルベースルーティング）

├── plugins/ # Nuxtプラグイン

├── public/ # 静的アセット

├── repositories/ # APIクライアント

├── test/ # テスト設定

├── types/ # TypeScript型定義

└── utils/ # ユーティリティ関数

```

  

### 主要ディレクトリの責務

  

#### **features/** - 機能別モジュール（最重要）

  

**Package by Feature** の中核となるディレクトリ。各機能が独立したモジュールとして管理されます。

  

**feature内部構造:**

  

```

features/{feature-name}/

├── api/ # 機能別API実装

│ ├── {feature}Repository.ts

│ └── types/ # API型定義

├── components/ # 機能専用コンポーネント

├── composables/ # 機能専用Composables（ロジック）

│ ├── store/ # useStoreで状態管理

├── constants/ # 定数定義

│ ├── index.ts

│ └── schemas.ts # Yup検証スキーマ

├── mock/ # MSWモック実装

├── model/ # ドメインモデル

├── utils/ # 機能専用ユーティリティ

└── types/ # 機能型定義

```

  

## コミットルール

  

コミットメッセージと絵文字を使用して、フォーマットされたコミットを作成します。

コミットメッセージは日本語で記述してください。

  

- ✨ feat: 新機能

- 🐛 fix: バグ修正

- 📝 docs: ドキュメントの変更

- ♻️ refactor: 機能を変更せずにコードを再構築

- 🎨 style: コードのフォーマット、セミコロンの欠落など

- ⚡️ perf: パフォーマンスの改善

- ✅ test: テストの追加または修正

- 🧑‍💻 chore: ツール、設定、メンテナンス、コメント追加/削除

- 🚧 wip: 作業中

- 🔥 delete: コードまたはファイルの削除

- 🚑 hotfix: 重要な修正

- 🔒 security: セキュリティの改善

  

## プルリクエストの作成ルール

  

- ユーザーの指示にマージ先が指定されていなければ質問すること

- stagingから切ったブランチをdevelopブランチへのマージする時にコンフリクトが起こる場合は、stagingから切ったブランチからdevelop用のブランチを切ってからマージすること（命名：`{stagingから切ったブランチ名}-dev`）