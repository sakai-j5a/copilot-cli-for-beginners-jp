![Chapter 02: Context and Conversations](images/chapter-header.png)

> **AIがファイル1つではなく、コードベース全体を見れたらどうでしょうか？**

この章では、GitHub Copilot CLIの強力な機能である「コンテキスト」を解放します。`@`構文を使ってファイルやディレクトリを参照する方法を学び、Copilot CLIにコードベースを深く理解させます。セッションを跨いで会話を維持し、数日後でも作業を正確な地点から再開できる方法を解説します。さらに、クロスファイル分析が単一ファイルのレビューでは見逃すバグを如何に発見するかも行います。

## 🎯 学習目標

この章を終えると、以下のことができるようになります：

- `@`構文でファイル、ディレクトリ、画像を参照する
- `--resume`と`--continue`で前のセッションを再開する
- [コンテキストウィンドウ](../GLOSSARY.md#context-window)の仕組みを理解する
- 効果的なマルチターンの会話を書く
- マルチプロジェクトワークフローでディレクトリの権限を管理する

> ⏱️ **所要時間の目安**: 約50分（20分読む + 30分ハンズオン）

---

## 🧩 現実世界のアナロジー: 同僚との作業

<img src="images/colleague-context-analogy.png" alt="Context Makes the Difference - Without vs With Context" width="800"/>

*Just like your colleagues, Copilot CLI isn't a mind reader. Providing more information helps humans and Copilot alike provide targeted support!*

同僚にバグを説明するときのことを想像してみてください：

> **コンテキストなし**: 「書籍アプリが動かない」

> **コンテキストあり**: 「`books.py`、特に`find_book_by_title`関数を見て。大文字小文字を区別しない一致ができていない」

Copilot CLIにコンテキストを提供するには、*`@`構文*を使って特定のファイルを指示します。

---

# 必須: 基本コンテキスト

<img src="images/essential-basic-context.png" alt="Glowing code blocks connected by light trails representing how context flows through Copilot CLI conversations" width="800"/>

このセクションでは、コンテキストを効果的に活用するための必要な知識をすべて解説します。この基本をまずマスターしてください。

---

## @ 構文

`@`シンボルは、プロンプト内でファイルやディレクトリを参照するためのものです。Copilot CLIに「このファイルを見て」と伝える方法です。

> 💡 **注記**: このコースの全例は、このリポジトリに含まれる`samples/`フォルダを使用しています。全てのコマンドを直接試せます。

### 今すぐ試す（セットアップ不要）

コンピュータ内の任意のファイルで試せます：

```bash
copilot

# 手元にあるファイルを指定する
> @package.json が何をするか説明してください
> @README.md を要約してください
> @.gitignore に何が書かれていて、なぜ必要なのですか？
```

> 💡 **プロジェクトがすぐそこにない？** クイックテストファイルを作ってみましょう：
> ```bash
> echo "def greet(name): return 'Hello ' + name" > test.py
> copilot
> > @test.py は何をしていますか？
> ```

### 基本的な@パターン

| パターン | 機能 | 使用例 |
|---------|--------------|-------------|
| `@file.py` | 単一ファイルの参照 | `@samples/book-app-project/books.py をレビューしてください` |
| `@folder/` | ディレクトリ内の全ファイルを参照 | `@samples/book-app-project/ をレビューしてください` |
| `@file1.py @file2.py` | 複数ファイルの参照 | `@samples/book-app-project/book_app.py と @samples/book-app-project/books.py を比較してください` |

### 単一ファイルの参照

```bash
copilot

> @samples/book-app-project/utils.py が何をするか説明してください
```

---

<details>
<summary>🎬 動作を確認！</summary>

![File Context Demo](images/file-context-demo.gif)

*デモの出力は異なります。実際のモデル、ツール、回答はここで示したものと異なる場合があります。*

</details>

---

### 複数ファイルの参照

```bash
copilot

> @samples/book-app-project/book_app.py と @samples/book-app-project/books.py の一貫性を比較してください
```

### ディレクトリ全体の参照

```bash
copilot

> @samples/book-app-project/ のすべてのファイルのエラー処理をレビューしてください
```

---

## クロスファイルのインテリジェンス

ここでコンテキストが強力な武器になります。単一ファイルの分析は有益ですが、クロスファイル分析は革命的です。

<img src="images/cross-file-intelligence.png" alt="Cross-File Intelligence - comparing single-file vs cross-file analysis showing how analyzing files together reveals bugs, data flow, and patterns invisible in isolation" width="800"/>

### デモ: 複数ファイルにまたがるバグを発見する

```bash
copilot

> @samples/book-app-project/book_app.py @samples/book-app-project/books.py
>
> これらのファイルはどのように連携していますか？データの流れはどうなっていますか？
```

> 💡 **応用オプション**: セキュリティ重視のクロスファイル分析には、Pythonのセキュリティ例を試してみましょう：
> ```bash
> > @samples/buggy-code/python/user_service.py @samples/buggy-code/python/payment_processor.py
> > 両方のファイルにまたがるセキュリティ脆弱性を見つけてください
> ```

---

<details>
<summary>🎬 動作を確認！</summary>

![Multi-File Demo](images/multi-file-demo.gif)

*デモの出力は異なります。実際のモデル、ツール、回答はここで示したものと異なる場合があります。*

</details>

---

**Copilot CLIが発見すること**:

```
クロスモジュール分析
===================

1. データフローのパターン
   book_app.py が BookCollection インスタンスを作成してメソッドを呼び出す
   books.py が BookCollection クラスを定義してデータの永続化を管理する

   フロー: book_app.py（UI） → books.py（ビジネスロジック） → data.json（ストレージ）

2. 重複する表示関数
   book_app.py:9-21    show_books() 関数
   utils.py:28-36      print_books() 関数

   影響: ほぼ同じことをする関数が2つある。一方を更新したとき（フォーマット変更など）、
   もう一方も必ず更新しなければならない。

3. 一貫性のないエラー処理
   book_app.py が年の変換時の ValueError を処理する
   books.py がエラー時に None/False をサイレントに返す

   パターン: モジュール間でエラー処理に統一されたアプローチがない
```

**なぜ重要か**: 単一ファイルのレビューでは全体像が見えません。クロスファイル分析だけが次のことを明らかにします：
- **重複コード** — 統合すべき箇所
- **データフローのパターン** — コンポーネントがどう連携するか
- **アーキテクチャの問題** — 保守性に影響する課題

---

### デモ: 60秒でコードベースを理解する

<img src="images/codebase-understanding.png" alt="Split-screen comparison showing manual code review taking 1 hour versus AI-assisted analysis taking 10 seconds" width="800" />

New to a project? Learn about it quickly using Copilot CLI.

プロジェクトに初めて参加する場合も、Copilot CLIを使えばすぐに全体像を把握できます。

```bash
copilot

> @samples/book-app-project/
>
> 一段落で、このアプリが何をするのか、また最大の品質の問題は何かを教えてください
```

**出力例**:
```
これは、JSONファイルに保存された書籍を追加・一覧表示・削除・検索できる
CLIの書籍コレクション管理アプリです。主な品質の問題は：

1. 重複した表示ロジック — show_books() と print_books() が同じことをしている
2. 一貫性のないエラー処理 — 例外をスローするものと False を返すものが混在する
3. 入力バリデーションなし — 年に 0 が入力でき、タイトル・著者に空文字列が許可される
4. テスト不足 — find_book_by_title などの重要な関数にテストカバレッジがない

優先修正: 重複した表示関数を統合し、入力バリデーションを追加する。
```

**結果**: コードを読みに1時間かかっていた作業が10秒に短縮されます。どこに集中するかが即座にわかります。

---

## 実践例

### 例1: コンテキスト付きコードレビュー

```bash
copilot

> @samples/book-app-project/books.py このファイルの潜在的なバグをレビューしてください

# Copilot CLI はファイルの全内容を持ち、具体的なフィードバックを返せる：
# 「49行目: 大文字小文字を区別した比較により書籍を見逃す可能性があります...」
# 「29行目: JSONデコードエラーはキャッチされていますが、データ破損がログに残りません...」

> @samples/book-app-project/book_app.py についてはどうですか？

# 今度は book_app.py をレビューするが、books.py のコンテキストも引き続き保持している
```

### 例2: コードベースを理解する

```bash
copilot

> @samples/book-app-project/books.py このモジュールは何をしていますか？

# Copilot CLIがbooks.pyを読み込み、BookCollectionクラスを理解する

> @samples/book-app-project/ コードの構造の概要を教えてください

# Copilot CLIがディレクトリをスキャンしてまとめる

> アプリはどのように書籍を保存・読み込みしていますか？

# Copilot CLIがすでに見たコードをたどって回答できる
```

<details>
<summary>🎬 マルチターンの会話を確認！</summary>

![Multi-Turn Demo](images/multi-turn-demo.gif)

*デモの出力は異なります。実際のモデル、ツール、回答はここで示したものと異なる場合があります。*

</details>

### 例3: 複数ファイルのリファクタリング

```bash
copilot

> @samples/book-app-project/book_app.py @samples/book-app-project/utils.py
> 重複した表示関数 show_books() と print_books() がある。これらを統合する方法を提案してください

# Copilot CLI は両方のファイルを見ているので、重複コードのマージ方法を提案できる
```

---

## セッション管理

セッションは作業中に自動保存されます。前のセッションを再開して中断したところから続けることができます。

### セッションの自動保存

すべての会話は自動的に保存されます。通常通り終了するだけです:

```bash
copilot

> @samples/book-app-project/ すべてのモジュールにわたるエラー処理を改善しましょう

[... 作業を行う ...]

> /exit
```

### 直前のセッションを再開する

```bash
# 中断したところから続ける
copilot --continue
```

### 特定のセッションを再開する

```bash
# セッション一覧からインタラクティブに選択
copilot --resume

# またはIDで特定のセッションを再開
copilot --resume abc123
```

> 💡 **セッションIDはどうやって調べる？** 覚える必要はありません。IDなしで `copilot --resume` を実行すると、過去のセッション一覧がインタラクティブに表示され、名前・ID・最終アクティブ時刻を確認して選択できます。
>
> **複数のターミナルウィンドウがある場合は？** 各ターミナルウィンドウは独自のコンテキストを持つ独立したセッションです。3つのターミナルでCopilot CLIを開いていれば、3つの独立したセッションになります。どのターミナルからでも `--resume` を実行すると全セッションを一覧表示できます。`--continue` フラグは最後に閉じたセッションを、どのターミナルで閉じたかに関係なく再開します。
>
> **再起動せずにセッションを切り替えられる？** はい。アクティブなセッション内から `/resume` スラッシュコマンドを使います：
> ```
> > /resume
> # 切り替え先のセッション一覧を表示
> ```

### セッションに名前をつける

後で見つけやすいようにセッションに意味のある名前を付けましょう：

```bash
copilot

> /rename book-app-review
# セッションに判別しやすい名前を付ける
```

### コンテキストの確認と管理

ファイルや会話を追加するにつれて、Copilot CLIの[コンテキストウィンドウ](../GLOSSARY.md#context-window)が埋まっていきます。処理を整理するためのコマンドがいくつか用意されています：

```bash
copilot

> /context
Context usage: 62k/200k tokens (31%)

> /clear
# Abandons the current session (no history saved) and starts a fresh conversation

> /new
# Ends the current session (saving it to history for search/resume) and starts a fresh conversation

> /rewind
# Opens a timeline picker allowing you to roll back to an earlier point in your conversation
```

> 💡 **`/clear`や`/new`を使うタイミング**: books.pyのレビュー中にutils.pyの議論に切り替えたい場合は、先に/newを実行してください（セッション履歴が不要なら/clear）。そうしないと古いトピックのコンテキストが残り、回答が混乱する可能性があります。

> 💡 **間違えたり、別のアプローチを試したいとき？** `/rewind`（またはEscを2回押す）を使うと**タイムラインピッカー**が開き、最新だけでなく会話中の任意のポイントに巻き戻せます。間違った方向に進んでしまったが、最初からやり直すほどではないときに便利です。

---

### 中断したところから再開する

<img src="images/session-persistence-timeline.png" alt="Timeline showing how GitHub Copilot CLI sessions persist across days - start on Monday, resume on Wednesday with full context restored" width="800"/>

*終了時にセッションは自動保存されます。数日後に再開しても、ファイル、識別した問題、進捗がそのまま復元されます。*

数日にわたるワークフローを想像してみてください：

```bash
# 月曜日: 書籍アプリのレビューを開始
copilot

> /rename book-app-review
> @samples/book-app-project/books.py
> コード品質の問題をすべてレビューして番号を振ってください

発見した品質の問題:
1. 重複した表示関数 (book_app.py と utils.py) - 中
2. 空文字列に対する入力バリデーションなし - 中
3. 年に 0 または負の値を許容 - 低
4. 全関数に型ヒントがない - 低
5. エラーログなし - 低

> 問題 #1（重複した関数）を修正してください
# 修正に取り組む...

> /exit
```

```bash
# 水曜日: 中断したところから再開
copilot --continue

> 書籍アプリのレビューで未修正の問題は何ですか？

book-app-review セッションの残り課題:
2. 空文字列に対する入力バリデーションなし - 中
3. 年に 0 または負の値を許容 - 低
4. 全関数に型ヒントがない - 低
5. エラーログなし - 低

問題 #1（重複した関数）は月曜日に修正済み。

> 次は問題 #2 に取り組みましょう
```

**これが強力な理由**: 数日後でもCopilot CLIは以下を記憶しています:
- 作業中の完全なファイル
- 番号付き識別した問題の一覧
- すでに対応済みの問題
- 会話のコンテキスト

再説明不要。ファイルの再読み込み不要。すぐ作業を再開できます。

---

**🎉 基本が身につきました！** `@`構文、セッション管理（`--continue`/`--resume`/`/rename`）、コンテキストコマンド（`/context`/`/clear`）だけで十分に生産性が高まります。以下はオプションです。準備ができたら戻ってください。

---

# オプション: さらに深く潜る

<img src="images/optional-going-deeper.png" alt="Abstract crystal cave in blue and purple tones representing deeper exploration of context concepts" width="800"/>

上記の基本を踏まえた発展的なトピックです。**兴味のある項目を選んで、または[実践](#practice)にスキップしてください。**

| 学びたい内容 | ジャンプ先 |
|---|---|
| ワイルドカードパターンと高度なセッションコマンド | [追加の@パターンとセッションコマンド](#additional-patterns) |
| 複数プロンプトでコンテキストを積み重ねる | [コンテキスト対応の会話](#context-aware-conversations) |
| トークン限界と`/compact` | [コンテキストウィンドウを理解する](#understanding-context-windows) |
| 参照するファイルの選び方 | [参照对象の選択方法](#choosing-what-to-reference) |
| スクリーンショットやモックアップの分析 | [画像を使って作業する](#working-with-images) |

<details>
<summary><strong>その他の@パターンとセッションコマンド</strong></summary>
<a id="additional-patterns"></a>

### その他の@パターン

上級ユーザー向けに、Copilot CLIはワイルドカードパターンと画像参照をサポートしています:

| Pattern | What It Does |
|---------|--------------|
| `@folder/*.py` | All .py files in folder |
| `@**/test_*.py` | Recursive wildcard: find all test files anywhere |
| `@image.png` | Image file for UI review |

```bash
copilot

> @samples/book-app-project/**/*.py内の全TODOコメントを見つけてください
```

### セッション情報の表示

```bash
copilot

> /session
# Shows current session details and workspace summary

> /usage
# Shows session metrics and statistics
```

### セッションを共有する

```bash
copilot

> /share file ./my-session.md
# Exports session as a markdown file

> /share gist
# Creates a GitHub gist with the session

> /share html
# Exports session as a self-contained interactive HTML file
# Useful for sharing polished session reports with teammates or saving for reference
```

</details>

<details>
<summary><strong>コンテキスト対応の会話</strong></summary>
<a id="context-aware-conversations"></a>

### コンテキスト対応の会話

互いに積み重なるマルチターンの会話で本当の威力が発掮されます。

#### 例: 段階的改善

```bash
copilot

> @samples/book-app-project/books.py BookCollection クラスをレビューしてください

Copilot CLI: 「クラスは機能していますが、以下が気になります:
1. 一部メソッドに型ヒントがない
2. 空のタイトル/著者のバリデーションなし
3. エラーハンドリングの改善が望ましい」

> すべてのメソッドに型ヒントを追加してください

Copilot CLI: 「型ヒントを全メソッドに追加したクラスです...」
[型付きバージョンを表示]

> 次にエラー処理を改善してください

Copilot CLI: 「型付きバージョンを基に、エラーハンドリングを改善しました...」
[バリデーションと適切な例外追加]

> 次にエラー処理を改善してください

Copilot CLI: 「型付きバージョンを基に、エラーハンドリングを改善しました...」
[バリデーションと適切な例外追加]

> この最終バージョンのテストを生成してください

Copilot CLI: 「型とエラーハンドリング付きクラスを基に...」
[包括的なテストを生成]
```

各プロンプトが前の作業を引き継いでいることに注目してください。これがコンテキストの強みです。

</details>

<details>
<summary><strong>コンテキストウィンドウを理解する</strong></summary>
<a id="understanding-context-windows"></a>

### コンテキストウィンドウを理解する

基本編で`/context`と`/clear`は学びました。コンテキストウィンドウの仕組みの詳細を説明します。

すべてのAIには「コンテキストウィンドウ」があります。これは一度に処理できるテキストの量です。

<img src="images/context-window-visualization.png" alt="Context Window Visualization" width="800"/>

*コンテキストウィンドウは机の上のようなもの: 一度に乗せられる量に限りがあります。ファイル、会話履歴、システムプロンプトがすべてスペースを消費します。*

#### 限界に達したときの挙動

```bash
copilot

> /context

Context usage: 45,000 / 128,000 tokens (35%)

# As you add more files and conversation, this grows

> @large-codebase/

Context usage: 120,000 / 128,000 tokens (94%)

# Warning: Approaching context limit

> @another-large-file.py

Context limit reached. Older context will be summarized.
```

#### `/compact`コマンド

コンテキストが埋まりかけているが会話を失いたくない場合、`/compact`で履歴を要約してトークンを空けます:

```bash
copilot

> /compact
# 会話の履歴を要約してコンテキストのスペースを確保する
# 重要な発見や決定事項は保持される
```

#### コンテキスト効率化のヒント

| 状況 | 操作 | 理由 |
|-----------|--------|-----|
| 新しいトピックに切り替える | `/clear` | 不要なコンテキストを削除 |
| 間違った方向に進んだ | `/rewind` | 任意の時点に巻き戻す |
| 長い会話 | `/compact` | 履歴を要約しトークンを空きにする |
| 特定ファイルが必要 | `@folder/`でなく`@file.py` | 必要なものだけ読み込む |
| 限界に近づいた | `/new`または`/clear` | コンテキストをリセット |
| 複数トピック | トピックごとに`/rename`を使用 | 適切なセッションを簡単に再開 |

#### 大規模コードベースでのベストプラクティス

1. **具体的に**: `@samples/book-app-project/`でなく`@samples/book-app-project/books.py`
2. **トピック間でコンテキストをリセット**: 焦点を切り替えるときは`/new`または`/clear`
3. **`/compact`を活用する**: 会話を要約してコンテキストを空きにする
4. **複数セッションを使う**: 機能またはトピックごとに1セッション

</details>

<details>
<summary><strong>参照対象の選択方法</strong></summary>
<a id="choosing-what-to-reference"></a>

### 参照対象の選択方法

コンテキストにおけるファイルの価値は等しくありません。賢く選択する方法を説明します。

#### ファイルサイズの考慮

| ファイルサイズ | およその[トークン](../GLOSSARY.md#token)数 | 戦略 |
|-----------|-------------------|----------|
| 小（100行未満） | ~500−1,500 | 自由に参照 |
| 中（100−500行） | ~1,500−7,500 | 特定ファイルを参照 |
| 大（500行以上） | 7,500+ | 慎重に選択し、特定ファイルを使用 |
| 非常に大きい（1000行以上） | 15,000+ | 分割または特定セクションを対象に |

**具体例:**
- 書籍アプリの4つのPythonファイル合計 ≈ 2,000−3,000トークン
- 標準的なPythonモジュール（200行） ≈ 3,000トークン
- Flask APIファイル（400行） ≈ 6,000トークン
- package.json ≈ 200−500トークン
- シンプルなプロンプト+応答 ≈ 500−1,500トークン

> 💡 **コードの簡単な目安:** 行数×15でおよそのトークン数が推定できます（あくまで目安です）。

#### 含めるべきもの vs 除外すべきもの

**高価値**（含める）:
- エントリポイント（`book_app.py`、`main.py`、`app.py`）
- 質問対象のファイル
- 対象ファイルが直接インポートするファイル
- 設定ファイル（`requirements.txt`、`pyproject.toml`）
- データモデルやデータクラス

**低価値**（除外を検討）:
- 生成ファイル（コンパイル済み出力、バンドル済みアセット）
- Node modulesやベンダーディレクトリ
- 大きなデータファイルやフィクスチャ
- 質問と無関係なファイル

#### 特定度のスペクトル

```
漠然 ────────────────────────► 具体的
@samples/book-app-project/                      @samples/book-app-project/books.py:47-52
     │                                       │
     └─ 全体をスキャン                     └─ 必要な部分だけ
        （より多くのコンテキストを消費）             （コンテキストを節約）
```

**広く指定する場合**（`@samples/book-app-project/`）:
- コードベースの初期探索
- 多数ファイルにまたがるパターンを見つける
- アーキテクチャレビュー

**特定する場合**（`@samples/book-app-project/books.py`）:
- 特定の問題のデバッグ
- 特定ファイルのコードレビュー
- 単一関数についてうずねる

#### 実践例: 段階的なコンテキスト読み込み

```bash
copilot

# ステップ1: 構造から始める
> @package.json このプロジェクトはどのフレームワークを使っていますか？

# ステップ2: 回答に従って絞り込む
> @samples/book-app-project/ プロジェクトの構造を見せてください

# ステップ3: 重要な部分に焦点を当てる
> @samples/book-app-project/books.py BookCollection クラスをレビューしてください

# ステップ4: 必要なときだけ関連ファイルを追加
> @samples/book-app-project/book_app.py @samples/book-app-project/books.py CLIは BookCollection をどのように使っていますか？
```

この段階的なアプローチでコンテキストを集中し、効率的に使えます。

</details>

<details>
<summary><strong>画像を使って作業する</strong></summary>
<a id="working-with-images"></a>

### 画像を使って作業する

`@`構文を使って会話に画像を含めるか、クリップボードから**ペースト**（Cmd+V / Ctrl+V）で入力できます。Copilot CLIはスクリーンショット、モックアップ、図表を分析してUIデバッグ、デザイン実装、エラー分析を支援します。

```bash
copilot

> @images/screenshot.png この画像で何が起きていますか？

> @images/mockup.png このデザインに合わせたHTMLとCSSを書いてください。index.htmlという新しいファイルに置き、CSSはstyles.cssに入れてください。
```

> 📖 **詳細情報**: サポートされているフォーマット、実践的なユースケース、画像とコードを組み合わせるヒントは[追加コンテキスト機能](../appendices/additional-context.md#working-with-images)を参照してください。

</details>

---

# 実践

<img src="../images/practice.png" alt="Warm desk setup with monitor showing code, lamp, coffee cup, and headphones ready for hands-on practice" width="800"/>

Time to apply your context and session management skills.

---

## ▶️ 試してみよう

### プロジェクト全体のレビュー

コースにはそのまま使えるサンプルファイルが含まれています。copilotを起動して次のプロンプトを実行してみましょう:

```bash
copilot

> @samples/book-app-project/ このプロジェクトのコード品質をレビューしてください

# Copilot CLI が以下のような問題を特定する:
# - 重複した表示関数
# - 入力バリデーションの欠如
# - 一貫性のないエラー処理
```

> 💡 **自分のファイルで試したい場合は？** 小さなPythonプロジェクトを作り（`mkdir -p my-project/src`）、.pyファイルをいくつか追加して、`@my-project/src/`でレビューしてみましょう。サンプルコードの作成もCopilotに頼めます！

### セッションワークフロー

```bash
copilot

> /rename book-app-review
> @samples/book-app-project/books.py 空のタイトルに対する入力バリデーションを追加しましょう

[Copilot CLI がバリデーションアプローチを提案する]

> その修正を実装してください
> 次に @samples/book-app-project/ の重複した表示関数を統合してください
> /exit

# 後で — 中断したところから再開する
copilot --continue

> 行った変更のテストを生成してください
```

---

デモを完了したら、以下のバリエーションも試してみましょう:

1. **クロスファイルチャレンジ**: book_app.pyとbooks.pyがどのように連携しているか分析する:
   ```bash
   copilot
   > @samples/book-app-project/book_app.py @samples/book-app-project/books.py
   > これらのファイルの関係は何ですか？コードスメルはありますか？
   ```

2. **セッションチャレンジ**: セッションを開始して`/rename my-first-session`で名前をつけ、何か作業して`/exit`で終了し、`copilot --continue`を実行してみましょう。作業内容を覚えていますか？

3. **コンテキストチャレンジ**: セッション中に`/context`を実行してみましょう。何トークン使っていますか？`/compact`を試して再確認してみましょう。（詳細は「もっと詳しく」の[コンテキストウィンドウを理解する](#understanding-context-windows)を参照。）

**自己チェック**: `@folder/`が個々のファイルを開くよりも強力な理由を説明できたら、コンテキストを理解しています。

---

## 📝 課題

### メインチャレンジ: データフローを追う

ハンズオン例ではコード品質レビューと入力バリデーションに焦点を当てました。今度は同じコンテキストスキルを別のタスク（データがアプリ内をどう流れるか追う）に活用しましょう:

1. インタラクティブセッションを開始: `copilot`
2. `books.py`と`book_app.py`を一緒に参照:
   `@samples/book-app-project/books.py @samples/book-app-project/book_app.py ユーザー入力から書籍がdata.jsonに保存されるまでの流れを追ってください。各ステップでどの関数が関わりますか？`
3. データファイルも追加してコンテキストを補強:
   `@samples/book-app-project/data.json このJSONファイルが欠損または破損した場合、どうなりますか？どの関数が失敗しますか？`
4. クロスファイルの改善案を求める:
   `@samples/book-app-project/books.py @samples/book-app-project/utils.py 両方のファイルで機能する一貫したエラー処理戦略を提案してください。`
5. セッションの名前変更: `/rename data-flow-analysis`
6. `/exit`で終了し、`copilot --continue`で再開してデータフローについてフォローアップの質問をする

**成功基準**: 複数ファイルをまたいでデータを追跡し、名前付きセッションを再開し、クロスファイルの提案を得ることができる。

<details>
<summary>💡 ヒント（クリックして展開）</summary>

**始め方:**
```bash
cd /path/to/copilot-cli-for-beginners
copilot
> @samples/book-app-project/books.py @samples/book-app-project/book_app.py ユーザー入力から data.json への書籍保存の流れを追ってください。
> @samples/book-app-project/data.json このファイルが欠損または破損した場合、どうなりますか？
> /rename data-flow-analysis
> /exit
```

その後 `copilot --continue` で再開する

**便利なコマンド:**
- `@file.py` - 単一ファイルを参照する
- `@folder/` - フォルダ内のすべてのファイルを参照する（末尾の`/`に注意）
- `/context` - コンテキストの使用量を確認する
- `/rename <name>` - セッションに名前をつけて再開しやすくする

</details>

### ボーナスチャレンジ: コンテキスト限界

1. `@samples/book-app-project/`で書籍アプリのファイルをすべて一度に参照する
2. 異なるファイルについて詳細な質問をいくつかする（`books.py`、`utils.py`、`book_app.py`、`data.json`）
3. `/context`を実行してトークン使用量を確認する。どのくらい早く埋まりますか？
4. `/compact`を使ってスペースを回収し、会話を続ける
5. ファイル参照をより具体的にして（例: フォルダ全体でなく`@samples/book-app-project/books.py`）コンテキスト使用量への影響を確認する

---

<details>
<summary>🔧 <strong>よくある間違いとトラブルシューティング</strong>（クリックして展開）</summary>

### よくある間違い

| 間違い | 何が起きるか | 対処法 |
|---------|--------------|-----|
| ファイル名の前に`@`を忘れる | Copilot CLIは「books.py」を通常テキストとして扱う | ファイル参照には`@samples/book-app-project/books.py`を使う |
| セッションが自動的に持続すると思い込む | `copilot`を新たに起動するとすべての前のコンテキストが失われる | `--continue`（最後のセッション）または`--resume`（セッション選択）を使う |
| 現在のディレクトリ外のファイルを参照する | 「Permission denied」または「File not found」エラー | `/add-dir /path/to/directory`でアクセスを許可する |
| トピック切り替え時に`/clear`を使わない | 古いコンテキストが新しいトピックの応答を混乱させる | 別のタスクを開始する前に`/clear`を実行する |

### トラブルシューティング

**「File not found」エラー** - 正しいディレクトリにいるか確認:

```bash
pwd  # 現在のディレクトリを確認
ls   # ファイルを一覧表示

# その後copilotを起動し、相対パスを使用
copilot

> @samples/book-app-project/books.py をレビューしてください
```

**「Permission denied」** - ディレクトリを許可リストに追加:

```bash
copilot --add-dir /path/to/directory

# またはセッション内で:
> /add-dir /path/to/directory
```

**コンテキストが早く埋まりすぎる**:
- ファイル参照をより具体的にする
- 異なるトピック間で`/clear`を使う
- 作業を複数のセッションに分割する

</details>

---

# まとめ

## 🔑 重要なポイント

1. **`@`構文**でCopilot CLIにファイル、ディレクトリ、画像のコンテキストを提供できる
2. **マルチターン会話**はコンテキストが蓄積されるにつれて積み重なる
3. **セッションは自動保存**: `--continue`または`--resume`で中断した箇所から再開できる
4. **コンテキストウィンドウ**には限りがある: `/clear`、`/compact`、`/context`、`/new`、`/rewind`で管理する
5. **パーミッションフラグ**（`--add-dir`、`--allow-all`）は複数ディレクトリへのアクセスを制御する。適切に使おう！
6. **画像参照**（`@screenshot.png`）でUIの問題を視覚的にデバッグできる

> 📚 **公式ドキュメント**: [Use Copilot CLI](https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli) でコンテキスト、セッション、ファイル操作の完全なリファレンスを参照してください。

> 📋 **クイックリファレンス**: [GitHub Copilot CLI command reference](https://docs.github.com/en/copilot/reference/cli-command-reference) でコマンドとショートカットの完全なリストを参照してください。

---

## ➡️ What's Next

Copilot CLIにコンテキストを与えられるようになったので、実際の開発タスクに活かしましょう。今学んだコンテキストのテクニック（ファイル参照、クロスファイル分析、セッション管理）は次章の強力なワークフローの基盤となります。

**[第03章: 開発ワークフロー](../03-development-workflows/README.md)** では以下を学びます:

- コードレビューワークフロー
- リファクタリングパターン
- デバッグ支援
- テスト生成
- Git連携

---

**[← Back to Chapter 01](../01-setup-and-first-steps/README.md)** | **[Continue to Chapter 03 →](../03-development-workflows/README.md)**
