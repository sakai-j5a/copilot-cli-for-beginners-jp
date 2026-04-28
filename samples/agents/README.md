# サンプルエージェント定義

このフォルダには、GitHub Copilot CLI向けのシンプルなエージェントテンプレートが含まれています。エージェントの使い始めのこうしに役立ててください。

## クイックスタート

```bash
# Copy an agent to your personal agents folder
cp hello-world.agent.md ~/.copilot/agents/

# Or copy to your project for team sharing
cp python-reviewer.agent.md .github/agents/
```

## このフォルダに含まれるサンプルファイル

| ファイル | 説明 | 最適な用途 |
|------|-------------|----------|
| `hello-world.agent.md` | 最小構成の例（11行） | フォーマットを学ぶ |
| `python-reviewer.agent.md` | Pythonコード品質レビューアー | コードレビュー、PEP 8、型ヒント |
| `pytest-helper.agent.md` | pytestテストのスペシャリスト | テスト生成、フィクスチャ、エッジケース |

## さらなるエージェントを見つける

- **[github/awesome-copilot](https://github.com/github/awesome-copilot)** - コミュニティエージェントと指示ファイルを含むGitHub公式リソース

---

## エージェントファイルのフォーマット

各エージェントファイルには、少なくとも`description`フィールドを含むYAMLフロントマターが必要です：

```markdown
---
name: my-agent
description: Brief description of what this agent does
tools: ["read", "edit", "search"]  # Optional: limit available tools
---

# Agent Name

Agent instructions go here...
```

**利用可能なYAMLプロパティ:**

| プロパティ | 必須 | 説明 |
|----------|----------|--------|
| `description` | **はい** | エージェントの機能 |
| `name` | いいえ | 表示名（デフォルトはファイル名） |
| `tools` | いいえ | 許可するツールのリスト（省略すると全て）。下記エイリアスを参照 |
| `target` | いいえ | `vscode`または`github-copilot`のみに限定 |

**ツールエイリアス**: `read`、`edit`、`search`、`execute`（シェル）、`web`、`agent`

> 💡 **注記**: `model`プロパティはVS Codeでは機能しますが、Copilot CLIではまだサポートされていません。
>
> 📖 **公式ドキュメント**: [Custom agents configuration](https://docs.github.com/copilot/reference/custom-agents-configuration)

## エージェントファイルの保存場所

エージェントは以下の場所に保存できます：
- `~/.copilot/agents/` - 全プロジェクトで利用できるグローバルエージェント
- `.github/agents/` - プロジェクト内のエージェント
- `.agent.md`ファイル - VS Code互換フォーマット

各エージェントは`.agent.md`拡張子を持つ別々のファイルです。

---

## 使用例

```bash
# Start with a specific agent
copilot --agent python-reviewer

# Or select an agent interactively during a session
copilot
> /agent
# Select "python-reviewer" from the list

# The agent's expertise applies to your prompts
> @samples/book-app-project/books.py Review this code for quality issues

# Switch to a different agent
> /agent
# Select "pytest-helper"

> @samples/book-app-project/tests/test_books.py What additional tests should we add?
```

---

## 独自エージェントを作成する

1. `~/.copilot/agents/`内に`.agent.md`拡張子で新規ファイルを作成
2. 少なくとも`description`フィールドを含むYAMLフロントマターを追加
3. 説明的なヘッダーを追加（例：`# セキュリティエージェント`）
4. エージェントの専門分野、標準、行動を定義する
5. `/agent`または`--agent <名前>`でエージェントを使用する

**効果的なエージェント作成のヒント:**
- 専門分野を具体的に示す
- コード標準やパターンを指定する
- エージェントが確認する項目を定義する
- 出力フォーマットの好みを包含する
