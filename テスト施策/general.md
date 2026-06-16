- 標準化はウェブアプリとマークアップの2つに分類して行う必要がありそう。
- 最終的に、ウェブアプリ、マークアップにおいて、テストの考え方（フロントにおけるユニット・統合・E2E等の考え方）、テストコードを書く手順、テスト計画（どこにテストを書くか）、良いテスト・悪いテストなど判断を中心に標準化を行いたい。
- 最終成果物のガイドライン（マニュアル）はテストそのものの書き方よりもテスト自動化を運用するための知識体系としたい。（ISOのマネジメントシステムの考え方を取り入れる）

## 大和冷機 テスト計画
- 橋本さん テスト観点のPR: https://github.com/sonicmoov/drk-specs/pull/30

## スケジュール
- 3/30 課題を立てる予定

## 工数
- 体系化工数：270,000円（60h）（MTGや調査、検証）
	- 第1回MTG：0.5h * 4人 = 2h
	- 現状把握：5案件 * 4h = 20h
	- 第2回MTG：0.5h * 4人 = 2h

- 標準化工数：180,000円（40h）（MTGや資料作成）

## 体系化フェーズ（6月末まで）
- **テスト実態の把握（5月末）**
	- 案件ごとのテストコードの書き方、判断の知見、導入効果などの調査・ヒアリングしてテスト実態を把握する
		- 調査すること：
			- どこまでできていて、どこからできていないか
- **ドラフトガイドライン作成・案件導入**
	- 現状把握でまとめた内容を実案件で導入（ドラフトのガイドライン）
- Check 検証
	- ヒアリング等
- Act 改善
	- チームで会議してガイドラインを改善
- DCAは6月末
	- とりあえず1PJ検証

## 標準化フェーズ（10月まで）
- 体系化を正式なガイドラインとしてまとめる

## 現状把握
### チケット
- [MEASURE-97](https://sonicmoov.backlog.jp/view/MEASURE-97) 【現状把握】ナビパーク
- [MEASURE-99](https://sonicmoov.backlog.jp/view/MEASURE-99) 【現状把握】イースト
- [MEASURE-98](https://sonicmoov.backlog.jp/view/MEASURE-98) 【現状把握】TRP
- [MEASURE-100](https://sonicmoov.backlog.jp/view/MEASURE-100) 【現状把握】ほほえみごはん
- [MEASURE-101](https://sonicmoov.backlog.jp/view/MEASURE-101) 【現状把握】オッズパーク LP

## 仮説検証
### チケット
- [MEASURE-144](https://sonicmoov.backlog.jp/view/MEASURE-144) 【検証】設計フェーズでのテスト計画
- [MEASURE-145](https://sonicmoov.backlog.jp/view/MEASURE-145) 【検証】テスト範囲・テストレイヤー判断基準の作成
- [MEASURE-146](https://sonicmoov.backlog.jp/view/MEASURE-146) 【検証】AIによるコンポーネント・E2Eテストの有効性（分割済み）
- [MEASURE-147](https://sonicmoov.backlog.jp/view/MEASURE-147) 【検証】Astro案件の代替テスト手段
- [MEASURE-148](https://sonicmoov.backlog.jp/view/MEASURE-148) 【検証】AIによるテストコード生成
- [MEASURE-149](https://sonicmoov.backlog.jp/view/MEASURE-149) 【検証】AIによるマニュアルテストの自動化
