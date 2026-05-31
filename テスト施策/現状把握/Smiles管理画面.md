## 調査概要

- 調査日: 2026-05-20
- 対象プロジェクト: `/Users/l_0026/Documents/smiles-circle-frontend`
- 対象種別: Smiles Circle フロントエンド
- 技術構成: React 19 / React Router v7 / TypeScript / Vite / Tailwind CSS / TanStack Query / Orval / MSW / Vitest
- アプリ特性: Smiles CircleのSPA。作品一覧・作品詳細・作品投稿/編集、コメント、MVP、お知らせ、FAQ、利用規約、マイページ、ユーザー詳細などを扱う。
- 調査方法: `package.json`、`README.md`、`vitest.config.ts`、`vitest.workspace.ts`、`.github/workflows/*`、`.github/pull_request_template.md`、`lefthook.yml`、`app` 配下のテスト・画面・機能ファイル、git履歴を確認
- 注意: 手動確認の実態、不具合が出やすい箇所は、リポジトリ内の記録から把握できる範囲で整理した。実際の運用ヒアリングは未実施。

## サマリー

Smiles Circleは、ユニットテストの導入が始まっており、共通ロジック、定数、検索パラメータ、ルーティング補助、投稿フィルタ、MVP変換、S3署名付きURL取得など、画面から切り出された処理を中心にテストがある。CIでも `develop` 向けPRでlint、typecheck、API生成、ユニットテスト、ブラウザテストを実行しており、最低限の品質ゲートは用意されている。

一方で、テスト資産はまだロジック中心で、画面・機能フローの自動テストは薄い。React Testing Libraryは導入されているが、画面単位の描画テストはほぼなく、Vitest Browserの対象も現状は `vitest-browser-example/HelloWorld.browser.spec.tsx` のサンプルのみ。PlaywrightはVitest Browserの実行基盤として入っているが、E2E用の `playwright.config` や `e2e` ディレクトリは確認できない。

標準化の観点では、「ロジックファイルはカバレッジ80%を目指す」というREADME上の方針、PRテンプレートの「動作確認・キャプチャ」、CIで `gen-api` -> lint -> typecheck -> test を流す構成は他案件にも展開しやすい。一方で、投稿/編集/コメント/マイページのようなユーザー操作が多い領域をどこまで自動化するか、画面フローと手動確認の分担を決める余地が大きい。

## 1. 現在あるテスト資産を把握

### テスト資産

| 種別 | 有無 | 主な場所 | 使用ツール | 備考 |
|---|---:|---|---|---|
| ユニットテスト | あり | `app/**/*.spec.ts`、一部 `app/**/*.spec.tsx` | Vitest / jsdom | `app` 配下に15ファイル。テストケース定義は67件。共通処理・hooks・constants・utilsが中心 |
| コンポーネントテスト | 一部あり | `app/components/Pagination.spec.ts` | Vitest | DOM描画というより、ページ番号配列を作るロジックのテスト |
| ブラウザテスト | あり | `vitest-browser-example/HelloWorld.browser.spec.tsx` | Vitest Browser / Playwright Chromium | サンプルのみで、アプリ本体のブラウザテストは確認できない |
| E2E | なし | なし | なし | `playwright.config`、`e2e` ディレクトリ、E2E用specは確認できない |
| APIモック | あり | `app/api/*/mocks`、`app/api/server.ts`、`public/mockServiceWorker.js` | MSW | circle/common APIのモック基盤あり。一部テストでもMSWを利用 |
| カバレッジ | 設定あり | `vitest.config.ts` | Vitest coverage v8 | `coverage.enabled: true`、`all: true`。ただし調査時点で生成済みcoverage成果物は確認できない |

### CI / ローカル品質ゲート

- `.github/workflows/ci_develop.yml`
  - `develop` へのPRで起動
  - `npm run gen-api`
  - `npm run lint`
  - `npm run typecheck`
  - `npx playwright install`
  - `npm run test && npm run test:browser`
