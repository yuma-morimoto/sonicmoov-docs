- [テスト DOM操作おけるクエリ優先度](https://testing-library.com/docs/queries/about/#priority)
- [小規模開発のためのテスト方針を考える](https://zenn.dev/ik_takagishi/books/5c6c9fe3a7ad2c/viewer/4be626)
- [フロントエンドのテストのガイドライン](https://zenn.dev/ik_takagishi/books/5c6c9fe3a7ad2c/viewer/10b441)
- [Spotify Engineering テストハニカム](https://engineering.atspotify.com/2018/01/testing-of-microservices)
	- https://x.com/searls/status/1393385209089990659
- [モックは必要悪で、しないにこしたことはない](https://blog.8-p.info/ja/2021/10/12/mock/)
- [E2Eテストは「人間が書く」から「AIに書かせる」時代に - Claude Codeで目指したテスト作成のAgent Loop -](https://tech.yappli.io/entry/create-e2e-test-with-claude-code)
- [Cucumber, シナリオテストにおけるBRIEFの原則](https://cucumber.io/blog/bdd/keep-your-scenarios-brief/)
	- BRIEF の 6 原則:
		- Business language: ドメイン用語を使う
		- Real data: 実データを使う
		- Intention revealing: 意図を書く、メカニクス（UI 操作）を書かない ← 我々が違反中
		- Essential: 本質的でない情報を削る
		- Focused: 1 シナリオ 1 ルール
		- Brief: 5 行以内
- [Zenn, CastingONEにおけるフロントエンドテスト戦略について](https://zenn.dev/castingone_dev/articles/e0e8c9becd0a3c)
- [qiita, たった２つのステップを意識するだけで書けない単体テストがほぼなくなる](https://qiita.com/_mi/items/ce66aa922ee46b00ab2d)
- [ipa.go.jp, IPA ソフトウェアテスト見積もりガイドブック](https://www.ipa.go.jp/archive/publish/qv6pgp0000000yho-att/000005132.pdf)
- [ipa.go.jp, IPA 高信頼化ソフトウェアのための 開発手法ガイドブック](https://www.ipa.go.jp/archive/files/000004550.pdf)
- logmi, フロントエンド開発テスト最前線（全4記事）
	- [web](https://logmi.jp/main/technology/328058) logmi, 自動テストはなぜあまり書かれてこなかったのか　和田卓人×倉見洋輔×古川陽介がひもとく、フロントエンドテストの歴史
	- [web](https://logmi.jp/main/technology/328078) logmi, “フロントエンドの出現”が、フロントエンドテストのターニングポイント　和田氏が語る、「テストは書いて当たり前の時代」までの流れ
	- [web](https://logmi.jp/main/technology/328087) logmi, 「フロントエンドのテストは“不安定さ・壊れやすさ”との戦い」　和田卓人×倉見洋輔×古川陽介が語る、アクセシビリティの重要性
	- [web](https://logmi.jp/main/technology/328084) logmi, 「自分たちが考えたモックやスタブは妄想にすぎない」　テスト駆動開発の第一人者が説く、フロントエンドテストの本質
- [pdf](https://jstqb.jp/dl/JSTQB-SyllabusFoundation_VersionV40.J02.pdf) JSTQB Foundation Level シラバス
	- 「テスト7原則」読んでおきたい

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


## 国内のテストガイドライン事例
自社のテストガイドライン作成の参考になりそうな公開事例と、実務でよく使われる構成をまとめました。

**参考になる公開資料（国内）**

IPA（情報処理推進機構）が最も充実しています。「高信頼化ソフトウェアのための開発手法ガイドブック」は、予防・検知活動の手法に加えて、株式会社ジャステックなど実企業の先進的な取り組み事例を紹介しており、自社ガイドラインの型として最も参考になります。「ソフトウェアテスト見積りガイドブック」はテスト仕様書・テスト手順書・テストデータなど構成要素の定義と見積り手法が具体的です。「組込みソフトウェア開発における品質向上の勧め（テスト編～事例集～）」はテスト設計観点の実装例が豊富で、組込み系なら特に有用です。経済産業省の「システム及びソフトウェア品質の見える化、確保及び向上のためのガイド」は品質保証活動全体のフレームと、国内企業の品質保証成果物の情報を付録にまとめています。

資格制度としてはJSTQB（ISTQBの日本組織）が「テストの7原則」やテストプロセス（計画・分析・設計・実装・実行・報告）を体系化しており、社内ガイドラインの章立てをこの型に合わせる企業が多いです。

**テストガイドライン/マニュアルによくある構成**

多くの事例に共通するのは、テスト計画（目的・対象範囲・体制・スケジュール）、テスト設計（観点・技法・テストケース設計基準）、テスト実施（環境・実行手順・記録方法）、テスト報告（結果集計・バグ管理・完了基準）の4段階構成です。これに加えて、単体/結合/システム/受入といったテストレベルごとの実施基準や、品質メトリクス（バグ密度、カバレッジ目標値など）の合格ライン、テストケース・不具合票のテンプレートを付録として持たせる企業が多く見られます。

**自社ガイドライン作成時のポイント**

IPAの資料でも繰り返し触れられていますが、汎用ガイドラインをそのまま使うのではなく「自組織の文化・標準に合わせてカスタマイズする」ことが前提とされています。実務では、まずJSTQBのテストプロセス（計画→分析→設計→実装→実行→報告）を骨格にし、そこにIPA事例集の観点リストやテンプレートを肉付けする進め方が現実的です。

Sources:

- [高信頼化ソフトウェアのための開発手法ガイドブック（IPA）](https://www.ipa.go.jp/archive/files/000004550.pdf)
- [ソフトウェアテスト見積りガイドブック（IPA）](https://www.ipa.go.jp/archive/publish/qv6pgp0000000yho-att/000005132.pdf)
- [組込みソフトウェア開発における品質向上の勧め［テスト編～事例集～］（IPA）](https://www.ipa.go.jp/archive/publish/qv6pgp000000103w-att/000005149.pdf)
- [予防と検証の事例を中心に SEC BOOKS（IPA）](https://www.ipa.go.jp/archive/publish/qv6pgp0000000zlp-att/000005144.pdf)
- [システム及びソフトウェア品質の見える化、確保及び向上のためのガイド（経済産業省）](https://www.meti.go.jp/policy/it_policy/softseibi/metrics/product_metrics.pdf)
- [同ガイド 付録：国内の品質保証成果物情報（経済産業省）](https://www.meti.go.jp/policy/it_policy/softseibi/metrics/product_metrics_appendix.pdf)
- [JSTQB ガイドライン](https://www.jstqb.jp/guideline/)
- [国際ソフトウェアテスト資格「JSTQB」が定めるテストプロセスとは（NSIT）](https://www.nsit.co.jp/column/427/)

必要であれば、これらを踏まえた自社ガイドラインの目次案や雛形をWord/Markdownで作成することもできます。



