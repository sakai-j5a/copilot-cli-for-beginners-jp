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

<!-- .slide: data-background-color="#0d1117" -->

# Chapter 00: クイックスタート

**GitHub Copilot CLI をインストールして、最初の動作確認まで**

⏱️ 所要時間の目安: 約10分

---

## 🎯 学習目標

この章を終えると：

- **GitHub Copilot CLI** をインストールできる
- **GitHubアカウント** でサインインできる
- **動作確認** ができる

---

## ✅ 前提条件

- **GitHubアカウント**（Copilotアクセス付き）
  - [サブスクリプションオプションを確認](https://github.com/features/copilot/plans)
  - 学生・教職員は [GitHub Education](https://education.github.com/pack) 経由でCopilot Proを無料利用可能

- **ターミナルの基礎**: `cd`、`ls` などの基本操作

--

## Copilotアクセスとは

[github.com/settings/copilot](https://github.com/settings/copilot) でステータスを確認できます

| プラン | 対象 |
|-------|------|
| **Copilot Individual** | 個人サブスクリプション |
| **Copilot Business** | 組織経由 |
| **Copilot Enterprise** | エンタープライズ経由 |
| **GitHub Education** | 認定学生・教職員（無料） |

「アクセス権がありません」と表示された場合は無料オプションまたはサブスクリプションが必要です

---

## インストール方法の選択

> ⏱️ インストールに2〜5分、認証に1〜2分追加でかかります

| 環境 | 推奨方法 |
|------|---------|
| **GitHub Codespaces** | ✅ ゼロセットアップ（推奨） |
| **Node.js インストール済み** | npm |
| **macOS/Linux** | Homebrew またはインストールスクリプト |
| **Windows** | WinGet |

--

## GitHub Codespaces（ゼロセットアップ）

Python・pytest・Copilot CLI が全て事前インストール済み！

1. このリポジトリを [GitHubアカウントにフォーク](https://github.com/github/copilot-cli-for-beginners/fork)
2. **Code** > **Codespaces** > **Create codespace on main** を選択
3. コンテナーのビルドが完了するまで数分待つ
4. 準備完了！ターミナルが自動的に開く

> 💡 **動作確認**: `cd samples/book-app-project && python book_app.py help` を実行

--

## ローカルインストール — npm / Homebrew

まずリポジトリをクローン：

```bash
git clone https://github.com/github/copilot-cli-for-beginners
cd copilot-cli-for-beginners
```

**全プラットフォーム（npm）:**

```bash
npm install -g @github/copilot
```

**macOS/Linux（Homebrew）:**

```bash
brew install copilot-cli
```

> 💡 Node.jsがインストール済みなら `npm` が最も手軽です

--

## ローカルインストール — WinGet / スクリプト

**Windows（WinGet）:**

```bash
winget install GitHub.Copilot
```

**macOS/Linux（インストールスクリプト）:**

```bash
curl -fsSL https://gh.io/copilot-install | bash
```

---

## 認証: Copilot CLIを起動する

リポジトリのルートでターミナルを開き、CLIを起動してください：

```bash
copilot
```

フォルダへのアクセスを許可するか確認されます（一回限りまたはセッション全体）

<img src="images/copilot-trust.png" alt="Trusting files in a folder with the Copilot CLI" width="65%"/>

--

## 認証: GitHubにサインイン

```
> /login
```

**その後の流れ:**

1. Copilot CLIがワンタイムコード（例: `ABCD-1234`）を表示
2. ブラウザでGitHubのデバイス認証ページが開く
3. プロンプトに従ってコードを入力
4. 「認可」を選択してCopilot CLIにアクセスを許可
5. ターミナルに戻ります — サインイン完了！

> 💡 **サインインはセッションを越えて持続します。** 一度だけ行えばOK

--

## 認証フロー図

<img src="images/auth-device-flow.png" alt="Device Authorization Flow" width="70%"/>

*ターミナルがコードを生成 → ブラウザで確認 → Copilot CLIが認証されます*

---

## 動作確認 — ステップ1: Copilot CLIの確認

CLIがまだ起動していない場合は起動してから：

```bash
> こんにちは！どんなことを手伝えるか教えてください
```

応答が返ってきたら成功！CLIを終了します：

```bash
> /exit
```

![Hello Demo](images/hello-demo.gif)

**期待される出力**: Copilot CLIの機能を一覧した親しみやすい応答

---

## 動作確認 — ステップ2: 書籍アプリの起動

コース全体で使用するサンプルアプリが正常に動作するか確認しましょう：

```bash
cd samples/book-app-project
python book_app.py list
```

**期待される出力**: 「The Hobbit」「1984」「Dune」など5冊の書籍の一覧

> **注意**: ローカルマシンの場合は [Python 3.10+](https://www.python.org/downloads/) が必要です。
> JavaScript版（`book-app-project-js`）やC#版（`book-app-project-cs`）も利用できます。

---

## 動作確認 — ステップ3: 書籍アプリで試す

リポジトリのルートに戻ってCopilotを起動：

```bash
cd ../..
copilot
```

```
> @samples/book-app-project/book_app.py は何をするファイルですか？
```

**期待される出力**: 書籍アプリの主要機能とコマンドのサマリー

完了したら：

```bash
> /exit
```

---

## ✅ 準備完了！

インストールは以上です。**本番の楽しさは第01章から始まります！**

第01章では：

- AIが書籍アプリをレビューしてコード品質の問題を即座に発見する様子を見る
- Copilot CLIの **3つの使い方** を学ぶ
- 自然言語から **動作するコードを生成** する

**[第01章へ進む: 最初の一歩 →](../01-setup-and-first-steps/README.md)**

---

## 🔧 トラブルシューティング

| 問題 | 解決方法 |
|------|---------|
| `command not found` | 別のインストール方法を試す（npm、Homebrew、WinGet） |
| アクセス権がない | [github.com/settings/copilot](https://github.com/settings/copilot) でサブスクリプション確認 |
| 認証失敗 | `> /login` を再実行 |
| ブラウザが開かない | [github.com/login/device](https://github.com/login/device) を手動で開く |

--

## よくある問題の解決コマンド

**再インストール（npm）:**

```bash
npm install -g @github/copilot
```

**再認証:**

```bash
copilot
> /login
```

> 📚 **まだ解決しない場合**: [GitHub Copilot CLIドキュメント](https://docs.github.com/copilot/concepts/agents/about-copilot-cli) または [GitHub Issues](https://github.com/github/copilot-cli/issues) を参照

---

## 🔑 まとめ: キーポイント

1. **Codespaces が最速の始め方** — Python・pytest・Copilot CLI が全て事前インストール済み
2. **複数のインストール方法** — システムに合った方法を選択可能
3. **一回の認証でOK** — トークンが期限切れるまでログイン状態が維持される
4. **書籍アプリを使う** — `samples/book-app-project` をコース全体で活用する

> 📋 [GitHub Copilot CLIコマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference)