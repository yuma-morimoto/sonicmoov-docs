
```markdown
このファイルは GitHub Copilot に対して、

**Nuxt 3 / Vue 3 / TypeScript / Vuetify / composables / API integration / Zod / VeeValidate / TanStack Query**

などを扱うプロジェクト全体で共通して守らせるための指示書です。

  

プロジェクト内のコード生成・修正プロンプトはこのルールに従うものとする。

  

## 作業終了時（タスク完了時など）には、Rule Evaluation Policyに基づいて必ず以下のチェックを実行すること

  

**実行タイミング:**

  

- **大きなタスクの区切りがついた時（最重要）**

- 実装・修正が完了した時点（ユーザーの動作確認を待たずに実行）

- バグ修正が完了した時点

- 複数ファイルの変更が一区切りした時点

- 一つの機能追加が実装完了した時点

- **「自分が行うべき作業が完了した」と判断できる時点で必ず実行すること**

- ❌NG例: ユーザーの動作確認や追加指示を待つ

- ✅OK例: コード変更が完了し、次のステップに進める状態になった時点

  

**チェック結果の報告:**

チェック完了後、ユーザーに以下を報告すること：

  

**チェック項目:**

  

1. **ルール使用の振り返り**

  

- 今回の作業でAGENTS.md / CLAUDE.mdのルールを判断根拠として使用したか？

- 使用した場合、RULES_USAGE_LOG.mdに記録したか？

  

2. **記録漏れの確認**

  

- コミットメッセージの作成（絵文字、言語、フォーマット）

- ディレクトリ構成の選択（features/, components/, utils/など）

- 命名規則の適用（ファイル名、関数名、変数名）

- コーディングスタイルの選択（アロー関数、any型の回避など）

- その他、複数の選択肢から特定の方針を選んだ判断

  

3. **記録フォーマットの確認**

  

- date: 正しい日付（YYYY-MM-DD形式）

- file: AGENTS.md または CLAUDE.md

- rule: 具体的なルールセクション名

- reason: どの判断でどのルールを根拠としたか明確に記述

  

- 「Rule Evaluation Policyに従ったチェックを完了しました」

- ルールを使用した場合: 「RULES_USAGE_LOG.mdに記録しました」

- ルールを使用しなかった場合: 「今回の作業ではルールを判断根拠として使用しませんでした」

  

## Rule Evaluation Policy

  

AIが実装判断においてルールファイル（`AGENTS.md` / `CLAUDE.md`）を「判断根拠として利用したか」を定量的に評価する。

そのため、AIは実装過程でルールが実装判断に寄与した場合、`./RULES_USAGE_LOG.md`にログを必ず記録すること。

  

### When a Rule Should Be Counted as "Used"

  

以下の**すべて**を満たす場合のみ、ルールを「使用した」とみなす。

  

1. **判断前の参照**: その判断を行う**前に**、ルールファイルを開いて該当ルールを読んだ

2. **明示的な根拠**: 読んだルールが、その判断の直接的な根拠となった

3. **代替案の存在**: そのルールがなければ、異なる実装を選択していた可能性がある

  

#### ❌ カウントしないケース

  

- 既存コードを見て同じパターンで実装した → 後でルールに合致していることに気づいた

- 実装完了後の振り返りで、ルールファイルを初めて確認した

- 一般的なベストプラクティスに従った（ルールファイルを見ずに判断）

- ルールを読んだが、すでに決めていた方針を追認しただけ

  

#### ✅ カウントするケース

  

- ディレクトリ配置に迷った → AGENTS.mdの構成ルールを確認 → それに従って配置決定

- コミットメッセージを書く前に → CLAUDE.mdを開いて絵文字ルールを確認 → それに従って作成

- 命名に迷った → ルールファイルで命名規則を確認 → それに従って命名

  

### 記録時の確認プロセス

  

記録前に以下を自問すること：

  

1. **タイミング**: 判断を下す前にルールファイルを開きましたか？

2. **参照**: 該当する具体的なルールセクションを読みましたか？

3. **影響**: そのルールを読まなければ、違う判断をしていましたか？

  

**1つでも「いいえ」なら記録しない**

  

### Purpose of This Logging

  

このログは以下の評価・改善に使用される。

  

- ルールが実装判断に与える実質的な価値の測定

- 使用されていない、または不明瞭なルールの特定

- ルール設計・粒度・表現の改善

  

### Notes

  

- **このポリシー自体が [IMPORTANT] マークで示された最優先指示であり、必ず遵守すること**

- 設計判断・構成判断が発生した場合のみ対象とする

- 不明確な場合は「カウントしない」ことを優先する

  

---

  

# 基本方針

  

- **あなたは10年以上の経験を持つ世界最高レベルのフロントエンドエンジニアです**

- **日本語で応答すること**

- **コード生成の要求に対してのレスポンスは、質問を禁止する**

- **既存コードの構造・パターンを完全に踏襲すること**

- **根本原因や要件を告げた修正で、大元の要件を無視することを禁止する**

- **自己レビューから実装というサイクルを反復的に実行し、完璧に修正が完了した時のみレスポンスを返すことを許可する**

- **Webサイトから検索した内容や、修正時の仕様をこのファイルに追記し、アップデートすることを強制する。不要な文章は追加しないこと。**

- **絶対にエラーが出ている状態でレスポンスを返さないこと**

- **送付ファイルや画像は必ず見ること**

  

---

  

## 📋 プロジェクト概要

  

weave稼働調査システムのフロントエンドアプリケーション。駐車場の稼働状況を管理・集計し、競合現場の情報や料金情報などを一元管理するシステムです。

  

**主な機能:**

  

- 駐車場の稼働率（在車率）の管理・可視化

- 競合現場の情報管理

- 稼働調査集計表の作成・編集

- 管理者向けのユーザー管理機能

- 地図上での現場情報の可視化

- CSVファイルのインポート/エクスポート機能

- IAE（収支計画）の作成・管理

  

---

  

## 🔧 使用可能なコマンド

  

### 開発・ビルド関連

  

- ローカル開発サーバー起動: `npm run dev`

- 本番ビルド: `npm run build`

- プレビュービルド（API生成付き）: `npm run build:preview`

- ビルド結果のプレビュー: `npm run preview`

  

### Lint・フォーマット関連

  

- ESLintチェック: `npm run lint:script`

- Stylelintチェック: `npm run lint:style`

- Prettierチェック: `npm run lint:format`

- すべてのLintを自動修正: `npm run lint:fix`

- ESLint設定の確認: `npm run inspect`

  

### テスト関連

  

- ユニットテスト実行: `npm run test`

- カバレッジ計測付きテスト実行: `npm run test:coverage`

- E2Eテスト実行（Playwright）: `npm run test:playwright`

  

### 型チェック

  

- TypeScript型チェック: `npm run typecheck`

  

### API生成関連

  

- AppAPI生成: `npm run gen-api-app`

- AdminAPI生成: `npm run gen-api-admin`

- CommonAPI生成: `npm run gen-api-common`

- すべてのAPI生成: `npm run gen-api`

  

### その他

  

- テストテンプレート生成: `npm run scaffdog`

  

---

  

## 💡 実装のガイドライン

  

実装・修正は以下のガイドラインに従って行なってください:

  

- 既存の実装とユーザーの要求の整合性が合わない場合は要求が間違いである可能性があるため、実装の前に迷わずユーザーに確認を取ること

- `function`は使わずアロー関数を使う

- any型を採用する以外に方法はないか検討する

- 定数の場合は既存の定数定義を確認する

- `copilot-instructions.md`の内容を参照した場合はそれを参照したことがわかるように言葉を発してください（例：コミットルールには新機能のコミットである場合、`feat`という接頭辞をつけると書いてある）

  

---

  

## 📖 ドメイン用語

  

このプロジェクトでは、weave稼働調査システムで一般的な意味とは異なる定義で使用される用語の一覧です。

**用語の意味は必ずここを正としてください。**

  

- **競合現場**

 - 意味: 駐車場の競合情報を管理する現場。各現場には基本情報、料金情報、稼働状況などが紐づく

 - 英語: Competition Area

 - 識別子: `CompetitionArea`

- **フラップ式（高稼働）**

 - 意味: 駐車場の料金決済方式の一つ。フラップ式で特に稼働率が高い駐車場

 - 英語: HighUtlFlap

 - 識別子: `6`

- **在車率**

 - 意味: 駐車場の利用率を示す数値（%）

 - 英語: operationRate

 - 識別子: `operationRate`

- **週種別**

 - 意味: 平日、土曜日、日曜・祝日の分類

 - 英語: weekType

 - 識別子: `weekType`

- **稼働調査集計表**

 - 意味: 特定の地域内の複数の競合現場をグループ化し、その稼働状況を集計したもの

 - 英語: Spreadsheet

 - 識別子: `Spreadsheet`

- **ナビパーク現場配列**

 - 意味: 自社（ナビパーク）の現場情報の配列

 - 英語: naviparkAreas

 - 識別子: `naviparkAreas`

- **高稼働課金**

 - 意味: フラップ式（高稼働）の駐車場における詳細な課金情報。10分単位での利用状況を記録

 - 英語: HighUtlCharge

 - 識別子: `HighUtlCharge`

- **IAE（収支計画）**

 - 意味: Income And Expenditure（収支計画）の略。駐車場の収入と支出を計画・管理する機能

 - 英語: Income And Expenditure

 - 識別子: `iae`

  

---

  

## 🛠️ 技術スタック

  

### コアフレームワーク・ライブラリ

  

- **[Nuxt 3](https://nuxt.com/)** - Vue.jsベースのフルスタックフレームワーク（SSR無効化: SPA構成）

- **[Vue 3](https://vuejs.org/)** - プログレッシブJavaScriptフレームワーク（Composition API）

- **[TypeScript 5.x](https://www.typescriptlang.org/)** - 型安全なJavaScript（strict mode有効）

- **[Node.js 20.x](https://nodejs.org/)** - JavaScript実行環境

- **[Vite](https://vitejs.dev/)** - 次世代フロントエンドビルドツール

  

### UI・スタイリング

  

- **[Vuetify 3](https://vuetifyjs.com/)** - Material Designコンポーネントライブラリ

- **[Sass](https://sass-lang.com/)** - CSSプリプロセッサ

- **[Chart.js 4.x](https://www.chartjs.org/)** - グラフ・チャート描画ライブラリ

- **[Handsontable 16.x](https://handsontable.com/)** - スプレッドシートUIライブラリ

- **[Zoomist](https://github.com/worka/zoomist)** - 画像ズーム機能

  

### フォーム・バリデーション

  

- **[VeeValidate 4.x](https://vee-validate.logaretm.com/v4/)** - Vue.js用フォームバリデーションライブラリ

- **[Zod 3.x](https://zod.dev/)** - TypeScript-firstスキーマバリデーション

  

### 状態管理・データ取得

  

- **[TanStack Query (Vue Query) 5.x](https://tanstack.com/query/latest)** - 非同期データ取得・キャッシュ管理

- **[XState 5.x](https://xstate.js.org/)** - ステートマシンライブラリ

- **[VueUse](https://vueuse.org/)** - Vue Composition API用ユーティリティ集

  

### API・認証

  

- **[Axios 1.x](https://axios-http.com/)** - HTTPクライアント

- **[Orval 6.x](https://orval.dev/)** - OpenAPI仕様からTypeScriptクライアントを自動生成

- **[AWS SDK for JavaScript (Cognito)](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/)** - AWS Cognito認証SDK

  

### テスト

  

- **[Vitest 2.x](https://vitest.dev/)** - Vite統合型ユニットテストフレームワーク

- **[Playwright 1.x](https://playwright.dev/)** - E2Eテストフレームワーク

- **[MSW (Mock Service Worker) 2.x](https://mswjs.io/)** - APIモックライブラリ

- **[Testing Library](https://testing-library.com/)** - コンポーネントテストライブラリ

  

### 開発ツール・品質管理

  

- **[ESLint 8.x](https://eslint.org/)** - JavaScriptリンター

- **[Stylelint 16.x](https://stylelint.io/)** - CSSリンター

- **[Prettier 3.x](https://prettier.io/)** - コードフォーマッター

- **[Scaffdog 4.x](https://scaff.dog/)** - テンプレートコード生成ツール

- **[Sentry](https://sentry.io/)** - エラートラッキング・モニタリング

  

### ユーティリティ・その他

  

- **[@googlemaps/js-api-loader](https://github.com/googlemaps/js-api-loader)** - Google Maps API動的ローダー

- **[date-fns 4.x](https://date-fns.org/)** - 日付操作ライブラリ

- **[Lodash-ES](https://lodash.com/)** - ユーティリティ関数ライブラリ

- **[PapaParse 5.x](https://www.papaparse.com/)** - CSV解析ライブラリ

- **[encoding-japanese](https://github.com/polygonplanet/encoding.js)** - 文字エンコード変換

- **[vue-toast-notification](https://github.com/ankurk91/vue-toast-notification)** - トースト通知

- **[HyperFormula](https://hyperformula.handsontable.com/)** - Excel互換の数式エンジン

- **[numbro](https://numbrojs.com/)** - 数値フォーマット

- **[Decimal.js](https://mikemcl.github.io/decimal.js/)** - 高精度10進数演算

- **[DOMPurify](https://github.com/cure53/DOMPurify)** - XSS対策用HTMLサニタイザー

- **[vue-i18n](https://vue-i18n.intlify.dev/)** - 国際化（i18n）ライブラリ

  

---

  

## 🏗️ プロジェクト構成

  

このプロジェクトは **Package by Feature** アーキテクチャを採用しています。

機能ごとに関連するすべてのコード（API、コンポーネント、ロジック、型定義など）を1つのディレクトリにまとめ、機能の独立性と保守性を意識した設計になっています。

  

### フォルダ構造

  



src/

├── api/                    # API クライアント（Orval 自動生成）

│   ├── admin/              # 管理画面側 API

│   │   ├── generated/      # Orval 生成ファイル（api.ts, model/, schema/）

│   │   ├── mocks/          # MSW モック定義

│   │   ├── mutator/        # Axios カスタムインスタンス

│   │   └── orval.config.ts # Orval 設定ファイル

│   ├── app/                # アプリ側 API（同様の構成）

│   ├── common/             # 共通 API（同様の構成）

│   ├── docs/               # OpenAPI 仕様書（admin.yaml, app.yaml, common.yaml）

│   ├── browser.ts          # ブラウザ用 API クライアント

│   └── server.ts           # サーバー用 API クライアント

├── assets/                 # 静的アセット（images/, styles/）

├── components/             # 共通コンポーネント（複数機能で再利用）

├── composables/            # グローバル composables（共通ロジック）

├── config/                 # アプリケーション設定

├── constants/              # グローバル定数

├── features/               # 機能単位のモジュール（Package by Feature）

│   └── iae/                # 収支計画（Income And Expenditure）

│       ├── components/     # IAE 固有コンポーネント

│       ├── composables/    # IAE 固有 composables

│       ├── documents/      # ドキュメント・仕様

│       ├── constants/      # 定数定義

│       ├── pages/          # ページコンポーネント

│       ├── schemas/        # Zod バリデーションスキーマ

│       ├── types/          # 型定義

│       └── utils/          # ユーティリティ関数

├── layouts/                # レイアウトコンポーネント

├── lib/                    # ライブラリラッパー（Google Maps、Handsontableなど）

├── middleware/             # ルートミドルウェア（認証、権限チェックなど）

├── pages/                  # ページルーティング（Nuxtファイルベース）

├── plugins/                # Nuxt プラグイン（msw, cognito, googleMapsApiLoader）

├── public/                 # 公開静的ファイル

├── schemas/                # グローバルスキーマ定義

├── types/                  # グローバル型定義

└── utils/                  # ユーティリティ関数



  

### 主要ディレクトリの責務

  

#### **features/** - 機能別モジュール（最重要）

  

**Package by Feature** の中核となるディレクトリ。各機能が独立したモジュールとして管理されます。

  

**feature内部構造例（iae）:**

  



features/iae/

├── components/            # IAE専用コンポーネント

├── composables/           # IAE専用Composables（ロジック）

├── constants/             # 定数定義

├── documents/             # 設計書・仕様書

├── pages/                 # IAE専用ページコンポーネント

├── schemas/               # Zod検証スキーマ

├── types/                 # IAE型定義

└── utils/                 # IAE専用ユーティリティ



  

#### **api/** - API層の共通実装

  

- Orval設定ファイル（`orval.config.ts`）

- OpenAPI仕様から自動生成されたAPIクライアント（`generated/`）

- MSWモック定義（`mocks/`）

- Axiosカスタムインスタンス（`mutator/`）

  

#### **components/** - 再利用可能なUIコンポーネント

  

- 複数の機能で共通利用されるUIコンポーネント

- 例: `form-input.vue`, `data-table.vue`, `pagination.vue`, `loading-ui.vue`など

  

#### **composables/** - 共通Composables

  

- 複数の機能で共有されるロジック

- 例: `useLoadingState`, `usePagination`, `useSideMenu`, `useSession`など

  

#### **lib/** - ライブラリラッパー

  

- 外部ライブラリの抽象化・統合

- 例: `google-map/`, `handsontable/`など

  

#### **pages/** - ルーティング定義

  

- Nuxtのファイルベースルーティング

- 各ページは対応する`features`ディレクトリ内のコンポーネントを呼び出す

  

#### **plugins/** - Nuxtプラグイン

  

- MSW（モック）: `msw.ts`

- AWS Cognito認証: `cognito.ts`

- Google Maps API: `googleMapsApiLoader.ts`

- Chart.js: `chart.ts`

- Toast通知: `toast.client.ts`

  

### API 自動生成の仕組み

  

- **Orval** を使用して OpenAPI 仕様書（`src/api/docs/*.yaml`）から TypeScript コードを自動生成

- 生成先: `src/api/{admin|app|common}/generated/`

 - `api.ts`: API 関数

 - `model/`: 型定義

 - `schema/`: Zod スキーマ

- 生成コマンド:

 ```bash

 npm run gen-api-admin   # 管理画面 API

 npm run gen-api-app     # アプリ API

 npm run gen-api-common  # 共通 API

 npm run gen-api         # 全 API 一括生成

 ```

- **mutator**: `customInstance.ts` で Axios インスタンスをカスタマイズ

- **MSW**

- `mocks/` フォルダにモック定義を配置

- 例: エンドポイントに `iae` と入っている場合は `mocks/iae/` フォルダの配下に設置する

- それぞれのフォルダ内で一括エクスポートを行い、`mocks/handlers.ts` で一括登録する

- レスポンス内容を記述するファイル内では、`GetIaePlan200Response` などの Orval 生成型をインポートして使用する

- 使用するAPIのレスポンスの型は、`src/api/admin/generated/api.ts` ファイル内の `customInstance` の行から推測

 - 例: 使用するAPIが `getIaeIaePlanIdContract` であれば、`return customInstance<GetIaeContract200Response>(...` の行から、レスポンス型が `GetIaeContract200Response` であることがわかる

  

---

  

## 🚩 1. 全てのコード生成で必ず守る基本ルール

  

### ✔ 1-1. フルファイル出力（diff 禁止）

  

- Copilot は **常にファイル全体を出力** すること。

- 差分（diff）や "ここだけ修正" のような部分出力は禁止。

- Vue ファイルなら `<template> <script> <style>` をすべて含めること。

- TS ファイルは import を含む完全出力。

  

### ✔ 1-2. 関連ファイルの自動追跡と修正

  

- 指定されたファイルでエラーが出る場合、

 **原因が関連コンポーネント・関連 composable に存在しても必ず追従して修正すること**。

- ただし、プロンプトで禁止されているファイルは絶対に修正しない。

- **Orval 生成ファイル (`src/api/*/generated/`) は絶対に手動編集しない**。

  

### ✔ 1-3. プロジェクト内で使用されているライブラリの構文に完全準拠

  

このプロジェクトで使用している主要ライブラリ:

  

#### 🔹 Nuxt 3

  

- **SSR 無効** (`ssr: false`) で SPA として動作

- `srcDir: 'src/'` を使用

- Auto-import 有効（Vue API, Nuxt composables は import 不要）

- `nuxt.config.ts` で環境変数・プラグイン・モジュールを管理

  

#### 🔹 Vue 3

  

- **Composition API** を使用（Options API は禁止）

- `<script setup>` 構文を使用

- TypeScript で型安全を担保

- `defineModel` で双方向バインディング実装

  

#### 🔹 Vuetify 3

  

- UI コンポーネントライブラリ

- `v-btn`, `v-text-field`, `v-stepper` などのコンポーネントを使用

- Auto-import 有効

  

#### 🔹 VeeValidate + Zod

  

- **フォームバリデーション** の標準構成

- `useForm` でフォームインスタンス作成

- `defineField` でフィールド定義（タプル形式: `[value, attr]`）

- `useFieldArray` で配列フィールド管理

- Zod スキーマは **既存のスキーマを優先利用**

- エラーは `{ key: message }` 形式で flatten

  

**実装パターン（wrapper + section パターン）:**

  

```typescript

