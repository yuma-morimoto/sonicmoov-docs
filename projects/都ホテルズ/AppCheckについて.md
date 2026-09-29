
- 参考
	- [Flutter アプリで App Check を使ってみる](https://firebase.google.com/docs/app-check/flutter/default-providers?authuser=0&hl=ja)

- 証明書プロバイダー
	- デフォルトプロバイダー（本番用）
		- App Attest
		- Play Integrity
	- デバッグプロバイダー（開発用）
		- [Android のデバッグ プロバイダで App Check を使用する](https://firebase.google.com/docs/app-check/android/debug-provider?hl=ja)
		- TTLは1時間で変更できない

- デバッグトークン
	- 開発者の端末であることを示すための身分証明
	- アプリ（App ID / バンドルID）ごとに個別でデバッグトークンを発行・設定する必要があるが、1つのトークンを発行して使いまわすことは可能

- App Check JWT
	- データベース等へアクセスするための「一時的な通行手形」

- 開発環境：デバッグトークンで認証 → App Check JWT を発行
- 本番環境：App Attest / Play Integrityで認証 → App Check JWT を発行


- `y.morimoto-local`として設定しているデバッグトークン
	- `0923598E-3A48-4929-96F4-5E156911BF69`