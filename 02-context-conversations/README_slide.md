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

# Chapter 02: コンテキストと会話

**AIがファイル1つではなく、コードベース全体を見れたらどうでしょうか？**

`@`構文でCopilot CLIにコードベースを深く理解させましょう

> ⏱️ 所要時間の目安: 約50分（20分読む + 30分ハンズオン）

---

## 🎯 学習目標

この章を終えると：

- `@`構文でファイル、ディレクトリ、画像を参照できる
- `--resume`と`--continue`で前のセッションを再開できる
- コンテキストウィンドウの仕組みを理解できる
- 効果的なマルチターンの会話を書ける

---

## 🧩 アナロジー: 同僚との作業

<img src="images/colleague-context-analogy.png" alt="Context Makes the Difference" width="60%"/>

同僚にバグを説明するとき：

> **コンテキストなし**: 「書籍アプリが動かない」

> **コンテキストあり**: 「`books.py`の`find_book_by_title`関数を見て。大文字小文字を区別しない一致ができていない」

Copilot CLIにも**コンテキスト**を提供することで、的確な回答が得られます

---

## @ 構文 — ファイルを参照する

`@`シンボルでプロンプト内からファイルやディレクトリを参照できます

```bash
copilot

> @package.json が何をするか説明してください
> @README.md を要約してください
> @.gitignore に何が書かれていて、なぜ必要なのですか？
```

> 💡 **プロジェクトが手元にない？** テストファイルを作って試しましょう：
> `echo "def greet(name): return 'Hello ' + name" > test.py`

--

## 基本的な@パターン

| パターン | 機能 | 使用例 |
|---------|------|--------|
| `@file.py` | 単一ファイル参照 | `@books.py をレビュー` |
| `@folder/` | ディレクトリ全体 | `@samples/book-app-project/ をレビュー` |
| `@file1 @file2` | 複数ファイル | `@book_app.py と @books.py を比較` |

--

## 単一ファイルの参照

```bash
copilot

> @samples/book-app-project/utils.py が何をするか説明してください
```

![File Context Demo](images/file-context-demo.gif)

--

## 複数ファイルの参照

```bash
copilot

> @samples/book-app-project/book_app.py と
  @samples/book-app-project/books.py の一貫性を比較してください
```

### ディレクトリ全体の参照

```bash
copilot

> @samples/book-app-project/ のすべてのファイルの
  エラー処理をレビューしてください
```

---

## クロスファイルのインテリジェンス

<img src="images/cross-file-intelligence.png" alt="Cross-File Intelligence" width="60%"/>

単一ファイルの分析 → 有益

**クロスファイル分析 → 革命的**

--

## デモ: 複数ファイルにまたがるバグを発見

```bash
copilot

> @samples/book-app-project/book_app.py
  @samples/book-app-project/books.py
  これらのファイルはどのように連携していますか？
```

![Multi-File Demo](images/multi-file-demo.gif)

--

## クロスファイル分析で発見できること

```
クロスモジュール分析
===================
1. データフローのパターン
   book_app.py（UI） → books.py（ロジック） → data.json（ストレージ）

2. 重複する表示関数
   book_app.py の show_books() と utils.py の print_books()

3. 一貫性のないエラー処理
   book_app.py は ValueError を処理
   books.py はサイレントに None/False を返す
```

---

## 60秒でコードベースを理解する

<img src="images/codebase-understanding.png" alt="AI-assisted analysis" width="60%"/>

```bash
copilot

> @samples/book-app-project/
  このアプリが何をするか、最大の品質問題は何か教えてください
```

**結果**: コードを読むのに1時間 → AIなら**10秒**

---

## セッション管理

セッションは**自動保存**されます

### 直前のセッションを再開

```bash
copilot --continue
```

### 特定のセッションを再開

```bash
copilot --resume     # 一覧からインタラクティブに選択
copilot --resume abc123   # IDで直接再開
```

--

## セッションに名前をつける

```bash
copilot

> /rename book-app-review
```

### コンテキストの確認と管理

```bash
> /context    # トークン使用量を表示
> /clear      # セッション破棄して新規開始
> /new        # セッション保存して新規開始
> /rewind     # タイムラインで任意のポイントに巻き戻し
> /compact    # 履歴を要約してトークンを節約
```

--

## 数日にわたるセッション活用例

<img src="images/session-persistence-timeline.png" alt="Session Persistence" width="60%"/>

```bash
# 月曜日: レビュー開始
copilot
> /rename book-app-review
> @samples/book-app-project/books.py の品質問題をレビューして

> /exit
```

```bash
# 水曜日: 続きから再開
copilot --continue
> 未修正の問題は何ですか？
```

**Copilotが記憶**: ファイル、問題リスト、進捗をすべて保持

---

## その他の@パターン（応用）

| パターン | 機能 |
|---------|------|
| `@folder/*.py` | フォルダ内の全.pyファイル |
| `@**/test_*.py` | 再帰的にテストファイルを検索 |
| `@image.png` | 画像ファイルのUI分析 |

### セッション共有

```bash
> /share file ./my-session.md   # Markdownにエクスポート
> /share gist                    # GitHub Gistを作成
> /share html                    # HTMLファイルにエクスポート
```

---

## コンテキストウィンドウを理解する

<img src="images/context-window-visualization.png" alt="Context Window" width="60%"/>

*コンテキストウィンドウは机の上のようなもの: 一度に乗せられる量に限りがあります*

| 状況 | 操作 | 理由 |
|------|------|------|
| 新トピックに切替 | `/clear` | 不要なコンテキストを削除 |
| 長い会話 | `/compact` | 履歴を要約 |
| 限界に近づいた | `/new` | リセット |
| 特定ファイルが必要 | `@file.py` | 必要なものだけ読み込む |

---

## ▶️ 実践

<img src="../images/practice.png" alt="Practice" width="60%"/>

学んだことを実際に試してみましょう！

--

## 実践1: コンテキスト付きコードレビュー

```bash
copilot

> @samples/book-app-project/books.py
  このファイルの潜在的なバグをレビューしてください

> @samples/book-app-project/book_app.py についてはどうですか？
```

> 💡 Copilotが `books.py` のコンテキストを保持したまま
> `book_app.py` もレビューできることに注目

--

## 実践2: マルチターン改善

```bash
copilot

> @samples/book-app-project/books.py BookCollectionクラスをレビュー

> すべてのメソッドに型ヒントを追加してください

> エラー処理を改善してください

> この最終バージョンのテストを生成してください
```

各プロンプトが**前の作業を引き継ぐ**ことを確認してください

--

## 実践3: セッション管理

```bash
copilot

> /rename my-first-review
> @samples/book-app-project/ の全体像を把握して
> /exit

# 後日再開
copilot --continue
> 前回のレビュー結果を要約して
```

---

## 📝 課題

1. `@samples/book-app-project/` でプロジェクト全体をレビューし、問題を重大度別にリスト化
2. `@book_app.py @books.py` でクロスファイル分析を行い、データフローを把握
3. セッション管理（`/rename`、`/exit`、`--continue`）を活用して翌日に再開
4. `/context` でトークン使用量を確認し、`/compact` で最適化

--

## チャレンジ

- **セキュリティ分析**: `@samples/buggy-code/python/user_service.py @samples/buggy-code/python/payment_processor.py` で両ファイルにまたがるセキュリティ脆弱性を発見してください

- **コンテキスト最適化**: 大規模プロジェクトで `/context` → `/compact` → `/context` を実行し、トークン削減量を確認してください