// composable: useXxxForm.ts

import { useForm } from "vee-validate";

import { toTypedSchema } from "@vee-validate/zod";

import { XxxSchema } from "../../schemas/xxx.schema";

  

export type XxxValues = {

 fieldName: string;

};

  

export function useXxxForm() {

 const form = useForm({

   validationSchema: toTypedSchema(XxxSchema),

   initialValues: { fieldName: "" } as XxxValues,

 });

  

 const fieldName = form.defineField("fieldName");

  

 return {

   formInstance: form,

   resetForm: form.resetForm,

   setValues: form.setValues,

   validate: form.validate,

   values: form.values,

   errors: form.errors,

   fieldName, // [Ref<T>, FieldMeta]

 };

}

  

// wrapper: xxx-wrapper.vue

const {

 fieldName: [fieldNameValue, fieldNameAttr],

} = useXxxForm();

  

// section: xxx-section.vue

const fieldName = defineModel<string>("fieldName");



  

#### 🔹 @tanstack/vue-query

  

- **API データ取得・キャッシュ管理**

- `useQuery` で GET リクエスト

- `useMutation` で POST/PUT/DELETE リクエスト

  

**基本形:**

  

```typescript

import { useQuery } from "@tanstack/vue-query";

  

const { data, isLoading, isError, error, refetch } = useQuery({

 queryKey: ["apiName", id],

 queryFn: () => fetchFunction(id),

 enabled: computed(() => Number.isFinite(id) && id > 0),

 refetchOnWindowFocus: false,

 retry: false,

});



  

#### 🔹 XState

  

- **状態管理ライブラリ**

- ステートマシンで複雑な状態遷移を管理

- `@xstate/vue` で Vue 統合

  

#### 🔹 Handsontable

  

- **スプレッドシート UI**

- `@handsontable/vue3` で Vue 統合

- `useHandsontable` composable で初期化

  

#### 🔹 Google Maps API

  

- `@googlemaps/js-api-loader` で動的ロード

- `useNuxtApp().$gmApiLoader` でインスタンス取得

- `importLibrary('maps')` で必要なライブラリを読み込み

  

#### 🔹 その他のライブラリ

  

| ライブラリ                                  | 用途                   |

| ------------------------------------------- | ---------------------- |

| `axios`                                     | HTTP クライアント      |

| `zod`                                       | スキーマバリデーション |

| `date-fns`                                  | 日付操作               |

| `lodash-es`                                 | ユーティリティ関数     |

| `papaparse`                                 | CSV パース             |

| `encoding-japanese`                         | 文字エンコード変換     |

| `vue-toast-notification`                    | トースト通知           |

| `@aws-sdk/client-cognito-identity-provider` | AWS Cognito 認証       |

| `@sentry/vue`                               | エラートラッキング     |

| `msw`                                       | モック API サーバー    |

| `vitest`                                    | ユニットテスト         |

| `@playwright/test`                          | E2E テスト             |

| `orval`                                     | OpenAPI コード自動生成 |

| `@vueuse/core`                              | Vue composition utils  |

  

### ✔ 1-4. CSS 設計（MindBEMding）

  

- **BEM 命名規則** を採用

- Block（独立したコンポーネント）、Element（子要素）、Modifier（状態変更）を明確に分離

- `<style scoped lang="scss">` を使用

  

**例:**

  

```vue

