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

# Chapter 04: Agents とカスタム指示

**Pythonコードレビューアー、テストエキスパート、セキュリティレビューアーを全部1つのツールで雇えたら？**

Agentsで専門知識を持つAIスペシャリストを作成・活用しましょう

> ⏱️ 所要時間の目安: 約55分（20分読む + 35分ハンズオン）

---

## 🎯 学習目標

この章を終えると：

- ビルトインAgent（`/plan`、`/review`）を使いこなせる
- `.agent.md`ファイルで専門Agentを作成できる
- `/agent`と`--agent`でAgentを切り替えられる
- プロジェクト用のカスタム指示ファイルを作成できる

---

## 🧩 アナロジー: スペシャリストの雇用

<img src="images/hiring-specialists-analogy.png" alt="Hiring Specialists" width="60%"/>

| 問題 | 専門家 | 理由 |
|------|--------|------|
| 水漏れ | 配管工 | 配管コードを尊重し、専用工具を持つ |
| 配線工事 | 電気工 | 安全要件を理解し、法規に準拠 |
| 屋根修理 | 屋根工 | 材料や地元の気候に精通 |

Agentも同じ。特定タスクに特化したAIスペシャリストを使いましょう

---

## 組み込みAgents

**Ch03で既に使っていました！** `/plan`と`/review`は組み込みAgentsです

| Agent | 呼び出し方 | 内容 |
|-------|-----------|------|
| **Plan** | `/plan`またはShift+Tab | 実装計画を作成 |
| **Code-review** | `/review` | 変更をレビュー |
| **Init** | `/init` | プロジェクト設定を生成 |
| **Explore** | *自動起動* | コードベース探索時 |
| **Task** | *自動起動* | コマンド実行時 |

--

## 組み込みAgentsの実例

```bash
copilot

# Plan Agentで実装計画を作成
> /plan 書籍アプリの年度入力にバリデーションを追加

# Code-review Agentで変更をレビュー
> /review

# ExploreとTaskは自動起動
> テストスイートを実行して        # Task agent
> 書籍データの読み込み方法を探索   # Explore agent
```

---

## Agentファイルの作り方

`.agent.md`ファイル = YAMLフロントマター + Markdown指示

```markdown
---
name: my-reviewer
description: Code reviewer focused on bugs and security
---

# Code Reviewer

You are a code reviewer focused on finding bugs
and security issues.

When reviewing code, always check for:
- SQL injection vulnerabilities
- Missing error handling
- Hardcoded secrets
```

> 💡 `description`は**必須**。`name`、`tools`、`model`は任意

--

## Agentファイルの配置場所

| 場所 | スコープ | 適切な用途 |
|------|--------|---------|
| `.github/agents/` | プロジェクト固有 | チーム共有Agent |
| `~/.copilot/agents/` | グローバル | 個人用Agent |

このプロジェクトのサンプル：

| ファイル | 説明 |
|---------|------|
| `hello-world.agent.md` | 最小限の例 |
| `python-reviewer.agent.md` | Pythonコード品質レビューアー |
| `pytest-helper.agent.md` | pytestテストスペシャリスト |

---

## カスタムAgentの使い方

### インタラクティブモード

```bash
copilot
> /agent
# Agent一覧から選択
```

### プログラマティックモード

```bash
copilot --agent python-reviewer
> @samples/book-app-project/books.py をレビュー
```

> 💡 **切り替え**: `/agent`でいつでも別のAgentに変更。
> **no agent**を選択すると標準モードに戻る

---

## スペシャリスト vs 汎用: 違いを見る

### Agentなし（汎用）

```python
def search_by_year_range(books, start_year, end_year):
    results = []
    for book in books:
        if book['year'] >= start_year and book['year'] <= end_year:
            results.append(book)
    return results
```

基本的。動作はする。でも足りないものが多い

--

## Python Reviewer Agent使用時

```python
def search_by_year_range(
    books: List[Dict[str, Any]],
    start_year: int, end_year: int
) -> List[Dict[str, Any]]:
    """Search for books within a year range.
    Raises:
        ValueError: If start_year > end_year
        TypeError: If year values are not integers
    """
    if not isinstance(start_year, int) or not isinstance(end_year, int):
        raise TypeError("Year values must be integers")
    if start_year > end_year:
        raise ValueError(f"Start year ({start_year}) > end year ({end_year})")
    return [book for book in books
            if isinstance(book.get('year'), int)
            and start_year <= book['year'] <= end_year]
```

**同じプロンプトで圧倒的に優れた出力**

---

## 複数Agentの連携

```bash
copilot

# python-reviewerで設計
> /agent
# "python-reviewer"を選択
> @books.py find_by_year_rangeメソッドの最善アプローチは？

# pytest-helperでテスト設計
> /agent
# "pytest-helper"を選択
> テストケースとエッジケースを設計して

# 両方の設計を統合
> 実装計画を作成して
```

**あなたはスペシャリストを指揮するアーキテクト**

![Python Reviewer Demo](images/python-reviewer-demo.gif)

---

## Copilot向けプロジェクト設定

Agentsは呼び出すスペシャリスト。**指示ファイル**は常時有効なチームルール

### /initでクイックセットアップ

```bash
copilot
> /init
```

Copilotがプロジェクトをスキャンし、カスタム指示ファイルを自動生成

--

## 指示ファイルの形式

| ファイル | スコープ |
|---------|--------|
| `AGENTS.md` | プロジェクトルート（推奨、クロスプラットフォーム標準） |
| `.github/copilot-instructions.md` | GitHub Copilot固有 |
| `.github/instructions/*.instructions.md` | トピック別の詳細指示 |

> 🎯 まず`AGENTS.md`から始めましょう。他の形式は後で探索できます

--

## Agentファイルリファレンス

| プロパティ | 必須 | 説明 |
|----------|------|------|
| `name` | 不要 | 表示名（デフォルト=ファイル名） |
| `description` | **必須** | Agentの内容説明 |
| `tools` | 不要 | 許可ツール（`read`,`edit`,`search`,`execute`） |
| `target` | 不要 | `vscode`または`github-copilot`に限定 |

```bash
# カスタム指示を無効化してデバッグ
copilot --no-custom-instructions
```

---

## ▶️ 実践

<img src="../images/practice.png" alt="Practice" width="60%"/>

--

## 実践1: Agentを作成する

```bash
mkdir -p .github/agents

cat > .github/agents/reviewer.agent.md << 'EOF'
---
name: reviewer
description: Senior code reviewer focused on security
---

# Code Reviewer Agent

**Review priorities:**
1. Security vulnerabilities
2. Performance issues
3. Maintainability concerns

**Output format:**
[CRITICAL], [HIGH], [MEDIUM], [LOW]
EOF
```

--

## 実践2: Agentを使う

```bash
copilot --agent reviewer
> @samples/book-app-project/books.py をレビュー
```

### 実践3: 複数Agent切り替え

```bash
copilot
> /agent          # python-reviewerを選択
> @books.py の設計をレビュー

> /agent          # pytest-helperに切り替え
> テストケースを設計して
```

---

## 📝 課題

1. セキュリティ特化のAgentを`.github/agents/`に作成
2. `--agent`で起動してbuggy-codeをレビュー
3. `/agent`でAgent切り替えを実践
4. `AGENTS.md`をプロジェクトルートに作成し、プロジェクト規約を定義

--

## チャレンジ

- **Agentチェーン**: python-reviewer → pytest-helper → 標準モードを1セッションで切り替えながら機能を設計・テスト・実装

- **チーム共有**: 作成したAgentを`.github/agents/`にコミットしてチームメンバーと共有
