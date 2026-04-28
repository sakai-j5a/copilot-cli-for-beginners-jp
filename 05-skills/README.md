![Chapter 05: Skills System](images/chapter-header.png)

> **Copilotが、毎回説明しなくても、チームのベストプラクティスを自動的に適用できたらどうでしょう？**

この章では、Agent Skillsについて学びます。Skillsは、タスクに関連するときにCopilotが自動的に読み込む指示を含むフォルダです。エージェントがCopilotの*考え方*を変えるのに対し、SkillsはCopilotに*特定のタスクの完了方法*を教えます。セキュリティに関する質問をすると自動適用されるセキュリティ監査Skillを作成し、一貫したコード品質を保つチームプラクティスを構築し、SkillsがCopilot CLI、VS Code、GitHub Copilotクラウドエージェントでどのように機能するかを学びます。


## 🎯 学習目標

この章を終えると、以下のことができるようになります：

- Agent Skillsの仕組みと使い忝を理解する
- SKILL.mdファイルでカスタムSkillsを作成する
- 共有リポジトリのコミュニティSkillsを利用する
- Skills、Agents、MCPの使い分けを知る

> ⏱️ **所要時間の目安**: 約55分（20分読む + 35分ハンズオン）

---

## 🧩 現実世界のアナロジー: 電動工具

汎用ドリルは便利ですが、専用アタッチメントがあると更に強力になります。
<img src="images/power-tools-analogy.png" alt="Power Tools - Skills Extend Copilot's Capabilities" width="800"/>


Skills work the same way. Just like swapping drill bits for different jobs, you can add skills to Copilot for different tasks:

| Skill Attachment | Purpose |
|------------|---------|
| `commit` | Generate consistent commit messages |
| `security-audit` | Check for OWASP vulnerabilities |
| `generate-tests` | Create comprehensive pytest tests |
| `code-checklist` | Apply team code quality standards |



*Skills are specialized attachments that extend what Copilot can do*

---

# Skillsの仕組み

<img src="images/how-skills-work.png" alt="星空を背景にCopilot Skillsを表すRPGスタイルの光るスキルアイコンが光の軌跡で繋がった画像" width="800"/>

Skillsとは何か、なぜ重要か、AgentsやMCPとの違いを学びます。

---

## *Skillsが初めての方へ:* まずここから！

1. **利用可能なSkillsを確認する:**
   ```bash
   copilot
   > /skills list
   ```
   CLI内組の**ビルトインSkills**、プロジェクトフォルダおよび個人フォルダのSkillsを含む全てのSkillsを表示します。

   > 💡 **ビルトインSkills**: Copilot CLIには最初からSkillsがインストールされています。例えば`customizing-copilot-cloud-agents-environment`スキルはCopilotクラウドAgent環境のカスタマイズのガイドを提供します。作成またはインストール不要で使用できます。`/skills list`で利用可能なものを確認してください。

2. **実際のSkillファイルを確認する:** 提供されている[code-checklist SKILL.md](../.github/skills/code-checklist/SKILL.md)でパターンを確認してください。YAMLフロントマターとmarkdownの指示だけで構成されています。

3. **基本概念を理解する:** SkillsはプロンプトがSkillの説明に一致するとCopilotが*自動的に*読み込むタスク固有の指示です。起動不要、自然に質問するだけでいい。


## Skillsの理解

Agent Skillsは、タスクに関連するときCopilotが**自動的に読み込む**指示、スクリプト、リソースを含むフォルダです。Copilotはプロンプトを読み込んで一致するSkillがあるか確認し、関連する指示を自動適用します。

```bash
copilot

> books.pyを品質チェックリストで確認して
# Copilotが"code-checklist"スキルに一致することを検知
# Python品質チェックリストを自動適用

> BookCollectionクラスのテストを生成して
# Copilotが"pytest-gen"スキルを読み込む
# 期待されるテスト構造を適用

> このファイルのコード品質の問題は何ですか？
# Copilotが"code-checklist"スキルを読み込む
# チームの標準に対してチェック
```

