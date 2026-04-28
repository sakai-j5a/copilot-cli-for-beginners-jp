![Chapter 06: MCP Servers](images/chapter-header.png)

> **CopilotがGitHubのIssueを読み、データベースを確認し、ターミナルからPRを作成できたらどうでしょう？**

これまで、Copilotは`@`で直接共有したファイル、会話履歴、訓練データだけを利用できました。しかし、自分でGitHubリポジトリを確認したり、プロジェクトのファイルを閲覧したり、ライブラリの最新ドキュメントを調べたりできたらどうでしょう？

それがMCP（Model Context Protocol）の役割です。Copilotを外部サービスに接続し、リアルなデータにアクセスさせる仕組みです。Copilotが接続する各サービスを「MCPサーバー」と呼びます。この章では、いくつかの接続を設定し、Copilotがいかに強力になるかを体験します。

> 💡 **MCPに既に粿熟している方？** [クイックスタート](#-use-the-built-in-github-mcp)に飛んで動作を確認し、サーバーの設定を開始してください。

## 🎯 学習目標

この章を終えると、以下のことができるようになります：

- MCPとは何か、なぜ重要なのかを理解する
- `/mcp`コマンドでMCPサーバーを管理する
- GitHub、ファイルシステム、ドキュメント用のMCPサーバーを設定する
- MCPサーバーを使ったワークフローを書籍アププロジェクトで実践する
- カスタムMCPサーバーの構築方法を知る（オプション）

> ⏱️ **所要時間の目安**: 約50分（15分読む + 35分ハンズオン）

---

## 🧩 現実世界のアナロジー: ブラウザ拡張機能

<img src="images/browser-extensions-analogy.png" alt="MCP Servers are like Browser Extensions" width="800"/>

MCPサーバーはブラウザ拡張機能のようなものです。ブラウザ単体でもウェブページを表示できますが、拡張機能によって追加サービスへの接続が可能になります:

| ブラウザ拡張機能 | 接続先 | MCPの対応物 |
|-------------------|---------------------|----------------|
| パスワードマネージャー | パスワード履 | **GitHub MCP** → リポジトリ、Issue、PR |
| Grammarly | 文章解析サービス | **Context7 MCP** → ライブラリドキュメント |
| ファイルマネージャー | クラウドストレージ | **Filesystem MCP** → ローカルプロジェクトファイル |

拡張機能なしでもブラウザは十分便利ですが、導入すると崷崾的なツールになります。MCPサーバーはCopilotに対して同様の役割を果たします。GitHub Issueの読み取り、ファイルシステムの辺り、最新ドキュメントの取得など、リアルなデータソースに接続してもらえます。

***MCPサーバーはCopilotを外部の世界—GitHub、リポジトリ、ドキュメントなど—に接続します***

> 💡 **重要なポイント**: MCPなしでは、Copilotは`@`で明示的に共有したファイルしか参照できません。MCPがあれば、プロジェクトを自己で探索したり、GitHubリポジトリを確認したり、ドキュメントを検索したりを自動で行えます。

---

<img src="images/quick-start-mcp.png" alt="Power cable connecting with bright electrical spark surrounded by floating tech icons representing MCP server connections" width="800"/>

# クイックスタート: 30秒でMCPを体験

## 組み込みGitHub MCPサーバーを使ってみよう
設定は後に回して、まず実際にMCPを体験してみましょう。
GitHub MCPサーバーはデフォルトで包含されています。以下を試してみてください:

```bash
copilot
> このリポジトリの最近のコミットを一覧表示して
```

Copilotが実際のコミットデータを返したら、MCPを実際に体験したことになります。GitHub MCPサーバーが代わりにGitHubにアクセスしています。しかしGitHubは*一つ*のサーバーに過ぎません。この章では追加のサーバー（ファイルシステムアクセス、最新ドキュメントなど）の追加方法を示します。

---

## `/mcp show`コマンド

`/mcp show`で設定済みMCPサーバーとその有効化状態を確認できます:

```bash
copilot

> /mcp show

MCP Servers:
✓ github (enabled) - GitHub integration
✓ filesystem (enabled) - File system access
```

> 💡 **GitHubサーバーしか表示されない？** 実はそれで大丈夫です！追加のMCPサーバーをまだ追加していなければ、GitHubのみが表示されます。次のセクションで追加します。

> 📚 **全MCP管理コマンドを見たい？** チャット内の`/mcp`スラッシュコマンドまたはターミナルから`copilot mcp`で管理できます。詳細はこの章末尾の[全コマンドリファレンス](#-additional-mcp-commands)を参照してください。

<details>
<summary>🎬 動作を確認！</summary>

![MCP Status Demo](images/mcp-status-demo.gif)

*デモの出力は異なります。実際のモデル、ツール、回答はここで示したものと異なる場合があります。*

</details>

---

## MCPが何を変えるのか？

MCPの実際の違いを見てみましょう：

**MCPなし:**
```bash
> GitHub Issue #42の内容は何ですか？

"コピペはGitHubにアクセスできません。Issueの内容をコピペして入力してください。"
```

**MCPあり:**
```bash
> このリポジトリのGitHub Issue #42の内容は何ですか？

Issue #42: 特殊文字でログインが失敗する
状態: オープン
ラベル: bug, priority-high
説明: パスワードに特殊文字を含むユーザーが...
```

MCPはCopilotを実際の開発環境に気付いた存在にします。

> 📚 **公式ドキュメント**: [About MCP](https://docs.github.com/copilot/concepts/context/mcp)でMCPとGitHub Copilotの連携について詳しく学べます。

---

# MCPサーバーの設定

<img src="images/configuring-mcp-servers.png" alt="Hands adjusting knobs and sliders on a professional audio mixing board representing MCP server configuration" width="800"/>

MCPの動作を確認したので、追加サーバーを設定しましょう。サーバーを追加する方法は2つあります: **組み込みレジストリから**（最簡単 — CLI内でガイド付きセットアップ）、または**設定ファイルを直接編集**（より高い柔軟性）。レジストリオプションが分からなければ最初こちらから始めてもいいでしょう。

---

## レジストリからMCPサーバーをインストールする

CLIには組み込みMCPサーバーレジストリがあり、ガイド付きセットアップで人気のサーバーを发見・インストールできます。JSON編集は不要です。

```bash
copilot

> /mcp search
```

Copilotが利用可能なサーバーのインタラクティブなピッカーを開きます。選択すると必要な設定（APIキー、パスなど）のガイドが表示され、設定ファイルへの自動追加のまでサポートされます。

> 💡 **レジストリを使う理由？** npmパッケージ名、コマンド引数、JSON構造を知らなくても始められる最も簡単な方法です。CLIが全て処理します。

---

## MCP設定ファイル

MCPサーバーは`~/.copilot/mcp-config.json`（ユーザーレベル、全プロジェクトに適用）または`.mcp.json`（プロジェクトルートに配置）で設定します。`/mcp search`を使用した場合は、CLIが既に作成または更新していますが、カスタマイズのためにフォーマットを理解すると便利です。

> ⚠️ **注記**: `.vscode/mcp.json`はMCP設定ソースとしてサポートされなくなりました。既存の`.vscode/mcp.json`があれば、プロジェクトルートの`.mcp.json`に移行してください。古い設定ファイルが検出された場合はCLIが移行ヒントを表示します。

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

*Most MCP servers are distributed as npm packages and run via the `npx` command.*

<details>
<summary>💡 <strong>New to JSON?</strong> Click here to learn what each field means</summary>

| Field | What It Means |
|-------|---------------|
| `"mcpServers"` | Container for all your MCP server configurations |
| `"server-name"` | A name you choose (e.g., "github", "filesystem") |
| `"type": "local"` | The server runs on your machine |
| `"command": "npx"` | The program to run (npx runs npm packages) |
| `"args": [...]` | Arguments passed to the command |
| `"tools": ["*"]` | Allow all tools from this server |

**JSONの重要なルール:**
- 文字列にはダブルクォート`"`を使用（シングルクォート不可）
- 最後の項目の後にトレイリングカンマ不可
- 有効のJSON形式であること（不明な場合は[JSONバリデーター](https://jsonlint.com/)を使用）

</details>

---

## MCPサーバーの追加

GitHub MCPサーバーは組み込みで設定不要です。以下に追加できるサーバーを列振します。**兴味のあるものから始めるか、順番に処理してください。**

| やりたいこと | ジャンプ先 |
|---|---|
| Copilotにプロジェクトファイルを閲覧させたい | [Filesystemサーバー](#filesystem-server) |
| 最新のライブラリドキュメントを取得したい | [Context7サーバー](#context7-server-documentation) |
| オプションの追加機能（カスタムサーバー、web_fetch）を試したい | [応用編](#beyond-the-basics) |

<details>
<summary><strong>Filesystemサーバー</strong> - Copilotにプロジェクトファイルを閲覧させる</summary>
<a id="filesystem-server"></a>

### Filesystemサーバー

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

> 💡 **`.`パスについて**: `.`は「カレントディレクトリ」を意味します。Copilotは起動位置からの相対パスでファイルにアクセスできます。Codespaceではワークスペースルートになります。希望すれば`/workspaces/copilot-cli-for-beginners`のように絶対パスを指定することもできます。

`~/.copilot/mcp-config.json`に追加してCopilotを再起動してください。

</details>

<details>
<summary><strong>Context7サーバー</strong> - 最新のライブラリドキュメントを取得</summary>
<a id="context7-server-documentation"></a>

### Context7 Server (ドキュメンテーション)

Context7はCopilotに人気のフレームワークやライブラリの最新ドキュメントへのアクセスを提供します。孤行の学習データに頃るのではなく、実際の最新ドキュメントを取得します。

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

- ✅ **APIキー不要** 
- ✅ **アカウント不要** 
- ✅ **コードはローカルに留まる**

`~/.copilot/mcp-config.json`に追加してCopilotを再起動してください。

</details>

<details>
<summary><strong>応用編</strong> - カスタムサーバーとWebアクセス（オプション）</summary>
<a id="beyond-the-basics"></a>

コアサーバーに慣れてきたら試してみるオプションの追加機能です。

### Microsoft Learn MCPサーバー

Filesystem、Context7などこれまで見たサーバーはまた自分のマシン上で実行しています。ただし、MCPサーバーはリモートで実行することもでき、URLを指定するだけで行えます。`npx`やPython、ローカルプロセス、依存関係のインストールは不要です。

[Microsoft Learn MCPサーバー](https://github.com/microsoftdocs/mcp)はその良い例です。Copilot CLIがAzure、Microsoft Foundry、AI関連トピック、.NET、Microsoft 365などMicrosoft公式ドキュメントに直接アクセスでき、モデルの学習データに頑ることなく、最新の情報を得られます。

- ✅ **APIキー不要** 
- ✅ **アカウント不要** 
- ✅ **ローカルインストール不要**

**`/plugin install`でクイックインストール:**

JSONファイルを手動変更するかわりに、コマンド一つでインストールできます：

```bash
copilot

> /plugin install microsoftdocs/mcp
```

サーバーと関連するAgent Skillsが自動一括インストールされます。インストールされるSkills:

- **microsoft-docs**: 概念、チュートリアル、事实確認
- **microsoft-code-reference**: API尋ね、コードサンプル、トラブルシューティング
- **microsoft-skill-creator**: Microsoft技術のカスタムSkill生成用のメタスキル

**使い方:**
```bash
copilot

> PythonアプリをAzure App Serviceにデプロイする推奨方法は何ですか？ Microsoft Learnを検索してください。
```

📚 Learn more: [Microsoft Learn MCP Server overview](https://learn.microsoft.com/training/support/mcp-get-started)

### Web Access（`web_fetch`）

Copilot CLIには任意のURLのコンテンツを取得できるビルトインの`web_fetch`ツールがあります。ターミナルを離れずにREADME、APIドキュメント、リリースノートを取り込むのに便利です。MCPServer不要。

`~/.copilot/config.json`（一般設定）でアクセス可能なURLを制御できます。これは`~/.copilot/mcp-config.json`（MCPServer定義）とは別です。

```json
{
  "permissions": {
    "allowedUrls": [
      "https://api.github.com/**",
      "https://docs.github.com/**",
      "https://*.npmjs.org/**"
    ],
    "blockedUrls": [
      "http://**"
    ]
  }
}
```

**使い方:**
```bash
copilot

> Fetch and summarize the README from https://github.com/facebook/react
```

### カスタムMCPサーバーの構築

自分のAPI、データベース、社内ツールにCopilotを接続したい場合、PythonでカスタムMCPサーバーを構築できます。既製サーバー（GitHub、Filesystem、Context7）でほとんどのユースケースに対応できるため、完全にオプションです。

📖 書籍アプリを例に使った完全な手順は[Custom MCPサーバーガイド](mcp-custom-server.md)を参照してください。

📚 背景知識には[MCP for Beginnersコース](https://github.com/microsoft/mcp-for-beginners)も参考になります。

</details>

<a id="complete-configuration-file"></a>

### 完全な設定ファイル

FilesystemとContext7サーバーを含む完全な`mcp-config.json`の例：

> 💡 **注記:** GitHub MCPは組み込みです。設定ファイルに追加する必要はありません。

```json
{
  "mcpServers": {
    "filesystem": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "."],
      "tools": ["*"]
    },
    "context7": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"],
      "tools": ["*"]
    }
  }
}
```

グローバルアクセスには`~/.copilot/mcp-config.json`、プロジェクト固有設定にはプロジェクトルートの`.mcp.json`として保存してください。

---

# MCPサーバーの使い方

MCPサーバーを設定したので、何ができるのか見てみましょう。

<img src="images/using-mcp-servers.png" alt="Using MCP Servers - Hub-and-spoke diagram showing a Developer CLI connected to GitHub, Filesystem, Context7, and Custom/Web Fetch servers" width="800" />

---

## サーバーの使用例

**試したいサーバーから始めるか、順番に処理してください。**

| 試したいこと | ジャンプ先 |
|---|---|
| GitHubリポジトリ、Issue、PR | [GitHubサーバー](#github-server-built-in) |
| プロジェクトファイルの閲覧 | [Filesystemサーバーの使い方](#filesystem-server-usage) |
| ライブラリドキュメントの検索 | [Context7サーバーの使い方](#context7-server-usage) |
| カスタムサーバー、Microsoft Learn MCP、web_fetch | [応用編の使い方](#beyond-the-basics-usage) |

<details>
<summary><strong>GitHubサーバー（組み込み）</strong> - リポジトリ、Issue、PRなどにアクセス</summary>
<a id="github-server-built-in"></a>

### GitHubサーバー（組み込み）

GitHub MCPサーバーは**組み込み**です。初期設定時にCopilotにログインしたなら、既に機能しています。設定不要です！

> 💡 **動作しない？** `/login`でGitHub再認証してください。

<details>
<summary><strong>Devコンテナーでの認証</strong></summary>

- **GitHub Codespaces**（推奨）: 認証は自動です。`gh` CLIがCodespaceトークンを引き継ぎます。操作不要です。
- **ローカル開発コンテナー（Docker）**: コンテナー起動後に`gh auth login`を実行し、Copilotを再起動してください。

**認証のトラブルシューティング:**
```bash
# 認証状態を確認
gh auth status

# 未認証の場合はログイン
gh auth login

# GitHub MCPの接続を確認
copilot
> /mcp show
```

</details>

| 機能 | 例 |
|---------|------|
| **リポジトリ情報** | コミット、ブランチ、コントリビューターの表示 |
| **Issues** | Issueの一覧表示、作成、検索、コメント |
| **Pull requests** | PRの表示、差分、作成、ステータス確認 |
| **コード検索** | リポジトリ内のコード検索 |
| **Actions** | ワークフロー実行状態の確認 |

```bash
copilot

# このリポジトリの最近のアクティビティを表示
> このリポジトリの最新のコミットをよ5件一覧表示してください

最新のコミット:
1. abc1234 - 第05章のスキル例の更新 (２日前)
2. def5678 - 書籍アプリのテストフィクスチャを追加 (３日前)
3. ghi9012 - 第03章READMEの誤字修正 (4日前)
...

# リポジトリ構造を探索
> このリポジトリにどのブランチが存在するか教えてください？

ブランチ:
- main (デフォルト)
- chapter6 (現在)

# リポジトリ全体でコードパターンを検索
> pytestをインポートしているファイルをこのリポジトリ内で検索してください

1件のファイルを検出:
- samples/book-app-project/tests/test_books.py
```

> 💡 **フォークで作業中ですか？** コースリポジトリをフォークした場傈、IssueやPRの作成など書き込み操作も試せます。以下の演習で練習しましょう。

> ⚠️ **結果が表示されない？** GitHub MCPはリポジトリのリモート（github.com上）に対して操作します。リモートが存在することを`git remote -v`で確認してください。

</details>

<details>
<summary><strong>Filesystemサーバー</strong> - プロジェクトファイルの閲覧と分析</summary>
<a id="filesystem-server-usage"></a>

### Filesystemサーバー

設定後、filesystem MCPはCopilotが自動的に使用できるツールを提供します:

```bash
copilot

> book-app-projectディレクトリ内のPythonファイルは何件ありますか？

samples/book-app-project/内にPythonファイルが3件あります:
- book_app.py
- books.py
- utils.py

> data.jsonファイルの合計サイズは？

samples/book-app-project/data.json: 2.4 KB

> 書籍アプリ内で型ヒントのない関数を全て見つけてください

型ヒントのない関数が2件あります:
- samples/book-app-project/utils.py:10 - get_user_choice()
- samples/book-app-project/utils.py:14 - get_book_details()
```

</details>

<details>
<summary><strong>Context7サーバー</strong> - ライブラリドキュメントの検索</summary>
<a id="context7-server-usage"></a>

### Context7サーバー

```bash
copilot

> pytest fixtureのベストプラクティスは何ですか？

pytestドキュメントより:

fixture — テストの固定ベースを提供するためにfixtureを使用する:

    import pytest

    @pytest.fixture
    def sample_books():
        return [
            {"title": "1984", "author": "George Orwell", "year": 1949},
            {"title": "Dune", "author": "Frank Herbert", "year": 1965},
        ]

    def test_find_by_author(sample_books):
        # fixtureは引数として自動で渡される
        results = [b for b in sample_books if "Orwell" in b["author"]]
        assert len(results) == 1

ベストプラクティス:
- setup/teardownメソッドの代わりにfixtureを使用
- 一時ファイルにはtmp_path fixtureを使用
- 環境の変更にはmonkeypatchを使用
- fixtureのスコープを適切に設定（function, class, module, session）

> これを書籍アプリのテストファイルに適用するにはどうすればいいですか？

# Copilotが公式pytestパターンを知り
# samples/book-app-project/tests/test_books.pyに適用できる
```

</details>

<details>
<summary><strong>Beyond the Basics</strong> - Custom server and web_fetch usage</summary>
<a id="beyond-the-basics-usage"></a>

### 応用編

**カスタムMCPServer**: [カスタムMCPServerガイド](mcp-custom-server.md)で書籍ルックアップサーバーを作成した場合、書籍コレクションを直接クエリできます:

```bash
copilot

> book lookup server\u3092\u4f7f\u3063\u3066\u300c1984\u300d\u306e\u60c5\u5831\u3092\u8abf\u3079\u3066\u304f\u3060\u3055\u3044\u3002George Orwell\u306e\u66f8\u7c4d\u3092\u691c\u7d22\u3057\u3066\u304f\u3060\u3055\u3044
```

**Microsoft Learn MCP**: [Microsoft Learn MCPServer](#microsoft-learn-mcp-server)\u3092\u30a4\u30f3\u30b9\u30c8\u30fc\u30eb\u3057\u305f\u5834\u5408\u3001Microsoft\u516c\u5f0f\u30c9\u30ad\u30e5\u30e1\u30f3\u30c8\u3092\u76f4\u63a5\u53c2\u7167\u3067\u304d\u307e\u3059:

```bash
copilot

> Azure Function\u306e\u30de\u30cd\u30fc\u30b8\u30c9ID\u3092\u69cb\u6210\u3059\u308b\u65b9\u6cd5\u3092\u6559\u3048\u3066\u304f\u3060\u3055\u3044\u3002Microsoft Learn\u3092\u691c\u7d22\u3057\u3066\u304f\u3060\u3055\u3044\u3002
```

**web_fetch**: \u30d3\u30eb\u30c8\u30a4\u30f3\u306e`web_fetch`\u30c4\u30fc\u30eb\u3067\u4efb\u610fURL\u304b\u3089\u30b3\u30f3\u30c6\u30f3\u30c4\u3092\u53d6\u308a\u8fbc\u3080:

```bash
copilot

> https://github.com/facebook/react \u306eREADME\u3092\u53d6\u5f97\u3057\u3066\u8981\u7d04\u3057\u3066\u304f\u3060\u3055\u3044
```

</details>

---

## マルチサーバーワークフロー

このワークフローは、開発者が「これなしではもう仕事できない」と言う理由を示しています。各例は一インスタンス内で複数のMCPサーバーを組み合わせます。

<img src="images/issue-to-pr-workflow.png" alt="Issue to PR Workflow using MCP - Shows the complete flow from getting a GitHub issue through creating a pull request" width="800"/>

*MCPワークフローの全体像: GitHub MCPがリポジトリデータを取得し、Filesystem MCPがコードを見つけ、Context7 MCPがベストプラクティスを提供し、Copilotが分析する*

各例は山海で完結しています。**興味のあるものから始めるか、全部読んでください。**

| 見たい内容 | ジャンプ先 |
|---|---|
| 複数サーバーの連携 | [マルチサーバー探索](#multi-server-exploration) |
| IssueからPRまで一セッションで | [Issue-to-PRワークフロー](#issue-to-pr-workflow) |
| プロジェクトヘルスチェック | [ヘルスダッシュボード](#health-dashboard) |

<details>
<summary><strong>マルチサーバー探索</strong> - Filesystem、GitHub、Context7を一セッションで使う</summary>
<a id="multi-server-exploration"></a>

#### 複数のMCPサーバーで書籍アプリを探索する

```bash
copilot

# ステップ1: filesystem MCPで書籍アプリを探索
> samples/book-app-project/内の全Pythonファイルを一覧表示して、各ファイルが何をするか要約してください

Pythonファイルが3件見つかりました:
- book_app.py: コマンドルーティングがCLIエントリポイント（list, add, remove, find）
- books.py: JSONでデータを永続化するBookCollectionクラス
- utils.py: ユーザー入力と表示のヘルパー関数

# ステップ2: GitHub MCPで最近の変更を確認
> samples/book-app-project/内のファイルを触った最新コミット3件を教えてください

書籍アプリに関わる最新コミット:
1. abc1234 - BookCollectionのテストフィクスチャを追加 (２日前)
2. def5678 - find_by_authorメソッドを追加 (５日前)
3. ghi9012 - 書籍アプリの初期セットアップ (１週前)

# ステップ3: Context7 MCPでベストプラクティスを取得
> JSONデータを永続化するためのPythonベストプラクティスは何ですか？

Pythonドキュメントより:
- ファイルI/Oにはコンテキストマネージャ（with文）を使用
- 滅したファイルにJSONDecodeErrorを処理
- 構造化データにはdataclassを使用
- データ破損防止のためにアトミック書き込みを検討

# ステップ4: 改善策をまとめる
> 書籍アプリのコードとこれらのベストプラクティスを踏まえて、改善候補を提案してください

改善案:
1. add_book()の空文字列と不正年に入力バリデーションを追加
2. データ破損防止のためにsave_books()にアトミック書き込みを検討
3. utils.pyの関数に型ヒントを追加（get_user_choice、get_book_details）
```

<details>
<summary>🎬 MCPワークフローの動作を確認！</summary>

![MCP Workflow Demo](images/mcp-workflow-demo.gif)

*デモの出力は異なります。実際のモデル、ツール、回答はここで示したものと異なる場合があります。*

</details>

**結果**: コード探索 → 履歴確認 → ベストプラクティス参照 → 改善プラン。**3つのMCPサーバーを併用して、一つのターミナルセッションで全て完結。**

</details>

<details>
<summary><strong>Issue-to-PRワークフロー</strong> - ターミナルを離れずにGitHub IssueからPRまで</summary>
<a id="issue-to-pr-workflow"></a>

#### Issue-to-PRワークフロー（自分のリポジトリで）

書き込み権限のある自分のフォークまたはリポジトリで最も有効です:

> 💡 **今すぐ試せなくても大丈夫です。** 読み取り専用のクローンを利用中なら、課題で練習できます。今はフローを理解するために読んでください。

```bash
copilot

> GitHub Issue #1の詳細を教えてください

Issue #1: 書籍年度の入力バリデーションを追加
状態: オープン
add_book関数が任意の年度値を受け付けます...

> @samples/book-app-project/books.py Issue #1に記載された問題を修正してください

[Copilotがadd_book()に年度バリデーションを実装する]

> 修正が正しく機能するかテストを実行して確認してください

8件のテストが全てパスしました ✓

> "書籍アプリに年度バリデーションを追加"というタイトルでプルリクエストを作成してください

✓ PR #2を作成しました: 書籍アプリに年度バリデーションを追加
```

**コピペなし。コンテキストスイッチなし。一つのターミナルセッションで完結。**

</details>

<details>
<summary><strong>ヘルスダッシュボード</strong> - 複数サーバーでプロジェクトヘルスチェック</summary>
<a id="health-dashboard"></a>

#### 書籍アプリ ヘルスダッシュボード

```bash
copilot

> 書籍アプリプロジェクトのヘルスレポートを作成してください:
> 1. samples/book-app-project/の全Pythonファイルの全関数を一覧表示する
> 2. 型ヒントあり・なしの関数を確認する
> 3. samples/book-app-project/tests/内のテストを表示する
> 4. このディレクトリの最近のコミット履歴を確認する

書籍アプリ ヘルスレポート
======================

📊 見つかった関数:
- books.py: BookCollection内の8メソッド（全て型ヒントあり ✓）
- book_app.py: 6関数（4つは型ヒントあり、2つはなし）
- utils.py: 3関数（1つは型ヒントあり、2つはなし）

🧪 テストカバレッジ:
- test_books.py: BookCollectionを対象に8つのテスト関数
- 欠如: book_app.pyのCLI関数のテストなし
- 欠如: utils.pyのヘルパー関数のテストなし

📝 最近のアクティビティ:
- 先週コ3件のコミット
- 最新: テストフィクスチャを追加

改善提案:
- utils.pyの関数に型ヒントを追加
- book_app.pyのCLIハンドラのテストを追加
- 全ファイルが適切なサイズ（100行以下） — 良い構造！
```

**結果**: 複数のデータソースを数秒で集約。手動で行うと、grep実行、行数カウント、git log確認、テストファイル閲覧などで軽く15分以上かかる作業です。

</details>

---

# 実践

<img src="../images/practice.png" alt="Warm desk setup with monitor showing code, lamp, coffee cup, and headphones ready for hands-on practice" width="800"/>

**🎉 基本を一通り学びました！** MCPを理解し、サーバー設定方法を知り、実際のワークフローを体験しました。今度は自分で試してみましょう。

---

## ▶️ 実践してみよう

書籍アプリプロジェクトでMCPサーバーを実践する演習です。

### 演習①: MCPの状態を確認する

利用可能なMCPサーバーを確認するところから始めましょう:

```bash
copilot

> /mcp show
```

GitHubサーバーが有効として表示されるはずです。表示されない場合は`/login`で認証してください。

---

### 演習②: Filesystem MCPで書籍アプリを探索する

Filesystemサーバーを設定した場合、書籍アプリを探索してみましょう:

```bash
copilot

> samples/book-app-project/にPythonファイルは何件ありますか？
> 各ファイルにどの関数が定義されていますか？
```

**期待される結果**: Copilotが`book_app.py`、`books.py`、`utils.py`とそれぞれの関数を一覧表示します。

> 💡 **Don't have filesystem MCP configured yet?** Create the config file from the [Complete Configuration](#complete-configuration-file) section above. Then restart Copilot.

---

### 演習④: GitHub MCPでリポジトリ履歴をクエリ

組み込みのGitHub MCPを使ってこのコースリポジトリを探索しましょう:

```bash
copilot

> このリポジトリの最新コミット5件を一覧表示してください

> このリポジトリにどのブランチが存在しますか？
```

**期待される結果**: CopilotがGitHubリモートから最近のコミットメッセージとブランチ名を返します。

> ⚠️ **Codespaceを使用中ですか？** 認証は自動的に引き継かれます。ローカルクローンでは`gh auth status`でログイン済みか確認してください。

---

### 演習④: 複数のMCPサーバーを併用する

FilesystemとGitHub MCPを一セッションで組み合わせてみましょう：

```bash
copilot

> samples/book-app-project/data.jsonを読んで、コレクションにどんな書籍があるか教えて。
> その後、最近のコミットでこのファイルが最後に変更された日時を確認して。
```

**期待される結果**: CopilotがJSONファイル（Filesystem MCP）を読み込み、2冊の書籍（「The Hobbit」「1984」「Dune」「To Kill a Mockingbird」「Mysterious Book」）を一覧表示し、GitHubのコミット履歴を査詢します。

**確認テスト**: MCPを理解できたら、「リポジトリのコミット履歴を確認して」が手動で`git log`を実行して内容をコピペするよりも優れている理由を説明できるはずです。

---

## 📝 課題

### メインチャレンジ: 書籍アプリ MCP探索

MCPサーバーを併用して書籍アプリプロジェクトを実践する演習です。一つのCopilotセッションで以下を実行してください:

1. **MCPの動作確認**: `/mcp show`を実行し、少なくともGitHubサーバーが有効なことを確認する
2. **Filesystem MCPの設定**（未設定の場合）: Filesystemサーバー設定で`~/.copilot/mcp-config.json`を作成する
3. **コード探索**: Filesystemサーバーを使って以下をCopilotに尋ねる:
   - `samples/book-app-project/books.py`内の全関数を一覧表示する
   - `samples/book-app-project/utils.py`で型ヒントの欠如した関数を確認する
   - `samples/book-app-project/data.json`を読んでデータ品質の問題を検出する（ヒント: 最後のエントリを見る）
4. **リポジトリ活動の確認**: GitHub MCPを使って以下をCopilotに尋ねる:
   - `samples/book-app-project/`内のファイルを触った最近のコミットを一覧表示する
   - オープンなIssueやPRがあるか確認する
5. **サーバーの併用**: 一つのプロンプトでCopilotに以下を尋ねる:
   - `samples/book-app-project/tests/test_books.py`のテストファイルを読む
   - `books.py`内の全関数とテスト済み関数を比較する
   - 欠けているテストカバレッジをまとめる

**成功基準**: FilesystemとGitHub MCPのデータを一つのCopilotセッションでシームレスに組み合わせられ、各MCPサーバーが回答に何を提供したかを説明できること。

<details>
<summary>💡 ヒント（クリックで展開）</summary>

**ステップ1: MCPの確認**
```bash
copilot
> /mcp show
# Should show "github" as enabled
# If not, run: /login
```

**ステップ2: 設定ファイルの作成**

上記の[完全な設定ファイル](#complete-configuration-file)のJSONを`~/.copilot/mcp-config.json`として保存してください。

**ステップ3: 確認するデータ品質の問題**

`data.json`の最後の書籍:
```json
{
  "title": "Mysterious Book",
  "author": "",
  "year": 0,
  "read": false
}
```
著者名が空で年が0。これがデータ品質の問題です！

**ステップ5: テストカバレッジの比較**

`test_books.py`のテスト対象: `add_book`、`mark_as_read`、`remove_book`、`get_unread_books`、`find_book_by_title`。`load_books`、`save_books`、`list_books`は直接テストなし。`book_app.py`のCLI関数や`utils.py`のヘルパーにはテストが一切ありません。

**MCPが機能しない場合:** 設定ファイル編集後Copilotを再起動してください。

</details>

### ボーナスチャレンジ: カスタムMCPServerを構築する

さらに深く参りますか？ [カスタムMCPServerガイド](mcp-custom-server.md)に従って、Pythonで任意のAPIに接続する独自のMCPServerを構築しましょう。

---

<details>
<summary>🔧 <strong>よくあるミスとトラブルシューティング</strong>（クリックで展開）</summary>

### よくあるミス

| ミス | 結果 | 修正方法 |
|---------|---------|----------|
| GitHub MCPが組み込みであることを知らない | 手動設定しようとする | GitHub MCPはデフォルトで包含。「リポジトリの最近のコミットを一覧表示して」と誕に記せるだけ |
| 設定の場所が間違っている | MCP設定を見つけられない | ユーザーレベルは`~/.copilot/mcp-config.json`、プロジェクトレベルはプロジェクトルートの`.mcp.json` |
| 設定ファイルのJSONが無効 | MCPサーバーが読み込み失敗 | `/mcp show`で設定を確認；JSON構文を検証する |
| MCPサーバーの認証忘れ | 「認証失敗」エラー | 一部のMCPは別途認証が必要。各サーバーの要件を確認する |

### トラブルシューティング

**「MCPサーバーが見つからない」** - 以下を確認:
1. npmパッケージが存在する: `npm view @modelcontextprotocol/server-github`
2. 設定が有効なJSON形式である
3. サーバー名が設定と一致している

`/mcp show`で現在の設定を確認してください。

**「GitHub認証失敗」** - 組み込みGitHub MCPは`/login`認証情報を使用します。以下を試してください：

```bash
copilot
> /login
```

GitHubで再認証されます。問題が持続する場合は、GitHubアカウントがアクセスするリポジトリに必要な権限を持っているか確認してください。

**「MCPサーバーが起動できない」** - サーバーログを確認する:
```bash
# サーバーコマンドを手動実行してエラーを確認
npx -y @modelcontextprotocol/server-github
```

**MCPツールが利用できない** - サーバーが有効化されているか確認する:
```bash
copilot

> /mcp show
# サーバーが一覧表示され、有効化されているか確認
```

サーバーが無効化されている場合は、下記の[追加`/mcp`コマンド](#-additional-mcp-commands)で再有効化方法を確認してください。

</details>

---

<details>
<summary>📚 <strong>追加のMCPコマンド</strong> (クリックで展開)</summary>
<a id="-additional-mcp-commands"></a>

MCPサーバーの管理方法は2つあります: チャットセッション内の**スラッシュコマンド**、またはターミナルから直接実行する**`copilot mcp`コマンド**です。

### 方法™1: スラッシュコマンド（チャットセッション内）

`copilot`起動後のチャット内で有効です:

| コマンド | 内容 |
|---------|--------|
| `/mcp show` | 全MCPサーバーとその状態を表示 |
| `/mcp add` | 新しいサーバー追加のインタラクティブセットアップ |
| `/mcp edit <server-name>` | 既存サーバー設定を編集 |
| `/mcp enable <server-name>` | 無効なサーバーを有効化（セッションを越えて持続） |
| `/mcp disable <server-name>` | サーバーを無効化（セッションを越えて持続） |
| `/mcp delete <server-name>` | サーバーを永久削除 |
| `/mcp auth <server-name>` | OAuthを使用するMCPサーバーと再認証（例: アカウント切り替え後） |

### オプション2: `copilot mcp`コマンド（ターミナルから）

チャットセッションを開始せずにターミナルから直接MCPServerを管理できます:

```bash
# List all configured MCP servers
copilot mcp list

# Enable a server
copilot mcp enable filesystem

# Disable a server
copilot mcp disable context7
```

> 💡 **使い分け**: チャットセッション内は`/mcp`コマンドを使用。セッション開始前にサーバー設定を確認または変更したい場合はターミナルから`copilot mcp`を使用。

ほとんどの場合、`/mcp show`だけで十分です。その他のコマンドは多くのサーバーを管理するようになったときに役立ちます。

</details>

---

# まとめ

## 🔑 重要なポイント

1. **MCP**はCopilotを外部サービス（GitHub、ファイルシステム、ドキュメント）に接続する
2. **GitHub MCPは組み込み** - 設定不要、`/login`だけでOK
3. **FilesystemとContext7**は`~/.copilot/mcp-config.json`で設定
4. **マルチサーバーワークフロー**で一セッション内に複数ソースを集約
5. **サーバー管理方法は2つ**: チャット内の`/mcp`コマンドまたはターミナルから`copilot mcp`
6. **カスタムサーバー**で任意のAPIを接続できる（オプション、付録ガイドに詳細あり）

> 📋 **クイックリファレンス**: [GitHub Copilot CLIコマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference)で全コマンドとショートカットを確認できます。

---

## ➡️ 次のステップ

モード、コンテキスト、ワークフロー、Agents、Skills、MCPと全ての素材が揃いました。いよいよ全てを組み合わせる時です。

**[Chapter 07: 全てを組み合わせる](../07-putting-it-together/README.md)**では以下を学びます:

- Agents、Skills、MCPを統合したワークフローの構築
- アイデアからマージ済みPRまでの完全な機能開発
- フックによる自動化
- チーム環境でのベストプラクティス

---

**[← Chapter 05に戻る](../05-skills/README.md)** | **[Chapter 07へ進む →](../07-putting-it-together/README.md)**
