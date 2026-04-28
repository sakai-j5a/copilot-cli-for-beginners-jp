![Chapter 04: Agents and Custom Instructions](images/chapter-header.png)

> **Pythonコードレビューアー、テストのエキスパート、セキュリティレビューアーを…全部で1つのツールで雇えたらどうでしょうか？**

第03章では、コードレビュー、リファクタリング、デバッグ、テスト生成、git統合などの重要なワークフローをマスターしました。それらはGitHub Copilot CLIで大変生産性が上がります。さらに一歩進めましょう。

これまではCopilot CLIを汎用目的のアシスタントとして使ってきました。エージェントを使えば、型ヒントとPEP 8を強制するコードレビューアーや、pytestケースを書くテスト支援エキスパートなど、特定のペルソナを持たせることができます。同じプロンプトでも、専門知識を持つエージェントが扱った方が着実に良い結果を得られることがわかります。

## 🎯 学習目標

この章を終えると、以下のことができるようになります：

- ビルトインエージェント（Plan `/plan`、Code-review `/review`）を使い、自動エージェント（Explore、Task）を理解する
- エージェントファイル（`.agent.md`）を使って専門エージェントを作成する
- ドメイン特定のタスクにエージェントを利用する
- `/agent`と`--agent`でエージェントを切り替える
- プロジェクト専用の標準に合わせたカスタム命令ファイルを作成する

> ⏱️ **所要時間の目安**: 約55分（20分読む + 35分ハンズオン）

---

## 🧩 現実世界のアナロジー: スペシャリストの雇用

家の修理が必要なとき、「万能ヘルパー」1人に頜るのではなく、専門家に籍ます：

| 問題 | 専門家 | 理由 |
|---------|------------|-----|
| 水漏れ | 配管工 | 配管コードを尊重し、専用工具を持つ |
| 配線工事 | 電気工 | 安全要件を理解し、法規に準拠する |
| 屋根修理 | 屋根工 | 材料や地元の気候に精通する |

エージェントも同じです。汎用AIUではなく、コードレビュー、テスト、セキュリティ、ドキュメントなど特定のタスクに特化したエージェントを使いましょう。指示は一度設定するだけで、その専門性が必要なときにいつでも再利用できます。

<img src="images/hiring-specialists-analogy.png" alt="Hiring Specialists Analogy - Just as you call specialized tradespeople for house repairs, AI agents are specialized for specific tasks like code review, testing, security, and documentation" width="800" />

---

# Agentsの使い方

組み込みAgentsとカスタムAgentsをすぐに始めましょう。

---

## *Agentsが初めての方へ:* まずここから！

Agentを使ったことがない方へ。このコースで始めるために必要なことだけをご紹介します。

1. **今すぐ*組み込み*Agentを試す:**
   ```bash
   copilot
   > /plan 書籍アプリの年入力バリデーションを追加する
   ```
   Plan Agentがステップバイステップの実装計画を作成します。

2. **カスタムAgentの例を見る:** 提供されている[python-reviewer.agent.md](../.github/agents/python-reviewer.agent.md)を見てパターンを把握してください。Agentの指示はシンプルに定義できます。

3. **基本概念を理解する:** Agentは汎用家ではなく専門家に相談するようなものです。「frontend agent」はリマインド不要で、アクセシビリティやコンポーネントパターンに自動的に注目します。Agentの指示に既に指定されているからです。


## 組み込みAgents

**Chapter 03開発ワークフローで既に組み込みAgentsを使っていました！**
<br>`/plan`と`/review`は実は組み込みAgentsです。内部で何が起きているかがわかりました。全一覧です:

| Agent | 呼び出し方 | 内容 |
|-------|---------------|------|
| **Plan** | `/plan`または`Shift+Tab`（モード切り替え） | コーディング前にステップバイステップの実装計画を作成 |
| **Code-review** | `/review` | ステージ済み/未ステージの変更を集中式フィードバックでレビュー |
| **Init** | `/init` | プロジェクト設定ファイル（指示、Agents）を生成 |
| **Explore** | *自動起動* | コードベースの探索・分析を依頼すると内部で使用される |
| **Task** | *自動起動* | テスト、ビルド、lint、依存関係インストールなどのコマンドを実行 |

<br>

**組み込みAgentsの実動例** - Plan、Code-review、Explore、Taskの呼び出し例

