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

# Chapter 01: 最初の一歩

**AIがバグを瞬時に発見し、コードを解説し、スクリプトを生成する**

3つのインタラクションモードをマスターしましょう

> ⚠️ **前提条件**: 第00章を完了し、GitHub Copilot CLIがインストール・認証済みであること

---

## 🎯 学習目標

この章を終えると：

- **Copilot CLI** が提供する生産性向上を体感できる
- タスクに応じた **3つのモード** を選択できる
- **スラッシュコマンド** でセッションを制御できる

> ⏱️ 所要時間の目安: 約45分（15分読む + 30分ハンズオン）

---

## 気軽に始める: 最初のプロンプト

**コードリポジトリは不要！** ターミナルを開いて起動するだけ：

```bash
copilot
```

**重要なポイント**: 特別な構文は不要。自然な言葉で質問するだけです。

探索が終わったら `/exit` でセッションを終了します。

--

## サンプルプロンプトを試してみよう

```
> Explain what a dataclass is in Python in simple terms

> Write a function that sorts a list of dictionaries by a specific key

> What's the difference between a list and a tuple in Python?

> Give me 5 best practices for writing clean Python code
```

Pythonを使わない方でも大丈夫！お好みの言語について質問してください。

---

## 実際に見てみる

### なぜ「シニアエンジニアがすぐそこにいるよう」と呼ばれるのか？

<img src="images/first-copilot-experience.png" alt="Developer with AI assistance" width="60%"/>

--

> 📖 **読み方**: `>` で始まる行はCopilot CLIセッション内のプロンプト、それ以外はシェルコマンド

> 💡 **サンプル出力は参考例**: 実際の応答は毎回異なります。返ってくる**情報の種類**に注目してください

--

## デモ1: 数秒でコードレビュー 🔍

```bash
copilot
```

```
> @samples/book-app-project/book_app.py のコード品質の問題をレビューして、
  改善点を提案してください
```
> 💡 **`@` 記号**: ファイルを読み込む指示。詳細はChapter 02で学びます

--

![Code Review Demo](images/code-review-demo.gif)

**要点**: 数秒でプロのコードレビュー完了！手動レビューより圧倒的に速い。

--

## デモ2: 難解なコードの解説 📖

```
> @samples/book-app-project/books.py が何をするか、わかりやすく説明してください
```

--

![Explain Code Demo](images/explain-code-demo.gif)

**要点**: 複雑なコードが、忍耐強いメンターに解説してもらえる感覚で理解できます

--

## デモ2 — 出力例

```
これはPythonのdataclassを使った書籍コレクション管理モジュールです。

主なコンポーネント：

1. @dataclass デコレーター（8行目）
   - __init__、__repr__ などを自動生成する
   - Pythonでデータ構造を定義するクリーンな方法

2. BookCollection クラス（16行目）
   - load_books() は data.json から読み込む
   - save_books() は asdict() を使って data.json に書き込む

3. 書籍操作: add_book / find_book_by_title / mark_as_read / find_by_author

一般的なパターン: JSON から読み込む → 処理する → JSON に書き戻す
```

--

## デモ3: 動作するコードの生成 ⚡

```
> 書籍のリストを受け取って、統計情報
  （合計冊数・既読数・未読数・最古と最新の書籍）を
  返すPython関数を書いてください
```

![Generate Code Demo](images/generate-code-demo.gif)

**要点**: 数秒で完成するコピペ実行可能な関数。コマンドラインから一切離れずに！

---

## 🍽️ アナロジー: 外食とモード選択

| モード | 外食のアナロジー | 使い所 |
|------|----------------|-------------|
| **Interactive** | ウェイターと話す | 探索・反復・マルチターンの会話 |
| **Plan** | GPSナビで計画 | 複雑なタスクを事前確認してから実行 |
| **Programmatic** | ドライブスルー注文 | 自動化・CI/CD・一回限りの実行 |

--

<img src="images/ordering-food-analogy.png" alt="Three Ways to Use GitHub Copilot CLI" width="70%"/>

---

## どのモードから始めるか？

**→ まず Interactive モードから始めましょう**

- 試行錯誤やフォローアップ質問ができる
- 会話を通じてコンテキストが自然に深まる
- `/clear` で間違いを簡単に修正できる

慣れたら：

- **Programmatic**: `copilot -p "<プロンプト>"` でスピーディーな一発質問
- **Plan**: `/plan` でコードを書く前に詳細な計画を立てる

---

## モード1: Interactive モード

<img src="images/interactive-mode.png" alt="Interactive Mode" width="25%"/>

**最適な用途**: 探索、反復、マルチターンの会話

```bash
copilot
```

```
> /help   # 利用可能なコマンドを確認
```

