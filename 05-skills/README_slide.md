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

# Chapter 05: Skills システム

**Copilotが毎回説明しなくても、チームのベストプラクティスを自動適用できたら？**

SkillsでCopilotにタスク固有の専門知識を教えましょう

> ⏱️ 所要時間の目安: 約55分（20分読む + 35分ハンズオン）

---

## 🎯 学習目標

この章を終えると：

- Agent Skillsの仕組みと使い方を理解できる
- SKILL.mdファイルでカスタムSkillsを作成できる
- コミュニティSkillsを利用できる
- Skills、Agents、MCPの使い分けを知る

---

## 🧩 アナロジー: 電動工具

<img src="images/power-tools-analogy.png" alt="Power Tools" width="60%"/>

汎用ドリルは便利ですが、**専用アタッチメント**でさらに強力に

--

| Skillアタッチメント | 用途 |
|-------------------|------|
| `commit` | 一貫したコミットメッセージ生成 |
| `security-audit` | OWASP脆弱性チェック |
| `generate-tests` | 包括的なpytestテスト生成 |
| `code-checklist` | チームコード品質基準の適用 |

---

## Skillsの仕組み

<img src="images/how-skills-work.png" alt="How Skills Work" width="60%"/>

Skillsはプロンプトに一致すると**自動的にトリガー**されます

```bash
copilot

> books.pyを品質チェックリストで確認して
# → "code-checklist"スキルが自動適用

> BookCollectionクラスのテストを生成して
# → "pytest-gen"スキルが自動読み込み
```

> 💡 **重要**: Skillsは`description`にマッチすると自動起動。明示的な呼び出し不要

--

## スラッシュコマンドで直接呼び出し

自動トリガーに加え、Skill名で**直接呼び出し**も可能：

```bash
> /generate-tests ユーザー認証モジュールのテストを作成

> /code-checklist books.pyのコード品質を確認

> /security-audit APIエンドポイントの脆弱性を確認
```

> 📝 **SkillsとAgentsの呼び出し方の違い**:
> - Skills: `/skill-name <プロンプト>`
> - Agents: `/agent`（リスト選択）or `--agent <name>`

---

## Skills vs Agents vs MCP

<img src="images/skills-agents-mcp-comparison.png" alt="Comparison" width="60%"/>

--

| 機能 | 内容 | 使い所 |
|------|------|--------|
| **Agents** | AIの考え方を変える | 多方面の専門知識が必要なとき |
| **Skills** | タスク特化の指示 | 特定タスクの繰り返し |
| **MCP** | 外部サービスに接続 | リアルタイムデータが必要なとき |

> AgentがSkillを自動適用することもある。例: コード確認を依頼 → `security-audit`スキルと`code-checklist`スキルを両方適用

---

## Skillsなし vs あり

### Skillsなし — 毎回長いプロンプト

```bash
> 裸のexcept節、型ヒントの欠如、ミュータブルなデフォルト引数、
  ファイルI/Oのコンテキストマネージャの欠如、
  50行を超える関数、本番コード内のprint文をチェック...
```

入力時間: **30秒以上**。一貫性: **記憶に依存**

--

## Skillsあり — 自動ベストプラクティス

```bash
copilot

> 書籍コレクションコードの品質問題をチェックして
```

<img src="images/skill-auto-discovery-flow.png" alt="Auto Discovery" width="60%"/>

**出力例**:

```
## Code Checklist: books.py

### Code Quality
- [PASS] All functions have type hints
- [PASS] No bare except clauses
- [FAIL] User input is not validated

### Summary
3 items need attention before merge
```

---

## SKILL.mdのフォーマット

```markdown
---
name: code-checklist
description: Comprehensive code quality checklist
  with security, performance, and maintainability checks
license: MIT
---

# Code Checklist

When checking code, look for:

## Security
- SQL injection vulnerabilities
- Sensitive data exposure

## Performance
- N+1 query problems
- Unnecessary loops

## Output Format
- [CRITICAL] Must fix before merge
- [HIGH] Should fix before merge
```

