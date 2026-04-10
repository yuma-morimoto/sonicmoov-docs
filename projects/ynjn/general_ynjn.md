
- [Slackチャンネル](https://app.slack.com/client/T02DHNZ5N/C09U57PRA8Y)
- **プロジェクトオーナー**: 橋本さん

## 案件概要

集英社様のヤングジャンプ新人賞に関するLINE機能の開発。
漫画家志望者が新人賞に応募・持ち込み予約をLINEで行えるようにする。

- **締切（実装）**: 2026-03-31
- **お客様確認**：2026-04-06
- **リリース予定**: 2026-04-27
- **Backlog**: <https://sonicmoov.backlog.jp/projects/SHUEISYA_SHINJINSYO_INNER>
- **基本設計書**: [Google Sheets](https://docs.google.com/spreadsheets/d/17AjDd-uWtkd7XXWgMM8zwq0I6ilhJtZlBoiLylfXWv8/edit?gid=989749509#gid=989749509)
- **FigmaMake**: <https://www.figma.com/make/w5ZDQSlI33LyyR1zRlPW1O/%E3%80%90%E9%9B%86%E8%8B%B1%E7%A4%BE%E3%80%91%E3%83%A4%E3%83%B3%E3%82%B8%E3%83%A3%E3%83%B3%E6%96%B0%E4%BA%BA%E8%B3%9E-LINE?t=LgPdY03rlDuwv1Q7-1>
- ミニアプリリンク(dev): [https://miniapp.line.me/2009622141-f6ChkpAa](https://miniapp.line.me/2009622141-f6ChkpAa)
- ミニアプリリンク(stg): [https://miniapp.line.me/2009502995-ExBKCM2t](https://miniapp.line.me/2009502995-ExBKCM2t)

### ミニアプリBasic認証

| 項目   | 内容       |
| ---- | -------- |
| ID   | ynjn     |
| PASS | D6dwGkrF |

### 構成

| 種別                | リポジトリ                                                                           |
| ----------------- | ------------------------------------------------------------------------------- |
| ミニアプリ用API（バックエンド） | [comsbi-miniapp-api-ynjn](https://github.com/sonicmoov/comsbi-miniapp-api-ynjn) |
| フロントエンド（LIFF）     | [comsbi-ynjn-frontend](https://github.com/sonicmoov/comsbi-ynjn-frontend)       |
| 管理画面              | [comsbi-admin-ynmg-v2](https://github.com/sonicmoov/comsbi-admin-ynmg-v2)       |


### DB テーブル

| テーブル名 | 概要 |
|-----------|------|
| ynjn_prizes | 漫画賞 |
| ynjn_prize_terms | 漫画賞開催期間 |
| ynjn_applications | 応募情報 |

## 自分のタスク

| チケット                                                                       | 概要                                  | 期間        | 状態  |
| -------------------------------------------------------------------------- | ----------------------------------- | --------- | --- |
| [INNER-29](https://sonicmoov.backlog.jp/view/SHUEISYA_SHINJINSYO_INNER-29) | ミニアプリ用API仕様書 作成（reference/app.yaml） | 3/25〜3/26 | 完了  |
| [INNER-20](https://sonicmoov.backlog.jp/view/SHUEISYA_SHINJINSYO_INNER-20) | フロントLIFF開発                          | 3/18〜3/31 | 着手中 |

## ミニアプリ用API仕様書

### 予約可能枠取得API

- **メソッド**: GET
- **パス**: `/reservations`
- **概要**: 指定した日付の予約可能な時間帯一覧を返す（Googleカレンダーのイベント配列）
- **フェッチ方式**: 日付クリック時にその日付分のみ取得するレイジーフェッチ

#### リクエスト

- クエリパラメータ
    - `date` (string, required): 対象日付 例: `2026-04-01`（YYYY-MM-DD形式、タイムゾーン情報なし）

#### レスポンス（200 OK）

- `date` (string): 日付 例: `2026-04-01`（YYYY-MM-DD形式）
- `events` (array): 予約可能な時間帯（Googleカレンダーイベント）の一覧
    - `eventId` (string): GoogleカレンダーのイベントID
    - `start` (string): 開始時刻（JST） 例: `10:00`（HH:mm形式）
    - `end` (string): 終了時刻（JST） 例: `10:30`（HH:mm形式）

```json
{
  "date": "2026-04-01",
  "events": [
    { "eventId": "abc123", "start": "10:00", "end": "10:30" },
    { "eventId": "def456", "start": "11:00", "end": "11:30" }
  ]
}
```

#### openapi

```app.yaml
  /reservations:
    get:
      summary: 予約可能枠取得API
      tags:
        - reservations
      operationId: get-reservations
      description: |-
        指定した日付の予約可能な時間帯一覧を返す。
        Googleカレンダーのイベントを取得し、その日の空き枠を配列で返す。
      parameters:
        - name: date
          in: query
          required: true
          description: 対象日付（YYYY-MM-DD形式、タイムゾーン情報なし）
          schema:
            type: string
            pattern: '^\d{4}-\d{2}-\d{2}$'
            example: '2026-04-01'
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: object
                properties:
                  date:
                    type: string
                    description: 対象日付（YYYY-MM-DD形式）
                    pattern: '^\d{4}-\d{2}-\d{2}$'
                  events:
                    type: array
                    description: 予約可能な時間帯（Googleカレンダーイベント）の一覧
                    items:
                      type: object
                      properties:
                        eventId:
                          type: string
                          description: GoogleカレンダーのイベントID
                        start:
                          type: string
                          description: 開始時刻（JST、HH:mm形式）
                          pattern: '^\d{2}:\d{2}$'
                        end:
                          type: string
                          description: 終了時刻（JST、HH:mm形式）
                          pattern: '^\d{2}:\d{2}$'
                      required:
                        - eventId
                        - start
                        - end
                required:
                  - date
                  - events
              examples:
                Example:
                  value:
                    date: '2026-04-01'
                    events:
                      - eventId: abc123
                        start: '10:00'
                        end: '10:30'
                      - eventId: def456
                        start: '11:00'
                        end: '11:30'
```