**重要**: コンテキストが維持されるため、各メッセージが前の返答に基づいて展開します

--

## Interactive モードの例

```bash
copilot

> @samples/book-app-project/utils.py をレビューして改善点を提案してください

> すべての関数に型ヒントを追加してください

> エラー処理をより堅牢にしてください

> /exit
```

各プロンプトが前の回答を**引き継いでいる**ことに注目してください。

毎回最初からやり直すのではなく、会話を続けています。

---

## モード2: Plan モード

<img src="images/plan-mode.png" alt="Plan Mode" width="25%"/>

**最適な用途**: 実行前にアプローチを確認したい複雑なタスク

```bash
copilot

> /plan 書籍アプリに「既読にする」コマンドを追加する
```

> 💡 **Shift+Tab** でモードを切り替え: Interactive → Plan → Autopilot

`--plan` フラグで直接起動することもできます: `copilot --plan`

--

## Plan モード — 出力例

```
📋 実装計画

ステップ1: book_app.py のコマンドハンドラーを更新する
  - "mark" コマンド用の新しい elif ブランチを追加する
  - handle_mark_as_read() 関数を作成する

ステップ2: ハンドラー関数を実装する
  - ユーザーに書籍タイトルを入力させる
  - collection.mark_as_read(title) を呼び出す
  - 成功・失敗メッセージを表示する

ステップ3: ヘルプテキストを更新する

ステップ4: フローをテストする

実装を進めますか？ [Y/n]
```

**重要**: コードが書かれる前にアプローチを確認・修正できます！

--

## Plan モード — 活用のヒント

計画が完成したらファイルに保存できます：

```
> この計画を mark_as_read_plan.md に保存して
```

さらに複雑なタスクも対応可能：

```
> /plan 書籍アプリに検索・フィルタリング機能を追加する
```

> 📚 **Autopilot モード**: 各ステップで入力を待たずに計画全体を実行します。
> まず Interactive と Plan に慣れてから試しましょう。
> [公式ドキュメント](https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot)

---

## モード3: Programmatic モード

<img src="images/programmatic-mode.png" alt="Programmatic Mode" width="25%"/>

**最適な用途**: 自動化・スクリプト・CI/CD・一回限りのコマンド

```bash
# コード生成
copilot -p "数が偶数か奇数かを判定する関数を書いてください"

# クイックヘルプ
copilot -p "PythonでJSONファイルを読み込む方法を教えてください"
```

**重要**: 迅速に回答して終了。会話なし、入力 → 出力のみ

--

## Programmatic モード — スクリプト活用

```bash
#!/bin/bash

# コミットメッセージを自動生成
COMMIT_MSG=$(copilot -p "以下の変更のコミットメッセージを生成: $(git diff --staged)")
git commit -m "$COMMIT_MSG"

# ファイルをレビュー
copilot --allow-all -p "@myfile.py の問題をレビューしてください"
```

> ⚠️ **`--allow-all` について**: ファイル読み込み・コマンド実行・URLアクセスの確認をスキップします。
> 自分で書いたプロンプトと**信頼できるディレクトリ**でのみ使用してください。

---

## 主要なスラッシュコマンド

| コマンド | 機能 | 使い所 |
|---------|------|--------|
| `/ask` | 会話履歴に影響せずクイック質問 | コンテキストを汚さず一発質問 |
| `/clear` | 会話をクリアしてリセット | トピックを切り替えるとき |
| `/help` | 全コマンドを表示 | コマンドを忘れたとき |
| `/model` | AIモデルの表示・切り替え | モデルを変えたいとき |
| `/plan` | コーディング前に作業を計画 | 複雑な機能向け |
| `/research` | GitHubとWebで深い調査 | コードを書く前のリサーチ |
| `/exit` | セッション終了 | 作業完了時 |

