![Chapter 03: Development Workflows](images/chapter-header.png)

> **あなたが気づかなかったバグもAIが見つけてくれたらどうでしょう？**

この章では、GitHub Copilot CLIが実務ディリバーになります。テスト、リファクタリング、デバッグ、Gitなど、日々のワークフローの中で広く活用できます。

## 🎯 学習目標

この章を終えると、以下のことができるようになります：

- Copilot CLIで包括的なコードレビューを実行する
- レガシーコードを安全にリファクタリングする
- AIサポートで問題をデバッグする
- テストを自動生成する
- Copilot CLIをgitワークフローに統合する

> ⏱️ **所要時間の目安**: 約60分（15分読む + 45分ハンズオン）

---

## 🧩 現実世界のアナロジー: 大工のワークフロー

大工はツールの使い方を知っているだけでなく、さまざまな仕事に法した*ワークフロー*を持っています:

<img src="images/carpenter-workflow-steps.png" alt="工房のイメージ: 家具制作（計測、切断、組み立て、仕上げ）、損傷修復（評価、撤去、修復、マッチング）、品質確認（検査、接合部テスト、水平確認）の3レーン" width="800"/>

同様に、開発者はさまざまなタスクに対するワークフローを持っています。GitHub Copilot CLIはそれぞれのワークフローを強化し、日常のコーディング作業をより効率的かつ効果的にします。

---

# 5つのワークフロー

<img src="images/five-workflows.png" alt="コードレビュー、テスト、デバッグ、リファクタリング、git連携ワークフローを表すネオン光るアイコン5つ" width="800"/>

以下の各ワークフローは独立しています。現在のニーズに合ったものを選んで、全部通して読んでも構いません。

---

## コースの進め方

この章では開発者が日常的に利用す5つのワークフローを紹介します。**ただし、全部を一度に読む必要はありません！** 各ワークフローは以下の折りたたみセクション内で独立しています。現在のプロジェクトに最も適したものを選んでください。後で他のものにも戻れます。

<img src="images/five-workflows-swimlane.png" alt="5つの開発ワークフロー: コードレビュー、リファクタリング、デバッグ、テスト生成、Git連携が水平スイムレーンで表示された図" width="800"/>

