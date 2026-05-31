## 1. 現在あるテスト資産を把握

### テスト資産

|種別|有無|主な場所|使用ツール|備考|
|---|---|---|---|---|
|ユニットテスト|あり|`app/**/*.unit.spec.ts`|Vitest / jsdom|6ファイル・69件。スキーマバリデーション・utils・hookが中心|
|ブラウザコンポーネントテスト|なし|`app/**/*.browser.spec.{ts,tsx}`|Vitest Browser / Playwright Chromium|設定は用意済みだが、現状ファイルなし|
|E2E|なし|—|—|playwright.config.ts・E2Eディレクトリなし|
|APIモック|なし|—|MSW等|モック機構自体が未整備|
|カバレッジ|あり（古い）|`coverage/`|Vitest coverage v8 / Codecov連携|既存レポートは Statements 34.1% / Branches 38.5% / Functions 36.4%（coverage対象ファイルが限定的）|

### CI

`.github/workflows/ci.yml` が存在し、以下を実行している。

- トリガー: `main` / `develop` へのPR、手動実行
- lint: `npm run lint:script`（stylelintはスキップ）
- test: `npm run test:coverage`（browser specsが存在する場合のみPlaywright Chromiumもインストール）
- Codecovへアップロード（カバレッジ・テスト結果）
- ビルド: `npm run build`

CD用の `.github/workflows/cd.yml` は `main` push・手動起動で本番ビルドのみ実行。E2E専用ワークフローは存在しない。

---

## 2. どのレイヤーにどんなテストがあるか

### レイヤー別の役割

|レイヤー|現状|主な役割|
|---|---|---|
|ユニット|基礎的なものはある|スキーマバリデーション（apply/estimate）、utils（日付・年齢計算・文字列操作）、hook（useCounter）|
|コンポーネント|なし（設定のみ）|Vitest Browser設定は `vitest.config.ts` に定義済みだが、`.browser.spec.{ts,tsx}` ファイルは存在しない|
|統合テスト|なし|フォームフローや複数フック/APIを結合したテストはない|
|E2E|なし|playwright.config.ts 自体が存在しない|

### テスト件数の概観

|対象|数値|
|---|---|
|specファイル数|6ファイル|
|テストケース数|69件|
|featureコンポーネント数|24ファイル（apply: 19, estimate: 5）|
|sharedコンポーネント数|7ファイル|
|routesファイル数|10ファイル（画面: estimate 2, apply 2, home 1, error 1, layout 4）|

### テストケース数が多いファイル

|ファイル|件数|
|---|---|
|`app/features/apply/schemas/applyBasicInputSchema.unit.spec.ts`|22件|
|`app/shared/utils/ageCalculator.unit.spec.ts`|22件|
|`app/shared/utils/dateTime.unit.spec.ts`|11件|
|`app/features/estimate/schemas/estimateInputSchema.unit.spec.ts`|6件|
|`app/shared/hooks/useCounter.unit.spec.ts`|5件|
|`app/shared/utils/stringUtils.unit.spec.ts`|3件|

---

## 3. どの画面・機能にテストがあるか

### 機能別の自動テスト状況

|機能領域|ユニット|コンポーネント|E2E|現状|
|---|---|---|---|---|
|見積もりフォーム（estimate）|一部あり|なし|なし|スキーマ（日時・件数バリデーション）のみ。フォームフロー全体は未カバー|
|申し込みフォーム（apply）|あり|なし|なし|電話番号・住所・郵便番号などスキーマテストが最も厚い。フォームフロー・子ども情報入力は未カバー|
|shared utils|あり|なし|なし|年齢計算・日時・文字列操作のロジックは担保されている|
|shared hooks|一部あり|なし|なし|useCounterのみ。useYubinBangoなど業務フックはなし|
|shared components|なし|なし|なし|CheckboxGroup・DateTimeFieldGroupなどUIコンポーネントはテストなし|
|ルーティング・ミドルウェア|なし|なし|なし|checkFriendMiddleware、LIFF初期化など実環境依存処理はテストなし|
|complete画面|なし|なし|なし|EstimateComplete・ApplyCompleteのUI/遷移確認なし|

---

## 4. テストの偏り

### 厚い領域

- **スキーマバリデーション**（apply の電話番号・住所・郵便番号など、estimate の日時範囲など）
- **純粋関数・ユーティリティ**（年齢計算、日付操作、文字列ユーティリティ）

### 手薄な領域

- **フォームフロー全体のテスト**  
    入力→確認→完了の3ステップは最も変更頻度が高い（`ApplyFormContainer.tsx`: 41回、`EstimateFormContainer.tsx`: 9回）にも関わらず、コンテナのテストが皆無
- **子ども情報入力フォーム（apply）**  
    `ChildBasicInfoSection`（22回）、`ApplyChildrenForm`（20回）など変更頻度が高いコンポーネント群にテストなし
- **useYubinBango（郵便番号API）**  
    外部API呼び出しを含むhookだが、テストなし
- **shared UIコンポーネント**  
    `CheckboxGroup`・`DateTimeFieldGroup`など複数画面で使われる部品にテストなし
- **LIFF・LINE連携**  
    liff.ts、checkFriendMiddleware.ts など実環境依存のコードはテスト対象外

### 数値から見た偏り

|対象|コンポーネント/ファイル数|specファイル数|
|---|---|---|
|apply feature|19コンポーネント + schemas/hooks/utils|1（スキーマのみ）|
|estimate feature|5コンポーネント + schemas|1（スキーマのみ）|
|shared utils/hooks|5ファイル|4（ほぼカバー）|
|shared components|7ファイル|0|
|routes|10ファイル|0|

---

## 5. 手動確認の実態を把握

