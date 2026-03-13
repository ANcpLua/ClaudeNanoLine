# ClaudeNanoLine

5時間枠・7日枠の API 利用量とリセットまでの残り時間を、1行で把握できる Claude Code ステータスラインです。

![demo](demo.png)

## スクリプト

### `claude-nano-line.sh`

Claude Code の [statusLine](https://docs.anthropic.com/ja/docs/claude-code/settings) に設定するコマンドです。

以下の情報をターミナルのステータスバーに表示します:

- **作業ディレクトリ** と **Git ブランチ**
- **使用中のモデル名**
- **コンテキスト使用率** (緑 / 黄 / 赤でカラー表示)
- **API 使用率**: 5 時間枠・7 日枠の utilization % とリセットまでの残り時間

API 使用率は OAuth トークン（macOS はキーチェーン、Windows/Linux は `~/.claude/.credentials.json`）を使って Anthropic API から取得します。360 秒間キャッシュします (`~/.claude/cache/claude-usage-cache.json`)。

## セットアップ

### 自動インストール (推奨)

```sh
curl -fsSL https://raw.githubusercontent.com/HappyOnigiri/ClaudeNanoLine/main/setup.sh | bash
```

`~/.claude/claude-nano-line.sh` のダウンロードと `~/.claude/settings.json` への設定追加を自動で行います。変更前に差分を表示して確認を求めます。

### 手動インストール

1. スクリプトを `~/.claude/` にコピーして実行権限を付与する:

```sh
cp claude-nano-line.sh ~/.claude/
chmod +x ~/.claude/claude-nano-line.sh
```

2. `~/.claude/settings.json` に以下を追加する:

```json
{
  "statusLine": {
    "type": "command",
    "command": "bash ~/.claude/claude-nano-line.sh"
  }
}
```

## 依存関係

- `bash`
- `jq`
- `python3`
- `curl`
- `security` (macOS のみ・キーチェーンアクセス用)

## Windows 対応

Git Bash または WSL 上で動作します。自動インストール・手動インストールともに、Git Bash から実行してください。

- **認証**: Windows では macOS のキーチェーンが使えないため、`~/.claude/.credentials.json` からトークンを取得します。Claude Code でログイン済みであれば、このファイルは自動で作成されます。
- **jq**: [chocolatey](https://chocolatey.org/) で `choco install jq`、または [scoop](https://scoop.sh/) で `scoop install jq` でインストールできます。

## トラブルシューティング

### API 使用率が `[5h] --%` / `[7d] --%` と表示される

- **トークン未取得**: Claude Code で一度ログインしてください。macOS はキーチェーン、Windows/Linux は `~/.claude/.credentials.json` にトークンが保存されます。
- **ネットワーク**: API への接続に失敗している可能性があります。ファイアウォールやプロキシ設定を確認してください。

### `Timeout` と表示される

API リクエストがタイムアウトしました。ネットワーク状況を確認し、数分後に再試行してください（360 秒キャッシュ後に自動で再取得されます）。

### `Usage API Rate Limit` と表示される

API のレート制限に達しました。しばらく待つと自動で復旧します。

### ログの確認

API 呼び出しの詳細は `~/.claude/cache/claude-usage-api.log` に記録されます。問題の切り分けに役立ちます。