```bash
copilot

# Plan Agentを起動して実装計画を作成する
> /plan 書籍アプリの年度入力にバリデーションを追加する

# 変更内容にCode-review Agentを起動する
> /review

# ExploreとTask Agentは関連すると自動起動される:
> テストスイートを実行して        # Task agentを使用

> 書籍データの読み込み方法を探索する    # Explore agentを使用
```

What about the Task Agent? It works behind the scenes to manage and track what is going on and to report back in a clean and clear format:

| Outcome | What You See |
|---------|--------------|
| ✅ **Success** | Brief summary (e.g., "All 247 tests passed", "Build succeeded") |
| ❌ **Failure** | Full output with stack traces, compiler errors, and detailed logs |


> 📚 **Official Documentation**: [GitHub Copilot CLI Agents](https://docs.github.com/copilot/how-tos/use-copilot-agents/use-copilot-cli#use-custom-agents)

---

# AgentsをCopilot CLIに追加する

独自のAgentsをワークフローの一部として簡単に定義できます！一度定義して、あとは指揮するだけ！

<img src="images/using-agents.png" alt="4体のカラフルAIロボットが全員別々のツールを持って並んでいる、専門化されたAgent能力を表現した画像" width="800"/>

## 🗂️ Agentを追加する

Agentファイルは`.agent.md`拡張子の付いたマークダウンファイルです。YAMLフロントマター（メタデータ）とmarkdown形式の指示の2部分で構成されています。

> 💡 **YAMLフロントマターが初めての方へ？** `---`マーカーで囲まれたファイル先頭の小さな設定ブロックです。YAMLは`key: value`形式のペアです。それ以外は送のマークダウンです。

最小限のAgentの例:

```markdown
---
name: my-reviewer
description: Code reviewer focused on bugs and security issues
---

# Code Reviewer

You are a code reviewer focused on finding bugs and security issues.

When reviewing code, always check for:
- SQL injection vulnerabilities
- Missing error handling
- Hardcoded secrets
```

> 💡 **必須 vs 任意**: `description`フィールドは必須です。`name`、`tools`、`model`などは任意です。

## Agentファイルの配置場所

| 場所 | スコープ | 適切な用途 |
|----------|-------|--------|
| `.github/agents/` | プロジェクト固有 | プロジェクト慣例を含むチーム共有Agent |
| `~/.copilot/agents/` | グローバル（全プロジェクト） | どこででも使える個人用Agent |

**このプロジェクトには[.github/agents/](../.github/agents/)フォルダにサンプルAgentファイルが含まれています**。熱分で書いたり、提供されたものをカスタマイズすることもできます。

<details>
<summary>📂 このコースのサンプルAgentsを見る</summary>

| ファイル | 説明 |
|------|--------|
| `hello-world.agent.md` | 最小限の例 - まずここから |
| `python-reviewer.agent.md` | Pythonコード品質レビューアー |
| `pytest-helper.agent.md` | pytestテストのスペシャリスト |

```bash
# または個人用Agentフォルダにコピー（どのプロジェクトでも利用可能）
cp .github/agents/python-reviewer.agent.md ~/.copilot/agents/
```

コミュニティAgentは[github/awesome-copilot](https://github.com/github/awesome-copilot)でも公開されています。

</details>


## 🚀 カスタムAgentの使い方 (2つの方法)

### インタラクティブモード
インタラクティブモード内では、`/agent`でAgent一覧を表示して使用するAgentを選択します。

```bash
copilot
> /agent
```

別のAgentに切り替えたり、デフォルトモードに戻ったりするには、再度`/agent`コマンドを使用します。

### プログラマティックモード

Agentを指定して新しいセッションを直接起動します。

```bash
copilot --agent python-reviewer
> @samples/book-app-project/books.py をレビューしてください
```

> 💡 **Agentの切り替え**: `/agent`または`--agent`でいつでも別のAgentに切り替えられます。標準Copilot CLIに戻るには`/agent`で**no agent**を選択します。

---

# Agentsをより深く理解する

<img src="images/creating-custom-agents.png" alt="Robot being assembled on a workbench surrounded by components and tools representing custom agent creation" width="800"/>

> 💡 **このセクションは任意です。** 組み込みAgents（`/plan`、`/review`）はほとんどのワークフローで十分強力です。作業全体を通じて一貫して適用されるような専門知識が必要な場合にカスタムAgentsを作成しましょう。

各トピックは独立しています。**興味のあるものを選んで読んでください——すべてを一度に読む必要はありません。**

| 尋ねていること... | ジャンプ先 |
|---|---|
| Agentsが汎用プロンプトより優れている理由 | [スペシャリスト vs 汎用：違いを見る](#specialist-vs-generic-see-the-difference) |
| 機能ごとにAgentsを組み合わせる | [複数Agentの連携](#working-with-multiple-agents) |
| Agentsの整理、命名、共有 | [Agentの整理・共有](#organizing--sharing-agents) |
| 常時適用のプロジェクトコンテキストを設定 | [Copilot向けプロジェクト設定](#configuring-your-project-for-copilot) |
| YAMLプロパティとツールの参照 | [Agentファイルリファレンス](#agent-file-reference) |

以下からシナリオを選択して展開してください。

---

<a id="specialist-vs-generic-see-the-difference"></a>
<details>
<summary><strong>スペシャリスト vs 汎用：違いを見る</strong> - Agentsが汎用プロンプトより優れた出力を生む理由</summary>

## スペシャリスト vs 汎用：違いを見る

Agentsの価値がわかる場面です。違いを見てみましょう：

### Agentなし（汎用Copilot）

```bash
copilot

> 書籍アプリに年度範囲で書籍を検索する関数を追加する
```

**汎用の出力**:
```python
def search_by_year_range(books, start_year, end_year):
    results = []
    for book in books:
        if book['year'] >= start_year and book['year'] <= end_year:
            results.append(book)
    return results
```

基本的。動作はする。でも足りないものが多い。

---

### Python Reviewer Agent使用

```bash
copilot

> /agent
# "python-reviewer"を選択

> 書籍アプリに年度範囲で書籍を検索する機能を追加してください
```

**スペシャリストの出力**:
```python
from typing import List, Dict, Any

def search_by_year_range(
    books: List[Dict[str, Any]],
    start_year: int,
    end_year: int
) -> List[Dict[str, Any]]:
    """
    Search for books within a specified year range.

    Args:
        books: List of book dictionaries
        start_year: Starting year (inclusive)
        end_year: Ending year (inclusive)

    Returns:
        List of books published within the year range

    Raises:
        ValueError: If start_year is greater than end_year
        TypeError: If year values are not integers
    """
    if not isinstance(start_year, int) or not isinstance(end_year, int):
        raise TypeError("Year values must be integers")

    if start_year > end_year:
        raise ValueError(f"Start year ({start_year}) cannot be greater than end year ({end_year})")

    return [
        book for book in books
        if isinstance(book.get('year'), int)
        and start_year <= book['year'] <= end_year
    ]
```

**python-reviewer agentが自動的に追加する内容**:
- ✅ 全パラメータと戻り値の型ヒント
- ✅ Args/Returns/Raisesを含む包括docstring
- ✅ 適切なエラーハンドリングによる入力バリデーション
- ✅ パフォーマンス向上のためのリスト内包表記
- ✅ エッジケース処理（年度の欠如/不正値）
- ✅ PEP 8準拠のフォーマット
- ✅ 防衞的プログラミング諸法

**違い**: 同じプロンプトで、圧倒的に優れた出力。Agentが汎用プロンプトでは要求し忘れるたい専門知識を挿入してくれます。

</details>

---

<a id="working-with-multiple-agents"></a>
<details>
<summary><strong>複数Agentの連携</strong> - スペシャリストの組み合わせ、セッション途中の切り替え、Agent as tools</summary>

## 複数Agentの連携

スペシャリストが連携するときに本当の力が発揮されます。

### 例: シンプルな機能を作る

```bash
copilot

> 書籍アプリに「年度範囲で検索」機能を追加したい

# 設計はpython-reviewerで
> /agent
# "python-reviewer"を選択

> @samples/book-app-project/books.py find_by_year_rangeメソッドを設計する。最希のアプローチは？

# テスト設計はpytest-helperに切り替え
> /agent
# "pytest-helper"を選択

> @samples/book-app-project/tests/test_books.py find_by_year_rangeメソッドのテストケースを設計する。
> どんなエッジケースをカバーすべき？

# 両方の設計を統合
> メソッド実装と包括的なテストを含む実装計画を作成する。
```

**重要な洞察**: あなたはスペシャリストを指揮するアーキテクトです。詳細は彼らが担い、ビジョンはあなたが持つ。

<details>
<summary>🎤 動作を見る！</summary>

![Python Reviewer Demo](images/python-reviewer-demo.gif)

*デモの出力は異なる場合があります——モデル、ツール、応答内容は図示と異なる可能性があります。*

</details>

### Agentをツールとして使う

Agentsが設定されている場合、Copilotは複雑なタスク中にそれらをツールとして呼び出すこともできます。フルスタックの機能を要求すると、Copilotが適切なスペシャリストAgentに自動デリゲートする場合があります。

</details>

---

<a id="organizing--sharing-agents"></a>
<details>
<summary><strong>Agentの整理・共有</strong> - 命名、配置場所、指示ファイル、チーム共有</summary>

## Agentの整理・共有

### Agentの命名

Agentファイルを作るとき、名前は重要です。`/agent`または`--agent`の後に入力する文字列であり、チームメンバーにAgent一覧として表示されます。

| ✅ 良い命名 | ❌ 避ける命名 |
|--------------|----------|
| `frontend` | `my-agent` |
| `backend-api` | `agent1` |
| `security-reviewer` | `helper` |
| `react-specialist` | `code` |
| `python-backend` | `assistant` |

**命名規則:**
- 小文字とハイフンを使用: `my-agent-name.agent.md`
- ドメインを含める: `frontend`、`backend`、`devops`、`security`
- 必要に応じて具体的に: `react-typescript`、シンプルな`frontend`など

---

### チームとの共有

`.github/agents/`に配置してバージョン管理します。リポジトリにプッシュするだけで、チーム全員が自動的に利用できます。ただし、Agentsはプロジェクトから読み込まれるファイルの一種にかぎません。Copilotは**指示ファイル**もサポートしており、`/agent`不要で全セッションに自動適用されます。

Agentsは呼び出すスペシャリスト、指示ファイルは常時有効なチームルール、という整理です。

### ファイルの配置場所

主な2つの場所はすでに学んでいます（[Agentファイルの配置場所](#where-to-put-agent-files)を参照）。この意思決定ツリーで選んでください：

<img src="images/agent-file-placement-decision-tree.png" alt="Agentファイルの配置場所の意思決定ツリー" width="800"/>

**シンプルに始める:** `*.agent.md`ファイルをプロジェクトフォルダに一つ作る。満足したら永続的な場所に移動する。

Agentファイル以外にも、Copilotは**プロジェクトレベルの指示ファイル**も自動で読み込みます（`/agent`不要）。`AGENTS.md`、`.instructions.md`、`/init`については[Copilot向けプロジェクト設定](#configuring-your-project-for-copilot)を参照してください。

</details>

---

<a id="configuring-your-project-for-copilot"></a>
<details>
<summary><strong>Copilot向けプロジェクト設定</strong> - AGENTS.md、指示ファイル、/initのセットアップ</summary>

## Copilot向けプロジェクト設定

Agentsは必要なときに呼び出すスペシャリストです。**プロジェクト設定ファイル**は異なります: Copilotが毎セッション自動で読み込み、プロジェクトの慣例、技術スタック、ルールを把握します。誰が`/agent`を実行する必要もなく、リポジトリで作業する全員に常時有効なコンテキストです。

### /initでクイックセットアップ

最速の方法は、Copilotに設定ファイルを自動生成させることです：

```bash
copilot
> /init
```

Copilotがプロジェクトをスキャンし、カスタマイズされた指示ファイルを作成します。その後に編集できます。

### 指示ファイルの形式

| ファイル | スコープ | 備考 |
|------|-------|-------|
| `AGENTS.md` | Project root or nested | **Cross-platform standard** - works with Copilot and other AI assistants |
| `.github/copilot-instructions.md` | Project | GitHub Copilot specific |
| `.github/instructions/*.instructions.md` | Project | Granular, topic-specific instructions |
| `CLAUDE.md`, `GEMINI.md` | Project root | Supported for compatibility |

> 🎯 **Just getting started?** Use `AGENTS.md` for project instructions. You can explore the other formats later as needed.

### AGENTS.md

`AGENTS.md` is the recommended format. It's an [open standard](https://agents.md/) that works across Copilot and other AI coding tools. Place it in your repository root and Copilot reads it automatically. This project's own [AGENTS.md](../AGENTS.md) is a working example.

A typical `AGENTS.md` describes your project context, code style, security requirements, and testing standards. Use `/init` to generate one, or write your own following the pattern in our example file.

### カスタム指示ファイル（.instructions.md）

For teams that want more granular control, split instructions into topic-specific files. Each file covers one concern and applies automatically:

```
.github/
└── instructions/
    ├── python-standards.instructions.md
    ├── security-checklist.instructions.md
    └── api-design.instructions.md
```

> 💡 **注意**: 指示ファイルはどの言語でも使えます。この例はコースプロジェクトに合わせてPythonを使用していますが、TypeScript、Go、Rust、その他の技術でも同様に作成できます。

**コミュニティの指示ファイルを探す:** [github/awesome-copilot](https://github.com/github/awesome-copilot)では.NET、Angular、Azure、Python、Dockerなど多数の技術をカバーする既製の指示ファイルを参照できます。

### カスタム指示の無効化

デバッグや動作比較のためにプロジェクト固有設定を無視する必要がある場合：

```bash
copilot --no-custom-instructions
```

</details>

---

<a id="agent-file-reference"></a>
<details>
<summary><strong>Agentファイルリファレンス</strong> - YAMLプロパティ、ツールエイリアス、完全な例</summary>

## Agentファイルリファレンス

### より完全な例

[最小限のAgent形式](#-add-your-agents)はすでに見ました。`tools`プロパティを使ったより包括Agetを作ってみましょう。`~/.copilot/agents/python-reviewer.agent.md`を作成：

```markdown
---
name: python-reviewer
description: Python code quality specialist for reviewing Python projects
tools: ["read", "edit", "search", "execute"]
---

# Python Code Reviewer

You are a Python specialist focused on code quality and best practices.

**Your focus areas:**
- Code quality (PEP 8, type hints, docstrings)
- Performance optimization (list comprehensions, generators)
- Error handling (proper exception handling)
- Maintainability (DRY principles, clear naming)

**Code style requirements:**

```markdown
---
name: python-reviewer
description: Python code quality specialist for reviewing Python projects
tools: ["read", "edit", "search", "execute"]
---

# Python Code Reviewer

You are a Python specialist focused on code quality and best practices.

**Your focus areas:**
- Code quality (PEP 8, type hints, docstrings)
- Performance optimization (list comprehensions, generators)
- Error handling (proper exception handling)
- Maintainability (DRY principles, clear naming)

**Code style requirements:**
- Use Python 3.10+ features (dataclasses, type hints, pattern matching)
- Follow PEP 8 naming conventions
- Use context managers for file I/O
- All functions must have type hints and docstrings

**When reviewing code, always check:**
- Missing type hints on function signatures
- Mutable default arguments
- Proper error handling (no bare except)
- Input validation completeness
```

### YAMLプロパティ

| プロパティ | 必須 | 説明 |
|----------|----------|-------------|
| `name` | 不要 | 表示名（デフォルトはファイル名） |
| `description` | **必須** | Agentの内容—Copilotが提案するタイミングを判断するのに使用されます |
| `tools` | 不要 | 許可するツールのリスト（省略=全ツール有効）。下記エイリアスを参照 |
| `target` | 不要 | `vscode`または`github-copilot`のみに限定 |

### ツールエイリアス

`tools`リストで使用する名前：
- `read` - ファイルの読み込み
- `edit` - ファイルの編集
- `search` - ファイルの検索（grep/glob）
- `execute` - シェルコマンドの実行（`shell`、`Bash`も使用可）
- `agent` - 他のカスタムAgentを呼び出す

> 📖 **公式ドキュメント**: [Custom agents configuration](https://docs.github.com/copilot/reference/custom-agents-configuration)
>
> ⚠️ **VS Codeのみ**: `model`プロパティ（AIモデル選択）はVS Codeで動作しますが、GitHub Copilot CLIではサポートされていません。クロスプラットフォームAgentファイルへの記載は安全で、GitHub Copilot CLIは無視します。

### その他のAgentテンプレート

> 💡 **初心者向けの注意**: 以下の例はテンプレートです。**技術内容はそれぞれのプロジェクトに合わせて書き換えてください。**重要なのはAgentの*構造*であり、指定する技術の種類ではありません。

本プロジェクトには[.github/agents/](../.github/agents/)フォルダに動作例が含まれています：
- [hello-world.agent.md](../.github/agents/hello-world.agent.md) - 最小限の例、まずここから
- [python-reviewer.agent.md](../.github/agents/python-reviewer.agent.md) - Pythonコード品質レビューアー
- [pytest-helper.agent.md](../.github/agents/pytest-helper.agent.md) - pytestテストのスペシャリスト

コミュニティAgentは[github/awesome-copilot](https://github.com/github/awesome-copilot)を参照してください。

</details>

---

# 実習

<img src="../images/practice.png" alt="モニターにコードが表示されたデスク、ランプ、コーヒーカップ、ヘッドフォンが置かれた手射き練習の宇宙描写" width="800"/>

独自のAgentを作成し、実際に使ってみましょう。

---

## ▶️ ハンズオンで試す

```bash

# Create the agents directory (if it doesn't exist)
mkdir -p .github/agents

# Create a code reviewer agent
cat > .github/agents/reviewer.agent.md << 'EOF'
---
name: reviewer
description: Senior code reviewer focused on security and best practices
---

# Code Reviewer Agent

You are a senior code reviewer focused on code quality.

**Review priorities:**
1. Security vulnerabilities
2. Performance issues
3. Maintainability concerns
4. Best practice violations

**Output format:**
Provide issues as a numbered list with severity tags:
[CRITICAL], [HIGH], [MEDIUM], [LOW]
EOF

# Create a documentation agent
cat > .github/agents/documentor.agent.md << 'EOF'
---
name: documentor
description: Technical writer for clear and complete documentation
---

# Documentation Agent

You are a technical writer who creates clear documentation.

**Documentation standards:**
- Start with a one-sentence summary
- Include usage examples
- Document parameters and return values
- Note any gotchas or limitations
EOF

# 実際に使ってみる
copilot --agent reviewer
> @samples/book-app-project/books.py をレビューしてください

# または Agent を切り替える
copilot
> /agent
# "documentor" を選択
> @samples/book-app-project/books.py をドキュメント化してください
```

---

## 📝 課題

### メインチャレンジ: 専門Agentチームを作る

ハンズオンの例では`reviewer`と`documentor`のAgentを作成しました。今度は別のタスク——書籍アプリのデータバリデーション改善——で実践してみましょう：

1. `.github/agents/`にAgentファイル（`.agent.md`）を3つ作成（各Agent1ファイル）
2. 各Agents:
   - **data-validator**: `data.json`の欠如・不正データをチェック（空の著者名、year=0、必須フィールド欠如）
   - **error-handler**: Pythonコードの一賫性のないエラーハンドリングをレビューし、統一アプローチを提案
   - **doc-writer**: docstringとREADMEコンテンツを生成・更新
3. 各Agentを書籍アプリで使用：
   - `data-validator` → `@samples/book-app-project/data.json`をチェック
   - `error-handler` → `@samples/book-app-project/books.py`と`@samples/book-app-project/utils.py`をレビュー
   - `doc-writer` → `@samples/book-app-project/books.py`にdocstringを追加
4. 連携: `error-handler`でエラーハンドリングの稼を特定し、`doc-writer`で改善アプローチを文書化

**完了基準**: 3つの動作するAgentが一貫した高品質な出力を作成し、`/agent`で切り替えられること。

<details>
<summary>💡 ヒント（クリックして展開）</summary>

**スターターテンプレート**: `.github/agents/`に各Agentファイルを作成：

`data-validator.agent.md`:
```markdown
---
description: Analyzes JSON data files for missing or malformed entries
---

You analyze JSON data files for missing or malformed entries.

**Focus areas:**
- Empty or missing author fields
- Invalid years (year=0, future years, negative years)
- Missing required fields (title, author, year, read)
- Duplicate entries
```

`error-handler.agent.md`:
```markdown
---
description: Reviews Python code for error handling consistency
---

You review Python code for error handling consistency.

**Standards:**
- No bare except clauses
- Use custom exceptions where appropriate
- All file operations use context managers
- Consistent return types for success/failure
```

`doc-writer.agent.md`:
```markdown
---
description: Technical writer for clear Python documentation
---

You are a technical writer who creates clear Python documentation.

**Standards:**
- Google-style docstrings
- Include parameter types and return values
- Add usage examples for public methods
- Note any exceptions raised
```

**Testing your agents:**

> 💡 **Note:** You should already have `samples/book-app-project/data.json` in your local copy of this repo. If it is missing, download the original version from the source repo:
> [data.json](https://github.com/github/copilot-cli-for-beginners/blob/main/samples/book-app-project/data.json)

```bash
copilot
> /agent
# リストから"data-validator"を選択
> @samples/book-app-project/data.json 著者名が空または年度が不正な書籍を確認
```

**ヒント:** YAMLフロントマターの`description`フィールドはAgentが動作するために必須です。

</details>

### ボーナスチャレンジ: 指示ライブラリを作る

Agentは必要なときに呼び出すものです。今度は逆の側——`/agent`不要でCopilotが毎セッション自動で読み込む**指示ファイル**——を試してみましょう。

`.github/instructions/`フォルダに3つ以上の指示ファイルを作成してください：
- `python-style.instructions.md` - PEP 8と型ヒント規則の適用
- `test-standards.instructions.md` - テストファイルへのpytest慣例の適用
- `data-quality.instructions.md` - JSONデータエントリの検証

各指示ファイルを書籍アプリのコードでテストしてください。

---

<details>
<summary>🔧 <strong>よくあるミスとトラブルシューティング</strong> （クリックして展開）</summary>

### よくあるミス

| ミス | 発生する営瓜 | 修正方法 |
|---------|-------------|----------|
| Agentフロントマターに`description`がない | Agentが読み込まれず、発見されない | YAMLフロントマターに必ず`description:`を含める |
| Agentの配置場所が不正である | 使用時にAgentが見つからない | `~/.copilot/agents/`（個人）または`.github/agents/`（プロジェクト）に配置 |
| `.agent.md`でなく`.md`を使用している | Agentとして認識されない可能性 | `python-reviewer.agent.md`のように命名する |
| Agentプロンプトが長すぎる | 30,000文字の上限に達する可能性 | Agent定義を簡潔に保ち、詳細な指示はスキルを使用する |

### トラブルシューティング

**Agentが見つからない** - 次の場所にAgentファイルがあるか確認：
- `~/.copilot/agents/`
- `.github/agents/`

利用可能なAgent一覧:

```bash
copilot
> /agent
# 利用可能な全Agentを表示
```

**Agentが指示に従わない** - プロンプトを明確にし、Agent定義に詳細を追加する：
- バージョン付きのフレームワーク/ライブラリ
- チーム慣例
- コードパターンの例

**カスタム指示が読み込まれない** - プロジェクト固有の指示を設定するために`/init`を実行：

```bash
copilot
> /init
```

Or check if they're disabled:
```bash
# 読み込みたい場合は --no-custom-instructions を使用しない
copilot  # デフォルトでカスタム指示を読み込む
```

</details>

---

# まとめ

## 🔑 重要なポイント

1. **組み込みAgents**: `/plan`と`/review`は直接呼び出し、ExploreとTaskは自動起動
2. **カスタムAgent**は`.agent.md`ファイルで定義するスペシャリスト
3. **良いAgent**は明確な専門性、標準、出力形式を持つ
4. **複数Agentの連携**は専門性を組み合わせて複雑な問題を解決
5. **指示ファイル** (`.instructions.md`) はチーム標準を自動適用でエンコード
6. **一賫した出力**は明確にAgent指示から生まれる

> 📋 **クイックリファレンス**: 全コマンドとショートカットの一覧は[GitHub Copilot CLIコマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference)を参照してください。

---

## ➡️ 次のステップ

AgentsはCopilotがコードで*どのようにアプローチし、ターゲットを絞ったアクションを起こすか*を変えます。次は**スキル**——*どのステップに従うか*を変えるモノ——を学びます。AgentsStreamsとAgentsの違いを知りたい方は、Chapter 05で系統的に解説します。

**[Chapter 05: Skills System](../05-skills/README.md)**で学ぶ内容:

- スキルがプロンプトから自動トリガーされる仕組み（スラッシュコマンド不要）
- コミュニティスキルのインストール
- SKILL.mdファイルでカスタムスキルを作成する
- Agents、Agents、MCPの違い
- それぞれの使い分け

---

**[← Back to Chapter 03](../03-development-workflows/README.md)** | **[Continue to Chapter 05 →](../05-skills/README.md)**
