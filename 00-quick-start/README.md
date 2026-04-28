![Chapter 00: Quick Start](images/chapter-header.png)

ようこそ！この章では、GitHub Copilot CLI（Command Line Interface）をインストールし、GitHubアカウントでサインインし、動作を確認します。これはサクッとセットアップする章です。準備ができたら、本番のデモは第01章から始まります！

## 🎯 学習目標

この章を終えると、以下ができるようになります：

- GitHub Copilot CLIのインストール
- GitHubアカウントでのサインイン
- 簡単なテストで動作確認

> ⏱️ **所要時間の目安**: 約10分（55分読む + 5分ハンズオン）

---

## ✅ 前提条件

- **GitHubアカウント**（Copilotアクセス付き）。[サブスクリプションオプションを確認](https://github.com/features/copilot/plans)。学生・教職員は[GitHub Education](https://education.github.com/pack)経由でCopilot Proを無料利用可能。
- **ターミナルの基礎**: `cd`、`ls`などの基本操作

### 「Copilotアクセス」とは

GitHub Copilot CLIには有効なCopilotサブスクリプションが必要です。[github.com/settings/copilot](https://github.com/settings/copilot)でステータスを確認できます。次のいずれかが表示されるはずです：

- **Copilot Individual** - 個人サブスクリプション
- **Copilot Business** - 組織経由
- **Copilot Enterprise** - エンタープライズ経由
- **GitHub Education** - 認定学生・教職員は無料

「GitHub Copilotのアクセス権がありません」と表示された場合は、無料オプションを利用するか、プランにサブスクライブするか、アクセスを提供する組織に参加する必要があります。

---

## インストール

> ⏱️ **所要時間の目安**: インストールに2ー5分、認証に、1ー2分追加でかかります。

### GitHub Codespaces（ゼロセットアップ）

前提条件をインストールしたくない場合は、GitHub Codespacesを使用できます。CodespacesにはGitHub Copilot CLIがすぐに使える状態で用意されており（サインインは必要）、Pythonとpytestも事前インストール済みです。

1. このリポジトリを[GitHubアカウントにフォーク](https://github.com/github/copilot-cli-for-beginners/fork)する
2. **Code** > **Codespaces** > **Create codespace on main**を選択
3. コンテナーのビルドが完了するまで数分待つ
4. 準備完了！ターミナルはCodespace環境で自動的に開く

> 💡 **Codespaceでの動作確認**: `cd samples/book-app-project && python book_app.py help`を実行してPythonとサンプルアプリが正常に動作するか確認しましょう。

### ローカルインストール

コースサンプルをローカルマシンで実行する場合は、次の手順に従ってください。

1. Clone the repo to get the course samples on your machine:

    ```bash
    git clone https://github.com/github/copilot-cli-for-beginners
    cd copilot-cli-for-beginners
    ```

2. Install Copilot CLI using one of the following options.

    > 💡 **どれを選ぶか迷ったら？** Node.jsがインストール済みなら`npm`が溅です。そうでなければ、システムに合ったオプションを選んでください。

    ### 全プラットフォーム（npm）

    ```bash
    # If you have Node.js installed, this is a quick way to get the CLI
    npm install -g @github/copilot
    ```

    ### macOS/Linux（Homebrew）

    ```bash
    brew install copilot-cli
    ```

    ### Windows（WinGet）

    ```bash
    winget install GitHub.Copilot
    ```

    ### macOS/Linux（インストールスクリプト）

    ```bash
    curl -fsSL https://gh.io/copilot-install | bash
    ```

---

## 認証

`copilot-cli-for-beginners`リポジトリのルートでターミナルを開き、CLIを起動してフォルダへのアクセスを許可してください。

```bash
copilot
```

リポジトリのフォルダを信躭するか確認されます（まだ完了していない場合）。一回限りまたは以後のセッション全体に対して信躭できます。

<img src="images/copilot-trust.png" alt="Trusting files in a folder with the Copilot CLI" width="800"/>

フォルダを信躭した後、GitHubアカウントでサインインできます。

```
> /login
```

**その後の流れ:**

1. Copilot CLIがワンタイムコード（例：`ABCD-1234`）を表示する
2. ブラウザでGitHubのデバイス認証ページが開く。まだサインインしていなければGitHubにサインインする
3. プロンプトに従ってコードを入力する
4. 「認可」を選択してGitHub Copilot CLIにアクセスを許可する
5. ターミナルに戻ります - サインイン完了！

<img src="images/auth-device-flow.png" alt="Device Authorization Flow - showing the 5-step process from terminal login to signed-in confirmation" width="800"/>

*デバイス認証フロー: ターミナルがコードを生成し、ブラウザで確認し、Copilot CLIが認証されます。*

**ヒント**: サインインはセッションを越えて持続します。トークンが期限切れまたは明示的にサインアウトしない限り、一度だけ行えばOKです。

---

## 動作確認

### ステップ1: Copilot CLIの動作確認

サインインが完了したので、Copilot CLIが正常に動作しているか確認しましょう。ターミナルでCLIをまだ起動していない場合は起動します：

```bash
> こんにちは！どんなことを手伝えるか教えてください
```

応答が返ってきたら、CLIを終了できます：

```bash
> /exit
```

---

<details>
<summary>🎬 動作を確認！</summary>

![Hello Demo](images/hello-demo.gif)

*デモの出力は異なります。実際のモデル、ツール、回答はここで示したものと異なる場合があります。*

</details>

---

**期待される出力**: Copilot CLIの機能を一覧した親しみやすい応答。

### ステップ2: サンプル書籍アプリの起動

コースでは、CLIを使って探索・改善するサンプルアプリが用意されています（コードは`/samples/book-app-project`で確認できます）。始める前にPythonで写かれた書籍コレクションターミナルアプリが動作するか確認しましょう。システムによって`python`または`python3`を使用してください。

> **注意:** コース全体の主要例はPython（`samples/book-app-project`）を使用しています。ローカルマシンを選択した場合は[Python 3.10+](https://www.python.org/downloads/)が必要です（Codespaceにはすでにインストール済み）。JavaScript（`samples/book-app-project-js`）やC#（`samples/book-app-project-cs`）バージョンも利用できます。各サンプルにはその言語での実行手順を記載したREADMEがあります。

```bash
cd samples/book-app-project
python book_app.py list
```

**期待される出力**: 「The Hobbit」「1984」「Dune」など〕5冊の書籍の一覧。

### ステップ3: 書籍アプリでCopilot CLIを試す

（ステップ2を実行した場合）まずリポジトリのルートに戻ります：

```bash
cd ../..   # 必要な場合はリポジトリルートに戻る
copilot 
> @samples/book-app-project/book_app.py は何をするファイルですか？
```

**期待される出力**: 書籍アプリの主要機能とコマンドのサマリー。

エラーが表示された場合は、下記の[トラブルシューティングセクション](#troubleshooting)を確認してください。

完了したらCopilot CLIを終了できます：

```bash
> /exit
```

---

## ✅ 準備完了！

インストールは以上です。本番の楽しさは第01章から始まります。第01章では：

- AIが書籍アプリをレビューしてコード品質の問題を即座に見つける様子を見る
- Copilot CLIの3つの使い方を学ぶ
- 英文の自然言語から動作するコードを生成する

**[第01章へ進む: 最初の一歩 →](../01-setup-and-first-steps/README.md)**

---

## トラブルシューティング

### 「copilot: command not found」

CLIがインストールされていません。別のインストール方法を試してみてください：

```bash
# brewが失敗した場合はnpmを試す：
npm install -g @github/copilot

# またはインストールスクリプト：
curl -fsSL https://gh.io/copilot-install | bash
```

### 「You don't have access to GitHub Copilot」

1. [github.com/settings/copilot](https://github.com/settings/copilot)でCopilotサブスクリプションを持っているか確認する
2. 業務用アカウントを使用している場合は、組織がCLIアクセスを許可しているか確認する

### 「Authentication failed」

再認証する：

```bash
copilot
> /login
```

### ブラウザが自動的に開かない場合

[github.com/login/device](https://github.com/login/device)を手動で開き、ターミナルに表示されたコードを入力してください。

### トークンが期限切れの場合

`/login`を再実行するだけです：

```bash
copilot
> /login
```

### まだ解決しない場合？

- [GitHub Copilot CLIドキュメント](https://docs.github.com/copilot/concepts/agents/about-copilot-cli)を確認する
- [GitHub Issues](https://github.com/github/copilot-cli/issues)を検索する

---

## 🔑 キーポイント

1. **GitHub Codespaceは最快の始め方** - Python、pytest、GitHub Copilot CLIが全て事前インストール済みなので即座にデモに進める
2. **複数のインストール方法** - システムに合った方法を選択（Homebrew、WinGet、npm、インストールスクリプト）
3. **一回の認証** - トークンが期限切れるまでログイン状態が維持される
4. **書籍アプリは動作する** - `samples/book-app-project`をコース全体を通じて使用する

> 📚 **公式ドキュメント**: インストールオプションと要件については[Copilot CLIのインストール](https://docs.github.com/copilot/how-tos/copilot-cli/cli-getting-started)を参照。

> 📋 **クイックリファレンス**: コマンドとショートカットの全一覧は[GitHub Copilot CLIコマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference)を参照。

---

**[第01章へ進む: 最初の一歩 →](../01-setup-and-first-steps/README.md)**
