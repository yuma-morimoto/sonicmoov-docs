
無作為に3つのシナリオにバグを仕込む

```gherkin
Feature: TODOアプリの利用者シナリオ
  利用者がワークスペースを管理し、TODOの登録・確認・更新・完了・削除まで、
  アプリ上でそれぞれの目的を達成できること

  Viewports:
  @pc = 1280x800

  Background:
    baseUrl: "https://smv_frontend:Ya3Q?g44@main.d4seoanxhgzxx.amplifyapp.com/workspaces/"

  Login:
    email: frontend@sonicmoov.com
    password: $S0nicm00v

  # ── ログイン ────────────────────────────────────────────────

  @pc
  Scenario: アカウントにログインできる
    Given "/login" を開いている
    When メールアドレスを入力する
    And パスワードを入力する
    And 「ログインする」を選ぶ
    Then ログイン後の画面が表示される

  @pc
  Scenario: 不正な認証情報ではログインできない
    Given "/login" を開いている
    When 誤ったメールアドレスまたはパスワードを入力する
    And 「ログインする」を選ぶ
    Then ログインに失敗したことが表示される
    And ログイン画面が表示され続ける

  # ── ワークスペース管理 ─────────────────────────────────────

  @pc
  Scenario: ワークスペース一覧を閲覧できる
    Given ログインしている
    When "/workspaces/" を開いている
    And 数秒待機する
    Then 「ワークスペース一覧」が表示される
    And ワークスペースの名前・説明・作成日・更新日を確認できる

  @pc
  Scenario: ワークスペース名から目的のワークスペースを検索できる
    Given ログインしている
    And "/workspaces/" を開いている
    When ワークスペース名を入力する
    And 「検索」を選ぶ
    Then 入力した名前に一致するワークスペースが表示される

  @pc
  Scenario: ワークスペースを新規作成できる
    Given ログインしている
    And "/workspaces/" を開いている
    When 「新規作成」を選ぶ
    And ワークスペース名を入力する
    And ワークスペースの説明を入力する
    And 「作成」を選ぶ
    Then ワークスペース一覧が表示される
    And 作成したワークスペースが一覧に表示される

  @pc
  Scenario: 必須項目未入力ではワークスペースを作成できない
    Given ログインしている
    And "/workspaces/new" を開いている
    When ワークスペース名を入力せずに「作成」を選ぶ
    Then ワークスペース名が必須であることが表示される
    And ワークスペースは作成されない

  @pc
  Scenario: ワークスペース作成をキャンセルできる
    Given ログインしている
    And "/workspaces/new" を開いている
    When 「キャンセル」を選ぶ
    Then ワークスペース一覧が表示される

  @pc
  Scenario: ワークスペースのTODO一覧を閲覧できる
    Given ログインしている
    And "/workspaces/" を開いている
    When 利用したいワークスペースを選ぶ
    Then 選んだワークスペースのTODO一覧が表示される

  # ── TODO管理 ────────────────────────────────────────────────

  @pc
  Scenario: TODOを新規登録できる
    Given ログインしている
    And TODO一覧を表示している
    When TODOの新規作成を選ぶ
    And TODOのタイトルを入力する
    And TODOの内容を入力する
    And 保存する
    Then TODO一覧が表示される
    And 登録したTODOが一覧に表示される

  @pc
  Scenario: TODOの詳細を閲覧できる
    Given ログインしている
    And TODO一覧を表示している
    When 確認したいTODOを選ぶ
    Then 選んだTODOのタイトルと内容が表示される

  @pc
  Scenario: TODOを編集できる
    Given ログインしている
    And TODO一覧を表示している
    When 編集したいTODOを選ぶ
    And TODOのタイトルまたは内容を書き換える
    And 保存する
    Then 更新後のTODO内容が表示される

  @pc
  Scenario: TODOを完了状態に変更できる
    Given ログインしている
    And 未完了のTODO一覧を表示している
    When 対象のTODOを完了に変更する
    Then 対象のTODOが完了状態で表示される

  @pc
  Scenario: 完了したTODOを未完了に戻せる
    Given ログインしている
    And 完了済みのTODO一覧を表示している
    When 対象のTODOを未完了に戻す
    Then 対象のTODOが未完了状態で表示される

  # ── セッション管理 ─────────────────────────────────────────

  @pc
  Scenario: ログアウトできる
    Given ログインしている
    When 「ログアウト」を選ぶ
    Then ログイン画面が表示される
    And 認証が必要な画面を閲覧できない
```