- `.github/workflows/ci_main.yml`
  - `main` push / PRで起動
  - `npm run gen-api`
  - `npm run lint`
  - `npm run typecheck`
  - `npm run test`
  - `npm run build`
- `lefthook.yml`
  - pre-commitでlint修正、typecheck、format修正、format checkを実行する設定
- `.github/workflows/review.yml`
  - develop向けPRでAI PR Reviewを実行

### 調査時点のテスト実行結果

`npm run test -- --run` を実行したところ、12ファイル57件は成功したが、3スイートは収集前に失敗した。

- `app/hooks/useAuth.spec.ts`
  - `~/api/circle/generated/api` が存在せずimport解決に失敗
- `app/utils/s3/getS3FilePresignedUrl.spec.ts`
  - `~/api/common/generated/model` が存在せずimport解決に失敗
- `app/features/creation-detail/hooks/useHTMLContent.spec.ts`
  - ローカルの `node_modules` に `sanitize-html` が無くimport解決に失敗

CIではテスト前に `npm run gen-api` を実行するため、生成APIファイル依存のテストはCI手順とセットで動かす前提になっている。

## 2. どのレイヤーにどんなテストがあるか

### レイヤー別の役割

| レイヤー | 現状 | 主な役割 |
|---|---|---|
| ユニット | あり | 日付フォーマット、末尾スラッシュ除去、カテゴリ検索、meta生成、検索パラメータバリデーション、投稿詳細URL判定、cookieからのアクセストークン取得など |
| hooks | あり | 認証ユーザー判定、投稿フィルタ、投稿ステップ、フッター状態など、UI状態に近いロジック |
| コンポーネント | 限定的 | `Pagination` のページ配列生成ロジックのみ。画面描画・ユーザー操作のテストは薄い |
| 統合テスト | 薄い | APIモックやReact Queryを含めた画面単位の統合テストはほぼ確認できない |
| ブラウザテスト | サンプルのみ | Vitest Browserの設定はあるが、アプリ本体の確認にはまだ使われていない |
| E2E | なし | 投稿、編集、コメント、ログイン状態、マイページなどのユーザージャーニーを自動で確認する層は未整備 |

### テスト件数の概観

- `app` 配下のspec: 15ファイル
- ブラウザspec: 1ファイル。ただし `vitest-browser-example` 配下のサンプル
- テストケース定義: 67件
- `app` 配下のTSXファイル: 168ファイル
- `app/features/*/page/Page*.tsx`: 9画面
- `app/routes` 配下のroute TSX: 11ファイル

### テストケース数が多いファイル

- `app/constants/CreationCategory.spec.ts`: 10件
- `app/utils/removeTrailingSlash.spec.ts`: 8件
- `app/hooks/useCreationFilter.spec.ts`: 7件
- `app/lib/validation/creationQueryParams.spec.ts`: 5件
- `app/components/Pagination.spec.ts`: 5件
- `app/utils/currentCreationDetail.spec.ts`: 4件
- `app/hooks/useFooterNavigateStatus.spec.ts`: 4件
- `app/hooks/useAuth.spec.ts`: 4件
- `app/constants/MetaContent.spec.ts`: 4件

## 3. どの画面・機能にテストがあるか

### 機能別の自動テスト状況

