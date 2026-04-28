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

# Chapter 03: 開発ワークフロー

**あなたが気づかなかったバグもAIが見つけてくれたらどうでしょう？**

テスト、リファクタリング、デバッグ、Git — 日々のワークフローを強化

> ⏱️ 所要時間の目安: 約60分（15分読む + 45分ハンズオン）

---

## 🎯 学習目標

この章を終えると：

- Copilot CLIで包括的なコードレビューを実行できる
- レガシーコードを安全にリファクタリングできる
- AIサポートで問題をデバッグできる
- テストを自動生成できる
- Copilot CLIをgitワークフローに統合できる

---

## 🧩 アナロジー: 大工のワークフロー

<img src="images/carpenter-workflow-steps.png" alt="大工のワークフロー" width="60%"/>

大工はツールの使い方だけでなく、仕事に応じた**ワークフロー**を持っています

開発者も同様。GitHub Copilot CLIは各ワークフローを強化します

---

## 5つのワークフロー

<img src="images/five-workflows.png" alt="5つのワークフロー" width="60%"/>

| やりたいこと | ワークフロー |
|---|---|
| コードをレビューする | WF1: コードレビュー |
| レガシーコードを整理する | WF2: リファクタリング |
| バグを追いかけて修正する | WF3: デバッグ |
| テストを自動生成する | WF4: テスト生成 |
| コミットとPRを書く | WF5: Git連携 |

各ワークフローは**独立**。必要なものだけ選んで実践できます

---

## WF1: コードレビュー 🔍

### 基本レビュー

```bash
copilot

> @samples/book-app-project/book_app.py
  のコード品質をレビューしてください
```

![Code Review Demo](images/code-review-demo.gif)

--

## WF1: プロジェクト全体レビュー

```bash
copilot

> @samples/book-app-project/ このプロジェクト全体をレビュー。
  見つかった問題を重大度別に分類したチェックリストを作成
```

出力例:
- **重大**: データ損失リスク、クラッシュ
- **高**: バグ、誤った動作
- **中**: パフォーマンス、保守性
- **低**: スタイル、小さな改善

--

## WF1: /review コマンド

`/review`は組み込み**code-review agent**を呼び出します

```bash
copilot

> /review
# ステージ済み/未ステージの変更をレビュー

> /review 認証におけるセキュリティ問題を確認
# 特定フォーカスでレビュー
```

> 💡 `/review` は保留中の変更があるときに最も効果的。
> `git add` でファイルをステージしてから実行

---

## WF2: リファクタリング 🔧

```bash
copilot

> @samples/book-app-project/book_app.py
  コマンド処理のif/elif連鎖を辞書ディスパッチパターンに書き換えて
```

![Refactor Demo](images/refactor-demo.gif)

--

## WF2: 安全なリファクタリング

テスト → リファクタリングのパターン：

```bash
copilot

> @samples/book-app-project/books.py
  リファクタリング前に現在の動作のテストを生成して

# まずテストを取得

> では BookCollection クラスをファイル操作に
  コンテキストマネージャーを使うよう書き換えて

# テストで動作が保持されていることを検証
```

> 💡 **初心者向け**: 型ヒントの追加や変数名改善などシンプルな依頼から始めましょう

---

## WF3: デバッグ 🐛

```bash
copilot

> @samples/book-app-buggy/books_buggy.py
  「The Hobbit」を検索しても結果が返らないと報告あり。
  データに存在するのになぜかデバッグして
```

![Fix Bug Demo](images/fix-bug-demo.gif)

--

## WF3: バグ探偵

```bash
copilot

> @samples/book-app-buggy/books_buggy.py
  ユーザー報告: "著者の部分一致で検索できない"
  なぜこれが起きるかデバッグして
```

Copilot CLIが**根本原因**を特定：

```
80行目: 完全一致(==)の代わりに部分一致(in)が必要

修正: return [b for b in self.books
             if author.lower() in b.author.lower()]
```

