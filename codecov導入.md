
odds-park-lp Codecov導入見送りについて                                                                     
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