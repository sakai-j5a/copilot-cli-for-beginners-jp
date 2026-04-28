---
theme: sky
highlightTheme: monokai
center: true
width: 1920
height: 1080
transition: slide
transitionSpeed: default
hash: true
---

# Chapter 06: MCPサーバー

**CopilotがGitHubのIssueを読み、ターミナルからPRを作成できたら？**

MCP（Model Context Protocol）でCopilotを外部サービスに接続しましょう

> ⏱️ 所要時間の目安: 約50分（15分読む + 35分ハンズオン）

---

## 🎯 学習目標

この章を終えると：

- MCPとは何か、なぜ重要なのかを理解できる
- `/mcp`コマンドでMCPサーバーを管理できる
- GitHub、ファイルシステム、ドキュメント用のMCPサーバーを設定できる
- MCPサーバーを使ったワークフローを実践できる

---

## 🧩 アナロジー: ブラウザ拡張機能

<img src="images/browser-extensions-analogy.png" alt="Browser Extensions" width="60%"/>

--

| ブラウザ拡張機能 | 接続先 | MCPの対応物 |
|----------------|--------|-----------|
| パスワードマネージャー | パスワード庫 | **GitHub MCP** → リポジトリ、Issue、PR |
| Grammarly | 文章解析 | **Context7 MCP** → ライブラリドキュメント |
| ファイルマネージャー | クラウドストレージ | **Filesystem MCP** → ローカルファイル |

> 💡 MCPなしでは`@`で共有したファイルのみ。MCPがあれば自動で探索・取得

---

## クイックスタート: 30秒でMCP体験

GitHub MCPサーバーは**デフォルトで組み込み**。すぐ試せます：

```bash
copilot
> このリポジトリの最近のコミットを一覧表示して
```

実際のコミットデータが返ったら、MCPを体験済み！

--

## /mcp show

```bash
copilot

> /mcp show

MCP Servers:
✓ github (enabled) - GitHub integration
✓ filesystem (enabled) - File system access
```

![MCP Status Demo](images/mcp-status-demo.gif)

> 💡 GitHubサーバーしか表示されなくても大丈夫。次のセクションで追加します

---

## MCPが変えること

### MCPなし

```bash
> GitHub Issue #42の内容は何ですか？

"CopilotはGitHubにアクセスできません。
 Issueの内容をコピペして入力してください。"
```

### MCPあり

```bash
> このリポジトリのGitHub Issue #42の内容は？

Issue #42: 特殊文字でログインが失敗する
状態: オープン
ラベル: bug, priority-high
説明: パスワードに特殊文字を含むユーザーが...
```

---

## MCP設定ファイル

MCPサーバーは設定ファイルで管理します

| ファイル | スコープ |
|---------|--------|
| `~/.copilot/mcp-config.json` | ユーザーレベル（全プロジェクト） |
| `.mcp.json`（プロジェクトルート） | プロジェクト固有 |

```json
{
  "mcpServers": {
    "server-name": {
      "type": "local",
      "command": "npx",
      "args": ["@package/server-name"],
      "tools": ["*"]
    }
  }
}
```

--

## レジストリからインストール

JSON編集不要のガイド付きセットアップ：

```bash
copilot

> /mcp search
```

Copilotが利用可能なサーバーのピッカーを開き、設定を自動追加

---

## Filesystemサーバー

Copilotにプロジェクトファイルを閲覧させる：

```json
{
  "mcpServers": {
    "filesystem": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "."],
      "tools": ["*"]
    }
  }
}
```

> 💡 `.`は「カレントディレクトリ」。Copilot起動位置からの相対パスでアクセス

--

## Context7サーバー

最新のライブラリドキュメントを取得：

```json
{
  "mcpServers": {
    "context7": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"],
      "tools": ["*"]
    }
  }
}
```

- ✅ APIキー不要
- ✅ アカウント不要
- ✅ コードはローカルに留まる

---

## 応用: リモートMCPサーバー

### Microsoft Learn MCPサーバー

ローカルインストール不要のリモートサーバー：

```bash
copilot
> /plugin install microsoftdocs/mcp
```

```bash
copilot
> PythonアプリをAzure App Serviceにデプロイする
  推奨方法は？ Microsoft Learnを検索して
```

--

## Web Access (web_fetch)

ビルトインの`web_fetch`ツールで任意のURLを取得：

```bash
copilot

> https://docs.github.com/copilot のREADMEを要約して
```

`~/.copilot/config.json`でアクセス可能URLを制御：

```json
{
  "permissions": {
    "allowedUrls": [
      "https://api.github.com/**",
      "https://docs.github.com/**"
    ]
  }
}
```

---

## MCPワークフロー実践

![MCP Workflow Demo](images/mcp-workflow-demo.gif)

```bash
copilot

# GitHub MCP: Issueを確認
> Issue #1の詳細を教えて

# Filesystem MCP: 関連コードを探索
> このリポジトリでfind_by_authorが定義されているファイルは？

# Context7 MCP: ドキュメントを確認
> Pythonのdataclassesの最新ドキュメントを取得
```

---

## ▶️ 実践

<img src="../images/practice.png" alt="Practice" width="60%"/>

--

## 実践1: 組み込みGitHub MCPを試す

```bash
copilot

> このリポジトリの最近のコミット5件を一覧表示して
> オープンIssueの一覧を表示して
```

--

## 実践2: MCPサーバーを追加

1. `~/.copilot/mcp-config.json`を作成（または編集）
2. Filesystemサーバーの設定を追加
3. Copilotを再起動して`/mcp show`で確認

--

## 実践3: MCPワークフロー

```bash
copilot

# Issueを確認してコードを分析
> "good first issue"ラベルのIssueを一覧表示
> 最もシンプルなIssueの修正計画を立てて
```

---

## 📝 課題

1. `/mcp show`で設定済みサーバーを確認
2. FilesystemサーバーまたはContext7を追加設定
3. GitHub MCPでIssueを読み、関連コードを`@`で分析
4. MCPなし vs MCPありのワークフローの違いを体感

--

## チャレンジ

- **フルワークフロー**: GitHub MCP → Issue確認 → コード分析 → 修正 → PR作成を1セッションで完了

- **ドキュメント活用**: Context7 MCPを追加して、最新のpytestドキュメントを参照しながらテストを生成
