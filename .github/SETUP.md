# GitHub Actions セットアップガイド

## Codex Session Keep-Alive（ChatGPTサブスクリプション）の設定方法

`codex-keepalive.yml` は API キーではなく、Codex CLI の認証キャッシュを使って実行します。

### 前提条件

- ChatGPT Plus/Pro/Team/Edu/Enterprise など Codex が使えるプラン
- ローカルで `codex login` 済みであること
- リポジトリの管理者権限

### GitHub Secrets の設定

1. ローカルで `~/.codex/auth.json` を確認
2. GitHub リポジトリの `Settings` → `Secrets and variables` → `Actions` を開く
3. `New repository secret` で以下を作成
   - **Name**: `CODEX_AUTH_JSON`
   - **Secret**: `~/.codex/auth.json` の中身をそのまま貼り付け

### 注意事項

- `~/.codex/auth.json` はアクセストークンを含むため厳重に扱ってください
- トークン期限切れ時は、再ログイン後に `CODEX_AUTH_JSON` を更新してください

## Claude Session Keep-Alive の設定方法

このワークフローは1時間ごとにClaude APIにダミーメッセージを送信し、セッションのローリングウィンドウを活用します。

### 前提条件

- Anthropic API キー（[Console](https://console.anthropic.com/)から取得）
- リポジトリの管理者権限

### セットアップ手順

#### 1. Anthropic API キーの取得

1. [Anthropic Console](https://console.anthropic.com/)にアクセス
2. API Keysセクションから新しいキーを作成
3. キーをコピー（後で使用）

#### 2. GitHub Secretsの設定

1. GitHubリポジトリページで `Settings` → `Secrets and variables` → `Actions` に移動
2. `New repository secret` をクリック
3. 以下を設定:
   - **Name**: `ANTHROPIC_API_KEY`
   - **Secret**: 手順1でコピーしたAPIキー
4. `Add secret` をクリック

#### 3. ワークフローの有効化

1. このファイルをコミット＆プッシュ
2. GitHub Actions が自動的にワークフローを検出
3. `Actions` タブで `Claude Session Keep-Alive` が表示されることを確認

### 動作確認

#### 手動実行でテスト

1. GitHubリポジトリの `Actions` タブを開く
2. 左側から `Claude Session Keep-Alive` を選択
3. `Run workflow` ボタンをクリック
4. 実行結果を確認

#### 自動実行の確認

- 毎時0分（UTC）に自動実行されます
- 日本時間では毎時9分（JST = UTC+9）
- `Actions` タブで実行履歴を確認可能

### 仕組み

```
時刻    アクション                        効果
────────────────────────────────────────────────────
9:00    GitHub Actions実行 → Session A開始
10:00   GitHub Actions実行 → Session B開始
11:00   GitHub Actions実行 → Session C開始
12:00   あなたの作業開始 → Session D開始    ← この時点でSession A-Cが
14:00   Session A期限切れ → 即座に利用可能     ローリングで利用可能
15:00   Session B期限切れ → 即座に利用可能
16:00   Session C期限切れ → 即座に利用可能
```

### コスト試算

- 1回の実行: 約10トークン（入力+出力）
- 1日24回実行: 約240トークン
- 1ヶ月（30日）: 約7,200トークン
- **推定コスト**: $0.02〜0.05/月（Claude 3.5 Sonnet使用時）

### トラブルシューティング

#### ワークフローが失敗する場合

1. `ANTHROPIC_API_KEY` が正しく設定されているか確認
2. APIキーが有効か確認（Consoleで確認）
3. API利用制限に達していないか確認

#### セッションが維持されない場合

- このワークフローはClaude Code CLIのセッションとは**別のセッション**を作成します
- ローカルでの作業とAPI経由のセッションは**独立**しています
- ただし、5時間ローリングウィンドウは共有されるため、理論上は利用可能なセッション数が増えます

### 注意事項

⚠️ **重要**: このワークフローの効果は理論的なものであり、実際のClaude Code CLIのセッション維持には直接的な影響がない可能性があります。

Claude Code CLIの「Current session」は、ローカルの対話セッションを指すため、GitHub Actionsからの呼び出しでは維持されません。

ただし、API利用のローリングウィンドウを活用することで、トークン制限の影響を緩和できる可能性があります。
