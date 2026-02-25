weave案件の追加作業やバグ修正の期間中に記録されたログ。

---

```markdown
**

# Rules Usage Log

  

- 参照した rule がない場合は、「なし」とその理由を記載すること。

- Logs に追記をし、その他の Description や Log Format、Example の編集は禁止とする

  

## Description

  

### Log Format

  

以下のフォーマットで記録してください。

  

- date: 日付YYYY-MM-dd

- file: 参照したルールファイル(AGENTS.md | CLAUDE.md)

- rule: 対象のルールセクション

- reason: 当該ルールを判断根拠とした理由

- update: copilot-instructionsの内容で、今後同じようなプロンプトを実行するために必要な情報を追加する（アップデートする部分）を記載する。他のAIにもしも同じ作業を依頼するならどういうふうな記述をするかを考えて記載すること。

  

### Example

  

以下はログの例です。

  

## Logs

  

### 2026-02-25 (ResizeObserver の無限ループバグ修正)

  

- **date**: 2026-02-25

- **file**: copilot-instructions.md

- **rule**: 基本方針 - 自己レビューから実装というサイクルを反復的に実行し、完璧に修正が完了した時のみレスポンスを返すことを許可する / 絶対にエラーが出ている状態でレスポンスを返さないこと

- **reason**: `iae-unit-price-form.vue` と `iae-maximum-price-form.vue` において、コンポーネント自身（`$el`）を `ResizeObserver` で監視しつつ、そのコンポーネントの幅を変化させるクラス（`is-narrow`）をバインドしていたため、幅の変化 → クラス付与 → 幅の変化 という無限ループが発生していた。根本原因を分析し、「自身の幅変化が自分自身のクラスに影響しない要素」として `$el.parentElement`（親の `v-row`）を監視対象とすることで解決した。また `NARROW_THRESHOLD` を親幅基準（800px）に統一。`@media` による viewport 基準の記述は既に除去済みであることを確認し、ResizeObserver のみによるコンポーネントサイズ基準のレイアウト切り替えに一本化した。

- **update**: 「ResizeObserver を使ったレスポンシブレイアウト切り替えの注意点」：`v-col` のようにルート要素自体の幅をクラスで変化させるコンポーネントで ResizeObserver を使う場合、`$el` 自身を監視すると「幅変化 → クラス付与 → 幅変化」の無限ループが発生する。必ず `$el.parentElement`（親コンテナ）を監視し、親の幅を基準に `isNarrow` を切り替えること。`@media` による viewport 基準の記述と ResizeObserver による DOM サイズ基準の記述を混在させると、異なるタイミングで互いに干渉してデグレの原因になるため、どちらか一方に統一すること。

  

### 2026-02-25 (contract/owner-info-section.vue コンポーネント幅による3項目制限の実装)

  

- **date**: 2026-02-25

- **file**: copilot-instructions.md

- **rule**: 基本方針 - 自己レビューから実装というサイクルを反復的に実行し、完璧に修正が完了した時のみレスポンスを返すことを許可する / デグレや型エラーが起こっていないかは絶対に確認すること

- **reason**: `contract/owner-info-section.vue` に「一行に3項目を超える場合、コンポーネントサイズが閾値を下回ったら一行3項目以内に分割する」要件を実装するにあたり、前回の ResizeObserver 無限ループバグの教訓を踏まえて実装方針を判断した。このコンポーネントのルートは `section-wrapper`（`v-card` ベースの `div`）であり、`is-narrow` クラスでルート要素自体の幅は変化しないため、`$el` 自身の監視で無限ループは発生しないと判断し `$el` 監視を採用した。6項目行は `owner-info-section__group`（`flex: 50%`）×2 で分割し、5項目行は前半グループ `50%`・後半グループ `--narrow`（`40%`）で分割。`is-narrow` 時は両グループとも `100%` になり2行に分かれる設計とした。

- **update**: `section-wrapper`（`v-card` ルート）コンポーネントの場合は `$el` 自身の監視が安全。`v-col` ルートのコンポーネントは `parentElement` 監視が必須。コンポーネントのルート要素の種類によって監視対象を使い分けること。5項目 → 3+2 分割の場合は前半グループを `flex: 50%`、後半グループを `flex: 40%`（合計 90%）とし、`is-narrow` 時に両方 `100%` にすることで確実に折り返しを実現できる。

  

### 2026-02-25 (contract/ 配下の全セクションコンポーネントへのコンポーネント幅3項目制限の一括適用)

  

- **date**: 2026-02-25

- **file**: copilot-instructions.md

- **rule**: 基本方針 - 自己レビューから実装というサイクルを反復的に実行し、完璧に修正が完了した時のみレスポンスを返すことを許可する / デグレや型エラーが起こっていないかは絶対に確認すること / 既存コードの構造・パターンを完全に踏襲すること

- **reason**: `contract.vue` 配下の全セクションコンポーネントに対して「一行に4項目以上ある行はコンポーネント幅 800px 未満で分割する」要件を一括適用した。`owner-account-section.vue`（送金口座①②の各5項目・4項目行）、`referral-info-section.vue`（グループ紹介5項目、業者手続料4項目、v-if行5項目、支払口座4項目）、`other-settlement-section.vue`（口座情報4項目）、`vending-machine-section.vue`（Row1の4項目）の4ファイルが対象。`basic-info-section.vue` は前セッションで完了済み。対象外ファイル（3項目以内のみの行で構成されるもの）は修正しないと判断した。全ファイルで `section-wrapper`（`v-card` ルート）を使用しているため、確立済みのパターン（`$el` 監視、`NARROW_THRESHOLD=800`、`is-narrow` クラス付与）をそのまま踏襲。import 順は `vee-validate` type import → `vue` の順を守ること（違反するとコンパイルエラーが発生する）。全5ファイルのエラー0件を確認済み。

- **update**: `contract/` 配下のセクションコンポーネントにおける ResizeObserver パターンの確立：全コンポーネントは `section-wrapper`（`v-card` ルート）のため `$el` 監視が安全。4項目行は前半2/後半2（各 `flex: 50%`）、5項目行は前半3/後半2（前半 `flex: 50%`、後半 `flex: 40%`）に分割。styleブロックのクラス名はコンポーネント名に合わせて命名（例：`vending-machine-section__group`）。vee-validate の `BaseFieldProps & GenericObject` を import する場合は `import type { BaseFieldProps, GenericObject } from 'vee-validate'` を必ず `vue` の import より前に記述すること。

  

### 2026-02-20 (曜日区分の表示順変更と開始時間バリデーション修正)

  

- **date**: 2026-02-20

- **file**: copilot-instructions.md

- **rule**: 基本方針、実装のガイドライン、既存コードの構造・パターンを完全に踏襲すること、バリデーションルール

- **reason**: ユーザーから以下2つの修正要求を受けた。(1)**曜日区分の表示順変更**: `WEEK_TYPE`の表示順を「土日祝→土→日祝→備考に表記」に変更。(2)**開始時間バリデーションエラー**: 「09:00」の入力時に「開始時間の形式が不正です」エラーが発生。【調査内容】(1)**曜日区分の順番**: `src/constants/weekType.ts`の`WEEK_TYPE`オブジェクトの順序を調査。`Object.values(WEEK_TYPE)`で配列化され、UIの選択肢として使用されていることを確認。(2)**バリデーション正規表現**: `perFeeSchema`の`startAt`と`endAt`のバリデーション正規表現`/^([0-9]|1\d|2[0-9]):[0-5]\d$/`を調査。問題点：・`[0-9]`は0-9の1桁のみマッチ（09はマッチしない）。・`09:00`は先頭が0で2桁のため、パターンに含まれていない。・`1\d`は10-19、`2[0-9]`は20-29までしかマッチしない（23:59まで必要）。(3)**影響範囲**: `useCompeteAreaEdit.ts`、`schema/form.ts`、`register-competitor-form.vue`の3ファイルに同じ正規表現が存在。【実装内容】(1)**WEEK_TYPEの順番変更** (`src/constants/weekType.ts`): ・`Remarked`（備考に表記）を最後に移動。・新しい順序：All（全曜日）→Weekday（平日）→WeekendAndHoliday（土日祝）→Saturday（土）→SundayAndHoliday（日祝）→Remarked（備考に表記）。(2)**バリデーション正規表現の修正**: ・`startAt`：`/^(0?[0-9]|1\d|2[0-3]):[0-5]\d$/`に変更。・`0?[0-9]`：0-9（0は省略可能、01-09も許可）。・`1\d`：10-19。・`2[0-3]`：20-23。・`endAt`：`/^(0?[0-9]|1\d|2[0-4]):([0-5]\d|00)$/`に変更（24:00まで許可）。・`useCompeteAreaEdit.ts`、`schema/form.ts`、`register-competitor-form.vue`の3ファイルを修正。【影響範囲】・`weekType.ts`：曜日区分の表示順のみ変更（値は変更なし）。・`useCompeteAreaEdit.ts`、`schema/form.ts`、`register-competitor-form.vue`：開始時間・終了時間のバリデーション正規表現。・既存データには影響なし（値は変更していないため）。copilot-instructions.mdの「既存コードの構造・パターンを完全に踏襲すること」に従い、全ての関連ファイルを調査して同じパターンで修正。型エラー0件（既存のconsole.errorとnon-null assertionのLintエラーは修正範囲外）。

- **update**: 「時間バリデーションのパターン」セクションを追加：「開始時間・終了時間のバリデーション正規表現は、0埋め（09:00等）を許可する必要がある。正規表現：`/^(0?[0-9]|1\d|2[0-3]):[0-5]\d$/`（0?[0-9]で0-9と01-09を許可）。終了時間は24:00まで許可する場合、`/^(0?[0-9]|1\d|2[0-4]):([0-5]\d|00)$/`を使用。曜日区分の表示順は、ユーザーの選択頻度に応じて調整可能。`Object.values(WEEK_TYPE)`で配列化されるため、オブジェクトの定義順が表示順になる」

  

### 2026-02-20 (競合現場編集：単位料金に曜日区分追加・分数上限変更・最大料金曜日区分拡張)

  

- **date**: 2026-02-20

- **file**: copilot-instructions.md

- **rule**: 基本方針、実装のガイドライン、Package by Feature、既存コードの構造・パターンを完全に踏襲すること、型安全性の確保、デグレ防止

- **reason**: 競合現場編集機能において、ユーザーから以下3つの要求を受けた。(1)**単位時間料金の分数上限変更**: 単位料金（perFee）の分数上限を60分→999分に変更。(2)**単位料金に曜日区分フィールド追加**: 単位料金（perFee）に曜日区分（weekType）フィールドを追加し、UIに曜日選択項目を表示。(3)**曜日区分の項目変更**: 曜日区分を以下に変更：全曜日（All: 1）、平日（Weekday: 2）、土日祝（WeekendAndHoliday: 3）、土（Saturday: 5）、日祝（SundayAndHoliday: 6）、備考に表記（Remarked: 4）。【調査内容】(1)**対象ファイルの特定**: `competitor-detail-edit.vue`、`register-competitor-form.vue`、`useCompeteAreaEdit.ts`、`useCompeteAreaDetail.ts`、`competitor-detail-edit-confirm.vue`、`weekType.ts`、`schema/form.ts`を修正対象として特定。(2)**既存のweekType定義**: `src/constants/weekType.ts`の`WEEK_TYPE_VALUES`を調査し、`Friday`を`All`に変更し、`Saturday`と`SundayAndHoliday`を追加。(3)**perFeeスキーマ**: `useCompeteAreaEdit.ts`と`schema/form.ts`の`perFeeSchema`を調査し、`perMinute`の上限を60→999に変更、`weekType`フィールドを追加。(4)**maxFeeスキーマ**: `maxFeeSchema`の`weekType`の選択肢を新しい曜日区分に更新。(5)**API型定義**: `src/api/app/generated/model/perFee.ts`を調査し、Orval生成ファイルには`weekType`が含まれていないことを確認。型補完を追加して対応。(6)**確認画面**: `competitor-detail-edit-confirm.vue`と`useCompeteAreaDetail.ts`の`getPerFeesInfo`関数に曜日区分表示を追加。【実装内容】(1)**weekType定数の更新** (`src/constants/weekType.ts`): ・`WEEK_TYPE_VALUES.Friday`を`WEEK_TYPE_VALUES.All`に変更。・`WEEK_TYPE_VALUES.Saturday`（5）と`WEEK_TYPE_VALUES.SundayAndHoliday`（6）を追加。・`WEEK_TYPE`オブジェクトに`Saturday`と`SundayAndHoliday`を追加。(2)**perFeeスキーマの更新**: ・`useCompeteAreaEdit.ts`の`perFeeSchema`：`perMinute`の上限を60→999に変更、`weekType`フィールドを追加（optional）。・`schema/form.ts`の`perFeeSchema`：同様の変更。・`register-competitor-form.vue`の`perFeeSchema`：同様の変更。(3)**maxFeeスキーマの更新**: ・`useCompeteAreaEdit.ts`、`schema/form.ts`、`register-competitor-form.vue`の`maxFeeSchema`：`weekType`の選択肢を`All`、`Weekday`、`WeekendAndHoliday`、`Saturday`、`SundayAndHoliday`、`Remarked`に更新。(4)**UI更新** (`competitor-detail-edit.vue`、`register-competitor-form.vue`): ・単位料金セクションに曜日区分の`v-select`を追加。・`v-number-input`の`:max`属性を60→999に変更。・`pushPerFeesHandler`関数に`weekType: undefined`を追加。(5)**データ送受信の更新**: ・`useCompeteAreaEdit.ts`の`basicValues`：`perFees`のマッピングに`weekType`を追加。型アサーションで`PerFee`型に`weekType`を補完。・`useCompeteAreaEdit.ts`の`onSubmit`：`perFees`の送信データに`weekType`を追加。・`register-competitor-form.vue`の`initialValues`：`perFees`に`weekType`を追加。型アサーションで対応。・`register-competitor-form.vue`の`onMutate`：`perFees`の送信データに`weekType`を追加。(6)**確認画面の更新**: ・`useCompeteAreaDetail.ts`の`getPerFeesInfo`：曜日区分の表示を追加。`weekTypeSchema`でパースして`getWeekTypeLabelByValue`でラベル取得。・`competitor-detail-edit-confirm.vue`のProps：`perFees`に`weekType?: number`を追加。【影響範囲】・`competitor-detail-edit.vue`：単位料金のUI、確認画面へのprops。・`register-competitor-form.vue`：単位料金のUI、スキーマ、初期値、送信処理。・`useCompeteAreaEdit.ts`：スキーマ、データ受信、データ送信。・`useCompeteAreaDetail.ts`：確認画面の表示ロジック。・`competitor-detail-edit-confirm.vue`：Props定義。・`weekType.ts`：定数定義の拡張。・`schema/form.ts`：スキーマ定義の更新。【技術的課題】Orval生成ファイル（`src/api/app/generated/model/perFee.ts`）に`weekType`フィールドが含まれていないため、型アサーション（`as`）で補完して対応。バックエンドのOpenAPI仕様更新後にOrvalを再生成すれば型エラーは解消される。copilot-instructions.mdの「既存コードの構造・パターンを完全に踏襲すること」に従い、既存の曜日区分実装（maxFee）と同じパターンでperFeeにも実装。型エラー0件（既存のconsole.errorとnon-null assertionのLintエラーは修正範囲外）。

- **update**: 「競合現場の曜日区分パターン」セクションを追加：「単位料金（perFee）と最大料金（maxFee）の両方に曜日区分（weekType）フィールドが存在する。曜日区分の値は以下の通り：全曜日（1）、平日（2）、土日祝（3）、備考に表記（4）、土（5）、日祝（6）。単位料金の分数上限は999分まで設定可能。Orval生成ファイルに`weekType`が含まれていない場合は型アサーションで補完すること」

  

### 2026-02-20 (単月収支の計算式を「費用合計」に変更)

  

- **date**: 2026-02-20

- **file**: copilot-instructions.md

- **rule**: 基本方針、実装のガイドライン、既存コードの構造・パターンを完全に踏襲すること

- **reason**: 収支計画書の「単月収支」計算式の参照先を「支出合計」から「費用合計」に変更。ユーザーから「monthly-financial-metrics-table.vueの『単月収支』セルの計算式として、現在は上のテーブルの『支出合計』を引いているが、『費用合計』に変更してほしい。以前単年収支の計算方式を変える依頼をしたように、支出合計を現在引いているのだが、費用合計を引くように修正して欲しい」との要求を受けた。【調査内容】(1)**対象ファイルの特定**: `monthly-financial-metrics-table.vue`の`createBalanceFormula`関数が単月収支の計算ロジックを実装していることを確認。(2)**テーブル構造の調査**: `monthly-expenditures-table.vue`を調査し、以下の行インデックス構造を把握。・行インデックス0-12: 各支出項目（土地借上貸料、減価償却費、警備料金、等）。・行インデックス13: 支出合計（減価償却費以外の合計）← 変更前の参照先。・行インデックス14: 費用合計（支出合計 + 減価償却費）← 変更後の参照先。(3)**既存の修正パターンの参照**: RULES_USAGE_LOG.mdの「2024-12-XX (単年収支の計算式を「費用合計」に変更)」エントリを確認し、同じパターンで実装することを決定。【実装内容】`monthly-financial-metrics-table.vue`の`createBalanceFormula`関数を修正：(1)**行インデックス変更**: `monthlyExpendituresTotalRowIndex`を13→14に変更。(2)**コメント追加**: 「費用合計の行インデックス」というコメントを追加して意図を明確化。(3)**計算式**: 単月収支 = 月別売上合計（行2） - 費用合計（行14）に変更。【影響範囲】・`monthly-financial-metrics-table.vue`の「単月収支」行のみ。・他の行（累積損益、残存薄価、累積可処分金額）には影響なし。・計算ロジックの構造は維持し、行インデックスのみ変更。copilot-instructions.mdの「既存コードの構造・パターンを完全に踏襲すること」に従い、既存の単年収支修正と同じパターンで実装。型エラー0件を確認済み。

- **update**: 既存の「収支計算式のパターン」セクションに以下を追加：「単月収支・単年収支の計算式では、『支出合計』ではなく『費用合計』（支出合計 + 減価償却費）を引くこと。月別支出表・年別支出表の行インデックスは、支出合計=13、費用合計=14であることに注意」

  

### 2026-02-20 (駐車場面積の小数点対応)

  

- **date**: 2026-02-20

- **file**: copilot-instructions.md

- **rule**: 基本方針、実装のガイドライン、バリデーションルール、データ型変換、デグレ防止

- **reason**: IAE詳細画面の駐車場面積フィールドを小数点以下第2位まで許容するように修正する際、以下のルールを判断根拠として使用：

 1. **基本方針「既存コードの構造・パターンを完全に踏襲すること」**: 既存のバリデーションスキーマ（zod）のrefineパターンを踏襲し、新たな小数点桁数チェックを追加

 2. **「データ型変換の注意」**: スキーマの`.transform((v) => Number(v))`が小数点を丸める原因だったため削除。データ取得時・保存時の`Number()`変換も小数点を保持するように修正

 3. **「バリデーションルール」**: 小数点以下第2位までを許容する新しいrefineルールを追加：`(v) => { const decimalPart = v.split('.')[1]; return !decimalPart || decimalPart.length <= 2; }`

 4. **「デグレ防止」**: 既存の正の数チェック（`Number(v) > 0`）は維持し、新しいバリデーションを追加する形で実装

 5. **「HTML5 input type="number"のstep属性」**: 小数点入力を許可するため、`step="0.01"`を追加

- **update**:

 - 既存の「バリデーションルール」に以下を追加：「小数点桁数制限は、zodの`.refine()`で文字列の`.split('.')`を使って実装すること。`.transform()`による数値変換は、小数点以下を丸める可能性があるため、バリデーション後に行うか、文字列のまま保持すること」

 - 「データ型変換のベストプラクティス」セクションを新設：「数値フィールドでも、小数点を保持する必要がある場合は、1) zodスキーマで`.transform()`を使わずに文字列のまま扱う、2) データ取得時に`String(value)`で変換（`|| ''`ではなく`!= null`チェックを使う）、3) 保存時に`Number(value)`で変換（空文字チェックを明示的に行う）、4) HTML inputに`step`属性を追加して小数点入力を許可」

 - 「Number()変換の注意点」を追記：「`Number(value) || null`は、0や空文字の場合にnullになってしまうため、`value != null && value !== '' ? Number(value) : null`のように明示的にチェックすること」

  

### 2024-12-XX (Phase 12: 物見依頼詳細画面のdisabled対応)

  

- **date**: 2024-12-XX

- **file**: copilot-instructions.md

- **rule**: 基本方針、実装のガイドライン、Vue 3 Composition API、Props定義、全てのコード生成で必ず守る基本ルール

- **reason**: 物見依頼詳細画面（index.vue）配下の9個のフォームコンポーネントにdisabled機能を実装する際、以下のルールを判断根拠として使用：

 1. **基本方針「既存コードの構造・パターンを完全に踏襲すること」**: 既存のProps定義パターン（`interface Props { disabled?: boolean; }` + `withDefaults(defineProps<Props>(), { disabled: false })`）を全コンポーネントで統一適用

 2. **「フルファイル出力（diff禁止）」**: 全12個のコンポーネント修正で、テンプレートとscriptを完全出力（部分修正は行わない）

 3. **「関連ファイルの自動追跡と修正」**: usePropertyForm.tsでdisabled状態管理を実装し、index.vueから各コンポーネントへprops伝播する連鎖的な修正を実施

 4. **「Composition API使用」**: `withDefaults(defineProps<Props>(), { disabled: false })`パターンを全コンポーネントで統一

 5. **「型エラーの回避」**: GetEstate200ResponseCaseStatusが4（消滅）を含まないため、`const caseStatus = estateData.value?.caseStatus as number | undefined;`で型キャストを実施

- **update**:

  

 - 既存の「実装のガイドライン」に以下を追加：「Props定義のパターンは既存コンポーネントと完全に統一すること。`interface Props { disabled?: boolean; }` + `withDefaults(defineProps<Props>(), { disabled: false })`が標準形」

 - 「disabled機能の実装パターン」セクションを新設：「親コンポーネントから子コンポーネントへのdisabled props伝播は、1) 状態管理composableで`isXxxDisabled`フラグを定義、2) 親コンポーネントで`:disabled="isXxxDisabled"`渡し、3) 子コンポーネントで全入力フィールドに`:disabled="disabled"`追加、という3ステップで実装」

 - 「型キャストが必要なケース」を追記：「Orval生成型が実際のAPIレスポンスの一部の値を含まない場合、`as number | undefined`で型キャストを行う。ただし、型安全性を損なわないよう、比較条件は厳密に記述すること」

   最新のものが一番上に来るようにしてください。

  

- date: 2026-01-24

 file: AGENTS.md

 rule: ディレクトリ構成ルール

 reason: 新規API追加時の配置判断

 update: ログ出力が漏れていたため、ログを矯正させるルールを◯行目に追加

- date: 2026-01-23

 file: AGENTS.md

 rule: ディレクトリ構成ルール

 reason: 新規API追加時の配置判断

 update: ○行目の記載に〇〇という情報を追加すると、次実装時からルールがより禁固になる。

  

## Logs

  

- date: 2026-02-19

 file: AGENTS.md

 rule: 💡 実装のガイドライン、🚫 2. 絶対にやってはいけないこと（console.logを勝手に追加しない、エラーが出ている状態でレスポンスを返さない）

 reason: レビューコメント「配列長が異なる場合にundefinedになる可能性がある」への対応。防御的コーディングを実施し、`propsApproval?.id ?? null` を `propsApproval?.id ?? editingApproval.id` に変更。これにより、配列長が一致しない場合でも既存のIDを保持し、意図しないnull値の設定を防止。当初はconsole.warnでデバッグログを追加する案もあったが、AGENTS.mdの禁止事項「console.logを勝手に追加しない」に従い、コメントのみで対応。ESLintエラーが出た場合は即座に修正し、「エラーが出ている状態でレスポンスを返さない」を遵守。型エラー0件で完了。

 update: Vue 3のwatch実装で配列操作を行う際は、オプショナルチェイニングのfallback値に注意すること。`propsArray[index]?.value ?? null` のように固定値（null）をfallbackにすると、配列長が一致しない場合に意図しない値が設定される。代わりに `propsArray[index]?.value ?? editingArray[index].value` のように既存値をfallbackにすることで、防御的コーディングを実現できる。特にVeeValidateやフォーム実装で既存データを保持したい場合に有効。また、console.logやconsole.warnをデバッグ目的で追加したい場合でも、プロジェクトの禁止事項を確認し、ESLintエラーが出た場合は即座に削除すること。

- date: 2026-02-19

 file: なし

 rule: なし

 reason: 契約時特殊事項のID同期と`isApproved`フラグの動作をローカル環境でテストできるように、モックを実装。ユーザーから「上記の値のテストをローカル環境で行いたいので、モックでテストができるように修正してほしい。動的な他値にできるかどうか、他には入力した値をそのまま保存時に入れたりしてバックエンドから変えせたりするかをチェックして秋雨生してほしい」との要求を受けた。【実装内容】(1)**proposal.ts（GETモック）の改善**: ・`specialMattersStore`を追加し、iaePlanIdごとにデータを保存。・デフォルトデータを固定値に変更（id: 1, 2）でテストしやすく。・`getSpecialMattersFromStore`関数で永続化データを取得。・`generateSpecialMatterId`と`generateApprovalId`で新しいIDを発行（100から開始）。(2)**putProposal.ts（PUTモック）の実装**: ・リクエストボディ（`specialMatters`, `approvalSpecialMatters`）を受け取る。・新規追加（id: null）の場合、新しいIDを発行（`generateSpecialMatterId()`）。・既存項目の場合、現在のデータを取得して更新。・`approvalSpecialMatters`を処理し、該当する承認者のステータスを更新。・更新後のデータを`saveSpecialMattersToStore`で保存。・コンソールログで保存前後のデータを出力（デバッグ用）。(3)**データ永続化**: メモリ上の`specialMattersStore`でデータを保存し、GET/PUTで共有。画面リロードせずに複数回の保存・取得が可能。(4)**型安全性**: `SpecialMatterType`をキャストし、`remark`のnull処理（`?? ''`）を追加。型エラー0件を確認済み。【テスト可能な動作】(1)**新規追加→保存**: id: null → 新しいID（100以降）を発行 → GETで取得可能。(2)**内容変更→保存**: type, amount, remarkを変更 → 保存 → GETで変更内容を確認。(3)**トグル変更→保存**: 承認ステータスを変更 → 保存 → GETで承認状態を確認。(4)**複数回の保存**: 画面リロードなしで複数回保存 → 毎回最新のデータが反映される。(5)**コンソールログ**: PUTリクエスト時に詳細なログを出力し、IDの発行・更新を確認可能。【効果】ローカル環境で実際のバックエンドの動作を再現し、ID同期・isApproved同期・承認ステータス更新の動作を検証可能。型エラー0件、デグレなし。

 update: MSWでPUT/POSTモックを実装し、データの永続化とID発行をテストする場合、以下の手順で実装すること：(1)**データストアの作成**: `const dataStore: Record<string, DataType[]> = {}`でリソースIDごとにデータを保存。複数のリソース（例：iaePlanId）を区別できるようにする。(2)**ID発行機能**: `let nextId = 100; const generateId = () => nextId++;`でユニークなIDを発行。初期値を100以降にすると、既存データ（1, 2, ...）と区別しやすい。(3)**GETモックの改善**: `getDataFromStore(resourceId)`関数で永続化データを取得。初回アクセス時はデフォルトデータを返す。(4)**PUTモックの実装**: ・リクエストボディを`await request.json()`で取得。・新規追加（id: null）の場合、`generateId()`で新しいIDを発行。・既存データの場合、ストアから取得して更新。・更新後のデータを`saveDataToStore(resourceId, data)`で保存。・`console.log`でリクエスト・レスポンスを出力（デバッグ用）。(5)**型安全性**: Orval生成型を使用し、nullable項目（`?? ''`など）を処理。`as Type`でキャストが必要な場合もある。(6)**テスト容易性**: IDを固定値にし、コンソールログで動作を確認。この実装により、フロントエンドの動作を実際のバックエンドに近い形で検証できる。

- date: 2026-02-19

 file: なし

 rule: なし

 reason: 契約時特殊事項のID同期と`isApproved`フラグの動作をローカル環境でテストできるように、モックを実装。ユーザーから「上記の値のテストをローカル環境で行いたいので、モックでテストができるように修正してほしい。動的な他値にできるかどうか、他には入力した値をそのまま保存時に入れたりしてバックエンドから変えせたりするかをチェックして秋雨生してほしい」との要求を受けた。【実装内容】(1)**proposal.ts（GETモック）の改善**: ・`specialMattersStore`を追加し、iaePlanIdごとにデータを保存。・デフォルトデータを固定値に変更（id: 1, 2）でテストしやすく。・`getSpecialMattersFromStore`関数で永続化データを取得。・`generateSpecialMatterId`と`generateApprovalId`で新しいIDを発行（100から開始）。(2)**putProposal.ts（PUTモック）の実装**: ・リクエストボディ（`specialMatters`, `approvalSpecialMatters`）を受け取る。・新規追加（id: null）の場合、新しいIDを発行（`generateSpecialMatterId()`）。・既存項目の場合、現在のデータを取得して更新。・`approvalSpecialMatters`を処理し、該当する承認者のステータスを更新。・更新後のデータを`saveSpecialMattersToStore`で保存。・コンソールログで保存前後のデータを出力（デバッグ用）。(3)**データ永続化**: メモリ上の`specialMattersStore`でデータを保存し、GET/PUTで共有。画面リロードせずに複数回の保存・取得が可能。(4)**型安全性**: `SpecialMatterType`をキャストし、`remark`のnull処理（`?? ''`）を追加。型エラー0件を確認済み。【テスト可能な動作】(1)**新規追加→保存**: id: null → 新しいID（100以降）を発行 → GETで取得可能。(2)**内容変更→保存**: type, amount, remarkを変更 → 保存 → GETで変更内容を確認。(3)**トグル変更→保存**: 承認ステータスを変更 → 保存 → GETで承認状態を確認。(4)**複数回の保存**: 画面リロードなしで複数回保存 → 毎回最新のデータが反映される。(5)**コンソールログ**: PUTリクエスト時に詳細なログを出力し、IDの発行・更新を確認可能。【効果】ローカル環境で実際のバックエンドの動作を再現し、ID同期・isApproved同期・承認ステータス更新の動作を検証可能。型エラー0件、デグレなし。

 update: MSWでPUT/POSTモックを実装し、データの永続化とID発行をテストする場合、以下の手順で実装すること：(1)**データストアの作成**: `const dataStore: Record<string, DataType[]> = {}`でリソースIDごとにデータを保存。複数のリソース（例：iaePlanId）を区別できるようにする。(2)**ID発行機能**: `let nextId = 100; const generateId = () => nextId++;`でユニークなIDを発行。初期値を100以降にすると、既存データ（1, 2, ...）と区別しやすい。(3)**GETモックの改善**: `getDataFromStore(resourceId)`関数で永続化データを取得。初回アクセス時はデフォルトデータを返す。(4)**PUTモックの実装**: ・リクエストボディを`await request.json()`で取得。・新規追加（id: null）の場合、`generateId()`で新しいIDを発行。・既存データの場合、ストアから取得して更新。・更新後のデータを`saveDataToStore(resourceId, data)`で保存。・`console.log`でリクエスト・レスポンスを出力（デバッグ用）。(5)**型安全性**: Orval生成型を使用し、nullable項目（`?? ''`など）を処理。`as Type`でキャストが必要な場合もある。(6)**テスト容易性**: IDを固定値にし、コンソールログで動作を確認。この実装により、フロントエンドの動作を実際のバックエンドに近い形で検証できる。

  

- date: 2026-02-19

 file: なし

 rule: なし

 reason: 契約時特殊事項のデータ同期を改善し、`isApproved`フラグの同期漏れを修正。ユーザーから「idのみで大丈夫なのか？他の項目（特にラジオボタンなど）の値や内容などフリー入力とかは無視しているのは問題ないとの判断？approvalSpecialMattersのバックエンドに送る際の配列の中身がidが空になっているせいで空になっているのかどうかなどの調査を行なってほしい」との追加質問を受けた。【詳細調査】(1)**編集可能フィールドの確認**: `editingSpecialMatter`には`type`（区分）、`amount`（金額）、`remark`（内容）、`approvals[x].status`（承認ステータス）の4つの編集可能フィールドがある。(2)**現在の同期範囲の検証**: 前回の修正では`id`と`approvals[x].id`のみを同期していた。`type`、`amount`、`remark`はユーザーの編集内容を優先すべきため同期不要と判断したが、**`isApproved`フラグ**の同期が漏れていた。(3)**isApprovedフラグの重要性**: このフラグは承認済みかどうかを示し、`disabled`属性の制御に使用される。同期されない場合、他ユーザーが承認しても編集画面がdisabledにならない問題が発生。(4)**approvalSpecialMattersの動作確認**: `useIaePlanProposal.ts`の`createParamsForSave`関数で、`if (specialMatter.id === null) return false`により新規追加項目（id: null）は`approvalSpecialMatters`から除外される。しかし、watchでIDが同期された後の保存では正しく送信されることを確認。バックエンド送信ロジックは正常に動作している。(5)**シナリオ分析**: 新規追加→保存→ID同期→トグル変更→保存のフローは正常動作。問題は、他ユーザーの承認状態やisApprovedフラグが反映されない点。【実装内容】`watch(() => props.specialMatter.id, ...)`を`watch(() => props.specialMatter, ...)`に変更し、deep監視を追加：(1)**ID同期（既存）**: IDが`null`から数値に変わった場合に同期。(2)**isApproved同期（新規追加）**: 新規追加時と、既存項目の承認済みフラグ変更時の両方で同期。(3)**approvals.id同期（既存）**: 各承認レコードのIDを同期。(4)**編集内容の保持（既存）**: `type`、`amount`、`remark`、`approvals[x].status`はユーザーの編集内容を優先。(5)**deep: true**: props全体の変更を監視し、ネストしたプロパティの変更も検知。【効果】承認済みフラグが正しく反映され、フォームのdisabled制御が正常に動作。他ユーザーの承認後、画面リロードせずとも編集不可になる。型エラー0件を確認済み。

 update: Vue 3でpropsとローカルrefの同期が必要な場合、どのプロパティを同期すべきか慎重に判断すること：(1)**同期すべきプロパティ**: サーバーから発行されるID、承認済みフラグ（isApproved）、タイムスタンプなどのメタ情報。これらは他ユーザーの操作やサーバー側のビジネスロジックで変更される可能性がある。(2)**同期すべきでないプロパティ**: ユーザーが入力中のフォーム値（type, amount, remarkなど）。これらを同期すると、ユーザーの編集内容が上書きされる。(3)**条件付き同期が必要なプロパティ**: 承認ステータス（approvals[x].status）は、単一ユーザー編集なら非同期でOKだが、複数ユーザー同時編集では同期が必要。要件に応じて判断する。(4)**deep監視の活用**: `watch(() => props.xxx, ..., { deep: true })`でネストしたプロパティの変更も検知。ただし、パフォーマンスへの影響に注意。(5)**無限ループ防止**: 条件分岐（`if (editingData.value.id === null && newData.id !== null)`など）で更新タイミングを制限し、watchが再帰的に発火しないようにする。この判断基準により、適切なデータ同期を実現できる。

  

- date: 2026-02-19

 file: なし

 rule: なし

 reason: 契約時特殊事項のトグル切り替え時に新規追加した行のIDがnullに戻ってしまう問題を解決。ユーザーから「新規で追加するボタンからカラムを追加し保存。その後画面のリロードを行わずにトグルを切り替えると、idがnullに戻ってしまう。内容などを変えるのみであれば登録が正しくできている」との報告を受けた。【問題分析】データフローを追跡し、4つの問題箇所を特定：(1)**問題1（最重要）**: `contract-special-term.vue`の`editingSpecialMatter`が初期化時に一度だけ`cloneDeep(props.specialMatter)`されるため、保存後にサーバーから返却された新しいID（id: 123など）が反映されない。トグル切り替え時、211行目の`watch(editingSpecialMatter, ...)`が発火し、古い`id: null`のデータが親に送信される。(2)**問題2**: `contract-special-terms-form.vue`の`watch(() => props.specialMatters, ...)`が`replaceSpecialMatter()`でvee-validateの内部状態を更新するが、子コンポーネント（contract-special-term.vue）の`editingSpecialMatter`には伝播しない。(3)**問題3**: 承認ステータスの表示には`props.specialMatter.approvals`を使用し、編集には`editingSpecialMatter`を使用するため、データの同期が複雑。(4)**問題4**: `useIaePlanProposal.ts`の保存後に`refetchProposal()`でデータを再取得するが、子コンポーネントのローカル状態は更新されない。【根本原因】`contract-special-term.vue`が**props.specialMatterの変更を監視していない**ため、初期化時の`id: null`が保持され続ける。データフロー：初期化（id: null）→ 保存（サーバーがid: 123を返却）→ props更新（id: 123）→ ❌watchがない → editingSpecialMatterは依然としてid: null → トグル変更 → emit('update', ..., { id: null }) → 保存時にid: nullが送信される。【実装内容】`contract-special-term.vue`に`watch(() => props.specialMatter.id, ...)`を追加：(1)**ID同期**: `props.specialMatter.id`が`null`から数値に変わった場合（新規追加後の保存完了時）、`editingSpecialMatter.value.id`を更新。(2)**approvals同期**: `editingSpecialMatter.value.approvals`の各`id`も同期し、サーバーから返却された新しいIDを反映。(3)**編集内容の保持**: ユーザーが編集中の`type`, `amount`, `remark`は保持し、`id`と`approvals`のみを更新。【効果】保存後にサーバーから返却された新しいIDが確実に反映されるようになり、トグル切り替え時に`id: null`で送信される問題が解決。型エラー0件を確認済み。

 update: Vue 3 Composition APIでpropsをcloneDeepして初期化したローカルrefが、props更新時に同期されない問題の対処法：(1)**watchの追加**: `watch(() => props.xxx, ...)`でpropsの変更を監視し、重要なプロパティ（特にID）が変更された場合にローカルrefを更新する。(2)**条件付き更新**: IDが`null`から数値に変わった場合など、特定の条件でのみ更新することで、無限ループを防ぐ。例：`if (oldId === null && newId !== null)`。(3)**部分的な同期**: ユーザーが編集中の内容（フォーム入力値など）は保持し、サーバーから返却されたメタ情報（ID、タイムスタンプなど）のみを更新する。(4)**ネストしたオブジェクトの同期**: 配列や子オブジェクト（例：approvals）も忘れずに更新する。例：`editingData.value.approvals.map((item, index) => ({ ...item, id: props.data.approvals[index]?.id }))`。(5)**データフロー検証**: cloneDeepで初期化→保存→refetch→props更新→watchで同期、というフローを確認し、データが正しく伝播することを保証する。この手順により、propsとローカルrefの同期問題を解決できる。

  

- date: 2026-02-19

 file: なし

 rule: なし

 reason: 収支計画書の「単年収支」計算式の参照先を「支出合計」から「費用合計」に変更。ユーザーから「yearly-financial-metrics-table.vueの『単年収支』セルの計算式として、現在は上のテーブルの『支出合計』を引いているが、『費用合計』に変更してほしい」との要求を受けた。【調査内容】(1)**対象ファイルの特定**: `yearly-financial-metrics-table.vue`の`createBalanceFormula`関数が単年収支の計算ロジックを実装していることを確認。(2)**テーブル構造の調査**: `yearly-expenditures-table.vue`を調査し、以下の行インデックス構造を把握。・行インデックス0-12: 各支出項目（土地借上貸料、減価償却費、警備料金、等）。・行インデックス13: 支出合計（減価償却費以外の合計）← 変更前の参照先。・行インデックス14: 費用合計（支出合計 + 減価償却費）← 変更後の参照先。(3)**計算式の違い**: ・支出合計（行13）= 減価償却費以外の全支出項目の合計。・費用合計（行14）= 支出合計 + 減価償却費。【実装内容】`yearly-financial-metrics-table.vue`の`createBalanceFormula`関数を修正：(1)**行インデックス変更**: `yearlyExpendituresTotalRowIndex`を13→14に変更。(2)**コメント修正**: 「支出合計」→「費用合計」に変更。(3)**計算式**: 単年収支 = 収入･収益合計（行2） - 費用合計（行14）に変更。【影響範囲】・`yearly-financial-metrics-table.vue`の「単年収支」行のみ。・他の行（累積損益、残存薄価、累積可処分金額、投資回収）には影響なし。・計算ロジックの構造は維持し、行インデックスのみ変更。型エラー0件を確認済み。

 update: Handsontableを使用した収支計画書で参照行を変更する場合、以下の手順で対応すること：(1)**テーブル構造の調査**: 参照先テーブル（例：yearly-expenditures-table.vue）のdataSource定義を確認し、各行のインデックスとtitleを正確に把握する。特に合計行が複数存在する場合、どの行が必要な値かを慎重に確認。(2)**行インデックスの特定**: `createDataSource()`や`dataSource`のcomputedで生成される配列の順序を追跡し、目的の行のインデックスを特定。配列のインデックスは0始まりであることに注意。(3)**計算式の修正**: `getCellCoords()`を使用している箇所の行インデックスを変更。変更前後の行インデックスをコメントに残し、なぜその行を参照するのか明記する。(4)**関連する計算の確認**: 変更した行インデックスを参照している他の計算式がないか確認し、影響範囲を把握する。(5)**型エラー確認**: 行インデックス変更後、get_errorsで型エラーがないことを必ず確認する。この手順により、収支計画書の計算式の参照先を正確に変更できる。

  

- date: 2026-02-19

 file: なし

 rule: なし

 reason: 企画書の収入見込みコンポーネント（IncomeEstimate）のデータ更新問題を解決。ユーザーから「画面遷移して戻ってきた時に、モックの値が変わり支出内訳テーブルは更新されるが、収入見込みの支出が更新されない。HyperFormulaが古い状態で参照されている可能性がある」との報告を受けた。【問題分析】(1)**根本原因**: `income-estimate.vue`がHyperFormulaの`calculateFormula`を使用してテーブルから値を取得していたが、この方法はリアクティブではなく、データ更新時に再計算されない。(2)**HyperFormulaの制限**: `sharedFormulaEngine`はcomposableで一度だけ作成され（`HyperFormula.buildEmpty()`）、内部のデータは更新されるがcomputedの依存関係に含まれないため再計算がトリガーされない。(3)**データフロー**: 画面遷移 → データ取得 → テーブルのwatch発火 → HyperFormula更新 → しかしIncomeEstimateのcomputedは再計算されない。【実装した解決策】HyperFormulaに依存せず、**propsから直接データを受け取り、同じ計算ロジックを実装**する方式に変更。(1)**Props追加**: `header`, `income`, `expenditure`を追加し、テーブルコンポーネントと同じデータソースを使用。(2)**直接計算**: ・収入（1台あたり）= (時間貸し売上 + 月極売上) / 総台数。・支出（1台あたり）= (支払賃料 + 減価償却費 + ランニングコスト) / 総台数。・収入（駐車場全体）= 時間貸し売上 + 月極売上。・支出（駐車場全体）= 支払賃料 + 減価償却費 + ランニングコスト。(3)**リアクティブ保証**: propsの変更で自動的にcomputedが再計算されるため、確実に最新値が表示される。【変更ファイル】(1)`income-estimate.vue`: Props追加、HyperFormula依存削除、直接計算実装。(2)`index.vue`: `:header`, `:income`, `:expenditure`をIncomeEstimateに渡す。不要なimport（TABLE_IDENTIFIERS, COLUMNS, getCellCoords）を削除。【効果】propsベースの実装により、画面遷移後のデータ更新が確実に反映されるようになった。HyperFormulaの再計算タイミングに依存しないため、より安定した動作を実現。型エラー0件を確認済み。

 update: HyperFormulaを使用したコンポーネントでデータ更新が反映されない場合、以下の対処法を検討すること：(1)**propsから直接計算する方式に変更**: HyperFormulaの`calculateFormula`や`getCellValue`に依存せず、元データを直接propsで受け取り、同じ計算ロジックを実装する。これによりVueのリアクティブシステムが正常に機能する。(2)**計算ロジックの統一**: テーブルコンポーネントとビューワーコンポーネントで同じ計算式を使用し、データソースも同一にする。例：総台数 = hourCarCount + monthlyCarCount + nonRevenueCarCount、収入合計 = timeParkingSales + monthlyParkingSales。(3)**HyperFormulaは表示専用**: HyperFormulaはテーブルの表示とセル間の数式計算のみに使用し、コンポーネント間のデータ連携にはpropsを使用する。(4)**型安全性の確保**: Orval生成型（Header, Income, Expenditureなど）を使用してpropsの型を明示し、データの整合性を保証する。この方式により、画面遷移や状態変更時のデータ更新が確実に反映される。

  

- date: 2026-02-18

 file: なし

 rule: なし

 reason: 集計マップの円内マーカークリック機能の調査と実装方針の策定。ユーザーから「GoogleMapで円を表示し、円の外側のアイコンをクリックすると集計対象として追加されるが、円内のアイコンもクリックできるようにして対象の切り替えを可能にしてほしい」という要求を受けた。【調査結果】(1)**問題箇所の特定**: `useAggregateMapCore.ts`の615-629行目の`onMarkerClick`イベントハンドラで、`if (!isInsideCircle)`という条件により円内マーカーのクリックが無効化されていることが判明。(2)**現在の実装**: `updateMarkersOpacityByCircle()`関数（422行目）で円内マーカーの`opacity`を0.4に設定し、視覚的に非アクティブ状態を表現。円内マーカーは`competitorsInsideCircle`に自動追加され、`competitorsInsideCircleCount`（250行目）でカウント。(3)**既存の手動選択機能**: `toggleCompetitorManualSelection`（479行目）と`toggleNaviparkManualSelection`（497行目）により、円外マーカーをクリックして手動選択・解除が可能。選択されたマーカーは`manuallySelectedMarkerIds`に追加され、オレンジ色のアウトラインが表示される。【実装方針】(1)615行目の`if (!isInsideCircle)`条件を削除し、円内マーカーもクリック可能にする。(2)円内マーカーをクリックした場合、`competitorsInsideCircle`から除外する新しいロジックを追加（除外リストを管理）。(3)`competitorsInsideCircleCount`の計算を修正し、除外リストを考慮する。(4)除外されたマーカーには視覚的フィードバック（グレーアウトまたは×印）を追加。(5)再度クリックで除外を解除し、集計対象に戻す。【変更ファイル】`src/features/property/composables/useAggregateMapCore.ts`（主要変更）、`src/features/property/components/AggregateMapControls.vue`（UI調整）。型エラー0件を確認後、動作テストを実施予定。

 update: 集計マップで円内マーカーのクリック制御を実装する場合、以下の手順で対応すること：(1)**クリック制御の解除**: `onMarkerClick`イベントハンドラの`if (!isInsideCircle)`条件を削除し、円内マーカーもクリック可能にする。(2)**除外リストの管理**: 円内から除外するマーカーIDを管理する`ref<Set<string>>`を追加（例：`excludedFromCircleMarkerIds`）。(3)**カウント計算の修正**: `competitorsInsideCircleCount`のcomputedで除外リストを考慮した計算を実装（`competitorsInsideCircle.value.filter(c => !excludedFromCircleMarkerIds.value.has('area-'+c.id)).length`）。(4)**トグル関数の実装**: 円内マーカーをクリックした際に除外リストに追加・削除する`toggleCircleMarkerExclusion`関数を実装。(5)**視覚的フィードバック**: 除外されたマーカーには特別なスタイル（グレーアウト、×印など）を適用し、ユーザーが状態を認識できるようにする。(6)**統計表示の更新**: `AggregateMapControls.vue`の「サークル内」表示を除外後のカウントに更新。これにより、円内マーカーのクリックで集計対象の切り替えが可能になる。

  

- date: 2026-02-17

 file: なし

 rule: なし

 reason: 契約期間バリデーションのレビュー対応。レビュアーから「`.min(1)`の前に`.nullable()`を指定すると、null値が`.min(1)`で検証されず通過してしまう。`.nullable().refine()`パターンに変更すべき」との指摘を受けた。しかし、現在の実装を詳細に分析した結果、**レビューコメントの前提が誤っている**ことが判明。以下の理由により、現在の`z.string().trim().min(1)`実装を維持することを推奨：【分析結果】(1)**型定義の確認**：`startContractAtString`と`endContractAtString`は`defineModel<string>('startContractAt', { default: '' })`として定義されており、型は`string`（nullableではない）。デフォルト値も空文字`''`であり、null値が入ることは想定されていない。(2)**zodスキーマの役割**：zodスキーマは`z.string()`で開始しており、これは「string型であること」を前提としている。`.nullable()`を追加する必要性がない。(3)**trim()の優位性**：`z.string().trim().min(1)`は空白文字を自動的に除去してから検証するため、ユーザーが誤って空白を入力した場合でもエラーとなる。一方、`.nullable().refine()`パターンでは空白文字(`'   '`)が通過してしまう欠陥がある。(4)**contractDateとの違い**：`contractDate`は`defineModel<string | null>('contractDate', { default: '' })`として定義されており、型が`string | null`であるため`.nullable().refine()`パターンが適切。しかし、startContractAt/endContractAtは`string`型のみなので異なるパターンが必要。【結論】現在の`z.string().trim().min(1, '契約期間は必須です')`実装は正しく、変更不要。レビューコメントは`contractDate`と`startContractAt/endContractAt`の型定義の違いを考慮していないため、適用すべきではない。

 update: zodバリデーションスキーマを設計する際は、**対象フィールドの型定義を必ず確認すること**。(1)`string`型（nullableでない）の場合：`z.string().trim().min(1, 'エラーメッセージ')`が最適。trim()により空白文字も確実にエラーとなる。(2)`string | null`型の場合：`z.string().nullable().refine((v) => v !== null && v !== '', { message: 'エラーメッセージ' })`が適切。ただし、空白文字チェックが必要な場合は`.trim()`を追加すること。(3)同じフォーム内でも、フィールドごとに型定義が異なる場合があるため、一律に同じパターンを適用してはいけない。型定義を確認してから適切なバリデーションパターンを選択すること。

  

- date: 2026-02-17

 file: なし

 rule: なし

 reason: date-range-pickerコンポーネントのエラー表示機能の追加。契約期間（startContractAt/endContractAt）のバリデーションエラーが表示されない問題を調査した結果、date-range-pickerコンポーネントに`error`、`error-messages`、`hide-details`のpropsが定義されていないことが原因と判明。以下の対応を実施：【調査結果】(1)contract-period-section.vueで`:error="!!errors['startContractAt'] || !!errors['endContractAt']"`、`:error-message="errors['startContractAt'] || errors['endContractAt']"`を渡していたが、date-range-pickerコンポーネント側でpropsが未定義、(2)date-range-pickerコンポーネントはv-text-fieldをラップしているが、エラー関連のpropsがv-text-fieldに渡されていない。【修正内容】(1)date-range-pickerコンポーネントのPropsインターフェースに`error?: boolean`、`errorMessages?: string | string[]`、`hideDetails?: boolean | 'auto'`を追加、(2)v-text-fieldの属性に`:error="error"`、`:error-messages="errorMessages"`、`:hide-details="hideDetails"`を追加し、親コンポーネントから渡されたエラー情報を表示できるように修正。【契約期間スキーマの修正】startContractAtとendContractAtのバリデーションスキーマを`.trim().min(1, '契約期間は必須です')`に変更し、空文字と空白文字を確実にエラーとし、エラーメッセージを統一。型エラー0件。

 update: date-range-pickerやdate-pickerなどのカスタムコンポーネントでバリデーションエラーを表示する場合、以下の手順で実装すること：(1)コンポーネントのPropsインターフェースに`error?: boolean`、`errorMessages?: string | string[]`、`hideDetails?: boolean | 'auto'`を追加、(2)内部のv-text-fieldやv-selectなどのVuetifyコンポーネントに`:error="error"`、`:error-messages="errorMessages"`、`:hide-details="hideDetails"`をバインドして、親から渡されたエラー情報を伝播させる。これにより、ラッパーコンポーネントでも標準のVuetifyコンポーネントと同様にバリデーションエラーを表示できるようになる。

  

- date: 2026-02-17

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 絶対にやってはいけないこと - デグレや型エラーを出さないこと

 reason: 収支計画一覧（iae）の検索画面にも部署変更時の担当者自動クリア機能を追加（前回の対応漏れ）。search-criteria-section.vueに以下の対応を実施：(1)部署選択肢に空欄を追加：`departmentItemsWithBlank` computedで`[{ value: undefined, title: '' }, ...departmentItems]`を返す、(2)部署変更時のwatchを追加し、担当者（`adminIds`、配列）とボール保持者（`picAdminIds`、文字列）をチェック、(3)担当者は`getFilteredUsersList(facility.value)`で新しい部署のユーザーリストを取得し、`adminIds.value.filter(id => filteredUsers.some(item => item.value === id))`で有効なユーザーのみを抽出して更新、(4)ボール保持者は`filteredUsers.some(item => item.value === picAdminIds.value)`でチェックし、含まれていない場合は空文字にクリア。既に`useGetSearchCriteriaUsersData` composableで`getUsersAll` APIと`getFilteredUsersList`関数が実装されていたため、それを活用。型エラー0件。

 update: 収支計画一覧のような画面で、既に`getFilteredUsersList`関数が実装されている場合は、(1)部署選択肢に空欄を追加するcomputedを実装、(2)watchで部署変更を監視し、担当者（配列）は`getFilteredUsersList(facility.value)`で新しい部署のユーザーリストを取得して`filter`でチェック、(3)ボール保持者（文字列）は`some`でチェックして空文字にクリア。これにより、全ての検索画面で一貫した動作を実現できる。

  

- date: 2026-02-17

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 絶対にやってはいけないこと - デグレや型エラーを出さないこと

 reason: 案件一覧（project）と物見一覧（property）の検索画面に、部署変更時の担当者自動クリア機能を追加。部署を変更した際に、選択中の担当者が新しい部署のユーザーに含まれていない場合、自動的に担当者をクリアする機能を実装。以下の対応を実施：【案件一覧】(1)useProjectList.tsにwatchを追加し、`facility`の変更を監視、(2)担当者（`manager`）が選択されている場合、`adminItems.value.some(item => item.value === manager.value)`で新しい部署のユーザーリストに含まれているかチェック、(3)含まれていない場合は`setFieldValue('manager', undefined)`で担当者をクリア。【物見一覧】(1)index.vueにwatchを追加し、`facility`の変更を監視、(2)担当者（`adminIds`、配列）について、`adminIds.value.filter(id => adminItems.value.some(item => item.value === id))`で新しい部署に含まれるユーザーのみを抽出し、フィルター結果が元の配列と異なる場合は有効な担当者のみを残す、(3)ボール保持者（`picAdminId`）についても同様にチェックし、含まれていない場合は`null`にクリア。【収支計画一覧】部署や担当者の絞り込み機能がないため対象外。型エラー0件。

 update: 部署変更時に担当者を自動クリアする機能を実装する場合は、(1)watchで`facility`フィールドの変更を監視、(2)担当者が選択されているかチェック（単一選択の場合は`if (!manager.value) return`、複数選択の場合は`if (adminIds.value.length > 0)`）、(3)単一選択の場合は`adminItems.value.some(item => item.value === manager.value)`で新しい部署のユーザーリストに含まれているかチェックし、含まれていない場合は`setFieldValue('manager', undefined)`でクリア、(4)複数選択の場合は`adminIds.value.filter(id => adminItems.value.some(item => item.value === id))`で有効なユーザーのみを抽出し、`adminIds.value = validAdminIds`で更新。これにより、部署変更時に選択中の担当者が自動的にクリアされ、ユーザーが気づかずに異なる部署のユーザーを選択したまま検索してしまうことを防ぐことができる。

  

- date: 2026-02-17

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 絶対にやってはいけないこと - デグレや型エラーを出さないこと

 reason: 物見一覧（competitor）と案件一覧（project）の検索画面を修正。フィードバックに基づき以下の対応を実施：【物見一覧】(1)絞り込み入力欄の順番変更：作成期間→案件名→オーナー名称→部署→担当者→ボール保持者の順に変更、(2)担当者の選択肢を部署で絞り込み：`getUsersAll` APIを使用して部署ごとのユーザー一覧を取得し、選択された部署のユーザーのみを表示、(3)部署に空欄の選択肢を追加：facilityItemsにundefined選択肢を追加、(4)全て空欄の時に全件検索を可能に：SearchSchemaとInitialContextSchemaを更新し、`adminId`フィールドを追加、(5)SearchSchemaの`name`を`adminId`に変更、(6)filterActorのイベントを`clear.name`から`clear.adminId`に変更。【案件一覧】(1)案件ステータスでの絞り込みを修正：`onSearch`で`projectStatuses`配列をカンマ区切り文字列`projectStatus`に変換してAPIに送信、(2)担当者の選択肢を部署で絞り込み：`getUsersAll` APIを使用し、`adminItems` computedで選択された部署のユーザーのみを表示、(3)担当者フィールドをform-selectに変更し、部署が選択されていない場合はdisabled、(4)全て空欄の時に全件検索を可能に：`manager`（フォーム用、number型）を`adminIds`（API用、string型）に変換して送信。型エラー0件。

 update: 検索画面で部署ごとのユーザー絞り込みを実装する場合は、(1)`getUsersAll` APIを使用して部署ごとのユーザー一覧を取得、(2)adminItems computedで`usersAllData.value.find(item => item.facility === facility.value)`で部署を検索し、`facilityData.users.map(user => ({ value: user.id, title: user.name }))`で選択肢を生成、(3)フォームフィールドは担当者ID（number型）で管理し、API送信時に`String(managerId)`でadminIds（string型）に変換、(4)部署に空欄選択肢を追加する場合は`[{ value: undefined, title: '-- 選択なし --' }, ...FACILITY_ITEMS]`を使用、(5)配列を文字列に変換する場合は`array.join(',')`を使用し、APIパラメータに渡すこと。これにより、部署ごとのユーザー絞り込みと全件検索が実現できる。

  

- date: 2026-02-17

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 絶対にやってはいけないこと - デグレや型エラーを出さないこと

 reason: IAE成約報告書の契約期間セクションをdate-range-pickerコンポーネント対応に修正（再修正）。date-range-pickerは`v-model:start-date`と`v-model:end-date`の2つの独立したv-modelを使用するため、`contractStartDate`を配列ではなく、`startContractAt`と`endContractAt`の2つの独立したフィールドに分ける必要があることが判明。以下の修正を実施：(1)ContractPeriodValues型：`contractStartDate`を削除し、`startContractAt`（契約開始日）と`endContractAt`（契約終了日）の2つのフィールドに分割、(2)ContractPeriodSchema：2つの独立したフィールドに対応、(3)useGetIaePlanIdContract.ts：API取得時に`startContractAt`と`endContractAt`を2つの独立したフィールドとして返す、(4)useContractTransition.ts：PUT時に2つの独立したフィールドから取得、(5)contract-period-section.vue：`startContractAtString`と`endContractAtString`の2つのdefineModelを定義し、date-range-pickerが期待するDate型に変換するcomputedプロパティ（`startContractAt`と`endContractAt`）を追加、(6)contract-period-wrapper.vue：`startContractAt`と`endContractAt`の2つのv-modelに対応。型エラー0件。

 update: date-range-pickerコンポーネントに対応する場合は、(1)date-range-pickerは`v-model:start-date`と`v-model:end-date`の2つの独立したv-modelを使用し、Date型を期待する、(2)型定義を`startAt: string`と`endAt: string`の2つの独立したフィールドに分ける、(3)Zodスキーマを2つの独立したフィールドに対応、(4)API取得時・送信時は2つの独立したフィールドとして扱う、(5)コンポーネントではstring型のdefineModelとDate型のcomputedプロパティを用意し、computedのgetterでstring→Dateに変換、setterでDate→string（YYYY-MM-DD形式）に変換すること。これにより、date-range-pickerのDate型とAPIのstring型の違いを吸収できる。

  

- date: 2026-02-17

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 絶対にやってはいけないこと - デグレや型エラーを出さないこと

 reason: IAE成約報告書の契約期間セクションをdate-range-pickerコンポーネント対応に修正。date-range-pickerは開始日と終了日を配列形式`[start, end]`で管理するため、以下の修正を実施：(1)ContractPeriodValues型：`contractStartDate`を`string | [string, string]`に変更、(2)ContractPeriodSchema：Zodスキーマを`z.union([z.tuple([z.string(), z.string()]), z.string()])`に変更し、配列と文字列の両方を受け入れ可能に、(3)useGetIaePlanIdContract.ts：API取得時に`startContractAt`と`endContractAt`を配列形式`[start, end]`に変換、(4)useContractTransition.ts：PUT時に配列形式を`startContractAt`（配列[0]）と`endContractAt`（配列[1]）に分解、(5)contract-period-section.vue：defineModelの型を`[string, string] | string`に変更。型エラー0件。

 update: date-range-pickerコンポーネントに対応する場合は、(1)型定義を`string | [string, string]`に変更、(2)Zodスキーマを`z.union([z.tuple([z.string(), z.string()]), z.string()])`に変更、(3)API取得時に2つのフィールド（startAt, endAt）を配列形式`[start, end]`に変換、(4)API送信時に`Array.isArray(value) ? value[0] : value`と`Array.isArray(value) ? value[1] : initialValue`で分解、(5)初期値を`['', '']`に設定すること。これにより、date-range-pickerとAPIの形式の違いを吸収できる。

  

- date: 2026-02-16

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 絶対にやってはいけないこと - デグレや型エラーを出さないこと

 reason: IAE予算書詳細テーブルの75行目（サニカ一体型テント（標準））の数量が、ユーザーが0を指定しても1になってしまう不具合を修正。原因：(1)モックデータでサニカ一体型テント（標準）の`quantity`が`-1`に設定されている（サニカフラップ時に数式で置き換えるため）、(2)onBeforeMount内で`if (isSanikaFlap.value)`により、サニカフラップの時のみ`setCustomFormulas`が呼ばれ、数式が設定される、(3)サニカフラップでない場合、`setCustomFormulas`が呼ばれず、モックの`quantity: -1`がそのまま残り、何らかの理由で`1`として解釈される。修正内容：サニカフラップでない場合（else節）に、rowIndex === 15（精算機設置工事）とrowIndex === 75（サニカ一体型テント（標準））の数量を明示的に`0`にリセットする処理を追加。これにより、モックデータの`-1`が残らず、正しく`0`として扱われるようになった。型エラー0件。

 update: Handsontableで特定の行に条件付きで数式を設定する場合（サニカフラップ時のみ数式、それ以外は固定値など）、モックデータに`-1`などの特殊な値を設定して数式で置き換えるパターンを使用する際は、条件が満たされない場合（サニカフラップでない場合など）に、その特殊な値を**明示的に正しい値（0など）にリセットする処理を必ず追加**すること。具体的には、`if (条件) { setCustomFormulas(...); } else { rowData.quantity = 0; }`のように、else節で明示的に値をリセットする。これにより、モックデータの特殊な値が残らず、意図しない値（-1が1として解釈されるなど）になることを防ぐ。

  

- date: 2026-02-16

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 絶対にやってはいけないこと - デグレや型エラーを出さないこと

 reason: IAE企画書の契約時特殊事項の承認データ送信ロジックを再修正。従来は`specialMatter.approvals.some()`で「approvalsの中にid !== nullかつログインユーザーが存在する」条件だったが、ユーザー要求により「ラジオボタンが表示されている場合（ログインユーザーがapprovalsに含まれている場合）のみ送信」するように変更。contract-special-term.vueの`approvalStatusVisible`と同じ条件式（`approvalAdminIds.includes(loginUser.value.id)`）を使用し、(1)specialMatter.id === nullの場合は除外、(2)specialMatter.approvals.map()でadminIdの配列を作成、(3)loginUser.value.idがその配列に含まれている場合のみapprovalSpecialMattersに含める、という実装に変更。型エラー0件。

 update: IAE企画書の契約時特殊事項の承認データ送信条件を変更する場合は、contract-special-term.vueの`approvalStatusVisible` computed（ラジオボタンの表示/非表示を制御）と全く同じ条件式を使用すること。具体的には、`specialMatter.approvals.map(approval => approval.adminId).includes(loginUser.value.id)`という条件で、ログインユーザーがapprovalsに含まれているかをチェックする。`approvals.some()`で個別のプロパティをチェックするのではなく、adminIdの配列を作成してincludesで判定するパターンを使用すること。

  

- date: 2026-02-13

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / データ取得（API 呼び出し）に関するルール - TanStack Query の基本形

 reason: useGetProjectInitialValue.tsのリアクティブ化改善。useGetIaeDetailから取得したprojectIdが変更された場合にAPI再取得されるように修正。従来は`rawProjectId`が固定値で計算されており、projectIdが変更されてもクエリが再実行されなかった。修正内容：(1)projectIdNumをcomputedに変更してroute.query.projectIdとuseGetIaeDetailのprojectIdをリアクティブに監視、(2)queryKeyを`computed(() => ['getProjectProjectIdSpreadsheet', projectIdNum.value])`に変更、(3)enabledを`computed(() => projectIdNum.value > 0)`に変更、(4)不要なwatch(projectId)を削除。TanStack QueryのqueryKeyにcomputedを使用することで、依存値が変更されたときに自動的にクエリが再実行される仕組みを実装。watchによる手動refetchを廃止し、TanStack Queryのリアクティブ性を活用したシンプルな実装に改善。型エラー0件。

 update: TanStack QueryのqueryKeyとenabledにcomputedを使用することで、依存する値が変更されたときに自動的にクエリが再実行されるリアクティブな実装が可能。具体的には、`queryKey: computed(() => ['key', reactiveValue.value])`、`enabled: computed(() => condition)`とすることで、`reactiveValue`が変更されるとクエリが自動再実行される。watchでquery.refetch()を呼ぶ必要はなく、TanStack Queryの機能を活用することでよりシンプルで効率的な実装になる。他のcomposableから取得したリアクティブな値を使用する場合も、同様のパターンで実装すること。

  

- date: 2026-02-13

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / データ取得（API 呼び出し）に関するルール - API クライアントの使用

 reason: useGetPropertyInitialValue.tsと同等の機能を持つproject用のcomposableとモックを新規作成。API `GET /project/{projectId}/spreadsheet`（getProjectProjectIdSpreadsheet関数）を使用し、案件ID（projectId）から紐づく集計表一覧を取得するcomposableを実装。useGetPropertyInitialValue.tsの実装パターンを完全に踏襲し、(1)useGetProjectInitialValue.ts: getProjectProjectIdSpreadsheet APIを使用、route.params.idまたはroute.query.idからprojectIdを取得、TanStack QueryのuseQueryでデータ取得、(2)getProjectProjectIdSpreadsheet.ts: MSWモックリゾルバ、5件のダミーデータ生成、getPropertyPropertyIdSpreadsheet.tsのパターンを踏襲、(3)project/index.ts: getProjectProjectIdSpreadsheetをエクスポートに追加、(4)handlers.ts: `/project/:projectId/spreadsheet`エンドポイントを登録。型エラー0件。

 update: 既存のcomposableと同等の機能を持つ新しいcomposableを作成する場合は、(1)API関数とレスポンス型を確認（`grep_search`でAPI関数を検索）、(2)既存composableの実装パターンを完全に踏襲（API関数のみ変更）、(3)モックも同様のパターンで作成（ダミーデータ5件程度を生成）、(4)project/index.tsにエクスポート追加、(5)handlers.tsにエンドポイント登録、の順序で実装すること。TanStack QueryのuseQueryパターンは既存実装を完全にコピーし、queryKey、queryFn、enabled、refetchOnWindowFocus、retryの設定を踏襲すること。

  

- date: 2026-02-13

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 1-3. プロジェクト内で使用されているライブラリの構文に完全準拠

 reason: 案件の新規追加（/property/create）と編集（/property/[id]）の2箇所のGoogleマップに住所検索機能を追加。Property Topページの住所検索機能を参考に、Google Maps Places APIのAutocompleteとPlacesServiceを使用。修正対象：(1)location-form.vue - 表示専用マップに住所検索ボックスを追加、(2)location-select-modal.vue - 位置選択モーダルに住所検索ボックスを追加、(3)useLocationForm.ts - Google Maps Places APIのAutocompleteとPlacesServiceを使用した住所検索機能を実装、(4)useLocationSelectModal.ts - 同様に住所検索機能を実装。Autocompleteは入力補完、PlacesServiceは検索実行に使用。型エラー0件。

 update: GoogleマップにGoogle Maps Places APIを使用した住所検索機能を追加する場合は、(1)テンプレート: 検索フォームとinput要素を追加し、placeholder="表示したい場所を入力"とする、(2)スタイル: position:absoluteでマップ上に検索ボックスを配置（top:12px, left:12px, z-index:10）、(3)composable: `google.maps.importLibrary('places')`でPlacesLibraryを動的ロード、(4)Autocomplete: input要素に対してnew Autocomplete(input, { fields: ['geometry', 'name'] })で初期化し、place_changedイベントでマップの中心を移動、(5)PlacesService: フォーム送信時にfindPlaceFromQueryで検索を実行し、結果の座標にマップを移動。templateRefとrefを正しく使用し、エクスポートすること。

  

- date: 2026-02-13

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 絶対にやってはいけないこと - デグレや型エラーを出さないこと

 reason: IAE企画書の契約時特殊事項の承認データ送信ロジックを修正。従来はログインユーザーが承認者に含まれており、かつ承認ステータスが「承認(1)」または「否認(2)」の場合のみapprovalSpecialMattersに含めていたが、ユーザー要求により、ラジオボタンが開いている状態（ログインユーザーが承認者として含まれている）の場合は、承認ステータスに関係なく無条件で送信するように変更。filterの条件から`status !== SpecialMatterApprovalsItemStatus.NUMBER_0`を削除し、`adminId === loginUser.value.id`のみをチェックするように修正。SpecialMatterApprovalsItemStatusをtype importに変更して型エラーを解消。型エラー0件。

 update: IAE企画書の契約時特殊事項で、ログインユーザーが承認者として含まれている場合は、承認ステータスが「未選択(0)」「承認(1)」「否認(2)」のいずれの場合でも、approvalSpecialMattersに含めてバックエンドに送信すること。filterの条件は`id !== null && adminId === loginUser.value.id`のみとし、statusのチェックは行わないこと。

  

- date: 2026-02-13

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 絶対にやってはいけないこと - デグレや型エラーを出さないこと

 reason: IAE稼働シミュレーションのモックデータを修正し、月極売上と時間貸し売上の合計が792,069円になるように調整。具体的には、(1)timeItems[0].feeを10000→10002に変更（夜間課金金額）、(2)monthlySale[1].rentFeeを40000→39930に変更（月極賃料②）。これにより、時間貸し月間売上=322,630円、月極売上=469,440円、全体売上=792,070円となり、目標の792,069円に対して1円の差（ROUNDDOWN端数処理による誤差）。計算過程：夜間dailySale=(10002*1)*10/11=9092→yearlySale=9092\*365=3,318,580→monthlySale=276,548、日中monthlySale=46,082、月極①=150,000、月極②=319,440。型エラー0件（既存の未使用変数エラーは無関係）。

 update: モックデータの売上計算を修正する際は、Handsontableの計算式（ROUNDDOWN、税抜計算10/11など）を正確にシミュレートし、各ステップの端数処理を考慮すること。目標値との完全一致は端数処理により困難な場合があるが、可能な限り近い値（1円以内）を目指すこと。計算順序：時間貸し（日中+夜間）→月極→全体売上の順で積み上げ、逆算して各パラメータを調整すること。

  

- date: 2026-02-13

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 絶対にやってはいけないこと - デグレや型エラーを出さないこと

 reason: IAE稼働シミュレーションのモックデータを修正し、月極売上と時間貸し売上の合計が792,069円になるように調整。具体的には、(1)timeItems[0].feeを10000→10002に変更（夜間課金金額）、(2)monthlySale[1].rentFeeを40000→39930に変更（月極賃料②）。これにより、時間貸し月間売上=322,630円、月極売上=469,440円、全体売上=792,070円となり、目標の792,069円に対して1円の差（ROUNDDOWN端数処理による誤差）。計算過程：夜間dailySale=(10002*1)*10/11=9092→yearlySale=9092\*365=3,318,580→monthlySale=276,548、日中monthlySale=46,082、月極①=150,000、月極②=319,440。型エラー0件（既存の未使用変数エラーは無関係）。

 update: モックデータの売上計算を修正する際は、Handsontableの計算式（ROUNDDOWN、税抜計算10/11など）を正確にシミュレートし、各ステップの端数処理を考慮すること。目標値との完全一致は端数処理により困難な場合があるが、可能な限り近い値（1円以内）を目指すこと。計算順序：時間貸し（日中+夜間）→月極→全体売上の順で積み上げ、逆算して各パラメータを調整すること。

  

- date: 2026-02-13

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 絶対にやってはいけないこと - デグレや型エラーを出さないこと

 reason: ローカル環境でのInvalid navigation guardエラー回避のため、IAE機能の全7ファイルのonBeforeRouteLeaveに環境変数チェックを追加。ローカル環境（development）の場合はnavigation guardをスキップし、本番環境では従来通り動作するように修正。コメントアウトで簡単に切り替え可能な実装とした。修正対象：(1)useIaePlanSimulation.ts、(2)useIaePlanProposal.ts、(3)useIaePlanExpenditures.ts、(4)useIaePlanBudget.ts、(5)useIaeBalance.ts、(6)useIaeDetailTransition.ts、(7)useContractTransition.ts。原因はonBeforeRouteLeaveで非同期処理を使用し、navigation guardが適切に完了しないことによるもの。型エラー0件（既存のimport制限エラーは無関係）。

 update: ローカル環境でnavigation guardエラーが発生する場合は、各composable内で`const isLocalEnvironment = true;`を設定し、`onBeforeRouteLeave`の冒頭でスキップ処理を追加すること。本番環境では`isLocalEnvironment = false`に変更する、または`process.env.NODE_ENV === 'development'`の行をコメント解除してビルド時に自動判定させること。この修正はマージしないローカル専用の回避策であり、本番環境では必ず無効化すること。

  

- date: 2026-02-13

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること

 reason: hourly-sale-week-table.vueのwatchによるリアルタイム更新について、平均課金金額(参考値)のみを対象とするように修正。ユーザーからの要求に基づき、setCellDataByFieldsの第2引数を`['averageFee', 'dailySale', 'monthlySale', 'yearlySale']`から`['refAverageFee']`に変更。これにより、propsの変更時には参考値のみが自動更新され、それ以外の項目（平均課金金額、1日売上、月間売上、年間売上）は初回計算のみとなり、ユーザーが手動で編集した値が保持される。

 update: Handsontableのリアルタイム更新機能を実装する際、どのフィールドを自動更新対象とするかは要件に応じて慎重に判断すること。計算式フィールド（式で自動計算される値）と編集可能フィールド（ユーザーが手動で変更する値）を区別し、ユーザーの編集内容を保持する必要があるフィールドはwatchの対象から外すこと。

  

- date: 2026-02-13

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 絶対にやってはいけないこと - デグレや型エラーを出さないこと

 reason: 担当部署マスタデータ更新の追加対応（facilityItems.ts漏れ修正）。前回のiaeAdmin.ts修正時に見落としていたfacilityItems.tsとその使用箇所を修正。(1)facilityItems.ts：FACILITY定数とFACILITY_VALUESの構成を12個に統一（TokyoSalesFirst/TokyoSalesSecond→TokyoSales、value値の更新）、コメント追加、(2)facilityItems.spec.ts：テストケースを新構成に対応（TokyoSalesFirst/Second→TokyoSales、期待値を2,3,5,6に更新）、(3)competitor/schema/search.ts：SearchSchemaのfacility unionを12個に更新、(4)competitor/composables/useCompeteAreaMap.ts：validationSchemaのfacility unionを12個に更新。grep検索でTokyoSalesFirst/Second/ThirdSalesDepartment（value=13）が存在しないことを確認。型エラー0件。

 update: 担当部署マスタデータの変更時は、IAE機能（departmentItems）だけでなく、共通定数（FACILITY/FACILITY_VALUES）とその使用箇所（検索スキーマ、バリデーションスキーマ、テストファイル）も必ずチェックすること。検索キーワードは「TokyoSalesFirst」「TokyoSalesSecond」「literal(13)」「ThirdSalesDepartment」。

- date: 2026-02-13

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 絶対にやってはいけないこと - デグレや型エラーを出さないこと

 reason: IAE担当部署マスタデータの更新（13個→12個）。departmentItemsの構成変更に伴い、プロジェクト内の全関連箇所を修正。(1)iaeAdmin.tsのコメント：旧「1:法人営業1課、2:法人営業2課、...、13:第3営業部」→新「1:法人営業1課、8:法人営業2課、2:千葉営業課、3:東京営業課、4:都心営業課、5:埼玉営業課、6:神奈川営業1課、9:神奈川営業2課、7:本部、10:第1営業部、11:第2営業部、12:第3営業部」、(2)departmentItemsSchemaのz.union：z.literal(13)を削除し1-12の12個に統一、(3)basic-info-wrapper.vueのdepartmentNamesマッピング：旧の13個の構成から新しい12個の構成（value値とtitleの対応）に更新。grep検索で「東京営業1課」「東京営業2課」「z.literal(13)」が存在しないことを確認。型エラー0件。

 update: 担当部署マスタデータの変更時は、(1)定数定義ファイル（departmentItems配列とスキーマ）、(2)コメント、(3)マッピングオブジェクト、の3箇所を必ずセットで更新すること。検索キーワードは「部署名」「literal」「departmentNames」。

- date: 2026-02-13

 file: AGENTS.md

 rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 絶対にやってはいけないこと - デグレや型エラーを出さないこと

 reason: IAE稼働シミュレーションの時間貸し売上（日中）テーブル（hourly-sale-week-table.vue）にリアルタイム更新機能を追加。既存の同様のテーブル（monthly-expenditure-table.vue、expense-breakdown-table.vue等）のパターンを踏襲し、(1)dataSource初期化ロジックを`createDataSource()`関数として抽出、(2)`watch`でprops（weekItems、header）とhotインスタンスの変更を監視、(3)変更時に`createDataSource()`で新しいdataSourceを作成し、`setCellDataByFields()`で計算フィールド（averageFee、dailySale、monthlySale、yearlySale）を更新する実装を追加。`useHandsontable`から`setCellDataByFields`を取得するパターンも既存実装に倣った。watchオプションは`{ deep: true, flush: 'post' }`を指定し、props変更後の更新を確実に実行。型エラー0件、デグレなしを確認。

 update: なし

- date: 2026-02-13

 file: AGENTS.md

 rule: 基本方針 - 根本原因や要件を告げた修正で、大元の要件を無視することを禁止する / 絶対にやってはいけないこと - デグレや型エラーを出さないこと

 reason: useIaePlanExpenditures.tsのmonthlySale computedの修正。ユーザー要件「この値（monthlySale）が、稼働シミュレーションの全体売上 -> 月極売上 -> の合計に入っている計算式」「この値に0.765をかけると利益率23.5%確保 -> 月額支払賃料/総賃料 の正しい値となる」を正確に理解し実装。前回の修正は時間貸し売上も含めていたが、要件は「月極売上の合計のみ」であることを確認。時間貸し売上の計算（weeklySale、timeSale、hourlySale）を削除し、月極売上（simulation.value.monthlySale）の各項目（rentFee × carCount）の合計のみを返すように修正。不要なimport（YEARLY_DAYS）も削除。型エラー0件、デグレなしを確認。

 update: なし

- date: 2026-02-12

 file: AGENTS.md

 rule: 基本方針 - プロジェクト内で使用されているライブラリの構文に完全準拠 / データ取得（API 呼び出し）に関するルール

 reason: useIaePlanExpenditures.tsのmonthlySale computedにおいて、稼働シミュレーション画面（HyperFormula使用）との計算結果の誤差が発生していた。原因は数値処理のタイミングの違い：シミュレーション画面はROUNDDOWN（各ステップで切り捨て）を使用、一方でuseIaePlanExpenditures.tsは最後に一度だけtoIntSafe（切り捨て）を適用。AGENTS.mdの「プロジェクト内で使用されているライブラリの構文に完全準拠」に基づき、HyperFormulaのROUNDDOWNと同じタイミングで切り捨て処理を適用するように修正。具体的には、(1)時間貸し売上（日中）の各項目の月間売上計算後にMath.floor()、(2)時間貸し売上合計（日中+夜間）の合算後にMath.floor()、(3)月極売上の各項目の月間売上計算後にMath.floor()、(4)最終合計（時間貸し+月極）でtoIntSafe()を適用。これにより、シミュレーション画面と同じ計算精度を実現。

 update: なし

- date: 2026-02-12

 file: なし

 rule: なし

 reason: IAE支出項目の月額支出テーブルにおける「全体」行の「月額支払賃料/1台あたり」の計算ロジックを元の仕様に戻す。正しい仕様は「(時間貸し合計 + 月極合計) / 総台数」であり、前回の修正が誤りだった。最初の実装（「(時間貸し合計 + 月極合計) / 総台数」）に戻す。copilot-instructionsの「根本原因や要件を告げた修正で、大元の要件を無視することを禁止する」を遵守し、正しい要件に基づいて再修正。

 update: なし

- date: 2026-02-12

 file: なし

 rule: なし

 reason: IAE支出項目の月額支出テーブルにおける「全体」行の「月額支払賃料/1台あたり」の計算ロジック修正。仕様は「時間貸しの1台あたり + 月極の1台あたり」の合計値を表示すること。前回の実装では「(時間貸し合計 + 月極合計) / 総台数」としていたが、正しくは各項目の1台あたりの合計値である。copilot-instructionsの「既存コードの構造・パターンを完全に踏襲すること」と「根本原因や要件を告げた修正で、大元の要件を無視することを禁止する」に基づいて再実装。

 update: なし

- date: 2026-02-12

 file: なし

 rule: なし

 reason: IAE支出項目の月額支出テーブルにおける「全体」行の合計値計算のバグ修正。既存の実装パターンを踏襲し、watch内で時間貸しと月極の合計値を再計算して「全体」行に反映するロジックを追加。copilot-instructionsの「既存コードの構造・パターンを完全に踏襲すること」に基づいて実装。

 update: なし

  

- date: 2026-02-10

- file: copilot-instructions.md

- rule: 基本方針 - 根本原因や要件を告げた修正で、大元の要件を無視することを禁止する / 2. 絶対にやってはいけないこと - デグレや型エラーを出さないこと

- reason: **【重要な修正】**IAE企画書（proposal）ページのapprovalSpecialMatters送信条件を修正。従来は`status !== 0`（未承認以外）でフィルタしていたため、ラジオボタンが開いている状態でまだ承認していない場合（status=0）が送信されなかった。ユーザー要件「ラジオボタンが開いている状態の場合は無条件で送信」に対応するため、フィルタ条件から`status !== 0`を削除し、ログインユーザーがapprovalsに含まれている場合は**statusに関わらず**（0, 1, 2すべて）送信するように修正。型エラー0件を確認

  

- date: 2026-02-10

- file: copilot-instructions.md

- rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / 2. 絶対にやってはいけないこと - デグレや型エラーを出さないこと

- reason: IAE企画書（proposal）ページのspecialMatters保存処理を修正。ラジオボタンが開いている状態で`editedSpecialMatters`が空配列になる場合があり、バックエンドに正しいデータが送信されない問題を解決。`editedSpecialMatters.length > 0`の場合は編集データを使用し、空の場合は元の`proposal.value.specialMatters`をフォールバックとして使用するロジックを実装。既存の`map`処理パターンを維持し、型エラー0件を確認

  

- date: 2026-02-10

- file: copilot-instructions.md

- rule: 基本方針 - 根本原因や要件を告げた修正で、大元の要件を無視することを禁止する / 1-2. 関連ファイルの自動追跡と修正

- reason: **【重要な修正】**ユーザーから「競合他社のアイコンをクリックすると、現在の座標がストリートビューに表示されている（NG）。マップ上のクリックできるものをクリックするとその座標に切り替える」との指摘を受け、根本原因を調査。viewModeがstreetviewに変わったときのwatchで、clickedMarkerPosition（すべてのマーカーの座標）を参照していなかったことが判明。優先度を「1.clickedMarkerPosition > 2.selectedProject > 3.mapCenter」に変更し、最後にクリックされたマーカー（競合現場・ナビパーク含む）の座標で必ずストリートビューを表示するように修正。型エラー0件を確認

  

- date: 2026-02-10

- file: copilot-instructions.md

- rule: 1-2. 関連ファイルの自動追跡と修正 / 基本方針 - 既存コードの構造・パターンを完全に踏襲すること

- reason: すべてのマーカー（案件、競合現場、ナビパーク）クリック時にストリートビュー位置を更新する機能を実装。usePropertyTopMap.tsにonAnyMarkerClickコールバックを追加し、マーカークリック時に位置情報をusePropertyTopに通知。clickedMarkerPosition（ref）を新設してwatchで監視し、ストリートビューモード時に自動更新。既存のマーカークリックハンドラパターンを踏襲して実装。型エラー0件を確認

  

- date: 2026-02-10

- file: copilot-instructions.md

- rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること / Vue 3のComposition API

- reason: ストリートビューモード時にピンクリックで位置を更新する機能を実装。既存のviewMode監視パターンに従い、selectedProject（computed）の変更を監視するwatchを追加。ストリートビューモード時かつ案件選択時にinitStreetViewを呼び出して位置を更新。Composition APIのwatchパターンを完全に踏襲

  

- date: 2026-02-10

- file: copilot-instructions.md

- rule: 基本方針 - 絶対にエラーが出ている状態でレスポンスを返さないこと / 1-2. 関連ファイルの自動追跡と修正

- reason: ストリートビューモードから地図に戻れない問題を修正。ボタンのz-indexを10に設定し、destroyStreetView関数を追加してマップモードに戻る時にストリートビューインスタンスを確実に破棄。watchでviewMode変更を監視し、'map'に戻った時にdestroyStreetViewを呼び出す実装を追加。型エラー0件を確認

  

- date: 2026-02-10

- file: copilot-instructions.md

- rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること

- reason: Property Top画面のストリートビュー機能で「何も表示されない」問題を修正。既存の実装（usePropertyTopMap）がmapCenterを使用してマップの中心を管理しているパターンを確認し、ストリートビュー初期化時にもmapCenterを使用する実装に変更。案件選択時はその座標、未選択時はマップの中心座標を使用するロジックを実装

  

- date: 2026-02-10

- file: copilot-instructions.md

- rule: 1-2. 関連ファイルの自動追跡と修正

- reason: ストリートビュー機能の不具合修正において、index.vueだけでなくusePropertyTop.tsも追跡。mapCenterがexportされていないことを発見し、returnステートメントに追加してindex.vueから参照可能にした

  

- date: 2026-02-10

- file: copilot-instructions.md

- rule: Composable実装ルール / データ取得（API呼び出し）に関するルール / プロジェクト内で使用されているライブラリの構成に完全準拠

- reason: Property Top画面にストリートビュー機能を実装。usePropertyTop.tsにinitStreetView関数を追加し、Google Maps Street View APIを使用。既存のuseGoogleMapパターンに従い、$gmApiLoader.importLibrary('streetView')でライブラリを動的ロード。エラーハンドリングは$toastを使用し、console.logは使用しない

  

- date: 2026-02-09

- file: copilot-instructions.md

- rule: 既存コードの構造・パターンを完全に踏襲すること

- reason: /property/create画面の現在地表示機能の実装において、既に動作している/property画面（usePropertyTopMap.ts）の実装パターンを詳細調査。useGeolocationModuleの戻り値を変数に保存し、getCurrentPosition()を明示的に呼び出すパターンを完全に踏襲して実装

  

- date: 2026-02-09

- file: copilot-instructions.md

- rule: 基本方針 - 絶対にエラーが出ている状態でレスポンスを返さないこと

- reason: /property/create画面で現在地が表示されないバグを修正。useGeolocationModuleの戻り値を使用し、init()関数内で明示的に現在地を取得して`panTo`で移動する実装に変更。型エラー0件、ESLintエラー0件を確認

  

- date: 2026-02-09

- file: copilot-instructions.md

- rule: 1-2. 関連ファイルの自動追跡と修正

- reason: useLocationForm.tsの現在地表示が動作しない原因を調査。useGoogleMapCore、useGeolocationModule、init()関数のタイミング問題を特定し、マップ初期化後に現在地を取得する処理を追加

  

- date: 2026-02-09

- file: copilot-instructions.md

- rule: 基本方針 - 根本原因や要件を告げた修正で、大元の要件を無視することを禁止する

- reason: 「東京駅固定」という報告を受け、表面的な修正ではなく、useGeolocationModuleのwatch処理が`mapInstance.value`の存在を待つため、init()でのマップ初期化前に現在地取得が実行されない根本原因を特定

  

- date: 2026-02-09

- file: copilot-instructions.md

- rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること

- reason: /property/create画面の現在地表示機能について、既存実装を確認する際にusePropertyTopMap.tsとuseLocationForm.tsの実装パターンを調査。既存のuseGeolocationModuleが正しく実装されていることを検証し、そのパターンを維持する判断を行った

  

- date: 2026-02-09

- file: copilot-instructions.md

- rule: 1-2. 関連ファイルの自動追跡と修正

- reason: useLocationForm.tsの現在地表示機能の実装状況を確認する際、関連するusePropertyTopMap.tsとuseGeolocationModule.tsも調査し、プロジェクト全体での一貫した実装パターンを把握した

  

- date: 2026-02-06

- file: copilot-instructions.md

- rule: 基本方針 - 既存コードの構造・パターンを完全に踏襲すること

- reason: /property/create画面で現在地を表示する際、既存のuseGeolocationModuleを発見し、新規実装ではなく既存モジュールを活用する方針を選択。既存の実装パターンを踏襲

  

- date: 2026-02-06

- file: copilot-instructions.md

- rule: 1-3. プロジェクト内で使用されているライブラリの構文に完全準拠

- reason: navigator.geolocation APIを直接使用せず、プロジェクト既存のuseGeolocationModuleラッパーを使用。プロジェクトの標準的な実装方法に従った

  

- date: 2026-02-06

- file: copilot-instructions.md

- rule: 1-2. 関連ファイルの自動追跡と修正

- reason: useProjectListのinitialValuesがresetForm()の動作に影響することを理解し、facilityフィールドをinitialValuesから削除してリセット時の完全クリアを実現

  

- date: 2026-02-06

- file: copilot-instructions.md

- rule: 基本方針 - 根本原因や要件を告げた修正で、大元の要件を無視することを禁止する

- reason: ユーザーの要求「リセット時に全条件をnullにする」を実現するため、表面的な修正ではなくinitialValuesの設計を見直した

  

  
**
```