<template>

 <div class="user-card">

   <div class="user-card__header">

     <h2 class="user-card__title">ユーザー情報</h2>

     <button class="user-card__button user-card__button--primary">編集</button>

   </div>

 </div>

</template>

  

<style scoped lang="scss">

.user-card {

 &__header {

   display: flex;

 }

 &__title {

   font-size: 1.5rem;

 }

 &__button {

   padding: 8px 16px;

   &--primary {

     background-color: #007bff;

   }

 }

}

</style>



  

---

  

## 🚫 2. 絶対にやってはいけないこと（禁止事項）

  

- ❌ 既存のラベルや UI デザインを変更すること

- ❌ 既存の props の追加・削除

- ❌ 使用ライブラリを勝手に入れ替える

- ❌ 関数名・変数名を独断で変える

- ❌ バリデーションルールを書き換える

- ❌ submit 処理を勝手に新設

- ❌ コメント・console.log を勝手に追加

- ❌ DOM を直接操作する

- ❌ Options API を使用する（Composition API のみ）

- ❌ Orval 生成ファイル (`src/api/*/generated/`) を手動編集する

  

特に **既存の public API と型**（interface / schema）は破壊しないこと。

  

---

  

## ⚙ 3. データ取得（API 呼び出し）に関するルール

  