| やりたいこと... | ジャンプ先 |
|---|---|
| マージ前にコードをレビューする | [ワークフロー1: コードレビュー](#workflow-1-code-review) |
| 混乱したまたはレガシーコードを整理する | [ワークフロー2: リファクタリング](#workflow-2-refactoring) |
| バグを追いかけて修正する | [ワークフロー3: デバッグ](#workflow-3-debugging) |
| コードのテストを自動生成する | [ワークフロー4: テスト生成](#workflow-4-test-generation) |
| より良いコミットとPRを書く | [ワークフロー5: Git連携](#workflow-5-git-integration) |
| コーディング前に調査する | [クイックヒント: 計画・コード化前のリサーチ](#quick-tip-research-before-you-plan-or-code) |
| バグ修正ワークフロー全体を見る | [総まとめ: バグ修正ワークフロー](#putting-it-all-together-bug-fix-workflow) |

**以下のワークフローを選んで展開**し、GitHub Copilot CLIがその分野でどのように開発プロセスを強化できるかを確認しましょう。

---

<a id="workflow-1-code-review"></a>
<details>
<summary><strong>ワークフロー1: コードレビュー</strong> - ファイルのレビュー、/review agentの利用、重大度別チェックリスト作成</summary>

<img src="images/code-review-swimlane-single.png" alt="コードレビューワークフロー: レビュー、課題の特定、優先度付け、チェックリスト作成。" width="800"/>

### 基本レビュー

この例では`@`シンボルでファイルを参照し、Copilot CLIがレビュー向けにその内容に直接アクセスできるようにしています。

```bash
copilot

> @samples/book-app-project/book_app.py のコード品質をレビューしてください
```

---

<details>
<summary>🎤 動作を見る！</summary>

![Code Review Demo](images/code-review-demo.gif)

*デモの出力は異なる場合があります。モデル、ツール、応答内容は図示と異なる可能性があります。*

</details>

---

### 入力バリデーションのレビュー

プロンプト内で気になるカテゴリを列挙することで、Copilot CLIに特定の関心事（ここでは入力バリデーション）に焦点を当てるよう依頼します。

```text
copilot

> @samples/book-app-project/utils.py の入力バリデーションの問題をレビューしてください。以下を确認してください: バリデーションの欠如、エラー処理の詳細、エッジケース
```


### プロジェクト全体のクロスファイルレビュー

`@`でディレクトリ全体を参照することで、Copilot CLIがプロジェクト内の全ファイルを一度にスキャンできます。

```bash
copilot

> @samples/book-app-project/ このプロエクト全体をレビューしてください。見つかった問題を重大度別に分類したマークダウンチェックリストを作成してください
```

### インタラクティブなコードレビュー

マルチターンの会話で深掘りします。広範なレビューから始め、セッションを再起動せずに追加質問で主務きます。

```bash
copilot

> @samples/book-app-project/book_app.py このファイルを以下の観点でレビューしてください:
> - 入力バリデーション
> - エラー処理
> - コードスタイルとベストプラクティス

# Copilot CLIが詳細なレビューを提供

> ユーザー入力処理について — 見逃しているエッジケースはありますか？

# Copilot CLIが空文字や特殊文字に関する潜在的な問題を表示

> 見つかった問題すべてのチェックリストを重大度順に作成してください

# Copilot CLIが優先度付きアクションアイテムを生成
```

### レビューチェックリストテンプレート

Copilot CLIに特定の形式（ここでは重大度別のmardownチェックリスト）で出力を構造化するよう依頼します。イシューにペーストするのに便利です。

```bash
copilot

> @samples/book-app-project/ をレビューして、見つかった問題を以下に分類したマークダウンチェックリストを作成してください:
> - 重大（データ結失リスク、クラッシュ）
> - 高（バグ、誤った動作）
> - 中（パフォーマンス、保守性）
> - 低（スタイル、小さな改善）
```

### Gitの変更を理解する（/review使用時の重要事項）

`/review`コマンドを使う前に、gitの2種類の変更を理解しておく必要があります：

| 変更の種類 | 意味 | 確認方法 |
|-------------|----------|------------|
| **ステージ済み変更** | `git add`で次のコミット向けにマークしたファイル | `git diff --staged` |
| **未ステージ変更** | 変更済みだがまだ追加していないファイル | `git diff` |

```bash
# クイックリファレンス
git status           # ステージ済み・未ステージ両方を表示
git add file.py      # コミット向けにファイルをステージ
git diff             # 未ステージ変更を表示
git diff --staged    # ステージ済み変更を表示
```

### /reviewコマンドの使い方

`/review`コマンドは組み込み**code-review agent**を呼び出します。ステージ済み/未ステージの変更を高シグナルノイズ比の出力で分析するのに最適化されています。フリーフォームのプロンプトではなく、スラッシュコマンドで専門の組み込みAgentを起動します。

```bash
copilot

> /review
# ステージ済み/未ステージの変更に対してcode-review agentを起動
# 焦点の絞った、実行可能なフィードバックを提供

> /review 認証におけるセキュリティ問題を確認してください
# 特定のフォーカスエリアでレビューを実行
```

> 💡 **チップ**: code-review agentは保留中の変更があるときに最も効果的です。`git add`でファイルをステージしてより絞ったレビューを作成してください。

</details>

---

<a id="workflow-2-refactoring"></a>
<details>
<summary><strong>ワークフロー2: リファクタリング</strong> - コードの構造化、関心の分離、エラーハンドリングの改善</summary>

<img src="images/refactoring-swimlane-single.png" alt="リファクタリングワークフロー: コード評価、変更計画、実装、動作確認。" width="800"/>

### シンプルなリファクタリング

> **まずこれを試す:** `@samples/book-app-project/book_app.py コマンド処理でif/elif連鎖を使っています。辞書ディスパッチパターンに書き換えてください。`

シンプルな改善から始めましょう。以下のプロンプトを書籍アプリで試してみてください。各プロンプトは`@`ファイル参照と具体的なリファクタリング指示を对にしているので、Copilot CLIが正確に何を変更するかを把握できます。

```bash
copilot

> @samples/book-app-project/book_app.py コマンド処理でif/elif連鎖を使っています。辞書ディスパッチパターンに書き換えてください。

> @samples/book-app-project/utils.py すべての関数に型ヒントを追加してください

> @samples/book-app-project/book_app.py 書籍表示ロジックをutils.pyに抽出して関心の分離を改善してください
```

> 💡 **リファクタリングが初めての方へ?** 複雑な変換に偷長する前に、型ヒントの追加や変数名の改善などシンプルな依頼から始めてください。

---

<details>
<summary>🎤 動作を見る！</summary>

![Refactor Demo](images/refactor-demo.gif)

*デモの出力は異なる場合があります。モデル、ツール、応答内容は図示と異なる可能性があります。*

</details>

---

### 関心の分離

リファクターが1つのプロンプトで複数の`@`ファイルを参照することで、Copilot CLIがリファクタリングの一環としてファイル間でコードを移動できます。

```bash
copilot

> @samples/book-app-project/utils.py @samples/book-app-project/book_app.py
> utils.pyではprint文がロジックと混在しています。表示関数とデータ処理を分離するようリファクタリングしてください。
```

### エラーハンドリングの改善

関連する2つのファイルと横断的な関心事を記述することで、Copilot CLIが両方に一賫した修正を提案できます。

```bash
copilot

> @samples/book-app-project/utils.py @samples/book-app-project/books.py
> これらのファイルではエラー処理が一貫していません。カスタム例外を使った統一されたアプローチを提案してください。
```

### ドキュメントの追加

各docstringに必要な内容を箇条書きのリストで尺定して指定します。

```bash
copilot

> @samples/book-app-project/books.py すべてのメソッドに包括的なdocstringを追加してください:
> - パラメータの型と説明を含める
> - 戻り値を記述する
> - 発生する例外を記載する
> - 使用例を追加する
```

### テストを使った安全なリファクタリング

マルチターンの会話で関連する2つの依頼を連鎖します。まずテストを生成し、安全網としてそのテストを使ってリファクタリングします。

```bash
copilot

> @samples/book-app-project/books.py リファクタリングの前に現在の動作のテストを生成してください

# まずテストを取得

> では BookCollection クラスをファイル操作にコンテキストマネージャーを使うよう書き換えてください

# 動作が保持されていることをテストで検証しながら自信を持ってリファクタリング
```

</details>

---

<a id="workflow-3-debugging"></a>
<details>
<summary><strong>ワークフロー3: デバッグ</strong> - バグの追跡、セキュリティオーディット、ファイル間の問題を追跡</summary>

<img src="images/debugging-swimlane-single.png" alt="デバッグワークフロー: エラーの理解、原因の特定、修正、テスト。" width="800"/>

### シンプルなデバッグ

> **まずこれを試す:** `@samples/book-app-buggy/books_buggy.py 「The Hobbit」を検索しても結果が返らないとユーザーが報告しています。データに存在するのになぜかデバッグしてください。`

何がおかしいかを説明することから始めましょう。以下はバグのある書籍アプリで試せる一般的なデバッグパターンです。各プロンプトは`@`ファイル参照と明確な症状説明を展開しているので、Copilot CLIがバグを見つけて診断できます。

```bash
copilot

# パターン: "期待Xだが結果Yが得られた"
> @samples/book-app-buggy/books_buggy.py 「The Hobbit」を検索しても結果が返らないとユーザーが報告しています。データに存在するのになぜかデバッグしてください。

# パターン: "予期しない動作"
> @samples/book-app-buggy/book_app_buggy.py 存在しない本を削除しようとすると、削除されたと表示されます。なぜか紹介してください。

# パターン: "誤った結果"
> @samples/book-app-buggy/books_buggy.py 1冊だけ既読にしようとすると、全ての本が既読になってしまいます。バグは何ですか？
```

> 💡 **デバッグのヒント**: *症状*（見えていること）と*期待値*（実現すべきこと）を説明してください。Copilot CLIが導いてくれます。

---

<details>
<summary>🎤 動作を見る！</summary>

![Fix Bug Demo](images/fix-bug-demo.gif)

*デモの出力は異なる場合があります。モデル、ツール、応答内容は図示と異なる可能性があります。*

</details>

---

### 「バグ探偵」 — AIが関連するバグも発見する

ここでコンテキスト対応デバッグの光る部分です。バグのある書籍アプリでこのシナリオを試してみてください。`@`でファイル全体を提供し、ユーザー報告の症状だけを説明することで、Copilot CLIが根本原因を追跡し、近くの別のバグも発見できます。

```bash
copilot

> @samples/book-app-buggy/books_buggy.py
>
> ユーザー報告: "荽名の部分一致で著者名が検索できない"
> なぜこれが起きるかデバッグしてください
```

**Copilot CLI が行うこと**:
```
根本原因: 80行目で完全一致（==）の代わりに部分一致（in）が必要です。

80行目: return [b for b in self.books if b.author == author]

find_by_author 関数は完全一致を必要とします。「Tolkien」で検索しても
「J.R.R. Tolkien」の本は見つかりません。

修正: 大文字小文字を無視した部分一致に変更:
return [b for b in self.books if author.lower() in b.author.lower()]
```

**なぜ重要か**: Copilot CLIはファイル全体を読み、バグレポートのコンテキストを理解して、明確な説明付きの具体的な修正を提示します。

> 💡 **ボーナス**: Copilot CLIはファイル全体を分析するため、尋ねていない*別の*問題も発見することがよくあります。例えば、著者検索を修正する途中で、`find_book_by_title`の大文字小文字区別のバグも指摘するかもしれません！

### 実際のセキュリティサイドバー

自分のコードのデバッグも重要ですが、本番アプリケーションのセキュリティ脆弱性を理解することも不可欠です。次の例を試してみましょう: 見慣れないファイルをCopilot CLIに指定し、セキュリティ問題を監査するよう依頼します。

```bash
copilot

> @samples/buggy-code/python/user_service.py このPythonユーザーサービス内のセキュリティ脆弱性を全て見つけてください
```

このファイルは本番アプリケーションで実際に遅遇するリアルワールドのセキュリティパターンを示しています。

> 💡 **Common security terms you'll encounter:**
> - **SQL Injection**: When user input is put directly into a database query, allowing attackers to run malicious commands
> - **Parameterized queries**: The safe alternative - placeholders (`?`) separate user data from SQL commands
> - **Race condition**: When two operations happen at the same time and interfere with each other
> - **XSS (Cross-Site Scripting)**: When attackers inject malicious scripts into web pages

---

### エラーの理解

`@`ファイル参照と一緒にスタックトレースをプロンプトに直接貼り付けて、Copilot CLIがエラーをソースコードにマッピングできるようにします。

```bash
copilot

> このエラーが発生しています:
> AttributeError: 'NoneType' object has no attribute 'title'
>     at show_books (book_app.py:19)
>
> @samples/book-app-project/book_app.py なぜ発生するのか、並びに修正方法を教えてください
```

### テストケースでデバッグ

正確な入力と観察された出力を説明して、Copilot CLIに再現性のある最小限のテストケースを与えます。

```bash
copilot

> @samples/book-app-buggy/books_buggy.py remove_book 関数にバグがあります。「Dune」を削除しようとすると「Dune Messiah」も削除されてしまいます。
> これをデバッグしてください: 根本原因を説明し、修正も提供してください。
```

### コード全体にわたって問題を追跡

複数のファイルを参照して、Copilot CLIにデータフローを追いかけて問題の発生源を特定するよう依頼します。

```bash
copilot

> 書籍リストの番号が0から始まるとユーザーが報告しています。
> @samples/book-app-buggy/book_app_buggy.py @samples/book-app-buggy/books_buggy.py
> リスト表示の流れをトレースして、問題が発生している箇所を特定してください
```

### データの問題を理解する

コードとそれを読み込むデータファイルを同時に提供することで、Copilot CLIがエラーハンドリングの改善案を提案する際に全体像を把握できます。

```bash
copilot

> @samples/book-app-project/data.json @samples/book-app-project/books.py
> JSONファイルが滅してアプリがクラッシュすることがあります。これを滑らかに処理するにはどうすればいいですか？
```

</details>

---

<a id="workflow-4-test-generation"></a>
<details>
<summary><strong>ワークフロー4: テスト生成</strong> - 包括的なテストとエッジケースを自動生成</summary>

<img src="images/test-gen-swimlane-single.png" alt="テスト生成ワークフロー: 関数の分析、テスト生成、エッジケースを含む、実行。" width="800"/>

> **まずこれを試す:** `@samples/book-app-project/books.py エッジケースを含む包括的なpytestテストを生成してください`

### 「テスト爆発」 — 2テスト vs 15+テスト

テストを手動で書くと、開発者は一般的に2、3つの基本テストを作成します：
- 有効な入力をテスト
- 無効な入力をテスト
- エッジケースをテスト

Copilot CLIに包括的なテストを生成させると何が起きるか見てみましょう！このプロンプトは`@`ファイル参照と構造化された箇条書きリストを使って、Copilot CLIが包括的なテストカバレッジを達成できるように誘導します：

```bash
copilot

> @samples/book-app-project/books.py 包括的なpytestテストを生成してください。以下のテストを含めてください:
> - 書籍の追加
> - 書籍の削除
> - タイトルで検索
> - 著者で検索
> - 既読にマーク
> - 空データのエッジケース
```

---

<details>
<summary>🎤 動作を見る！</summary>

![Test Generation Demo](images/test-gen-demo.gif)

*デモの出力は異なる場合があります。モデル、ツール、応答内容は図示と異なる可能性があります。*

</details>

---

**得られるもの**: 15+の包括的なテスト（以下を含む）:

```python
class TestBookCollection:
    # ハッピーパス
    def test_add_book_creates_new_book(self):
        ...
    def test_list_books_returns_all_books(self):
        ...

    # 検索操作
    def test_find_book_by_title_case_insensitive(self):
        ...
    def test_find_book_by_title_returns_none_when_not_found(self):
        ...
    def test_find_by_author_partial_match(self):
        ...
    def test_find_by_author_case_insensitive(self):
        ...

    # エッジケース
    def test_add_book_with_empty_title(self):
        ...
    def test_remove_nonexistent_book(self):
        ...
    def test_mark_as_read_nonexistent_book(self):
        ...

    # データ永続化
    def test_save_books_persists_to_json(self):
        ...
    def test_load_books_handles_missing_file(self):
        ...
    def test_load_books_handles_corrupted_json(self):
        ...

    # 特殊文字
    def test_add_book_with_unicode_characters(self):
        ...
    def test_find_by_author_with_special_characters(self):
        ...
```

**結果**: 30秒で、考え抬くのに1時間かかるエッジケーステストが得られます。

---

### ユニットテスト

1つの関数を対象にし、テストしたい入力カテゴリを列挙することで、Copilot CLIが絞った包括unitテストを生成します。

```bash
copilot

> @samples/book-app-project/utils.py get_book_details を対象に包括的なpytestテストを生成してください:
> - 有効な入力
> - 空文字列
> - 無効な年形式
> - 非常に長いタイトル
> - 著者名の特殊文字
```

### テストの実行

Copilot CLIにツールチェーンについて日常言葉で質問することで、正しいシェルコマンドを生成してくれます。

```bash
copilot

> テストを実行するにはどうすればいいですか？ pytest コマンドを教えてください。

# Copilot CLIの応答:
# cd samples/book-app-project && python -m pytest tests/
# 詳細出力: python -m pytest tests/ -v
# print文の表示: python -m pytest tests/ -s
```

### 特定シナリオのテスト

Copilot CLIがハッピーパスを超えるよう、トリッキーなシナリオを一覧表示します。

```bash
copilot

> @samples/book-app-project/books.py 以下のシナリオのテストを生成してください:
> - 重複書籍の追加（同じタイトルと著者）
> - 部分タイトル一致で書籍を削除
> - コレクションが空のときに書籍を検索
> - 保存中のファイルパーミッションエラー
> - 書籍コレクションへの同時アクセス
```

### 既存ファイルにテストを追加

1つの関数に*追加*のテストを依頼することで、Copilot CLIが既存のテストを補完する新しいケースを生成します。

```bash
copilot

> @samples/book-app-project/books.py
> find_by_author 関数のエッジケースの追加テストを生成してください:
> - ハイフン付き著者名（例: 「Jean-Paul Sartre」）
> - 複数のファーストネームを持つ著者
> - 空文字列の著者名
> - アクセント文字付きの著者名
```

</details>

---

<a id="workflow-5-git-integration"></a>
<details>
<summary><strong>ワークフロー5: Git連携</strong> - コミットメッセージ、PR説明、/pr、/delegate、/diff</summary>

<img src="images/git-integration-swimlane-single.png" alt="Git連携ワークフロー: 変更をステージ、メッセージ生成、コミット、PR作成。" width="800"/>

> 💡 **このワークフローはgitの基本操作の知識を前提**としています（ステージング、コミット、ブランチ）。gitが初めての方は、まず他の4つのワークフローを試してみてください。

### コミットメッセージの生成

> **まずこれを試す:** `copilot -p "次の変更に対する従来形式のコミットメッセージを生成してください: $(git diff --staged)"` — 変更をステージしてから実行すると、Copilot CLIがコミットメッセージを書いてくれます。

この例では`-p`インラインプロンプトフラグとシェルコマンド置換を使って、`git diff`の出力をワンショットのコミットメッセージ生成のためにCopilot CLIに直接パイプします。`$(...)`構文は可括内のコマンドを実行してその出力を外側のコマンドに挿入します。

```bash

# 変更内容を確認
git diff --staged

# コミットメッセージを[Conventional Commit](../GLOSSARY.md#conventional-commit)フォーマットで生成
# （「feat(books): add search」や「fix(data): handle empty input」などの構造化メッセージ）
copilot -p "次の変更に対する従来形式のコミットメッセージを生成してください: $(git diff --staged)"

# 出力: "feat(books): 著者名の部分一致検索を追加
#
# - find_by_authorを部分一致対応に更新
# - 大文字小文字を無視した比較を追加
# - 著者検索時のユーザー体験を改善"
```

---

<details>
<summary>🎤 動作を見る！</summary>

![Git Integration Demo](images/git-integration-demo.gif)

*デモの出力は異なる場合があります。モデル、ツール、応答内容は図示と異なる可能性があります。*

</details>

---

### 変更の説明

`git show`の出力を`-p`プロンプトにパイプして、直前のコミットの内容を日常言語で胥砕してもらいます。

```bash
# このコミットは何を変更した？
copilot -p "このコミットが何をしているか説明してください: $(git show HEAD --stat)"
```

### PRの説明

`git log`の出力と構造化されたプロンプトテンプレートを組み合わせて、完全なプルリクエスト説明を自動生成します。

```bash
# ブランチの変更からPR説明を生成
copilot -p "これらの変更に対するプルリクエストの説明を生成してください:
$(git log main..HEAD --oneline)

以下を含めてください:
- 変更の要約
- 変更理由
- テスト内容
- 破壊的変更の有無（はい/いいえ）"
```

### 現在のブランチでインタラクティブモードの/prを使用

If you're working with a branch in Copilot CLI's interactive mode, you can use the `/pr` command to work with pull requests. Use `/pr` to view a PR, create a new PR, fix an existing PR, or let Copilot CLI auto-decide based on the branch state.

```bash
copilot

> /pr [view|create|fix|auto]
```

### プッシュ前のレビュー

`git diff main..HEAD`を`-p`プロンプト内に使って、プッシュ前のブランチ全体の変更をさっと突き合わせます。

```bash
# プッシュ前の最終確認
copilot -p "プッシュ前にこれらの変更を問題がないかレビューしてください:
$(git diff main..HEAD)"
```

### バックグラウンドタスクへの/delegateの使用

`/delegate`コマンドは作業をGitHub CopilotクラウドAgentに定義されたタスクに引き渡します。`/delegate`スラッシュコマンド（または`&`ショートカット）で、明確なタスクをバックグラウンドAgentに委譲します。

```bash
copilot

> /delegate Add input validation to the login form

# または&プレフィックスショートカットを使用:
> & Fix the typo in the README header

# Copilot CLI:
# 1. 変更を新しいブランチにコミット
# 2. ドラフトプルリクエストを開く
# 3. GitHub上でバックグラウンドで作業
# 4. 完了時にレビューを依頼
```

明確なタスクを他の作業に集中しながら完了させたいときに最適です。

### セッションの変更を確認する/diffの使用

`/diff`コマンドは現在のセッション内の全変更を表示します。コミット前にCopilot CLIが変更した内容のビジュアル差分を確認するにはこのスラッシュコマンドを使用します。

```bash
copilot

# 変更を加えた後...
> /diff

# このセッション中に変更された全ファイルのビジュアル差分を表示
# コミット前にレビューするのに便利
```

</details>

---

## クイックヒント: 計画・コード化前のリサーチ

ライブラリを調査したり、ベストプラクティスを理解したり、未知のトピックを探索する際は、コードを書く前に`/research`で深居りの調査を実行してください：

```bash
copilot

> /research What are the best Python libraries for validating user input in CLI apps?
```

CopilotがGitHubリポジトリやウェブソースを検索し、小出元付きのサマリーを返します。新機能を始める前に情報に基づいた意思決定をしたいときに便利です。結果は`/share`で共有できます。

> 💡 **ヒント**: `/research`は`/plan`の*前に*実行すると効果的です。アプローチをリサーチしてから実装を計画しましょう。

---

## 総まとめ: バグ修正ワークフロー

報告されたバグを修正するための全体的なワークフローです：

```bash

# 1. バグレポートを理解する
copilot

> ユーザー報告: '著者名の部分一致で書籍が検索できない'
> @samples/book-app-project/books.py 原因を分析して特定してください

# 2. 問題をデバッグする（同じセッションを継続）
> 分析結果に基づいてfind_by_author関数を表示し、問題を説明してください

> find_by_author関数を部分一致に対応するよう修正してください

# 3. 修正のテストを生成する
> @samples/book-app-project/books.py 以下のpytestテストを生成してください:
> - 完全一致の著者名
> - 部分一致の著者名
> - 大文字小文字を無視した一致
> - 著者名が見つからない場合

# 4. コミットメッセージを生成する
copilot -p "次の変更のコミットメッセージを生成してください: $(git diff --staged)"

# Output: "fix(books): support partial author name search"
```

### バグ修正ワークフローのまとめ

| ステップ | アクション | Copilotコマンド |
|------|--------|-----------------|
| 1 | バグを理解する | `> [バグ内容] @relevant-file.py Analyze the likely cause` |
| 2 | 詳細分析を得る | `> Show me the function and explain the issue` |
| 3 | 修正を実装する | `> Fix the [具体的な問題]` |
| 4 | テストを生成する | `> Generate tests for [シナリオ]` |
| 5 | コミットする | `copilot -p "Generate commit message for: $(git diff --staged)"` |

---

# 実習

<img src="../images/practice.png" alt="モニターにコードが表示されたデスク、ランプ、コーヒーカップ、ヘッドフォンが置かれた手射き練習の宇宙描写" width="800"/>

これらのワークフローを実際に適用してみましょう。

---

## ▶️ ハンズオンで試す

デモを完了したら、これらのバリエーションを試してみてください:

1. **バグ探偵チャレンジ**: `samples/book-app-buggy/books_buggy.py`の`mark_as_read`関数をCopilot CLIでデバッグする。なぜ1冊だけでなく**全て**の書籍が既読になるのか説明してくれるか？

2. **テストチャレンジ**: 書籍アプリの`add_book`関数のテストを生成する。Copilot CLIが生成するエッジケースのうち、自分では思いつかなかったものを数えてみてください。

3. **コミットメッセージチャレンジ**: 書籍アプリのファイルに小さな変更を加えてステージし（`git add .`）、実行する:
   ```bash
   copilot -p "次の変更に対する従来形式のコミットメッセージを生成してください: $(git diff --staged)"
   ```
   自分で迅速に書いたものより良いメッセージか？

**自己チェック**: 「このバグをデバッグ」が「バグを探す」より強力な理由を説明できれば開発ワークフローを理解できています（コンテキストが重要！）。

---

## 📝 課題

### メインチャレンジ: リファクタリング、テスト、リリース

ハンズオン例では`find_book_by_title`とコードレビューに焦点を当てました。今度は`book-app-project`の別の関数で同じワークフロースキルを実践しましょう:

1. **レビュー**: `books.py`の`remove_book()`をエッジケースと潜在的な問題について確認するようCopilot CLIに依頼:
   `@samples/book-app-project/books.py remove_book()関数をレビューしてください。別の本とタイトルが部分一致する場合（例: 「Dune」 vs 「Dune Messiah」）どうなりますか？処理されていないエッジケースはありますか？`
2. **リファクタリング**: Copilot CLIに`remove_book()`を改善するよう依頼（大文字小文字を無視したマッチングや、本が見つからない場合の有用なフィードバックの返却など）
3. **テスト**: 改善された`remove_book()`専用のpytestテストを生成する（以下を網羅）:
   - 存在する本を削除
   - 大文字小文字を無視したタイトルマッチング
   - 存在しない本は適切なフィードバックを返す
   - 空のコレクションから削除
4. **レビュー**: 変更をステージして`/review`を実行し、残りの問題がないか確認する
5. **コミット**: 従来のコミットメッセージを生成:
   `copilot -p "次の変更に対する従来形式のコミットメッセージを生成してください: $(git diff --staged)"`

<details>
<summary>💡 ヒント（クリックして展開）</summary>

**各ステップのサンプルプロンプト:**

```bash
copilot

# ステップ1: レビュー
> @samples/book-app-project/books.py remove_book()関数の処理されていないエッジケースをレビューしてください

# ステップ2: リファクタリング
> remove_book()を大文字小文字を無視したマッチングを使い、本が見つからないときに明確なメッセージを返すよう改善してください。変更前後のコードを表示してください。

# ステップ3: テスト
> 改善されたremove_book()関数のpytestテストを以下を含めて生成してください:
> - 存在する本を削除する
> - 大文字小文字を無視したマッチング（「dune」で「Dune」を削除できる）
> - 本が見つからない場合は適切な応答を返す
> - 空のコレクションから削除する

# ステップ4: レビュー
> /review

# ステップ5: コミット
> このリファクタリングの従来形式のコミットメッセージを生成してください
```

**ヒント:** `remove_book()`を改善した後、Copilot CLIに「このファイルの他の関数で同様の改善が必要なものはある？」と質conjuntoしてみてください。`find_book_by_title()`や`find_by_author()`にも同様の改善を提案するかもしれません。

</details>

### ボーナスチャレンジ: Copilot CLIでアプリケーションを作る

> 💡 **注意**: このGitHub SkillsエクササイズはPythonではなく**Node.js**を使用します。イシューの作成、コードの生成、ターミナルからのコラボレーションなどCopilot CLIの技術はどの言語にも適用できます。

このエクササイズでは、Node.jsで電卓アプリを作りながらGitHub Copilot CLIでイシューの作成、コードの生成、ターミナルからのコラボレーションを学びます。CLIのインストール、テンプレートとAgentsの利用、イテラティブなコマンドライン驱動の開発を繋習します。

##### <img src="../images/github-skills-logo.png" width="28" align="center" /> ["Create applications with the Copilot CLI" Skillsエクササイズを始める](https://github.com/skills/create-applications-with-the-copilot-cli)

---

<details>
<summary>🔧 <strong>よくあるミスとトラブルシューティング</strong> （クリックして展開）</summary>

### よくあるミス

| ミス | 発生する営瓜 | 修正方法 |
|---------|-------------|----------|
| 「Review this code」など曖昧なプロンプトを使用 | 具体的な問題を見逃す汎用的なフィードバック | 具体的に: "Review for SQL injection, XSS, and auth issues" |
| コードレビューに`/review`を使わない | 最適化されたcode-review agentを利用した小 | 高シグナルノイズ比にチューニングされた`/review`を使用 |
| コンテキストなしで「バグを探す」と依頼 | Copilot CLIが何のバグを知らない | 症状を説明: "ユーザーが「…」の場合にXが発生すると報告" |
| フレームワークを指定せずテストを生成 | 誤った構文またはアサーションライブラリのテスト | 指定: "Generate tests using Jest" or "using pytest" |

### トラブルシューティング

**レビューが不完全に見える** - 何を探すかをより具体的に指定する:

```bash
copilot

# ダメな例:
> @samples/book-app-project/book_app.py をレビューしてください

# 良い例:
> @samples/book-app-project/book_app.py 入力バリデーション、エラー処理、エッジケースについてレビューしてください
```

**テストがフレームワークに合わない** - フレームワークを指定する:

```bash
copilot

> @samples/book-app-project/books.py pytest（unittestではなく）でテストを生成してください
```

**リファクタリングが動作を変える** - Copilot CLIに動作の保持を依頼する:

```bash
copilot

> @samples/book-app-project/book_app.py Refactor command handling to use dictionary dispatch. IMPORTANT: Maintain identical external behavior - no breaking changes
```

</details>

---

# まとめ

## 🔑 重要なポイント

<img src="images/specialized-workflows.png" alt="各タスク向けの専門ワークフロー: コードレビュー、リファクタリング、デバッグ、テスト、Git連携" width="800"/>

1. **コードレビュー**は具体的なプロンプトで包括的になる
2. **リファクタリング**はテストを先に生成することで安全になる
3. **デバッグ**はエラーとコードの両方をCopilot CLIに示すことで効果が大きくなる
4. **テスト生成**はエッジケースとエラーシナリオを含めるべき
5. **Git連携**はコミットメッセージとPR説明を自動化する

> 📋 **クイックリファレンス**: 全コマンドとショートカットの一覧は[GitHub Copilot CLIコマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference)を参照してください。

---

## ✅ チェックポイント: 基礎をマスターした

**おめでとうございます！** GitHub Copilot CLIで生産性を発揮するための基礎スキルを全て習得しました:

| スキル | 章 | できること |
|-------|---------|------------------|
| 基本コマンド | Ch 01 | インタラクティブモード、Planモード、プログラマティックモード（-p）、スラッシュコマンドの利用 |
| コンテキスト | Ch 02 | `@`でファイルを参照する、セッション管理、コンテキストウィンドウの理解 |
| ワークフロー | Ch 03 | コードレビュー、リファクタリング、デバッグ、テスト生成、git連携 |

Chapter 04-06はさらなる機能をカバーしており、学ぶ値打ちがあります。

---

## 🛠️ 個人ワークフローの構築

GitHub Copilot CLI の「正しい使い方」は一つではありません。自分のパターンを確立するためのヒント:

> 📚 **公式ドキュメント**: [Copilot CLI ベストプラクティス](https://docs.github.com/copilot/how-tos/copilot-cli/cli-best-practices) - GitHub からの推奨ワークフローとヒント。

- **非軽微なことには`/plan`から始める**。実行前に計画を細調整する — 良い計画はより良い結果を生む。
- **効果的なプロンプトを保存する**。Copilot CLI が失敗したとき、何が悪かったか記録する。時間とともにこれが個人のプレイブックになる。
- **自由に実験する**。長く詳細なプロンプトを好む開発者もいれば、短いプロンプトと追加質問を好む人もいる。横々なアプローチを試して自分に合ったものを見つけよう。

> 💡 **次のステップ**: Chapter 04 と 05 では、Copilot CLI が自動読み込むカスタム指示とスキルにベストプラクティスをコード化する方法を学びます。

---

## ➡️ 次のステップ

残りの章では Copilot CLI の能力を拡張する追加機能を紹介します：

| 章 | 内容 | 必要な時 |
|---------|----------------|---------------------|
| Ch 04: Agents | 専門化された AI ペルソナを作成 | ドメインを知るスペシャリスト（frontend、security）が欲しいとき |
| Ch 05: Skills | タスク用の指示を自動読み込み | 同じプロンプトを繰り返すとき |
| Ch 06: MCP | 外部サービスに接続 | GitHub やデータベースのライブデータが必要なとき |

**推奨**: コアワークフローを1週間試してから、特定のニーズが生まれたら Chapter 04-06 に戻ってきてください。

---

## 追加トピックへ進む

**[Chapter 04: Agents and Custom Instructions](../04-agents-custom-instructions/README.md)** で学ぶ内容:

- 組み込み Agents の利用（`/plan`、`/review`）
- `.agent.md` ファイルで専門化されたAgents（frontend エキスパート、セキュリティ監査人）を作成
- マルチ Agent コラボレーションパターン
- プロジェクト標準のカスタム指示ファイル

---

**[← Back to Chapter 02](../02-context-conversations/README.md)** | **[Continue to Chapter 04 →](../04-agents-custom-instructions/README.md)**
