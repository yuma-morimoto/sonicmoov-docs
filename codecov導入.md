## nhk-admin-frontend
- backlog: https://sonicmoov.backlog.jp/view/MEASURE-80
- github PR: https://github.com/sonicmoov/nhk-admin-frontend/pull/15
- 状態：完了
- nhk-admin-frontendでcircle ciのエラーが出てしまっているので念の為確認する。→ [清水さん、村岡さんへ確認済み](https://sonicmoov.slack.com/archives/C0ACCK9S62W/p1772507365529359)
- developブランチは使われていない。
- 2年以上使われていないリポジトリ、村岡さんもわからない。
- masterにマージすることにする。
- やること
	- [x] PR作成
	- [x] レビュー承認
	- [x] マージ
## odds-park-sp-mockup-v2
- backlog: https://sonicmoov.backlog.jp/view/MEASURE-73
- github: https://github.com/sonicmoov/odds-park-sp-mockup-v2
- PR: https://github.com/sonicmoov/odds-park-sp-mockup-v2/pull/163
- やること
	- [x] vitest入れる
	- [x] PR作成
	- [ ] レビュー承認
	- [ ] マージ
## odds-park-lp
- backlog: https://sonicmoov.backlog.jp/view/MEASURE-74
- github: https://github.com/sonicmoov/odds-park-lp
- PR: https://github.com/sonicmoov/odds-park-lp/pull/128
- PR作成対象のブランチ：develop
- 状態：
- やること
	- [x] vitest入れる
	- [x] [ここ](https://docs.codecov.com/docs/github-5b-setting-coverage-standards-with-flags)を参考にFlagsを設定する
	- [ ] ダミーのテストファイルを削除する
	- [x] PR作成
	- [ ] レビュー承認
	- [ ] マージ
### Codecov導入見送りについて                                                                     
対象リポジトリ: sonicmoov/odds-park-lp                                                                        
現状のリポジトリ構成ではCodecovの導入が困難なため、今回の対応から除外しました。
理由：
1. ルートに package.json が存在しない
リポジトリ直下にはプロジェクト設定ファイルがなく、campaign / oddsbattle07 / oddsbattle081 / otokuweek / play /
yamawakeweek という複数のLPが独立したディレクトリとして並列管理されている構成です。ワークフローから単一のエン
トリーポイントでテストを実行できません。
2. Nodeバージョン管理ファイルが存在しない
他のリポジトリで使用されている .node-version や .tool-versions
がなく、CI上でNodeのバージョンを特定できません。
3. テストの仕組みが整備されていない
テストフレームワークが導入されておらず、カバレッジを計測・アップロードする前提が整っていません。

今後の対応に向けて：
導入を進めるには、テストフレームワークの選定・導入、およびリポジトリ構成の整理（モノレポ対応など）を別途検討する必要があります。

## 概要
- githubのリポジトリにcodecovを接続する作業

## 対象リポジトリ
- git clone git@yuma-morimoto:sonicmoov/odds-park-sp-mockup-v2.git
- git clone git@yuma-morimoto:sonicmoov/odds-park-lp
- git clone git@yuma-morimoto:sonicmoov/nhk-admin-frontend

## 作業手順
1. codecov導入対象のリポジトリをクローンする
2. 作業ブランチを切る
3. codecov.ymlを作成する

```yml
name: Integration with CodeCov

on:
  workflow_dispatch:
  pull_request:
    branches:
      - main

jobs:
  coverage:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install dependencies
        uses: sonicmoov/github-actions-frontend/.github/actions/install-dependencies-with-npm@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          node-version-file: ".node-version"

      - name: Run tests with coverage
        shell: bash
        run: npm test -- --coverage

      - name: Upload coverage to Codecov
        uses: sonicmoov/github-actions-frontend/.github/actions/codecov-upload@v1
        with:
          codecov-token: ${{ secrets.CODECOV_TOKEN }}
          slug: ${{ github.repository }}
```
3. コミットする
4. プッシュする
5. PRを作成する（ghコマンドを使用）

