# MCP連携セットアップガイド

このガイドでは、AI経営管理システムで使用する外部ツールとの連携方法を説明します。

---

## 連携予定のツール

1. ✅ **GitHub** - タスク・Issue管理
2. ⏳ **Google Calendar** - スケジュール管理
3. ⏳ **Google Sheets** - データ管理

---

## 1. GitHub連携（gh CLI）

### 前提条件
- GitHub CLIがインストールされていること
- GitHubアカウントを持っていること

### セットアップ手順

#### 1-1. GitHub CLIのインストール確認
```bash
gh --version
```

インストールされていない場合：
```bash
# Linux/macOSの場合
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update
sudo apt install gh
```

#### 1-2. GitHub認証
```bash
gh auth login
```

以下を選択：
- GitHub.com
- HTTPS
- Yes (authenticate Git with GitHub credentials)
- Login with a web browser

#### 1-3. 接続テスト
```bash
# リポジトリ一覧を取得
gh repo list

# Issueを作成してみる
gh issue create --title "テストIssue" --body "MCP連携テスト"
```

---

## 2. Google Calendar連携

### 前提条件
- Googleアカウント
- Google Cloud Platformプロジェクト

### セットアップ手順

#### 2-1. Google Cloud Consoleでプロジェクト作成

1. https://console.cloud.google.com/ にアクセス
2. 新しいプロジェクトを作成（例: "AI-Management-System"）
3. プロジェクトを選択

#### 2-2. Google Calendar APIを有効化

1. 「APIとサービス」→「ライブラリ」に移動
2. "Google Calendar API"を検索
3. 「有効にする」をクリック

#### 2-3. OAuth 2.0クライアントIDを作成

1. 「APIとサービス」→「認証情報」に移動
2. 「認証情報を作成」→「OAuth クライアント ID」を選択
3. アプリケーションの種類：「デスクトップアプリ」
4. 名前を入力（例: "Claude AI Assistant"）
5. 「作成」をクリック
6. **クライアントIDとクライアントシークレットをメモ**

#### 2-4. MCP設定ファイルに追加

`~/.claude/mcp_servers.json` を編集（ファイルがなければ作成）：

```json
{
  "google-calendar": {
    "command": "npx",
    "args": ["-y", "@cocal/google-calendar-mcp"],
    "env": {
      "GOOGLE_CLIENT_ID": "YOUR_CLIENT_ID",
      "GOOGLE_CLIENT_SECRET": "YOUR_CLIENT_SECRET"
    }
  }
}
```

#### 2-5. 接続テスト

Claudeで以下を試す：
```
今日の予定を教えて
```

---

## 3. Google Sheets連携

### 前提条件
- Googleアカウント
- Google Calendar連携で作成したGoogle Cloud Platformプロジェクト

### セットアップ手順

#### 3-1. Google Sheets APIを有効化

1. Google Cloud Console（https://console.cloud.google.com/）にアクセス
2. 先ほど作成したプロジェクトを選択
3. 「APIとサービス」→「ライブラリ」に移動
4. "Google Sheets API"を検索
5. 「有効にする」をクリック

#### 3-2. 認証情報の設定

Google Calendar連携で作成したOAuth 2.0クライアントIDを使用できます。

#### 3-3. MCP設定ファイルに追加

`~/.claude/mcp_servers.json` に追記：

```json
{
  "google-calendar": {
    "command": "npx",
    "args": ["-y", "@cocal/google-calendar-mcp"],
    "env": {
      "GOOGLE_CLIENT_ID": "YOUR_CLIENT_ID",
      "GOOGLE_CLIENT_SECRET": "YOUR_CLIENT_SECRET"
    }
  },
  "google-sheets": {
    "command": "npx",
    "args": ["-y", "@modelcontextprotocol/server-google-sheets"],
    "env": {
      "GOOGLE_CLIENT_ID": "YOUR_CLIENT_ID",
      "GOOGLE_CLIENT_SECRET": "YOUR_CLIENT_SECRET"
    }
  }
}
```

#### 3-4. 接続テスト

Claudeで以下を試す：
```
スプレッドシート「経理管理」の内容を読み取って
```

---

## トラブルシューティング

### GitHub CLIが認証できない
- ブラウザでログインできているか確認
- `gh auth refresh` で再認証

### Google APIの認証エラー
- クライアントIDとシークレットが正しいか確認
- OAuth同意画面の設定を確認
- ブラウザで認証URLにアクセスしてみる

### MCPサーバーが起動しない
- Node.jsがインストールされているか確認（`node --version`）
- MCPサーバーのログを確認（`~/.claude/mcp_logs/`）
- 設定ファイルのJSON構文が正しいか確認

---

## 設定完了後の確認

すべてのツールが正しく連携されたら、CLAUDE.mdの「外部ツール連携」セクションを更新してください。

```markdown
## 外部ツール連携

### 接続済みツール

#### GitHub
- **用途**: タスク・Issue管理、プロジェクト進捗管理
- **連携方法**: gh CLI
- **リポジトリ**: [リポジトリ名]

#### Google Calendar
- **用途**: スケジュール管理、工程表自動生成
- **カレンダーID**: [カレンダーID]
- **注意事項**: プライベート予定は詳細を非表示に設定

#### Google Sheets
- **用途**: 経理データ管理、KPI追跡
- **主要スプレッドシート**:
  - 経理管理: [スプレッドシートURL]
  - プロジェクト一覧: [スプレッドシートURL]
```

---

**最終更新**: 2026-02-13
