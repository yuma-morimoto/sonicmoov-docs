## 概要

- 既存LPの改修作業
- チケット：
  - https://sonicmoov.backlog.jp/view/SMV_HP2021-385
  - https://sonicmoov.backlog.jp/view/SMV_HP2021-386
- **リポジトリ管理なし。サーバーに直接アップロードする形式。**

## 対象ディレクトリ

サーバー上の `public_html/` 以下が対象。`lp/` ディレクトリはサーバー上に既存。

| URL | サーバーパス |
| --- | --- |
| `sonicmoov.com/lp/ux_consultation/` | `public_html/lp/ux_consultation/` |
| `sonicmoov.com/lp/vendor/` | `public_html/lp/vendor/` |
| 386で作成するフォーム・完了ページ | `public_html/lp/` 以下に新規作成 |

## 作業

- 作業ディレクトリ：`/Users/l_0026/Documents/sonicmoov-official`

## SMV_HP2021-385 【LP】タグ設置

- チケット: https://sonicmoov.backlog.jp/view/SMV_HP2021-385 GTMタグ（`GTM-WX3K7JDZ`）を以下のページに設置する。

**対象ページ：**

- UXデザイン相談室LP（`/lp/ux_consultation/`）
- UXデザイン相談室フォームページ × 2（SMV_HP2021-386 で作成するページ）
- ベンダー切り替えLP（`/lp/vendor/`、`/lp/vendor/#contact`）

**設置箇所：**

- `<head>` 内にGTMスクリプト
- `<body>` 直後にGTM noscript タグ

---

## SMV_HP2021-386 【LP】フォーム・完了ページ作成

- チケット: https://sonicmoov.backlog.jp/view/SMV_HP2021-386 新規ページを4つ作成し、URLを共有する。

| \# | ページ | 内容 |
| --- | --- | --- |
| ① | UXデザイン相談室 問い合わせページ | LeadPadフォームの埋め込み（iframe）に変更した別ページを作成 |
| ② | UXデザイン相談室 問い合わせ完了ページ | Thank you メッセージ＋デザインイメージあり |
| ③ | ベンダー切り替えLP 問い合わせ完了ページ | Thank you メッセージ＋デザインイメージあり |
| ④ | ベンダー切り替えLP 資料ダウンロード完了ページ | Thank you メッセージ＋PDFダウンロードリンクあり |

**共通：** カラーコードは各現行LPに合わせる

**作業順序：** 386（ページ作成）→ 385（タグ設置）

---

## 確認事項

- [x] **ページのURL（ディレクトリ名）** は以下で問題ないか？
  - `lp/ux_consultation/contact/` （①問い合わせページ）
  - `lp/ux_consultation/thanks/` （②完了ページ）
  - `lp/vendor/thanks/` （③完了ページ）
  - `lp/vendor/thanks-download/` （④ダウンロード完了ページ）

- [x] **`lp/ux_consultation/thanks/` および `lp/vendor/thanks/` のトップへ戻るボタン**の遷移先はどこにするか？（現在は `../` = 各LPのトップになっている）

- [x] **vendorLP の `#contact` フォーム**（LeadPad）の送信後リダイレクト先を③完了ページに設定する／④ダウンロード完了ページへの誘導経路を確認する

- [x] **vendor系ページのフッターのコピーライト権表記**（`© 2025 株式会社ソニックムーブ`）を2026に修正するか？（現行LPに合わせて2025のままにするか確認）

→上記は質問済み