### リポジトリから見える手動確認

- PRテンプレート（`.github/pull_request_template.md`）は存在しない。動作確認の運用フォームは未整備。
- READMEにはテストコマンドの記述がない（AGENTS.md には記載あり）。
- LIFFアプリのため、実機（LINE app内WebView）での動作確認が必須となる。LIFF、LINE友達判定（checkFriendMiddleware）は実環境がないと動作しない。

### 手動確認が多い領域

- **LIFF初期化・LINE認証**：`liff.init()`、友達判定、LIFF URL遷移（ローカルでは `VITE_LIFF_ID` が必要）
- **フォーム入力〜確認〜完了フロー**：見積もり・申し込みの3ステップ遷移
- **子ども情報の複数件入力（apply）**：タブ切り替え、子ども追加・削除の動的UI
- **多言語切り替え**：日本語/英語切り替え時のUI/バリデーションメッセージ
- **郵便番号API（yubinbango）**：外部API連携の住所自動補完
- **LINE Messaging API（GAS）**：フォーム送信後のLINEメッセージ配信確認
- **モバイル実機での表示崩れ・タップ操作感**

---

## 6. 不具合や変更が起きやすい領域

### 変更頻度が高い領域（直近180日）

|ディレクトリ/ファイル|変更回数|
|---|---|
|`app/features/apply/components/containers/ApplyFormContainer.tsx`|41回|
|`app/shared/libs/i18n/ja/translation.json`|34回|
|`app/shared/libs/i18n/en/translation.json`|31回|
|`app/features/apply/components/presentational/childFields/ChildBasicInfoSection.tsx`|22回|
|`app/features/apply/components/presentational/ApplyChildrenForm.tsx`|20回|
|`app/features/apply/components/presentational/ApplyBasicInputForm.tsx`|19回|
|`app/routes/_friend/apply/layout.tsx`|16回|
|`app/features/estimate/components/presentational/EstimateInputForm.tsx`|16回|

### 不具合が出やすい領域

- **申し込みフォーム（apply）**：子ども件数に応じた動的バリデーション・タブ切り替え・条件分岐が複雑。最も変更頻度が高く、テストが薄い
- **多言語対応**：翻訳JSONの変更頻度が高く、バリデーションメッセージの多言語同期ミスが起きやすい
- **フォームの入力→確認→完了フロー**：`ApplyFormContainer` は41回変更されているがテストなし
- **EstimateInputFormのスキーマ**：`estimateInputSchema.ts`（13回）と連動するバリデーション変更
- **郵便番号連動・住所入力**：`useYubinBango` + schema の組み合わせはテストなし

---

## 7. なぜ現状のテスト実態になっているか

### 背景の整理

- **スキーマ・ユーティリティへの集中は方針整合**  
    year/month/day分割バリデーションなど難しいロジックがZodスキーマに集約されており、そこへのユニットテストが中心になっている
- **フォームコンテナのテストが皆無**  
    `ApplyFormContainer`・`EstimateFormContainer` はConform + Zod + React Router hooksが絡む複合コンポーネントで、ユニットテストでのセットアップコストが高い。ブラウザテスト設定は用意されているが活用されていない
- **E2Eが未整備**  
    他案件と異なりE2Eは一切なく、フォーム送信が実際に動くかの自動確認手段がない。LIFFアプリという性質上、実機前提の外部依存（liff.init、LINE Messaging）があることが自動化の障壁になっている可能性が高い
- **ブラウザコンポーネントテスト基盤は用意済み**  
    `vitest.config.ts` にはブラウザテストのプロジェクト設定が存在し、CIでも「browser.spec があれば Playwright をインストールする」対応が行われている。技術的な土台はあり、実装が追いついていない状態
- **PRテンプレートが未整備**  
    動作確認を記録する仕組みがなく、手動確認の属人化につながりやすい

---

## 8. この案件で標準化の検討価値がある部分

### 標準化に使えそうな実践

- **スキーマ（Zod）への条件分岐集約 + ユニットテスト**  
    year/month/day分割フォームの相関バリデーションをスキーマで表現してテストする手法は他案件にも展開しやすい
- **Vitest Browser（Chromium）の活用**  
    設定済みの `test:browser` を使ってフォームUI・多言語切り替えのブラウザテストを追加するのが最低コストで即効性が高い
- **CIへのtypecheck明示追加**  
    現状CIのlint-and-testアクションは `skip-test: true` でtypecheckの実行が明示されていない。AGENTS.mdの手順と整合させる必要がある

### 標準化するとよさそうな判断基準

|対象|推奨テスト|
|---|---|
|日時相関バリデーション、入力形式チェック|ユニットテスト（Zodスキーマ）|
|ユーティリティ関数（年齢計算、日時変換等）|ユニットテスト|
|フォームUIの表示分岐・多言語切り替え|ブラウザコンポーネントテスト|
|入力→確認→完了の主要フロー|ブラウザコンポーネントテスト or E2E|
|LIFF初期化・LINE API連携|手動確認項目として明文化、可能な範囲でモック化|

### 次にやるとよさそうなこと（優先度順）

1. **`ApplyFormContainer` へのブラウザコンポーネントテスト追加**（変更頻度No.1でテストなし）
2. **`applyChildrenInputSchema` のユニットテスト追加**（`applyChildrenInputSchema.ts` は12回変更されているがspecなし）
3. **多言語（i18n）テストの方針を決める**（翻訳JSON変更65回超だが自動検証なし）
4. **PRテンプレートの作成**（動作確認・テスト実施項目の記録）
5. **READMEへのテストコマンド記述追加**（AGENTS.mdと乖離している）
6. **`useYubinBango` のテスト追加**（外部API hookで郵便番号連動はバグリスクが高い）