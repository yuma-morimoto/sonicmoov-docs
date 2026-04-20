## オッズパーク 概要

セガXD様のオッズパーク（公営競技投票サービス）のSPサイト・LP保守運用・開発案件。
LPのデザイン・コーディング、SPサイト改修などを継続的に対応。

- **Backlog**: https://sonicmoov.backlog.jp/projects/SMV_ODDS_PARK
- **Figma**:
  - [オッズパーク サイト運用](https://www.figma.com/files/1480257551590778175/team/1509022265727280408)
  - [オッズパーク アプリ](https://www.figma.com/files/1480257551590778175/team/1509785267796791115)
- **Googleドライブ（社外）**: https://drive.google.com/drive/u/0/folders/0AA0pX2_KA0L7Uk9PVA
- **Googleドライブ（社内）**: https://drive.google.com/drive/folders/15KYWgA2QgvGCT752HDm_-DFYxCbJJEeX
- **作業報告書**: https://docs.google.com/spreadsheets/d/1jyfLiIHFlVjs55r-gwJB1PADgWMhdi8H/edit?gid=1319887179#gid=1319887179

## リポジトリ

| 項目 | URL |
| --- | --- |
| 本体SPサイト v2（移行中・現行）| https://github.com/sonicmoov/odds-park-sp-mockup-v2 |
| 本体SPサイト v1（旧・アーカイブ予定）| https://github.com/sonicmoov/odds-park-sp-mockup |
| LP | https://github.com/sonicmoov/odds-park-lp |

- v1 は `Eleventy` + `pug` 構成。一部LPも混在。管理が煩雑なためv2へ移行中
- v2 は `Astro` に移行中。新規対応は基本v2・LP用リポジトリを使用
- LPは新規分からLP用リポジトリで管理

## 技術スタック

| 項目 | 内容 |
| --- | --- |
| SPサイト v1 | Eleventy + pug |
| SPサイト v2 | Astro |

## 開発フロー

1. `develop` から作業ブランチを作成して対応
2. 作業ブランチをリモートにpushし、`develop` へのプルリクを作成（自動で確認用URLが生成される）
3. 社内レビューを依頼
4. 問題なければ `develop` にマージ
5. マージ後URLをディレクターさんに共有
6. 納品まで完了したら `main` へのプルリクを作成しマージ

## 主なチケット種類

- LPデザイン・コーディング
- SPサイト改修（デザイン・コーディング）
- バナー制作
- 投票機能関連ページ対応

## 進行中チケット（2026/04時点）

| チケット | 概要 | ステータス |
| --- | --- | --- |
| [SMV_ODDS_PARK-71](https://sonicmoov.backlog.jp/view/SMV_ODDS_PARK-71) | Vポイント導入にともなうページ等のご制作 | 先方確認中 |
| [SMV_ODDS_PARK-75](https://sonicmoov.backlog.jp/view/SMV_ODDS_PARK-75) | 既存SPサイトへの要素追加（買い目のオッズ一覧・チェックボックス） | 先方確認中 |
| [SMV_ODDS_PARK-76](https://sonicmoov.backlog.jp/view/SMV_ODDS_PARK-76) | 投票照会ページの「＞」と「-」のsvgについて | 先方確認中 |
| [SMV_ODDS_PARK-68](https://sonicmoov.backlog.jp/view/SMV_ODDS_PARK-68) | SPサイト改修（デザイン/コーディング）の見積り | 処理中 |