--

## YAMLプロパティ

| プロパティ | 必須 | 説明 |
|---------|------|------|
| `name` | **必須** | 一意の識別子（小文字、ハイフン区切り） |
| `description` | **必須** | Skillの内容と適用タイミング |
| `license` | 任意 | ライセンス |

### Skillsの保存場所

| 場所 | スコープ |
|------|--------|
| `.github/skills/` | プロジェクト固有（gitで共有） |
| `~/.copilot/skills/` | ユーザー固有（個人用） |

---

## 最初のSkillを作成する

```bash
# Skillディレクトリを作成
mkdir -p .github/skills/security-audit

# SKILL.mdを作成
cat > .github/skills/security-audit/SKILL.md << 'EOF'
---
name: security-audit
description: Security-focused code review checking
  OWASP Top 10 vulnerabilities
---

# Security Audit

Check for:
## Injection Vulnerabilities
- SQL injection
- Command injection

## Sensitive Data
- Hardcoded credentials
- API keys in code

## Output
1. File and line number
2. Severity (CRITICAL/HIGH/MEDIUM/LOW)
3. Recommended fix
EOF
```

--

## Skillのテスト

```bash
copilot

> @samples/book-app-project/ セキュリティ脆弱性をチェック
# → "security-audit"スキルが自動適用

# 期待される出力
[HIGH] Hardcoded file path (book_app.py, line 12)
  Fix: Use environment variable or config file

[MEDIUM] No input validation (book_app.py, line 34)
  Fix: Add input validation before processing
```

---

## /skills コマンド

| コマンド | 内容 |
|---------|------|
| `/skills list` | インストール済みSkillを一覧表示 |
| `/skills info <name>` | 特定Skillの詳細 |
| `/skills add <name>` | Skillを有効化 |
| `/skills remove <name>` | Skillを無効化 |
| `/skills reload` | SKILL.md編集後に再読み込み |

![List Skills Demo](images/list-skills-demo.gif)

--

## コミュニティSkillsの利用

### GitHub CLIでインストール

```bash
# awesome-copilotからインタラクティブに選択
gh skill install github/awesome-copilot

# 特定Skillを直接インストール
gh skill install github/awesome-copilot code-checklist

# 全プロジェクトで個人利用
gh skill install github/awesome-copilot code-checklist --scope user
```

### Pluginsでインストール

```bash
copilot
> /plugin marketplace    # 利用可能Pluginsを閲覧
> /plugin install <name> # インストール
```

> ⚠️ インストール前にSKILL.mdを必ず読んでから

---

## ▶️ 実践

<img src="../images/practice.png" alt="Practice" width="60%"/>

--

## 実践1: Skill一覧を確認

```bash
copilot
> /skills list
```

ビルトインSkills、プロジェクトSkills、個人Skillsを確認

--

## 実践2: セキュリティSkillを作成

上記の手順でsecurity-auditスキルを作成し、テスト：

```bash
copilot
> @samples/book-app-project/ セキュリティ脆弱性をチェック
```

--

## 実践3: SkillsとAgentsの組み合わせ

```bash
copilot --agent code-reviewer
> 書籍アプリの品質問題を確認して
# code-reviewer agentの専門知識と
# code-checklist skillのチェックリストを組み合わせ
```

---

## 📝 課題

1. `/skills list` でインストール済みSkillsを確認
2. security-audit Skillを作成してbuggy-codeでテスト
3. SkillsとAgentsを組み合わせてコードレビュー
4. SKILL.mdのdescriptionを改善して自動トリガーの精度を向上

--

## チャレンジ

- **チームSkill**: PRレビュー用のSkillを作成（セキュリティ、品質、テスト、ドキュメントの4カテゴリ）

- **description最適化**: 同じSkillのdescriptionを変えて、どのプロンプトでトリガーされるか実験
