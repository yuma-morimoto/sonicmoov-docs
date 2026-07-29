
- Backlog: [MEASURE-149](https://sonicmoov.backlog.jp/view/MEASURE-149)
- 状態: 着手中
- 担当者: 森本悠真
- 期間: 2026-06-10〜2026-07-31

## 背景と課題

- 手動確認に頼っている画面操作やUI挙動の確認が多く、確認手順や実施結果が属人化しやすい。
- 毎回人が同じ操作を確認しており、確認工数や確認漏れが発生しやすい。
- AIを使ってマニュアルテストの一部を自動化できるか、どこまで実務で使えるかが整理されていない。

## 検証したい仮説

- AIに確認観点や操作手順を渡すことで、マニュアルテストの実行や確認結果の整理を一部自動化できる。
- PlaywrightやChrome DevTools MCPをAIと組み合わせることで、画面操作、表示確認、コンソールエラー、通信状況などの確認工数を減らせる。
- 自動化できる確認と、人間が目視・判断すべき確認を切り分けられる。

## 対象案件

- コーポレートサイト改修

## タスク

- [ ] パイロット案件から、手動確認している画面またはユーザーフローを1つ選ぶ。
- [ ] 人間が普段確認している手順・期待結果・注意点を整理する。
- [ ] AIに確認手順の実行または実行支援を依頼する。
- [ ] PlaywrightまたはChrome DevTools MCPを使って、画面操作・表示・コンソール・通信状況を確認できるか試す。
- [ ] AIによる確認結果と、人間による確認結果を比較する。
- [ ] AIで自動化できた確認、できなかった確認、人間の判断が必要な確認を整理する。
- [ ] マニュアルテスト自動化としてガイドラインに入れる / 条件付きで入れる / 入れないを判断する。

## 仮説の有効さを判断する

- [x] AIを使うことで、手動確認の作業時間や手順整理の手間が減った。
- [x] AIによる確認で、表示崩れ、操作不備、コンソールエラー、通信エラーなどに気づけた。
- [ ] AIの確認結果を人間がレビューすれば、実務で使える水準にできた。（未実施）
- [ ] AIに任せてよい確認と、人間が判断すべき確認を切り分けられた。（未実施）
- [x] 別案件でも使えそうな確認手順や運用ルールが整理できた。（スキル化）

## 成果物

- [x] AIによるマニュアルテスト自動化の検証メモ
- [x] AIに渡す確認手順・期待結果のテンプレート
- [x] AIで自動化できる確認 / 人間に残す確認の整理
- [ ] Playwright / Chrome DevTools MCPの使い分けメモ（未実施）

---
## 報告書
### 実施概要
- コーポレートサイトにてAIによるマニュアルテストの自動化を実施しました。
- Gherkin記法によるテストシナリオの設計と、エージェントがテストを行うSKILL.mdの作成を行いました。
- 実際にエージェントにテストをやらせてみたところ問題なくテストできました。
- テストシナリオはGherkinによる自然言語の記述なので、エージェントがよしなに解釈するのでフレーキーは低減する可能性があることがわかった。
### 有効さの判断基準
- [x] AIを使うことで、手動確認の作業時間や手順整理の手間が減った。
- [x] AIによる確認で、表示崩れ、操作不備、コンソールエラー、通信エラーなどに気づけた。
- [ ] AIの確認結果を人間がレビューすれば、実務で使える水準にできた。（未実施）
- [ ] AIに任せてよい確認と、人間が判断すべき確認を切り分けられた。（未実施）
- [x] 別案件でも使えそうな確認手順や運用ルールが整理できた。（スキル化）

### 結論
- 実際の効果が不十分なのと、標準化まで持っていくのが難しいことがわかったため、ガイドラインには載せない。
- やるとしたらAI施策でやる。

---

## 以下はplaywrightを使ったマニュアルテストのスキル
---

name: manual-testing

description: BRIEF 準拠の Gherkin .feature ファイルをテストオラクルとして、playwright-cli で実ブラウザ上のマニュアルテストを実施しレポートする。マニュアルテスト実行・仕様に対するUI検証・ページ挙動の検証を求められたときに使用する。

allowed-tools: Bash(playwright-cli:*) Bash(npx:*) Bash(npm:*)

---

  

# マニュアルテスト実施手順（Gherkin オラクル駆動）

  

`tests/manual/*.feature`（Gherkin 記法）をテストオラクルとして受け取り、`playwright-cli` で実ブラウザを操作して**ユーザー視点の振る舞い**を検証し、結果をレポートする。

  

このプロジェクトの `.feature` は **BRIEF 原則**で書かれており、シナリオは意図（「選ぶ」「閲覧できる」）で記述されている。

  

## snapshot と screenshot の役割分担

  

| コマンド | 何が得られるか | 使う場面 |

| --- | --- | --- |

| `playwright-cli snapshot` | アクセシビリティツリー（テキスト） | **When のみ**: 操作対象の ref/name を探す |

| `playwright-cli screenshot` | ブラウザの見た目（画像） | **Then のみ**: ユーザーが目で見る結果の証跡 |

  

> **Then で snapshot を使ってはいけない。** snapshot はテキスト構造であり、ユーザーが目で見るものではない。ユーザーが観察するのは画面の見た目であるため、Then の合否判定は screenshot（画像）で行う。

  

## 大原則: ユーザーが目で見られることだけを検証する

  

| 検証してよい | 検証してはいけない（実装詳細） |

| --- | --- |

| スクリーンショット（画像として目視） | CSS クラス名・DOM 構造 |

| アドレスバーの URL（`location.pathname` / `location.search`） | `href` 属性値 |

| | `getComputedStyle` の数値 |

| | `aria-expanded` 等の内部属性 |

| | `querySelector('.xxx')` の有無 |

  

> URL はアドレスバーに表示されユーザーが見る/ブックマークできるため観察可能。ただし URL は補助的な確認に留め、**主判定は screenshot**。

  

## 操作（When）での要素特定

  

`snapshot` はアクセシビリティツリーを返す。**When で操作対象を探すためだけ**に使う。

  

```bash

playwright-cli snapshot # 操作したい要素の ref を確認する

playwright-cli click e15 # ref で操作

```

  

ref が使いにくい場合は role/text ロケーターを使う（クラスセレクタは使わない）。

  

```bash

playwright-cli click "getByRole('link', { name: 'スマホアプリ開発' })"

playwright-cli click "getByText('LINE')"

```

  

## 前提: 開発サーバー

  

ベースURL は `http://localhost:3000`。起動していなければ起動を促す。

  

```bash

npm run dev &

sleep 10

```

  

`playwright-cli` が無い場合は `npx --no-install playwright-cli --version` で確認し、なければユーザーにその旨を伝えて中断する。

  

## ワークフロー

  

### ステップ1: .feature を読み、テスト計画を宣言する

  

対象の `.feature` を読み込み、Feature / Background / Scenario / タグ（`@pc` `@sp`）をすべて把握してから宣言する。

  

```

## テスト計画

- Feature: 実績ページの挙動 (tests/manual/works.feature)

- Scenario 数: 7

- タグ内訳: @pc @sp (7件) → PC・SP 両方で実行

- ビューポート: PC(1280x800) / SP(375x812)

```

  

### ステップ2: Scenario をタグに従って実行する

  

各 Scenario は**独立**して実行する（前のシナリオの状態を引き継がない）。

  

```bash

# @pc のシナリオ

playwright-cli resize 1280 800

  

# @sp のシナリオ

playwright-cli resize 375 812

  

# @pc @sp の両タグ → PC・SP の両方で実行する

```

  

### ステップ3: 各ステップを実行・検証する

  

- `Given` → `goto` でページを開く

- `When` → `snapshot` で操作対象を探し、`click` / `fill` / `hover` などで操作する

- `Then` → `screenshot` で画像を保存する（必要に応じて URL を `eval` で補助確認）

  

### ステップ4: レポートを出力する

  

全シナリオ終了後、「レポート形式」に従って出力する。

  

## Gherkin キーワードの対応

  

| キーワード | 意味 | playwright-cli での扱い |

| --- | --- | --- |

| `Given` | 前提条件 | `goto` でページを開く / `resize` でビューポート設定 |

| `When` | ユーザー操作 | `snapshot` で ref を確認 → `click` / `fill` / `hover` |

| `Then` | 期待結果 | `screenshot` で画像を保存（補助的に `eval` で URL 確認） |

| `And` `But` | 直前キーワードの継続 | 同上 |

| `Background` | 全シナリオ共通前提 | 各シナリオ実行前に満たす（サーバー起動確認） |

  

## ステップ解釈ガイド（意図 → playwright-cli）

  

### 「〜を開いている」（Given）

  

```bash

playwright-cli goto http://localhost:3000/works

```

  

### 「〜を選ぶ」「〜を見る」（When）

  

```bash

playwright-cli snapshot # 操作対象を探す

playwright-cli click "getByRole('link', { name: 'スマホアプリ開発' })" # クリック

```

  

対象が一意でない場合（「実績を選ぶ」「タグを選ぶ」）は最初の該当要素を選び、選んだ内容をレポートに記録する。

  

### 「〜ページが表示される」「〜が表示される」（Then）

  

```bash

playwright-cli screenshot --filename=.playwright-cli/screenshots/works-03-pass-pc.png

# 遷移を伴う場合は URL を補助確認

playwright-cli eval "location.pathname"

```

  

Then の合否は **screenshot を目視して判定する**。URL が変わらないケースは screenshot のみで判定する。

  

### 「〜で絞り込まれた〜が表示される」（Then）

  

絞り込み前後の見た目の変化を screenshot で確認する。

  

```bash

# 絞り込み前の screenshot

playwright-cli screenshot --filename=...before.png

playwright-cli click "getByText('LINE')" # When: タグを選ぶ

# 絞り込み後の screenshot（表示件数・内容が変化したことを目視で確認）

playwright-cli screenshot --filename=...after.png

# 補助的に URL のクエリを確認（ユーザーがブックマーク可能）

playwright-cli eval "location.search"

```

  

## レポート形式

  

```markdown

## テスト結果レポート

  

**Feature**: 実績ページの挙動 (tests/manual/works.feature)

**実施日時**: YYYY-MM-DD

**ブラウザ**: Chromium

  

| # | Scenario | タグ | PC | SP | 証跡（screenshot） | 備考 |

| --- | ---------------------- | ------- | --- | --- | ------------------------- | ---- |

| 1 | 実績一覧を閲覧できる | @pc @sp | ✅ | ✅ | works-01-pass-pc/sp.png | |

| 2 | タグで実績を絞り込める | @pc @sp | ✅ | ❌ | works-02-fail-sp.png | SP でタグ選択後も表示が変わらない |

  

**合計**: X件合格 / Y件不合格 / Z件スキップ

```

  

証跡は `.playwright-cli/screenshots/` に保存する。ファイル名規則: `{feature名}-{番号}-{pass|fail}-{pc|sp}.png`

  

## 失敗時の対処

  

1. `screenshot --filename=...-fail.png` で証跡を保存する

2. 原因調査が必要なら `playwright-cli console` / `playwright-cli requests` を使う

3. レポートの「備考」に**期待した見た目**と**実際の見た目**を記述する

4. テストは中断せず次のシナリオに進む

  

## このプロジェクトの .feature 一覧

  

| ファイル | 対象 |

| --- | --- |

| `tests/manual/top.feature` | トップページ（各セクション → 詳細への遷移） |

| `tests/manual/works.feature` | 実績ページ（一覧・タグ絞り込み・詳細・関連） |

| `tests/manual/news.feature` | お知らせページ（一覧・タグ絞り込み・詳細） |

  

## 実行例: works.feature「個別実績の詳細を閲覧できる」(@pc @sp)

  

```gherkin

@pc @sp

Scenario: 個別実績の詳細を閲覧できる

Given "/works" を開いている

When 実績を選ぶ

Then 個別実績ページが表示される

```

  

```bash

# --- PC ---

playwright-cli resize 1280 800

playwright-cli goto http://localhost:3000/works # Given

playwright-cli snapshot # When: 実績リンクの ref を確認

playwright-cli click e[最初の実績リンクの ref] # When: 最初の実績を選ぶ（選んだ名前を記録）

playwright-cli screenshot --filename=.playwright-cli/screenshots/works-03-pass-pc.png # Then: 画像で目視判定

playwright-cli eval "location.pathname" # Then(補助): /works/xxxx であることを確認

  

# --- SP ---

playwright-cli resize 375 812

playwright-cli goto http://localhost:3000/works

playwright-cli snapshot

playwright-cli click e[最初の実績リンクの ref]

playwright-cli screenshot --filename=.playwright-cli/screenshots/works-03-pass-sp.png

playwright-cli eval "location.pathname"

```