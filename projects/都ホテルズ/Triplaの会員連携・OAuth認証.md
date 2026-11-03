
- [Booking Widget API Access Using OAuth Access Token](https://developers.tripla.ai/docs/booking-widget-api-access-using-oauth-access-token)


## 1. 調査概要

Triplaが提供するOAuth認証のSilent Sign-in Process Flowを確認し、アプリからTriplaへログインする場合の認証手順と、IGP会員との紐付けに関するリスクを整理する。


## 2. OAuth認証の概要

Tripla OAuthは、OAuth 2.0 Authorization Code GrantとPKCEを利用する認証方式である。

アプリはTriplaの認可エンドポイントへ遷移し、Tripla側でユーザーが手動ログインする。認証成功後、Triplaはアプリに認可コードを返却する。アプリは認可コードをTriplaのToken Endpointへ送信し、アクセストークン、IDトークン、リフレッシュトークンを取得する。

## 3. アプリからTriplaへログインする流れ

### 3.1 認可リクエスト

アプリはWebViewまたはブラウザを介して、TriplaのOAuth認可エンドポイントへ遷移する。

```http
GET https://{tripla-host}/oauth/authorize
  ?hotel_code={hotel_code}
  &response_type=code
  &client_id={client_id}
  &redirect_uri={redirect_uri}
  &scope=openid profile email
  &state={state}
  &nonce={nonce}
  &code_challenge={code_challenge}
  &code_challenge_method=S256
  &locale=ja
```

主なパラメータ:

- `hotel_code`: Triplaから発行されるホテル識別子
- `response_type`: `code`
- `client_id`: TriplaのOAuthクライアントID
- `redirect_uri`: Tripla側へ事前登録したコールバックURL
- `scope`: `openid profile email`
- `state`: CSRF対策およびリクエスト対応付け用の値
- `nonce`: ID Tokenのリプレイ対策用の値
- `code_challenge`: PKCE用のチャレンジ値
- `code_challenge_method`: `S256`
- `locale`: Triplaログイン画面の表示言語

### 3.2 Tripla側の手動ログイン

Tripla側にログインセッションが存在しない場合、Triplaはログイン画面を表示する。

```text
アプリ
  → Tripla /oauth/authorize
  → Tripla /sign-in
  → ユーザーがTriplaの認証情報を入力
  → Tripla側でログインセッションを作成
  → /oauth/authorizeへ戻る
```

ログイン成功後、Triplaは一回限りの認可コードを生成する。

### 3.3 アプリへのコールバック

Triplaは、認可コードと`state`を付けてアプリの`redirect_uri`へ戻る。

```text
{redirect_uri}?code={authorization_code}&state={state}
```

アプリは次の検証を行う。

1. `state`が認可リクエスト時の値と一致することを確認する。
2. `code`が存在することを確認する。
3. OAuthエラーが返された場合は認証失敗として処理する。
4. 認可コードをログへ出力しない。

### 3.4 Token Exchange

アプリは認可コードをToken Endpointへ送信する。

```http
POST https://{tripla-host}/oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
client_id={client_id}
client_secret={client_secret}
redirect_uri={redirect_uri}
code={authorization_code}
code_verifier={code_verifier}
```

成功時、Triplaは次の情報を返す。

- `access_token`
- `id_token`
- `refresh_token`
- `expires_in`
- `token_type`
- `scope`
- `created_at`

取得したトークンは、Tripla APIまたはTripla Booking Widget APIへのアクセスに使用する。

## 4. PKCEの扱い

アプリは認可リクエストごとに`code_verifier`を生成する。

```text
code_challenge =
  BASE64URL-ENCODE(SHA256(code_verifier))
```

認可リクエストには`code_challenge`を付与し、Token Exchangeには元の`code_verifier`を付与する。

`code_verifier`は認証フロー完了まで一時的に保持し、認証完了後に破棄する。

## 5. IGP会員と連携に関するリスク

### 5.1 手動ログインでは別アカウントを利用できる

Tripla OAuthの手動ログインで確認されるのは、入力されたTriplaアカウントの認証情報である。

IGPの会員番号、IGPアクセストークン、`RC-API(APP)-1-07`で取得する`access_key`との一致確認は、OAuth認証フローに含まれていない。

そのため、次のような状態が発生する。

```text
アプリ:
  IGP会員Aでログイン済み

ブラウザ:
  Tripla会員BのログインCookieが残っている
  またはユーザーがTripla会員Cの認証情報を入力

OAuth:
  Tripla会員BまたはCとして認証成功
```

この場合、アプリ上ではIGP会員Aがログイン中でも、Tripla OAuthセッションは会員BまたはCのものになる。

### 5.2 Silent Sign-inでも同じ問題が起きる

Silent Sign-inは、Tripla側の既存ログインセッションを利用する。

```text
prompt=none
  → Tripla Cookieが有効
  → 既存Triplaアカウントで認証成功
```

ブラウザCookieが別のTriplaアカウントのものであれば、IGP会員と異なるTriplaアカウントでSilent Sign-inが成功する可能性がある。

Cookieを削除すれば既存セッションの再利用は防げるが、ユーザーが別のTriplaアカウントで手動ログインする可能性までは防げない。

### 5.3 OAuth資料にIGP連携情報がない

Tripla OAuth資料に記載されている認可リクエストのパラメータは、次の項目である。

- `hotel_code`
- `client_id`
- `redirect_uri`
- `scope`
- `state`
- `nonce`
- `code_challenge`
- `locale`
- `prompt`

`membership_number`、IGPアクセストークン、`access_key`は記載されていない。

また、`hotel_code`はホテル識別子であり、IGP会員識別子ではない。`organization_id`についても、Tripla上の特定Organization向けログインを指定するパラメータとして説明されており、IGP会員との紐付け用途は確認できない。

## 6. `access_key`との関係

`RC-API(APP)-1-07`で取得する`access_key`は、Tripla OAuthのパラメータではない。

既存仕様上の役割は、Triplaが予約サイト表示時にIGPまたはResort Cloudへ会員情報を照会するためのキーである。

```text
アプリ
  → IGP: RC-API(APP)-1-07
  ← access_key

アプリ
  → access_keyを付けてTripla予約サイトへ遷移

Tripla
  → access_keyを使ってIGP / Resort Cloudへ会員照会
  ← IGP会員情報
```

したがって、`access_key`を次の用途へ流用する根拠はない。

- OAuth認可リクエストの認証情報
- `client_id`の代替
- PKCEの`code_verifier`または`code_challenge`
- Token Exchangeの認証情報
- Tripla OAuthユーザーとIGP会員の連携識別子

## 7. 必要な追加仕様

OAuth方式でIGP会員との紐付けを成立させるには、Triplaまたはバックエンドに次のいずれかが必要である。

### 案1: Tripla側で事前紐付け

```text
Triplaアカウント
  ↔ IGP membership_number
```

OAuth認証後、Tripla APIから紐付け済みIGP会員を取得する。

### 案2: 一時連携セッションを利用

```text
1. アプリでIGP会員Aを認証
2. バックエンドがA専用の一時連携IDを発行
3. Tripla OAuthを実行
4. OAuth callback codeと一時連携IDをバックエンドへ送信
5. バックエンドがTriplaアカウントとIGP会員Aを紐付け
6. 紐付け成功後に予約画面を表示
```

一時連携IDには、IGP会員番号、有効期限、使用回数制限を紐付ける。認可コードやアクセストークンをURLへ付与してはならない。


## 8. 結論

Tripla OAuthの手動ログインは、アプリからTriplaへログインする手段としては利用できる。

しかし、手動ログインしたTriplaアカウントが、現在アプリへログインしているIGP会員と同一であることは、OAuthフローだけでは確認できない。

そのため、次の実装は不可とする。

```text
IGP会員Aでアプリへログイン
  → Tripla OAuth手動ログイン
  → 認証成功したTriplaアカウントを会員Aへ紐付け
```

OAuth方式を採用する場合は、Triplaまたはバックエンドによる正式なアカウント連携処理を追加する必要がある。正式な連携仕様が提供されない場合、既存の`access_key`方式とOAuth方式を置き換えることはできない。

## 9. Triplaへの確認事項

1. OAuth tokenまたはUserInfo APIからTriplaユーザーを一意に特定できるか。
2. TriplaユーザーとIGP会員を紐付けるAPIがあるか。
3. IGP会員をTriplaへ渡す正式なパラメータがあるか。
4. `organization_id`をIGP会員連携に利用できるか。
5. `hotel_code`が会員連携に関係する値か、単なるホテル識別子か。
6. `access_key`に代わるOAuth用の連携キーがあるか。
7. モバイルアプリで`client_secret`を使用せずPKCEでToken Exchangeできるか。
8. WebView利用時のCookie保存・削除・アカウント切り替え仕様は何か。