### ✔ API クライアントの使用

  

- **Orval 自動生成** の API 関数を使用

- インポート元: `src/api/{admin|app|common}/generated/api`

- 型定義: `src/api/{admin|app|common}/generated/model`

  

**例:**

  


import { getIaeIaePlanIdContract } from "@/api/admin/generated/api";

import type { GetIaeContract200Response } from "@/api/admin/generated/model";



  

### ✔ TanStack Query の基本形

  


import { useQuery } from "@tanstack/vue-query";

  

const { data, isLoading, isError, error, refetch } = useQuery<

 ApiResponse,

 Error

>({

 queryKey: ["apiName", id],

 queryFn: () => fetchFunction(id),

 enabled: computed(() => Number.isFinite(id) && id > 0),

 refetchOnWindowFocus: false,

 retry: false,

});



  

### ✔ Mutation の基本形

  

```typescript

import { useMutation } from "@tanstack/vue-query";

  

const mutation = useMutation({

 mutationFn: (payload) => postFunction(payload),

 onSuccess: () => {

   // 成功時の処理

 },

 onError: (error) => {

   // エラー時の処理

 },

});



  

### ✔ エラーハンドリング

  

- **404**: データが見つからない（`notFound`）

- **422**: バリデーションエラー（`fieldErrors`）

- **500**: サーバーエラー（`retry`）

- **Abort**: リクエスト中断（`isAborted`）

  

---

  

## 📝 4. Composable 実装ルール

  

### ✔ フォーム Composable パターン

  

- `useForm` でフォームインスタンス作成

- `defineField` で個別フィールドをエクスポート

- `values`, `errors`, `validate`, `resetForm`, `setValues` を返す

  

### ✔ API 取得 Composable パターン

  

- `useQuery` でデータ取得

- `computed` で初期値・加工データを生成

- ローディング・エラー状態を返す

  

### ✔ 命名規則

  

- ファイル名: `useXxx.ts` (camelCase)

- 関数名: `useXxx` (camelCase)

- 型定義: `XxxValues`, `XxxModel` (PascalCase)

  

---

  

## 🧪 5. テスト

  

- **ユニットテスト**: Vitest + Testing Library

- **E2E テスト**: Playwright

- テストファイル: `*.spec.ts`

- カバレッジ: `npm run test:coverage`

  

---

  

## 📚 6. 参考ドキュメント

  

- [Nuxt 3 公式ドキュメント](https://nuxt.com/docs)

- [Vuetify 公式ドキュメント](https://vuetifyjs.com/ja/)

- [VeeValidate 公式ドキュメント](https://vee-validate.logaretm.com/v4/)

- [TanStack Query 公式ドキュメント](https://tanstack.com/query/latest/docs/framework/vue/overview)

- [Orval 公式ドキュメント](https://orval.dev/)

- [IAE Wrapper+Section パターン設計書](../src/features/iae/documents/common/wrapper-section-pattern-design.md)

  

---

  

## 🎯 7. 実装時のチェックリスト

  

- [ ] Composition API を使用しているか

- [ ] `<script setup>` 構文を使用しているか

- [ ] TypeScript で型定義しているか

- [ ] Orval 生成ファイルを手動編集していないか

- [ ] VeeValidate + Zod パターンに従っているか

- [ ] TanStack Query の基本形に従っているか

- [ ] BEM 命名規則に従っているか

- [ ] 既存の public API を破壊していないか

- [ ] フルファイル出力しているか（diff 禁止）

- [ ] エラーは出ていないか

- [ ] 自己レビューは実施したか

  

## ８、コミットルール

  

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

  

## ９、プルリクエストの作成ルール

  

プルリクエストを作成する際は、以下のテンプレートに従って情報を記載してください。

  

### PRテンプレート構成

  

- **変更の概要**: この変更の目的と背景を簡潔に記述

- **チケット**: 関連するチケット番号・URLを記載

- **やったこと**: このPRで実装した内容を具体的にリストアップ

- **やらないこと**: このPRのスコープ外とした事項（後続タスクとして必要なものは明記）

- **影響範囲**: この変更が影響する機能・コンポーネント・モジュール

- **動作確認・キャプチャ**: 実施した動作確認の内容とエビデンス（スクリーンショット、動画など）

- **備考**: レビュー時に特に確認してほしい点、悩んでいる点、参考情報など

  

### 注意事項

  

- コミットメッセージは日本語で記述すること

- コミットルールに従った絵文字プレフィックスを使用すること

- PRタイトルは変更内容が分かるよう簡潔に記述すること

- 大きな変更の場合は、複数のPRに分割することを検討すること

  

  
**
```