> 📚 [CLIコマンドリファレンス（公式）](https://docs.github.com/copilot/reference/cli-command-reference)

--

## 追加コマンド — エージェント環境

| コマンド | 機能 |
|---------|------|
| `/agent` | 利用可能なAgentを参照・選択 |
| `/env` | 読み込み済み環境の詳細を表示 |
| `/init` | リポジトリ用のCopilot指示を初期化 |
| `/mcp` | MCPサーバーの設定を管理 |
| `/skills` | Skillsを管理 |

> 💡 Agent → Ch04 / Skills → Ch05 / MCP → Ch06 で詳しく解説

--

## 追加コマンド — モデル・サブエージェント・コード

| コマンド | 機能 |
|---------|------|
| `/delegate` | タスクをクラウドエージェントに引き渡す |
| `/fleet` | 複雑なタスクを並列サブタスクに分割して高速完了 |
| `/model` | AIモデルの表示・切り替え |
| `/diff` | 現在のディレクトリの変更をレビュー |
| `/pr` | 現在のブランチのPRを操作 |

--

## 追加コマンド — 権限

| コマンド | 機能 |
|---------|------|
| `/add-dir <directory>` | ディレクトリを許可リストに追加 |
| `/allow-all [on\|off\|show]` | 全権限プロンプトを自動承認 |
| `/yolo` | `/allow-all on` のクイックエイリアス |
| `/cwd`, `/cd [directory]` | 作業ディレクトリの表示・変更 |
| `/list-dirs` | 許可済みディレクトリを全表示 |

> ⚠️ `/allow-all` と `/yolo` は確認プロンプトをスキップします。信頼済みプロジェクトのみで使用してください。

--

## 追加コマンド — セッション管理

| コマンド | 機能 |
|---------|------|
| `/clear` | セッションを破棄（履歴保存なし）して新規開始 |
| `/compact` | 会話を要約してコンテキスト使用量を削減 |
| `/context` | コンテキストウィンドウのトークン使用量を表示 |
| `/new` | セッションを終了（履歴保存）して新規開始 |
| `/resume` | 別のセッションに切り替え（IDを指定可） |
| `/rewind` | タイムラインピッカーで任意のポイントに巻き戻す |
| `/share` | セッションをMarkdown/Gist/HTMLとしてエクスポート |

--

## 追加コマンド — クイックシェル & モデル切り替え

`!` プレフィックスでAIを通さずシェルコマンドを直接実行：

```bash
copilot

> !git status          # AIをバイパスして直接実行
> !python -m pytest tests/
```

モデルの切り替え：

```bash
copilot
> /model
# 利用可能なモデルを表示して選択できます。Claude Sonnet 4.5を選んでみてください。
```

> 💡 **1x表示のモデル**（例: Claude Sonnet 4.5）はプレミアムリクエストの消費が抑えられ、デフォルト選択として優れています。

---

## ▶️ 実践

<img src="../images/practice.png" alt="Practice" width="60%"/>

学んだことを実際に試してみましょう！

--

## 実践1: インタラクティブ探索

```bash
copilot

> @samples/book-app-project/book_app.py を改善できる点は何ですか？

> if/elif チェーンをより保守しやすい構造にリファクタリングしてください

> すべてのハンドラー関数に型ヒントを追加してください

> /exit
```

--

## 実践2: Plan モードで機能を計画する

```bash
copilot

> /plan タイトルまたは著者で書籍を検索できる機能を書籍アプリに追加する

# 計画を確認する → 承認または修正する → ステップバイステップで実装
```

--

## 実践3: Programmatic モードで自動化

**bash:**

```bash
for file in samples/book-app-project/*.py; do
  echo "Reviewing $file..."
  copilot --allow-all -p "@$file のコード品質を簡易レビューしてください。重要な問題のみ"
done
```

**PowerShell (Windows):**

```powershell
Get-ChildItem samples/book-app-project/*.py | ForEach-Object {
  $relativePath = "samples/book-app-project/$($_.Name)"
  Write-Host "Reviewing $relativePath..."
  copilot --allow-all -p "@$relativePath のコード品質を簡易レビューしてください。重要な問題のみ"
}
```

---

## 💡 Tip: リモートセッション

ブラウザやモバイルアプリからCLIセッションを操作できます！

```bash
copilot --remote   # QRコードとリンクが表示される
```

またはセッション中に `> /remote`

- スマートフォンやブラウザタブでリアルタイム監視
- フォローアッププロンプトをリモートで送信
- エージェントをリモートで指示

[詳細ドキュメント](https://docs.github.com/copilot/how-tos/copilot-cli/steer-remotely)

---

## 📝 課題: utils.py を改善する

1. `copilot` でインタラクティブセッション開始
2. `@samples/book-app-project/utils.py このファイルの各関数は何をしていますか？`
3. 空入力・数字以外を処理するバリデーションを `get_user_choice()` に追加
4. 空文字列のガードを `get_book_details()` に追加
5. `get_book_details()` に包括的なdocstringを追加

**確認**: プロンプト間でコンテキストが引き継がれることを確認してください！

--

## チャレンジ

1. **Interactive チャレンジ**: `@samples/book-app-project/books.py` について質問して、改善を3回続けて依頼してみましょう

2. **Plan チャレンジ**: `/plan 書籍アプリに評価・レビュー機能を追加する` を実行。計画は理にかなっていますか？

3. **Programmatic チャレンジ**: 以下を実行して一発で機能するか確認しましょう

```bash
copilot --allow-all -p "@samples/book-app-project/book_app.py のすべての関数を列挙して、それぞれが何をするか説明してください"
```