> 💡 **重要なポイント**: Skillsはプロンプトがそのdescriptionに一致したとき**自動的にトリガー**されます。自然に質問するだけで、Copilotが関連するSkillsを裏で適用します。次に学ぶ直接呼び出しも可能です。

> 🧰 **すぐ使えるテンプレート**: [.github/skills](../.github/skills/)フォルダにコピーするだけで使えるシンプルなSkillsのサンプルがあります。

### スラッシュコマンドによる直接呼び出し

自動トリガーがSkillsの主な動作方法ですが、Skill名をスラッシュコマンドとして使い**直接呼び出す**こともできます：

```bash
> /generate-tests ユーザー認証モジュールのテストを作成して

> /code-checklist books.pyのコード品質を確認して

> /security-audit APIエンドポイントの脆弱性を確認して
```

特定のSkillを確実に使いたいときに明示的な制御が可能です。

> 📝 **SkillsとAgentsの呼び出し方の違い**: Skillの呼び出しとAgentの呼び出しを混同しないように注意:
> - **Skills**: `/skill-name <プロンプト>`、例: `/code-checklist このファイルをチェックして`
> - **Agents**: `/agent`（リストから選択）または `copilot --agent <name>`（コマンドライン）
>
> 同じ名前のSkillとAgentが両方ある場合（例: "code-reviewer"）、`/code-reviewer`と入力すると**Skill**が呼び出されます（Agentではありません）。

### Skillが使われたかどうかの確認方法

Copilotに直接尋ねることができます：

```bash
> その回答でどのSkillsを使いましたか？

> セキュリティレビュー用にどんなSkillsが使えますか？
```

### Skills vs Agents vs MCP

SkillsはGitHub Copilotの拡張性モデルの一部に過ぎません。AgentsやMCPサーバーとの違いは以下の通りです。

> *MCPについてはまだ心配しなくて大丈夫です。[Chapter 06](../06-mcp-servers/)で学びます。Skillsが全体像の中でどこに位置するかを示すために乗せています。*

<img src="images/skills-agents-mcp-comparison.png" alt="Comparison diagram showing the differences between Agents, Skills, and MCP Servers and how they combine into your workflow" width="800"/>

| 機能 | 内容 | 使い忝 |
|---------|-----------|----------|
| **Agents** | AIの考え方を変える | 多標にわたる専門知識が必要なとき |
| **Skills** | タスク特化型の指示を提供する | 詳細な手順が必要な特定のタスクの繰り返し |
| **MCP** | 外部サービスに接続する | APIからリアルタイムデータが必要なとき |

Agentsは幅広い専門知識に、Skillsは特定タスクの指示に、MCPは外部データに向いています。会話中にAgentが1つ以上のSkillsを使うこともあります。例えば、Agentにコード確認を依頼すると、`security-audit`スキルと`code-checklist`スキルの両方を自動適用することがあります。