| 機能領域 | ユニット | コンポーネント | E2E | 現状 |
|---|---:|---:|---:|---|
| 共通utils / lib | あり | なし | なし | 日付、URL、cookie、S3署名付きURL、検索パラメータなどのロジックにテストあり |
| constants / meta | あり | なし | なし | 投稿カテゴリ検索、ページtitle/description生成にテストあり |
| 投稿一覧 / フィルタ | あり | なし | なし | `useCreationFilter`、投稿詳細URL判定にテストあり。画面操作フローはなし |
| 投稿詳細 | 一部あり | なし | なし | HTML本文判定のhookにテストあり。コメント投稿/編集/削除、スタンプ選択、詳細表示フローの自動テストはなし |
| 投稿登録 / 編集 | 薄い | なし | なし | ステップ管理hookにテストあり。フォーム入力、画像アップロード、確認、送信、編集のフローは未確認 |
| MVP | 一部あり | なし | なし | ホーム表示用MVP変換にテストあり。MVP一覧画面のスクロール/取得フローはなし |
| お知らせ | なし | なし | なし | 一覧・詳細画面はあるが自動テストは見当たらない |
| マイページ / ユーザー詳細 | 一部あり | なし | なし | 認証ユーザー判定はあるが、プロフィール編集、画像編集、ユーザー詳細表示の画面テストはなし |
| FAQ / 利用規約 | なし | なし | なし | 静的表示に近いが、表示崩れやリンク確認の自動テストはなし |
| 共通レイアウト / フッター / ヘッダー | 一部あり | なし | なし | フッター状態判定のhookはある。ナビゲーション、モーダル、検索UIの画面テストはなし |

### ルーティング上の主要画面

- `/`
- `/faq`
- `/notice`
- `/notice/:noticeId`
- `/term`
- `/creation`
- `/creation/:creationId`
- `/creation/:creationId/edit`
- `/mvp`
- `/user/:userId`
- `/mypage`

## 4. テストの偏り

### 厚い領域

- URL・ルーティング補助
  - 末尾スラッシュ除去、投稿詳細URL判定、型安全ルートの検索パラメータバリデーションがテストされている。
- 投稿一覧の絞り込み
  - カテゴリ/タグ選択時のURLパラメータ制御を `useCreationFilter` で確認している。
- 投稿カテゴリ・meta生成
  - 定数やmeta生成のような副作用の少ない処理はテストしやすく、ケースも比較的多い。
- MVP変換
  - ホーム表示向けにbest MVPとその他MVPを分ける処理がテストされている。
- APIモックの土台
  - MSWのhandlers/resolversがあり、今後の統合テストや画面テストへ広げやすい。

### 手薄な領域

- 画面単位の表示・操作
  - `app/features/*/page` や `app/routes` の画面フローを直接検証するテストはほぼ確認できない。
- 投稿登録/編集フロー
  - 画像アップロード、カテゴリ/タグ選択、確認画面、投稿送信、編集、閉じる確認モーダルなど、ユーザー操作が多いが自動テストは薄い。
- 投稿詳細のコメント機能
  - Tiptap editor、スタンプ挿入、コメント投稿/編集/削除、ログインユーザーによる表示差分があるが、hook単体以外の自動テストは薄い。
- マイページ/プロフィール編集
  - ヘッダー画像・アイコン画像、プロフィール編集、ログインユーザー情報の反映は手動確認に残りやすい。
- お知らせ/FAQ/利用規約
  - 静的・一覧/詳細表示領域は画面テストや最低限のリンク確認がない。
- ブラウザ/E2E
  - Vitest Browserは導入されているがアプリ本体では未活用。E2E層は未整備。

### 数値から見た偏り

- `app` 配下のspec: 15ファイル
- `app` 配下のTSXファイル: 168ファイル
- `app/features` 配下の機能ファイル数:
  - home: 16
  - creation-form: 11
  - creation-detail: 8
  - me: 5
  - notice/notices/mvp/faq/term/userなど: 少数
- テスト配置:
  - `app/hooks`: 4ファイル
  - `app/utils`: 4ファイル
  - `app/constants`: 2ファイル
  - `app/lib`: 2ファイル
  - `app/features`: 2ファイル
  - `app/components`: 1ファイル

画面数・コンポーネント数に対して、テストはhooks/utils/constants/libに寄っている。

## 5. 手動確認の実態を把握

### リポジトリから見える手動確認

