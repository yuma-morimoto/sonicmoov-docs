

**【Tripla社 返答】**
■ IGP認証済み会員の認証・紐付け（処理フロー）
・大まかな流れは以下のとおりです。

アプリがIGPから会員のトークンを受け取る
アプリがそのトークンをtriplaの会員サインインAPIに渡す **▶️Membership Sign in by open id使える？**
tripla側がそのトークンを検証し、IGPから返る会員の識別子でtripla会員と紐付ける
triplaのアクセストークンが返る
アプリはそのアクセストークンでtriplaの会員APIを呼び、予約情報などを取得する
・具体的なAPIや処理の詳細は、IGP側がどの連携方式に対応できるか（トークン検証用APIの提供か、OpenID Connect対応か）によって変わるため、
開発チームを交えた協議で決める形になると思います。

■ 導入条件（各社の作業、追加開発の有無、期間、検証環境・テスト用アカウント）
・今回の要件が固まらないと、弊社側で見積もりや条件の提示ができない内容です。
まずは要件を確定いただけますと幸いです。

↓

## Membership Sign in by open id
- [Membership Sign in by open id](https://developers.tripla.ai/reference/membership-sign-in-by-open-id) が使えそう

### リクエスト
```
curl -X POST "https://endpoint.staging.tripla.ai/hotels/{hotel_code}/memberships/open_id/sign_in" \ 
	-H "Content-Type: application/json" \ 
	-H "Tripla-Locale: ja" \ 
	-H "App-Version: tripla-API/1.0" \ 
	-d '{ 
		"id_token": "<JWT_ID_TOKEN>", 
		"sso_token": "<SSO_ACCESS_TOKEN>", 
		"user_code": "<AUTHORIZATION_CODE>" 
	}'
```

- **リクエストボディ**:
    - `sso_id` (数値・任意): SNS認証設定のID
    - `sso_token` (文字列・任意): SSOから返却されたアクセストークン
    - `user_code` (文字列・任意): SSOから受け取った認可コード
    - `id_token` (文字列・任意): SSOから受け取ったJWT形式のユーザーIDトークン
    - `uid` (文字列・任意): SSOのstate値
    - `redirect_uri` (文字列・任意): ログイン後のリダイレクトURI
    - `grant_type` (文字列・任意): `refresh_token` を取得する場合は `client_credentials` を指定
    - `password` (文字列・任意): 次回ログイン時のパスワード入力を省略するためのパスワード

### 結果
- sso_token指定の場合：`open_id is not active`
- user_code指定の場合：`open_id is not active`
- uid指定の場合：`open_id is not active`





- 実現したいこと
	- Triplaのカスタム認証（外部認証APIでトークンを検証する方式）を利用したいと考えている
- 確認したいこと
	- 宿泊予約ページ（/booking/result）へ渡していたclient_tokenをTripla BW APIでどのように渡すのかを知りたい
- 試したこと / 結果

---

```
社内で検討を進めた結果、連携方式の要件が固まりましたのでご連絡いたします。

■ 確定した連携要件

・実装方式：方式2（tripla APIを呼び出してネイティブ実装する方法）

・認証方式：カスタム認証（IGP側のトークン検証用APIを利用した連携）

　※OpenID Connectではなく、現在近鉄様でも有効になっているカスタム認証（SOAP形式のトークン検証API）を利用する方針で確定いたしました。

上記要件に伴い、ご提示いただいた処理フローのうち、2番目のステップである「triplaの会員サインインAPI」の具体的な仕様を確認したく存じます。

> 1. アプリがIGPから会員のトークンを受け取る

> 2. アプリがそのトークンをtriplaの会員サインインAPIに渡す

> 3. tripla側がそのトークンを検証し、IGPから返る会員の識別子でtripla会員と紐付ける

公開APIドキュメント（developers.tripla.ai）を確認いたしましたが、カスタム認証用のIGP発行のトークンを受け渡すための「会員サインインAPI」のエンドポイントおよびパラメータ仕様が特定できませんでした。

なお、弊社側ではドキュメント上の既存APIから以下2点を試しましたが、いずれもエラーとなっております。

■ 試したこと

1. `[POST] Membership Sign in by open id token` の `authorization` にIGP発行のトークンを渡してリクエスト

　→ エラー内容：「Open ID トークンが違います。」

2. `[POST] Membership Sign in by open id` のボディの `sso_token`、`user_code` 等にIGP発行のトークンを渡してリクエスト

　→ エラー内容：「open_id is not active」

つきましては、以下の点についてご教示いただけますでしょうか。

1. 該当する「会員サインインAPI」のエンドポイントURL・メソッド（developers.tripla.ai上の該当API名やURLがあればご案内いただけますと幸いです）

2. ※公開ドキュメントに記載がない場合：リクエスト仕様（カスタム認証用トークンを渡す際のパラメータ名や格納場所：ヘッダー／ボディ等）

3. ※公開ドキュメントに記載がない場合：レスポンス仕様（発行されるtriplaのアクセストークンの形式や有効期限など）
```


---

```
社内で検討を進めた結果、連携方式の要件が固まりましたのでご連絡いたします。

■ 確定した連携要件
・実装方式：方式2（tripla APIを呼び出してネイティブ実装する方法）
・認証方式：カスタム認証（IGP側のトークン検証用APIを利用した連携）
　※OpenID Connectではなく、現在近鉄様でも有効になっているカスタム認証（SOAP形式のトークン検証API）を利用する方針で確定いたしました。

つきましては、上記要件を実現するにあたり、以下の点についてご確認・ご教示いただけますでしょうか。

■ 1. 会員サインインAPIの仕様について
公開APIドキュメント（developers.tripla.ai）を確認いたしましたが、カスタム認証用のIGP発行のトークンを受け渡すための「会員サインインAPI」のエンドポイントおよびパラメータ仕様が特定できませんでした。

なお、弊社側ではドキュメント上の既存APIから以下2点を試しましたが、いずれもエラーとなっております。

1. `[POST] Membership Sign in by open id token` の `authorization` にIGP発行のトークンを渡してリクエスト
　→ エラー内容：「Open ID トークンが違います。」
2. `[POST] Membership Sign in by open id` のボディの `sso_token`、`user_code` 等にIGP発行のトークンを渡してリクエスト
　→ エラー内容：「open_id is not active」

既存の公開APIでカスタム認証トークンを受け取れるエンドポイントやパラメータ仕様が存在する場合は、そちらをご教示いただけますでしょうか。（developers.tripla.ai に記載がない場合は、仕様書等をご共有いただけますと幸いです）

■ 2. 導入条件・追加開発の有無について
もし既存の公開APIでは対応しておらず、新たにAPIを用意する必要がある場合は、Tripla様・IGP側・アプリ側それぞれで必要となる作業、追加開発の有無、期間、検証環境やテスト用アカウントの提供条件をご教示ください。

```
