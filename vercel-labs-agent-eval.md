
- [vercel-labs/agent-eval](https://github.com/vercel-labs/agent-eval)
- [Vercel Sandbox](https://vercel.com/sandbox)

## agent-eval

- `EVAL.ts`
- `PROMPT.md`

```
npx @vercel/agent-eval init eval-project
cd eval-project npm install cp .env.example .env
```


```
eval-project/
├── .env.example
├── experiments/     # 実験設定
│   └── cc.ts        # Claude Code用のデフォルト設定
├── evals/           # 評価セット
│   └── add-greeting/
│       ├── PROMPT.md    # エージェントへの指示
│       ├── EVAL.ts      # vitestによる検証
│       └── src/App.tsx  # 編集対象のコード
└── results/         # 実行結果（自動生成）
```

### 機能単位の評価
#### 1. 失敗原因が特定しやすい
- アプリ全体 eval は「何かが失敗した」しかわからず、どの機能が原因かの切り分けに時間がかかります。
- 機能単位なら失敗した eval = 失敗した能力が即わかります。
#### 2. 並列実行の恩恵を受ける
- agent-eval は eval を並列実行します。
- 機能単位で細かく分割するほど並列度が上がり、全体の実行時間が短縮されます。
#### 3. パス率に意味をたせる
- 「アプリ全体が完成する確率 30%」は情報量が少いです。
- 「ソート機能 80% / エラーハンドリング 40%」なら「エラー処理の指示を改善すべき」という具体的なアクションに繋がります。
## Vercel Sandbox

|        | Vercel Sandbox      | Docker    |
| ------ | ------------------- | --------- |
| 分離レベル  | Firecracker MicroVM | Container |
| セキュリティ | ハードウェアレベル           | プロセスレベル   |


### サンドボックスの分離（eval実行ごとに独立した環境）
- Vercel Sandbox: eval 1実行ごとに独立したVM（VercelSandbox.create() で毎回新規作成）
- Docker: eval 1実行ごとに独立したコンテナ（AutoRemove: true で実行後自動削除）
****
 eval-1 実行 → Dockerコンテナ A (自動削除)
 eval-2 実行 → Dockerコンテナ B (自動削除)
 eval-3 実行 → Dockerコンテナ C (自動削除)

## References
- [@vercel/agent-evalでCLAUDE.mdの効果を検証する](https://zenn.dev/edash_tech_blog/articles/84f0cd8567646b)