- PRテンプレートに「動作確認・キャプチャ」欄があり、ケースとエビデンスを貼る運用が想定されている。
- PRテンプレートのレビュー観点に「テストの網羅性」が含まれている。
- READMEに「ロジックファイル(hooks・utils・関数を実装しているconstants)はテストカバレッジ80%を目指す」とあり、画面確認は自動テストよりPRごとの動作確認に寄っていると見られる。
- E2E/VRT/Storybook/Chromaticは確認できないため、画面遷移・レスポンシブ表示・デザイン差分は手動確認の比重が高いと推定される。

### 手動確認が多そうな領域

- 投稿登録/編集
  - 入力、画像選択、確認、投稿、編集、閉じる確認モーダル
- 投稿詳細/コメント
  - Tiptap editor、スタンプ挿入、コメント投稿/編集/削除、ログインユーザーによる表示差分
- マイページ/プロフィール編集
  - アイコン/ヘッダー画像変更、プロフィール保存、ログインユーザー情報の反映
- 一覧/詳細画面
  - 投稿一覧、投稿詳細、MVP一覧、お知らせ一覧/詳細、ユーザー詳細
- 共通ナビゲーション
  - ヘッダー、フッター、ハンバーガーメニュー、検索/通知モーダル
- API連携
  - Orval生成API、TanStack Query、MSW、本番/検証APIとの差分
- レスポンシブ表示
  - SP/PCでDialog/Drawerを出し分ける箇所、画像表示、カード一覧、フッター固定導線

## 6. 不具合や変更が起きやすい領域

### 変更頻度が高い領域

リポジトリのgit履歴全体で見ると、変更が多い順に以下が目立つ。なお、調査時点の最新コミットは2025-05-26であり、直近運用の変更傾向はヒアリングや最新ブランチ確認が必要。

- `app/components`: 390件
- `app/api`: 165件
- `app/routes`: 130件
- `app/features/home`: 103件
- `app/features/creation-register`: 67件
- `app/features/creation-detail`: 48件
- `app/features/me`: 46件
- `app/utils`: 44件
- `app/features/creation-form`: 42件
- `app/lib`: 41件
- `app/hooks`: 35件

### 不具合が出やすそうな領域

コードとテスト資産から見た推定:

- 投稿登録/編集
  - フォーム、画像、カテゴリ/タグ、確認、API送信が絡み、ユーザー操作も多い。
- 投稿詳細/コメント
  - Tiptap、HTMLサニタイズ、スタンプ画像、コメント編集/削除、ログインユーザー判定が絡む。
- 画像/S3連携
  - 署名付きURL取得、画像hash、アップロード/表示、ユーザー画像・投稿画像の扱いがある。
- ルーティング/検索パラメータ
  - `react-router-typesafe-routes` とzodで型安全化しているが、カテゴリ/タグ/ページングなどURL状態に依存する画面が多い。
- 共通レイアウト/ナビゲーション
  - ヘッダー、フッター、モーダル、検索導線は複数画面に横断影響がある。
- API生成/型生成
  - CIでは `gen-api` が前提。生成ファイルが無い状態では一部テストが収集できない。

### 影響が大きい領域

- 投稿登録/編集: サービスの主要行動であり、失敗すると投稿体験に直結する。
- 投稿詳細/コメント: 閲覧・コミュニケーションの中心であり、ログイン状態や権限差分も絡む。
- マイページ/プロフィール編集: ユーザー情報と画像表示に影響する。
- 共通レイアウト/ナビゲーション: 全画面の導線に影響する。
- API/Orval/MSW: データ取得・型・モックの土台であり、広範囲に影響する。

## 7. なぜ現状のテスト実態になっているか

### 背景の整理

- README上のテスト方針が「ロジックファイル中心」になっている
  - `hooks`、`utils`、関数を実装している `constants` のカバレッジ80%を目指すと明記されており、現状のテスト配置と整合している。
