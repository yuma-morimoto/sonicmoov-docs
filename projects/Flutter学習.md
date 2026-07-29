
- [フロントエンドFlutter学習施策](https://docs.google.com/document/d/1pvmQ9H6Mw_gnQ4Ub3FuBWlnQj4TnyFIVf3TSjPkjPAo/edit?tab=t.0)
- [Flutter環境構築 - wiki](https://github.com/sonicmoov/flutter_sample_mvvm/wiki/Flutter%E7%92%B0%E5%A2%83%E6%A7%8B%E7%AF%89)
- [フロントエンド向けFlutterメモ - wiki](https://github.com/sonicmoov/flutter_sample_mvvm/wiki/%E3%83%95%E3%83%AD%E3%83%B3%E3%83%88%E3%82%A8%E3%83%B3%E3%83%89%E5%90%91%E3%81%91Flutter%E3%83%A1%E3%83%A2)
- [weave 定期清掃 清掃報告システム](https://github.com/sonicmoov/weave-cleaning-report-app)

## 学ぶ必要があること
- Dart
	- [List](https://api.flutter.dev/flutter/dart-core/List-class.html)（リスト操作）
- Flutter の Widget による UI 実装
	- Scaffold
	- Understanding constraints
- Riverpod による状態管理と依存性注入
	- Provider - 読み取りのみ DI用途で使う
	- StateProvider - 簡易的な状態管理 2.0以降は非推奨らしい
	- NotifierProvider / AsyncNotifierProvider
	- Ref
	- [ConsumerWidgetとStatefulConsumerWidgetの使い分け](https://zenn.dev/gekitenius/articles/flutter_consumer_vs_statefulconsumer)
	- Reverpod Generator
		- riverpod_annotation
- MVVM + Repository パターンによる責務分離
- Dio + Retrofit による API 通信
- Freezed によるイミュータブルなデータ定義
- Drift による SQLite キャッシュ
- build_runner によるコード生成
## 個人的に学ぶ
- flutter_hooks

## Dart
### リダイレクトコンストラクタ
### リダイレクト・ファクトリコンストラクタ
`factory クラス名() = 別クラス名;` と書くと、「このコンストラクタが呼ばれたら、右側のクラスに処理を丸投げ（リダイレクト）する

### factory Pattern
- 普通のコンストラクタだと1種類のクラスしか作れないが、factoryだと引数に応じて数種類のクラスを作れる。
```
class Car {
	final String type; 
	
	Car._internal(this.type); // クラスの内部だけで使う隠しコンストラクタ
	
	factory Car.create(String roadCondition) { 
		if (roadCondition == "雪道") { 
			return Car._internal("4WDのSUV"); // 雪道ならSUVを返す 
		} else { 
			return Car._internal("普通のセダン"); // それ以外ならセダンを返す 
		} 
	}
}
```

## Constraints
> **Constraints go down. Sizes go up. Parent sets position.**

## Column & Row

MainAsixAlignmentとCrossAsixAlignment

![](https://flutter.ctrnost.com/images/basic/layout/03/column_row_03.svg)

## MVVM-アプリユニットでのFlutter推奨アーキテクチャ

アプリユニットではiOSとAndroidのアプリ開発と同様にMVVMを推奨しています。  
ただ、便宜上MVVMと言っているだけで、ページや関心ごと単位で状態ストアを作成するというだけです。(フロントで言えばJotaiではなくZustandライク)

MVVMはView/ViewModel/Modelのアーキテクチャ

View: Widget  
ViewModel: RiverpodのNotifier　状態ストアに当たるもの  
Model: Repository(APIやDBへのアクセス)やドメインロジックやサービス

APIからデータを取得しようとした場合このような流れになる。

View <- ViewModel <- Repository <- API

詳しくは[README](https://github.com/sonicmoov/flutter_sample_mvvm/blob/main/README.md)の通りです。


## Riverpod
- riverpodできること
	- 状態管理
	- メモ化（キャッシュ）
	- DI
	- シングルトン

- Providerクラス - 状態管理のバケツ
	- 状態に対する全ての操作はProvider内で定義する
	- Providerがインスタン化されるのはreadやwatchが初めて呼び出された時で、それまではインスタンス化されない。
- ref.read(...) -「画面の更新は起こさずに、今すぐバケツの中身にアクセスするよ」と宣言
	- `ref.read(counterProvider)` ➔ 現在の数字（`int`）が取れる（例: `5`）
	- `ref.read(counterProvider.notifier)` ➔ 数字を操作するクラス（`Counter`）が取れる
- ref.watch
	- when
		- data, error, loading の 3 状態を全て定義する
		- https://qiita.com/vsuine/items/480ebec85901ae4f1c93#when
```
ref.watch(sampleProvider).when(
  data: (data) => Text(data.toString()),
  error: (error, stackTrace) => const Text('Error'),
  loading: () => const CircularProgressIndicator())

```