> 💡 **ボーナス**: ファイル全体を分析するため、尋ねていない別の問題も発見することが多い

--

## WF3: エラーの理解

スタックトレースを貼り付けてデバッグ：

```bash
copilot

> このエラーが発生しています:
> AttributeError: 'NoneType' object has no attribute 'title'
>     at show_books (book_app.py:19)
>
> @samples/book-app-project/book_app.py
> なぜ発生するのか、修正方法を教えて
```

---

## WF4: テスト生成 🧪

```bash
copilot

> @samples/book-app-project/books.py
  エッジケースを含む包括的なpytestテストを生成
```

![Test Generation Demo](images/test-gen-demo.gif)

--

## WF4: テスト爆発 — 2テスト vs 15+テスト

手動: 2-3テスト → Copilot CLI: **15+テスト**

```python
class TestBookCollection:
    # ハッピーパス
    def test_add_book_creates_new_book(self): ...
    def test_list_books_returns_all_books(self): ...

    # エッジケース
    def test_add_book_with_empty_title(self): ...
    def test_remove_nonexistent_book(self): ...
    def test_load_books_handles_corrupted_json(self): ...

    # 特殊文字
    def test_add_book_with_unicode_characters(self): ...
```

**30秒で、1時間分のエッジケーステストが完成**

--

## WF4: テストの実行

```bash
copilot

> テストを実行するにはどうすればいいですか？

# Copilot CLIの応答:
# cd samples/book-app-project
# python -m pytest tests/ -v
```

---

## WF5: Git連携 📦

### コミットメッセージの自動生成

```bash
# Conventional Commitフォーマットで生成
copilot -p "次の変更のコミットメッセージを生成:
  $(git diff --staged)"
```

![Git Integration Demo](images/git-integration-demo.gif)

--

## WF5: PR操作

```bash
copilot

# PRを操作する組み込みコマンド
> /pr view     # 現在のPRを表示
> /pr create   # PRを作成
> /pr fix      # PRのレビューコメントに対応
> /pr auto     # 自動修正

# 自然言語でも可能
> "機能: 検索機能の追加"というタイトルでPRを作成して
```

--

## WF5: /diff と /delegate

```bash
copilot

# ローカルの変更を確認
> /diff

# タスクをクラウドエージェントに引き渡す
> /delegate この修正をCopilotクラウドエージェントに任せて
```

---

## 💡 クイックヒント: リサーチ

コードを書く前に調査する：

```bash
copilot

> /research Pythonの大文字小文字を無視した
  文字列マッチングのベストプラクティス
```

GitHubとWebで深い調査を実行し、コードを書く前に最適なアプローチを把握

---

## ▶️ 実践

<img src="../images/practice.png" alt="Practice" width="60%"/>

--

## 実践1: コードレビュー

```bash
copilot

> @samples/book-app-project/ このプロジェクト全体をレビューして、
  重大度別にマークダウンチェックリストを作成
```

--

## 実践2: デバッグ

```bash
copilot

> @samples/book-app-buggy/books_buggy.py
  1冊だけ既読にしようとすると全ての本が既読になる。
  バグは何ですか？
```

--

## 実践3: テスト生成

```bash
copilot

> @samples/book-app-project/books.py
  以下のテストを含む包括的なpytestテストを生成:
> - 書籍の追加・削除
> - タイトル/著者で検索
> - 既読マーク
> - 空データのエッジケース
```

---

## 📝 課題: バグ修正ワークフロー

バグ調査 → 原因特定 → 修正 → テスト → コミットの全フローを実践：

1. `@samples/book-app-buggy/books_buggy.py` でバグを3つ見つける
2. 各バグの根本原因を特定して修正案を取得
3. 修正後のコードのテストを生成
4. コミットメッセージを自動生成

--

## チャレンジ

- **セキュリティ監査**: `@samples/buggy-code/python/user_service.py` のセキュリティ脆弱性をすべて発見

- **自動化スクリプト**: Programmaticモードで複数ファイルを一括レビューするスクリプトを書いてみましょう