- React Router SPAの画面テスト方針がまだ固まっていない
  - routes、React Query、APIモック、ログイン状態を含めた画面単位テストはコストが上がるため、まずは切り出した処理をユニットで押さえている状態に見える。
- E2EよりもCIの基本品質ゲートが先に整備されている
  - lint、typecheck、unit test、browser test、buildはあるが、E2Eはまだ無い。
- API生成が前提の構成になっている
  - Orval生成物を使うため、テスト前に `gen-api` を行うCI順序が必要。ローカルでも同じ前処理を知らないとテストが失敗しやすい。
- UI確認はPR単位の手動確認に残っている
  - PRテンプレートに「動作確認・キャプチャ」があり、画面やレスポンシブ差分は手動エビデンスで担保する運用が中心と推定される。
- Vitest Browserの導入途中に見える
  - workspace設定とCI実行はあるが、対象はサンプルのみで、アプリ本体のブラウザテストへ展開する前段階と考えられる。

## 8. この案件で標準化の検討価値がある部分

### 標準化に使えそうな実践

1. ロジックファイルのテスト対象をREADMEで明示する
   - `hooks`、`utils`、関数を持つ `constants` を対象にする方針は、他案件にも展開しやすい。
2. URL/検索パラメータ/ルーティング補助をユニットテストで押さえる
   - 一覧絞り込み、ページング、詳細URL判定は画面全体を立ち上げなくても検証しやすい。
3. API生成をCIの最初に実行する
   - Orval利用案件では `gen-api` をlint/typecheck/test前に実行する順序を標準化できる。
4. MSWのhandlers/resolversを整備しておく
   - 現状は主にモック基盤だが、統合テストや画面テストへ発展させやすい。
5. PRテンプレートで「動作確認・キャプチャ」を必須観点にする
   - 自動化しきれていない画面確認を、ケースとエビデンスで残す運用として使える。
6. ブラウザテスト導入時はサンプルで終わらせず、共通部品から対象を決める
   - Header/Footer、投稿カード、Pagination、Dialog/Drawerなど、画面横断の部品から始めると効果が出やすい。

### 標準化するとよさそうな判断基準

| 対象 | 推奨テスト |
|---|---|
| 形式変換、URL処理、日付、カテゴリ検索、meta生成 | ユニットテスト |
| hooks内の状態遷移、URLパラメータ操作、ログインユーザー判定 | ユニットテスト |
| APIレスポンス変換、S3署名付きURL取得、MSWで再現できるAPI境界 | ユニットテストまたは統合テスト |
| 投稿登録/編集、コメント、プロフィール編集などの主要フロー | E2Eまたは画面統合テスト |
| Header/Footer、Pagination、Dialog/Drawer、投稿カードなどの共通UI | ブラウザコンポーネントテスト |
| レスポンシブ表示、画像表示、モーダル/ドロワーの見た目 | 手動確認、必要に応じてVRT |
| FAQ/規約など静的ページ | smoke test、リンク/表示確認、手動確認 |

### この案件で優先して標準化候補にできる部分

- ロジックファイルに対するテスト作成基準
  - すでにREADMEに方針があり、既存テストもあるため、他案件へ展開しやすい。
- Orval/MSW利用時のCI順序
  - `gen-api` を先に行わないとテスト・型チェックが失敗しやすい点は、標準手順として明文化する価値がある。
- 画面フローをどこから自動化するかの優先順位
  - 投稿登録/編集、投稿詳細/コメント、マイページ編集、共通ナビゲーションを優先候補にするとよい。
- 手動確認の記録粒度
  - PRテンプレートの「動作確認・キャプチャ」を、確認ケース、端末/ブラウザ、結果、スクリーンショット/動画の形式まで具体化できる。
- Vitest Browserの活用基準
  - サンプルから、共通UIやユーザー操作がある小さな部品へ広げる判断基準を作れる。
