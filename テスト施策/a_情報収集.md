- [DOM操作おけるクエリ順序](https://testing-library.com/docs/queries/about/#priority)
- [小規模開発のためのテスト方針を考える](https://zenn.dev/ik_takagishi/books/5c6c9fe3a7ad2c/viewer/4be626)
- [フロントエンドのテストのガイドライン](https://zenn.dev/ik_takagishi/books/5c6c9fe3a7ad2c/viewer/10b441)
- [Spotify Engineering テストハニカム](https://engineering.atspotify.com/2018/01/testing-of-microservices)
	- https://x.com/searls/status/1393385209089990659
- [モックは必要悪で、しないにこしたことはない](https://blog.8-p.info/ja/2021/10/12/mock/)
- [E2Eテストは「人間が書く」から「AIに書かせる」時代に - Claude Codeで目指したテスト作成のAgent Loop -](https://tech.yappli.io/entry/create-e2e-test-with-claude-code)
- [シナリオテストにおけるBRIEFの原則 - Cucumber](https://cucumber.io/blog/bdd/keep-your-scenarios-brief/)
	- BRIEF の 6 原則:
		- Business language: ドメイン用語を使う
		- Real data: 実データを使う
		- Intention revealing: 意図を書く、メカニクス（UI 操作）を書かない ← 我々が違反中
		- Essential: 本質的でない情報を削る
		- Focused: 1 シナリオ 1 ルール
		- Brief: 5 行以内
- [CastingONEにおけるフロントエンドテスト戦略について](https://zenn.dev/castingone_dev/articles/e0e8c9becd0a3c)
- [たった２つのステップを意識するだけで書けない単体テストがほぼなくなる](https://qiita.com/_mi/items/ce66aa922ee46b00ab2d)

## テストプロセス
- テストスクリプトから書き始めるのではなく、何をどうテストするのかから考え始めることが重要である。
- テスト分析・設計を頭の中で暗黙的に行うのをやめ、言語化してチームで共通認識が取れている状態を目指す。
![[テストプロセス.png]]


## シナリオテスト Playwright × AI (ZOZOTOWN)
- [テストケースをコードで書かないE2Eテスト ── Claude CodeとPlaywright CLIで実現する自然言語テスト自動化](https://techblog.zozo.com/entry/claude-code-with-playwright-cli)
![[20260501105335.png]]

```bash
# ref番号を使って要素をクリック
playwright-cli click e42 --session=pc

# テキストを入力
playwright-cli fill e15 "test@example.com" --session=pc

# スクリーンショットを取得
playwright-cli screenshot --output screenshots/cart-top.png --session=pc
```



## リスクベーステスト
- すべての機能を同じ粒度でテストするのではなく、不具合が起きたときの影響度が高い箇所から優先的にテストする考え方
- 影響度だけでなく、発生しやすさ・変更頻度・事業上の重要度も合わせて見る
- フロントエンドでは、決済・申込・認証・ユーザーが必ず通る主要導線などを優先しやすい
- E2Eテストは実装・保守コストが高いため、リスクが高いシナリオやリグレッション防止の価値が高い箇所に絞る
- エンジニアだけで判断せず、ディレクターやQA担当者と一緒に「どこが壊れると困るか」を要件定義・キックオフ時点で確認する

## テスティングトロフィーとフロントテストの関係

![[テストアーキテクチャ.png]]

- ユニットテスト全てではなくポイントで書く
- フロントエンドのテストは統合テスト（コンポーネントテスト、ブラウザテスト）に集中する
- E2Eテスト（シナリオテストやスモークテスト）はリスキーなものに限定する
- 最も影響度が高いものからテストを書く
- [【Webフロントエンド】テスト自動化のロードマップを考えてみる](https://zenn.dev/mczkzk/articles/a09d590e08e69d)
- [フロントエンド開発のためのテスト入門 今からでも知っておきたい自動テスト戦略の必須知識](https://amzn.asia/d/0iGlNX1w)

- 「誰の視点でテストを書くか？」が重要（アクター）
- 良いテストとは「説明テスト」と「網羅テスト」両方を持つテストである
	- 説明テスト：コードの意図や使い方を示すためのテスト
	- 網羅テスト：リグレッション防止
- ユニットテストは「純粋な計算ロジック」に限定し、UIやAPI連携は統合テストで行う
	- フォームのテストは統合テストでやる（入力、バリデーション、送信ボタン、エラー表示が合成された状態でテストするべき）
- [フロントエンドで本当に価値あるテストの書き方](https://zenn.dev/fitness_densuke/articles/2026-01-02-frontend-testing-principles)


## ガイドライン策定
- [自動テストのガイドライン策定によるテスト改善への取り組み - サイボウズ](https://blog.cybozu.io/entry/2025/07/16/113000)

ガイドライン策定後にいくつかの機能において改善を実施し、以下のような効果が得られました。
- UIテストの削減: 94件 → 52件（約45%削減）
- テスト実行時間の短縮: 約5分短縮
- 不安定なテストの削減: 数十件のUI操作起因の不安定テストを削除
普段の機能開発においてもガイドラインが参照され、より小さい分類での自動テストを選択されることが増えています。新機能の開発時には、まず単体テストや統合テストでカバーできる範囲を検討し、UIテストは必ず担保したいハッピーパスなどに対するテスト手段として位置づけるようになりました。

また、副次的な効果ではあるのですが、実装スコープやテスト対象が明確になったこともあり、QAが自律的にテストの実装や修正を進められる状態になりました。

## シフトレフトテスト
- [PayPayにおけるシフトレフトテストのアプローチ：効果と導入](https://blog.paypay.ne.jp/shift-left-testing-approach/)


## 静的なテストはカバレッジに影響しないのでなくていいかも
- 左は静的テスト3つ、右は静的テスト1つだがカバレッジは同じ
- ファイル自体を除外したほうが良いかというとそうではなく、今後拡張された時にカバレッジが自動で下がるようにするため、静的テスト1つだけ残してパスさせておく
![[スクリーンショット 2026-06-01 3.40.35.png]]