> 📚 **詳細ドキュメント**: [About Agent Skills](https://docs.github.com/copilot/concepts/agents/about-agent-skills)公式ドキュメントでSkillの形式やベストプラクティスの全容を確認できます。

---

## 手動プロンプトから自動専門知識へ

Skillsの作り方に入る前に、なぜ学ぶ価値があるのかを*小さな体験*が物語ってくれます。一貫性の向上を確認すると、「どうやるのか」の理解が深まります。

### Skillsのない場合: 不専門なレビュー

コードレビューのたびに、何かを忘れてしまうことがあります：

```bash
copilot

> このコードの問題をレビューして
# 汎用的なレビュー — チーム固有の視点が抑される可能性あり
```

または、毎回長いプロンプトを入力する必要があります：

```bash
> 裸のexcept節、型ヒントの欠如、ミュータブルなデフォルト引数、
> ファイルI/Oのコンテキストマネージャの欠如、
> 50行を超える関数、本番コード内のprint文をチェックして...
```

入力時間: **30秒以上**。一貫性: **記憑を〜に依存**。

### Skillsありの場合: 自動的なベストプラクティス

`code-checklist` Skillがインストールされれば、自然に質問するだけです：

```bash
copilot

> 書籍コレクションコードの品質問題をチェックして
```

**裏側で起きていること**:
1. Copilotがプロンプト内の「コード品質」と「問題」を検知
2. Skillのdescriptionを確認し、`code-checklist` Skillが一致することを検出
3. チームの品質チェックリストを自動読み込み
4. リスト不要で全チェックを実行

<img src="images/skill-auto-discovery-flow.png" alt="How Skills Auto-Trigger - 4-step flow showing how Copilot automatically matches your prompt to the right skill" width="800"/>

*自然に質問するだけ。Copilotが適切なSkillにマッチさせ、自動的に適用します。*

**出力例**:
```
## Code Checklist: books.py

### Code Quality
- [PASS] All functions have type hints
- [PASS] No bare except clauses
- [PASS] No mutable default arguments
- [PASS] Context managers used for file I/O
- [PASS] Functions are under 50 lines
- [PASS] Variable and function names follow PEP 8

### Input Validation
- [FAIL] User input is not validated - add_book() accepts any year value
- [FAIL] Edge cases not fully handled - empty strings accepted for title/author
- [PASS] Error messages are clear and helpful

### Testing
- [FAIL] No corresponding pytest tests found

### Summary
3 items need attention before merge
```

**違い**: チームの基準が毎回自動的に適用され、入力不要で一貫性が保たれます。

---

<details>
<summary>🎬 動作を確認！</summary>

![Skill Trigger Demo](images/skill-trigger-demo.gif)

*デモの出力は異なります。実際のモデル、ツール、回答はここで示したものと異なる場合があります。*

</details>

---

## 大規模での一貫性: チームPRレビューSkill

チームに10点のPRチェックリストがあるとします。Skillがなければ、全工エンジニアが10点すべてを覚えていなければならず、必ず誰かが一つ忘れます。`pr-review` Skillがあれば、チーム全全一貫したレビューが可能になります：

```bash
copilot

> このPRをレビューしてもらえますか？
```

Copilotはチームの`pr-review`スキルを自動読み込み、4項目すべてを確認します:

```
PR Review: feature/user-auth

## セキュリティ ✅
- ハードコードされた秘密情報なし
- 入力バリデーションあり
- 裸のexcept条なし

## コード品質 ⚠️
- [警告] 45行のprint文 — マージ前に削除
- [警告] 78行のTODOにイシュー参照なし
- [警告] 公開関数に型ヒントなし

## テスト ✅
- 新規テスト追加済み
- エッジケースをカバー

## ドキュメント ❌
- [失敗] 破壊的変更がCHANGELOGに記載なし
- [失敗] API変更にOpenAPIスペックの更新が必要
```

**力リ**: 全エンジニアが同じ基準を自動適用できます。新入社員はチェックリストを記憶する必要がなく、Skillが全て処理してくれます。

---

# カスタムSkillsの作成

<img src="images/creating-managing-skills.png" alt="Human and robotic hands building a wall of glowing LEGO-like blocks representing skill creation and management" width="800"/>

SKILL.mdファイルから独自のSkillsを作成しましょう。

---

## Skillsの保存場所

Skillsは`.github/skills/`（プロジェクト固有）または`~/.copilot/skills/`（ユーザーレベル）に保存されます。

### CopilotがSkillsを探す場所

Copilotは以下の場所を自動スキャンします：

| 場所 | スコープ |
|------|-------|
| `.github/skills/` | プロジェクト固有（gitでチーム共有） |
| `~/.copilot/skills/` | ユーザー固有（個人用Skills） |

### Skillsの構成

各Skillは自身のフォルダ内に`SKILL.md`ファイルを持ちます。必要に応じてスクリプト、例示、その他のリソースを含めることもできます：

```
.github/skills/
└── my-skill/
    ├── SKILL.md           # 必須: Skill定義と指示
    ├── examples/          # Optional: Example files Copilot can reference
    │   └── sample.py
    └── scripts/           # Optional: Scripts the skill can use
        └── validate.sh
```

> 💡 **ティップ**: ディレクトリ名はSKILL.mdのfrontmatter内の`name`（小文字・ハイフン区切り）と一致させてください。

### SKILL.mdのフォーマット

SkillはシンプルなYAML frontmatter付きMarkdown形式を使用します:

```markdown
---
name: code-checklist
description: Comprehensive code quality checklist with security, performance, and maintainability checks
license: MIT
---

# Code Checklist

When checking code, look for:

## Security
- SQL injection vulnerabilities
- XSS vulnerabilities
- Authentication/authorization issues
- Sensitive data exposure

## Performance
- N+1 query problems (running one query per item instead of one query for all items)
- Unnecessary loops or computations
- Memory leaks
- Blocking operations

## Maintainability
- Function length (flag functions > 50 lines)
- Code duplication
- Missing error handling
- Unclear naming

## Output Format
Provide issues as a numbered list with severity:
- [CRITICAL] - Must fix before merge
- [HIGH] - Should fix before merge
- [MEDIUM] - Should address soon
- [LOW] - Nice to have
```

**YAMLプロパティ:**

| プロパティ | 必須 | 説明 |
|---------|------|---------|
| `name` | **必須** | 一意の識別子（小文字、スペースはハイフン） |
| `description` | **必須** | Skillの内容とCopilotが使うタイミング |
| `license` | 任意 | このSkillに適用されるライセンス |

> 📖 **公式ドキュメント**: [About Agent Skills](https://docs.github.com/copilot/concepts/agents/about-agent-skills)

### 最初のSkillを作成する

OWASP Top 10の脆弱性を確認するセキュリティ監査Skillを作成しましょう：

```bash
# Create skill directory
mkdir -p .github/skills/security-audit

# Create the SKILL.md file
cat > .github/skills/security-audit/SKILL.md << 'EOF'
---
name: security-audit
description: Security-focused code review checking OWASP (Open Web Application Security Project) Top 10 vulnerabilities
---

# Security Audit

Perform a security audit checking for:

## Injection Vulnerabilities
- SQL injection (string concatenation in queries)
- Command injection (unsanitized shell commands)
- LDAP injection
- XPath injection

## Authentication Issues
- Hardcoded credentials
- Weak password requirements
- Missing rate limiting
- Session management flaws

## Sensitive Data
- Plaintext passwords
- API keys in code
- Logging sensitive information
- Missing encryption

## Access Control
- Missing authorization checks
- Insecure direct object references
- Path traversal vulnerabilities

## Output
For each issue found, provide:
1. File and line number
2. Vulnerability type
3. Severity (CRITICAL/HIGH/MEDIUM/LOW)
4. Recommended fix
EOF

# Skillのテスト（プロンプトに応じて自動ロード）
copilot

> @samples/book-app-project/ Check this code for security vulnerabilities
# Copilotが「Security vulnerabilities」に一致することを検出し
# 自動的にOWASPチェックリストを適用
```

**期待される出力**（結果は異なる場合があります）:

```
Security Audit: book-app-project

[HIGH] Hardcoded file path (book_app.py, line 12)
  File path is hardcoded rather than configurable
  Fix: Use environment variable or config file

[MEDIUM] No input validation (book_app.py, line 34)
  User input passed directly to function without sanitization
  Fix: Add input validation before processing

✅ No SQL injection found
✅ No hardcoded credentials found
```

---

## 良いSkill descriptionの書き方

SKILL.mdの`description`フィールドは非常に重要です！CopilotがSkillを読み込むかどうかをここで判断します：

```markdown
---
name: security-audit
description: セキュリティレビュー、脆弱性スキャン、
  SQLインジェクション、XSS、認証問題の確認、
  OWASP Top 10の脆弱性、セキュリティのベストプラクティスに使用する
---
```

> 💡 **ヒント**: 自分が自然に尋ねるときのキーワードを含めてください。「セキュリティレビュー」と言う場合は、descriptionに「セキュリティレビュー」を含めてください。

### SkillsとAgentsの組み合わせ

Skillsとagentsは連携して機能します。agentが専門知識を提供し、skillが具体的な指示を提供します:

```bash
# code-reviewer agentを開始
copilot --agent code-reviewer

> 書籍アプリの品質問題を確認して
# code-reviewer agentの専門知識と
# code-checklist skillのチェックリストを組み合わせる
```

---

# Skillsの管理と共有

インストール済みSkillの確認、コミュニティSkillの発見、自分のSkillの共有方法。

<img src="images/managing-sharing-skills.png" alt="Managing and Sharing Skills - showing the discover, use, create, and share cycle for CLI skills" width="800" />

---

## `/skills`コマンドでSkillsを管理する

`/skills`コマンドでインストール済みSkillを管理します:

| コマンド | 内容 |
|---------|-------------- |
| `/skills list` | インストール済みSkillを一覧表示 |
| `/skills info <name>` | 特定のSkillの詳細を表示 |
| `/skills add <name>` | Skillを有効化（リポジトリまたはマーケットプレイスから） |
| `/skills remove <name>` | Skillを無効化またはアンインストール |
| `/skills reload` | SKILL.md編集後Skillを再読み込み |

> 💡 **覚えておいて**: 各プロンプトでSkillを「有効化」する必要はありません。インストール後は、プロンプトがdescriptionに一致すると**自動的にトリガー**されます。これらのコマンドはSkillの利用ではなく、利用可能なSkillの管理のためのものです。

### 例: Skillの一覧表示

```bash
copilot

> /skills list

利用可能な Skill:
- security-audit: OWASP Top 10を確認するセキュリティ重点のコードレビュー
- generate-tests: エッジケースを含む包括的なユニットテストを生成
- code-checklist: チームのコード品質チェックリスト
...

> /skills info security-audit

Skill: security-audit
ソース: プロジェクト
場所: .github/skills/security-audit/SKILL.md
説明: OWASP Top 10脆弱性を確認するセキュリティ重点のコードレビュー
```

---

<details>
<summary>動作を確認！</summary>

![List Skills Demo](images/list-skills-demo.gif)

*デモの出力は異なります。実際のモデル、ツール、回答はここで示したものと異なる場合があります。*

</details>

---

### `/skills reload`を使うタイミング

SkillのSKILL.mdを作成または編集した後、Copilotを再起動しなくても`/skills reload`で変更を反映できます：

```bash
# Skillファイルを編集する
# その後Copilot内で:
> /skills reload
Skills reloaded successfully.
```

> 💡 **筮にりたいこと**: `/compact`で会話履歴を要約した後もSkillsは有効のままです。要約後にリロードする必要はありません。

---

## コミュニティSkillsの検索と利用

### Pluginsを使ってSkillsをインストールする

> 💡 **Pluginsとは？** PluginsはSkills、Agents、MCPサーバー設定をまとめてバンドルできるインストール可能なパッケージです。Copilot CLI用の「アプリストア」拡張機能といえます。

`/plugin`コマンドでこれらのパッケージを閲覧またはインストールできます：

```bash
copilot

> /plugin list
# インストール済みPluginsを表示

> /plugin marketplace
# 利用可能Pluginsを閲覧

> /plugin install <plugin-name>
# マーケットプレイスからPluginをインストール
```

ローカルのPluginカタログを最新に保つには：

```bash
copilot plugin marketplace update
```

一つのPluginに関連するSkills、Agents、MCPサーバー設定をまとめてバンドルすることもできます。

### コミュニティのSkillリポジトリ

事前作成済みSkillsはコミュニティリポジトリからも利用できます：

- **[Awesome Copilot](https://github.com/github/awesome-copilot)** - Skillsのドキュメントと例を含むGitHub Copilot公式リソース

### GitHub CLIでコミュニティSkillをインストールする

GitHubリポジトリからSkillをインストールする最簡単な方法は`gh skill install`コマンドです（[GitHub CLI v2.90.0+](https://github.blog/changelog/2026-04-16-manage-agent-skills-with-github-cli/)必須）：

```bash
# awesome-copilotからインタラクティブに選択してインストール
gh skill install github/awesome-copilot

# 特定のSkillを直接インストール
gh skill install github/awesome-copilot code-checklist

# 全プロジェクトで個人利用する場合（userスコープ）
gh skill install github/awesome-copilot code-checklist --scope user
```

> ⚠️ **インストール前に必ず確認**: SkillのSKILL.mdを必ず読んでからインストールしてください。SkillsはCopilotの動作を制御し、悪意あるSkillは有害なコマンドの実行や予期せぬコード変更を指示する可能性があります。

---

# 実践

<img src="../images/practice.png" alt="Warm desk setup with monitor showing code, lamp, coffee cup, and headphones ready for hands-on practice" width="800"/>

学んだ内容を実際に自分のSkillを構築して确かめましょう。

---

## ▶️ 実践してみよう

### さらに多くのSkillsを構築する

下記は異なるパターンを示㊉2つのSkillsです。上記の「Creating Your First Skill」と同じ`mkdir` + `cat`の手順を実行するか、適切な場所にコピペなしてください。[.github/skills](../.github/skills)にサンプルがあります。

### pytestテスト生成Skill

コードベース全体で一貫した pytest構造を確保するSkill：

```bash
mkdir -p .github/skills/pytest-gen

cat > .github/skills/pytest-gen/SKILL.md << 'EOF'
---
name: pytest-gen
description: Generate comprehensive pytest tests with fixtures and edge cases
---

# pytest Test Generation

Generate pytest tests that include:

## Test Structure
- Use pytest conventions (test_ prefix)
- One assertion per test when possible
- Clear test names describing expected behavior
- Use fixtures for setup/teardown

## Coverage
- Happy path scenarios
- Edge cases: None, empty strings, empty lists
- Boundary values
- Error scenarios with pytest.raises()

## Fixtures
- Use @pytest.fixture for reusable test data
- Use tmpdir/tmp_path for file operations
- Mock external dependencies with pytest-mock

## Output
Provide complete, runnable test file with proper imports.
EOF
```

### チームPRレビューSkill

チーム全体で一貫したPRレビュー基準を強制するSkill:

```bash
mkdir -p .github/skills/pr-review

cat > .github/skills/pr-review/SKILL.md << 'EOF'
---
name: pr-review
description: Team-standard PR review checklist
---

# PR Review

Review code changes against team standards:

## Security Checklist
- [ ] No hardcoded secrets or API keys
- [ ] Input validation on all user data
- [ ] No bare except clauses
- [ ] No sensitive data in logs

## Code Quality
- [ ] Functions under 50 lines
- [ ] No print statements in production code
- [ ] Type hints on public functions
- [ ] Context managers for file I/O
- [ ] No TODOs without issue references

## Testing
- [ ] New code has tests
- [ ] Edge cases covered
- [ ] No skipped tests without explanation

## Documentation
- [ ] API changes documented
- [ ] Breaking changes noted
- [ ] README updated if needed

## Output Format
Provide results as:
- ✅ PASS: Items that look good
- ⚠️ WARN: Items that could be improved
- ❌ FAIL: Items that must be fixed before merge
EOF
```

### ステップアップ

1. **Skill作成チャレンジ**: 3項目のチェックリストに対応する`quick-review` Skillを作成してください:
   - 裸のexcept節
   - 型ヒントの欠如
   - 分かりにくい変数名

   「books.pyのクイックレビューをして」と尋ねてテストしてください

2. **Skillの比較**: セキュリティレビューのプロンプトを手動で入力する時間を計りましょう。それから「このファイルのセキュリティ問題を確認して」と言って、security-audit Skillに自動適用させてみてください。Skillはどれくらい時間を節約できたでしょうか？

3. **チームSkillチャレンジ**: チームのコードレビューチェックリストを考えてみてください。Skillとしてエンコードできますか？常に確認すべき3項目を書き出してみましょう。

**確認テスト**: `description`フィールドがなぜ重要か（CopilotがSkillを読み込むかどうかを判断するため）を説明できれば、Skillsを理解した証拠です。

---

## 📝 課題

### メインチャレンジ: 書籍サマリーSkillの構築

上記の例では`pytest-gen`と`pr-review` Skillsを作成しました。今度はまったく違う種類のSkillを作成して、データからフォーマットされた出力を生成する等、実践してみましょう。

1. 現在のSkillsリストを表示する: Copilotを起動し`/skills list`を実行してください。`ls .github/skills/`でプロジェクトSkills、`ls ~/.copilot/skills/`で個人 Skillsを確認できます。
2. `.github/skills/book-summary/SKILL.md`に書籍コレクションのフォーマット済みmarkdownサマリーを生成する`book-summary` Skillを作成する
3. Skillには以下を含める:
   - 明確なnameとdescription（descriptionはマッチングに不可欠です！）
   - 具体的なフォーマットルール（例: タイトル、著者、年、読みたか状態のmarkdownテーブル）
   - 出力規則（例: 読みたか状態に✅/❌、年順ソート）
4. Skillをテスト: `@samples/book-app-project/data.json このコレクションの書籍をまとめて`
5. `/skills list`で自動トリガーを確認する
6. `/book-summary このコレクションの書籍をまとめて`で直接呼び出しも試す

**成功基準**: 書籍コレクションについて質問するとCopilotが自動適用する`book-summary` Skillが完成していること。

<details>
<summary>💡 ヒント（クリックして展開）</summary>

**スターターテンプレート**: `.github/skills/book-summary/SKILL.md`を作成:

```markdown
---
name: book-summary
description: Generate a formatted markdown summary of a book collection
---

# Book Summary Generator

Generate a summary of the book collection following these rules:

1. Output a markdown table with columns: Title, Author, Year, Status
2. Use ✅ for read books and ❌ for unread books
3. Sort by year (oldest first)
4. Include a total count at the bottom
5. Flag any data issues (missing authors, invalid years)

Example:
| Title | Author | Year | Status |
|-------|--------|------|--------|
| 1984 | George Orwell | 1949 | ✅ |
| Dune | Frank Herbert | 1965 | ❌ |

**Total: 2 books (1 read, 1 unread)**
```

**テストに抜る:**
```bash
copilot
> @samples/book-app-project/data.json このコレクションの書籍をまとめて
# descriptionの一致に基づいてSkillが自動トリガーされるはず
```

**トリガーされない場合:** `/skills reload`を実行してから再度尋ねてください。

</details>

### ボーナスチャレンジ: コミットメッセージSkill

1. 一貫したフォーマットでconventional commitメッセージを生成する`commit-message` Skillを作成する
2. 変更をステージして「ステージング済みの変更に対するコミットメッセージを生成して」と尋ねてテストする
3. Skillをドキュメント化し、`copilot-skill`トピックでGitHubに共有する

---

<details>
<summary>🔧 <strong>よくあるミスとトラブルシューティング</strong> (クリックで展開)</summary>

### Common Mistakes

| ミス | 結果 | 修正方法 |
|---------|---------|----------|
| `SKILL.md`以外のファイル名にする | Skillが認識されない | ファイル名は必ず正確に`SKILL.md`にすること |
| `description`が曖昧 | Skillが自動読み込みされない | descriptionは主要な検索メカニズム。指尋語を明記すること |
| フロントマターの`name`または`description`がない | Skillが読み込み失敗 | YAMLフロントマターに両方追加する |
| フォルダ位置が間違っている | Skillが見つからない | `.github/skills/skill-name/`（プロジェクト）または`~/.copilot/skills/skill-name/`（個人）を使用 |

### トラブルシューティング

**Skillが使われない** - 気になるSkillがCopilotに使われていない場合:

1. **descriptionを確認**: 尋ね方と一致していますか？
   ```markdown
   # 悪い例: 曖昧すぎる
   description: コードをレビューする

   # 良い例: 指尋語を含む
   description: コードレビュー、品質確認、
     バグ検出、セキュリティ問題、ベストプラクティス違反の確認に使用
   ```

2. **ファイル位置を確認**:
   ```bash
   # プロジェクトSkills
   ls .github/skills/

   # ユーザー Skills
   ls ~/.copilot/skills/
   ```

3. **SKILL.mdのフォーマットを確認**: frontmatterは必須です:
   ```markdown
   ---
   name: skill-name
   description: Skillの内容と使うタイミング
   ---

   # 指示事項はここ
   ```

**Skillが表示されない** - フォルダ構成を確認:
```
.github/skills/
└── my-skill/           # フォルダ名
    └── SKILL.md        # 正確にSKILL.md（大文字小文字区別あり）
```

Skillを作成または編集した後は`/skills reload`で変更を反映してください。

**Skillが実際に機能しているかテスト** - Copilotに直接尋ねる:
```bash
> コード品質の確認に利用できるスキルはありますか？
# Copilotが見つかった関連Skillを説明します
```

**Skillが実際に機能しているかどうか確認する方法**

1. **出力フォーマットを確認**: Skillが出力フォーマット（例: `[CRITICAL]`タグ）を指定している場合、応答内にそれが存在するか確認
2. **直接尋ねる**: 回答を得た後、「その回答に何がSkillsを使いましたか？」と尋ねる
3. **有無で比較する**: `--no-custom-instructions`で同じプロンプトを試してみる:
   ```bash
   # Skillsあり
   copilot --allow-all -p "Review @file.py for security issues"

   # Skillsなし（ベースライン比較）
   copilot --allow-all -p "Review @file.py for security issues" --no-custom-instructions
   ```
4. **特定のチェックを確認する**: Skillに特定のチェック（「50行超える関数」など）があれば、出力にそれが含まれるか確認する

</details>

---

# まとめ

## 🔑 重要なポイント

1. **Skillsは自動適用**: プロンプトがSkillのdescriptionに一致するとCopilotが読み込みます
2. **直接呼び出し**: `/skill-name`のスラッシュコマンドでSkillを直接呼び出すことも可能
3. **SKILL.md形式**: YAMLフロントマター（name、description、オプショナルのlicense）+ markdown指示
4. **保存場所**: プロジェクト/チーム共有は`.github/skills/`、個人利用は`~/.copilot/skills/`
5. **descriptionが鍵**: 自分が自然に質問するキーワードを含むdescriptionを書く

> 📋 **クイックリファレンス**: [GitHub Copilot CLIコマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference)で全コマンドとショートカットを確認できます。

---

## ➡️ 次のステップ

Skillsは自動読み込みによってCopilotの機能を拡張します。では、外部サービスへの接続はどうでしょう？そこでMCPの登場です。

**[Chapter 06: MCP Servers](../06-mcp-servers/README.md)**では以下を学びます：

- MCP（Model Context Protocol）とは何か
- GitHub、ファイルシステム、ドキュメントサービスへの接続
- MCPサーバーの設定
- マルチサーバーワークフロー

---

**[← Chapter 04に戻る](../04-agents-custom-instructions/README.md)** | **[Chapter 06へ進む →](../06-mcp-servers/README.md)**
