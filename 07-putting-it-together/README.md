![Chapter 07: Putting It All Together](images/chapter-header.png)

> **これまで学んだ全てがここで結合します。イデアからマージ済みPRまで、一度のセッションで完了させましょう。**

この章では、学んだ全てを完全なワークフローにまとめます。マルチエージェントの連携を使って機能を構築し、コミット前にセキュリティ問題を捕捉するプリコミットフックを設定し、CopilotをCI/CDパイプラインに統合し、機能アイデアからマージ済みPRまでを一つのターミナルセッションで完了させます。ここでこそ、GitHub Copilot CLIが真のフォースマルチプライヤーになります。

> 💡 **注記**: この章ではこれまで学んだ全てを組み合わせる方法を示します。**Agents、Skills、MCPは生産性を高めるのに役立ちますが、必須ではありません。** 基本的なワークフロー―説明、計画、実装、テスト、レビュー、リリース―は第00−0章の機能だけで十分実現できます。

## 🎯 学習目標

この章を終えると、以下のことができるようになります：

- Agents、Skills、MCPを統合したワークフローを構築する
- マルチツールアプローチで完全な機能を開発する
- フックで基本的な自動化を設定する
- プロフェッショナルな開発のベストプラクティスを実践する

> ⏱️ **所要時間の目安**: 約75分（15分読む + 60分ハンズオン）

---

## 🧩 現実世界のアナロジー: オーケストラ

<img src="images/orchestra-analogy.png" alt="Orchestra Analogy - Unified Workflow" width="800"/>

シンフォニーオーケストラには山のセクションがあります:
- **弦楽器**が基盤を提供する（コアワークフローのように）
- **金管楽器**がパワーを加える（専門知識を持つAgentsのように）
- **木管楽器**が色彩を加える（機能を拡張するSkillsのように）
- **打楽器**がリズムを刺す（外部システムに接続するMCPのように）

各セクション単独では限界がありますが、指揮者によってまとめられると素晴らしい音楽が生まれます。

**これがこの章で学ぶことです！**<br>
*指揮者がオーケストラを導くように、Agents、Skills、MCPを統合したワークフローを指揮する*

コードを修改し、テストを生成し、レビューし、PRを作成する―全て一セッションで―シナリオを歩んでみましょう。

---

## 一セッションでアイデアからマージ済みPRまで

エディター、ターミナル、テストランナー、GitHub UIを行き来してその度コンテキストを失わせる代わりに、一つのターミナルセッションで全ツールを組み合わせられます。このパターンは下記の[統合パターン](#the-integration-pattern-for-power-users)セクションで説明します。

```bash
# インタラクティブモードでCopilotを起動
copilot

> 書籍アプリに「未読の一覧表示」コマンドを追加したい。
> readがFalseの書籍だけを表示する機能です。どのファイルを変更する必要がありますか？

# Copilotが高レベルの計画を作成...

# PYTHON-REVIEWER AGENTに切り替え
> /agent
# "python-reviewer"を選択

> @samples/book-app-project/books.py get_unread_booksメソッドを設計してください。
> 最善のアプローチは何ですか？

# Python-reviewer agentが以下を生成:
# - メソッドシグネチャと戻り値の型
# - リスト内包表記を使ったフィルター実装
# - 空のコレクションのエッジケース処理

# PYTEST-HELPER AGENTに切り替え
> /agent
# "pytest-helper"を選択

> @samples/book-app-project/tests/test_books.py 未読書籍のフィルタリングの
> テストケースを設計してください。

# Pytest-helper agentが以下を生成:
# - 空のコレクションのテスト
# - 既読/未読混在のテスト
# - 全て既読のテスト

# 実装
> books.pyのBookCollectionにget_unread_booksメソッドを追加してください
> book_app.pyに"list unread"コマンドオプションを追加してください
> show_help関数のヘルプテキストを更新してください

# テスト
> 新機能の包括的なテストを生成してください

# 以下のようなテストが複数生成される:
# - ハッピーパス（3テスト） — 正しくフィルター、既読を除外、未読を含む
# - エッジケース（4テスト） — 空のコレクション、全て既読、未読なし、書籍、1冊
# - パラメータライズ（5ケース） — @pytest.mark.parametrizeで既読/未読の割合を変化
# - 統合テスト（4テスト） — mark_as_read、remove_book、add_book、データ整合性との連携

# 変更をレビュー
> /review

# レビューが通ったら、/prで現在のブランチのPRを操作する
> /pr [view|create|fix|auto]

# またはターミナルから自然に依頼する
> "機能: 未読書籍の一覧表示コマンドを追加"というタイトルでプルリクエストを作成してください
```

**一般的なアプローチ**: エディター、ターミナル、テストランナー、ドキュメント、GitHub UIを行き来す。度その度コンテキストを失い、摩擦を生じる。

**重要な洞察**: アーキテクトのように専門家を指揮した。寓は詳細を処理し、かなたはビジョンに集中できた。

> 💡 **更に進む**: このような大規模なマルチステップ計画には、`/fleet`で独立したサブタスクを並列実行してみましょう。詳細は[Official docs](https://docs.github.com/copilot/concepts/agents/copilot-cli/fleet)を参照してください。

---

# 追加ワークフロー

<img src="images/combined-workflows.png" alt="People assembling a colorful giant jigsaw puzzle with gears, representing how agents, skills, and MCP combine into unified workflows" width="800"/>

Chapter 04-06を完了したパワーユーザー向けに、Agents、Skills、MCPが如何に効果を乗算するかを示すワークフローです。

## 統合パターン

全てを組み合わせるためのメンタルモデルです:

<img src="images/integration-pattern.png" alt="The Integration Pattern - A 4-phase workflow: Gather Context (MCP), Analyze and Plan (Agents), Execute (Skills + Manual), Complete (MCP)" width="800"/>

---

## ワークフロー1: バグ調査と修正

完全なツール連携による現実のバグ修正:

```bash
copilot

# フェーズ1: GitHub MCPでバグを理解する
> Issue #1の詳細を教えてください

# 学習: "find_by_authorが部分名で機能しない"

# フェーズ2: ベストプラクティスを調査する（web + GitHubソースで深いリサーチ）
> /research Pythonの大文字小文字を無視した文字列マッチングのベストプラクティス

# フェーズ3: 関連コードを見つける
> @samples/book-app-project/books.py find_by_authorメソッドを表示してください

# フェーズ4: エキスパートに分析してもらう
> /agent
# "python-reviewer"を選択

> 部分名マッチングの問題についてこのメソッドを分析してください

# Agentが特定: メソッドは部分一致でなく完全一致を使っている

# フェーズ5: Agentのガイダンスで修正
> 小文字比較と'in'演算子を使って修正を実装してください

# フェーズ6: テストを生成
> /agent
# "pytest-helper"を選択

> find_by_authorの部分一致のpytestテストを生成してください
> テストケース: 部分名、大文字小文字のバリエーション、一致なし

# フェーズ7: コミットとPR
> この修正のコミットメッセージを生成してください

> Issue #1にリンクするプルリクエストを作成してください
```

---

## ワークフロー2: コードレビューの自動化（オプション）

> 💡 **このセクションはオプションです。** pre-commitフックはチームに役立ちますが、生産性が高いために必須ではありません。始めたての方はスキップしても構いません。
>
> ⚠️ **パフォーマンスの注意事項**: このフックはステージ済みファイルごとに`copilot -p`を呼び出し、ファイルごとに数秒かかります。大規模なコミットでは、重要ファイルのみに絞り込むか、`/review`で手動レビューすることを検討してください。

**gitフック**とはGitが特定のタイミング（例: コミット直前）に自動実行するスクリプトです。これを使ってコードの自動チェックを設定できます。Copilotレビューをコミットに自動適用する方法は以下の通りです：

```bash
# Create a pre-commit hook
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash

# Get staged files (Python files only)
STAGED=$(git diff --cached --name-only --diff-filter=ACM | grep -E '\.py$')

if [ -n "$STAGED" ]; then
  echo "Running Copilot review on staged files..."

  for file in $STAGED; do
    echo "Reviewing $file..."

    # Use timeout to prevent hanging (60 seconds per file)
    # --allow-all auto-approves file reads/writes so the hook can run unattended.
    # Only use this in automated scripts. In interactive sessions, let Copilot ask for permission.
    REVIEW=$(timeout 60 copilot --allow-all -p "Quick security review of @$file - critical issues only" 2>/dev/null)

    # Check if timeout occurred
    if [ $? -eq 124 ]; then
      echo "Warning: Review timed out for $file (skipping)"
      continue
    fi

    if echo "$REVIEW" | grep -qi "CRITICAL"; then
      echo "Critical issues found in $file:"
      echo "$REVIEW"
      exit 1
    fi
  done

  echo "Review passed"
fi
EOF

chmod +x .git/hooks/pre-commit
```

> ⚠️ **macOSユーザーへ**: `timeout`コマンドはmacOSにはデフォルトで包含されていません。`brew install coreutils`でインストールするか、`timeout 60`をタイムアウトなしの単純呼び出しに概えてください。

> 📚 **公式ドキュメント**: フックの全APIは[Use hooks](https://docs.github.com/copilot/how-tos/copilot-cli/use-hooks)と[Hooks設定リファレンス](https://docs.github.com/copilot/reference/hooks-configuration)を参照してください。
>
> 💡 **組み込みの代替手段**: Copilot CLIには`copilot hooks`という組み込みのフックシステムがあり、pre-commitなどのイベントで自動実行できます。手動gitフックで完全な制御が可能で、組み込みシステムはよりシンプルに設定できます。上記ドキュメントの完全な参照をご確認ください。

これで、全コミットにクイックなセキュリティレビューが自動適用されます:

```bash
git add samples/book-app-project/books.py
git commit -m "Update book collection methods"

# Output:
# Running Copilot review on staged files...
# Reviewing samples/book-app-project/books.py...
# Critical issues found in samples/book-app-project/books.py:
# - Line 15: File path injection vulnerability in load_from_file
#
# Fix the issue and try again.
```

---

## ワークフロー3: 新しいコードベースへのオンボーディング

新しいプロジェクトに参加するとき、コンテキスト、Agents、MCPを組み合わせて忘くランプアップできます：

```bash
# インタラクティブモードでCopilotを起動
copilot

# フェーズ1: コンテキストで全体像を把握
> @samples/book-app-project/ このコードベースの高レベルなアーキテクチャを説明してください

# フェーズ2: 特定のフローを理解する
> @samples/book-app-project/book_app.py ユーザーが"python book_app.py add"を実行したときの流れを教えてください

# フェーズ3: Agentで専門家の分析を得る
> /agent
# "python-reviewer"を選択

> @samples/book-app-project/books.py 設計上の問題、欠けているエラー処理、
> またはお勧めの改善箇所はありますか？

# フェーズ4: 作業できるイシューを見つける（GitHub MCPがGitHubアクセスを提供）
> "good first issue"ラベルのオープンIssueを一覧表示してください

# フェーズ5: 貢献を始める
> 最もシンプルなオープンIssueを選んで、修正計画のアウトラインを作成してください
```

このワークフローは`@`コンテキスト、agents、MCPを一つのオンボーディングセッションに組み合わせます。まさにこの章の冗頭で解説した統合パターンそのものです。

---

# ベストプラクティスと自動化

ワークフローをより効果的にするパターンと習慣。

---

## ベストプラクティス

### 1. 分析前にコンテキストを集める

分析を依頼する前に必ずコンテキストを入力してください:

```bash
# 良い例
> Issue #42の詳細を教えてください
> /agent
# python-reviewerを選択
> このIssueを分析してください

# 効果が低い例
> /agent
# python-reviewerを選択
> ログインバグを修正してください
# Agentはイシューのコンテキストなし
```

### 2. 違いを知る: Agents、Skills、カスタム指示

各ツールには得意な場面があります:

```bash
# Agents: 明示的に有効化する専門ペルソナ
> /agent
# python-reviewerを選択
> この認証コードのセキュリティをレビューして

# Skills: プロンプトのdescriptionに合致したとき自動起動するモジュール機能
# （事前に作成が必要 — Chapter 05参照）
> このコードの包括的なテストを作成して
# テストSkillが設定されていれば自動起動

# カスタム指示 (.github/copilot-instructions.md): 常に適用される
# 切り替えやトリガー不要で全セッションに適用されるガイダンス
```

> 💡 **重要なポイント**: AgentsとSkillsはどちらも分析とコード生成の両方ができます。実際の違いは**起動方法**にあります — Agentsは明示的（`/agent`）、Skillsは自動（プロンプトマッチ）、カスタム指示は常時有効。

### 3. セッションを集中して保つ

`/rename`でセッションにラベルを付け（履歴から見つけやすくなる）、`/exit`でクリーンに終了できます:

```bash
# 良い例: 一セッションに一機能
> /rename list-unread-feature
# list unreadを作業
> /exit

copilot
> /rename export-csv-feature
# CSVエクスポートを作業
> /exit

# 少効率な例: 全てを一つの長いセッションに試みる
```

### 4. Copilotでワークフローを再利用可能にする

ワークフローをWikiに文模化するだけでなく、Copilotが使える形でリポジトリに直接エンコードしましょう:

- **カスタム指示** (`.github/copilot-instructions.md`): コーディング基準、アーキテクチャルール、ビルド/テスト/デプロイ手順の常時適用ガイダンス。
- **プロンプトファイル** (`.github/prompts/`): チームで共有できるコードレビュー、コンポーネント生成、PR説明などのテンプレート。
- **カスタムAgents** (`.github/agents/`): セキュリティレビュワーやドキュメントライターなど、チーム誤れが`/agent`で起動できる専門ペルソナ。
- **カスタムSkills** (`.github/skills/`): 関連すると自動起動するステップバイステップのワークフロー指示をパッケージ化。

> 💡 **成果**: 新しいチームメンバーがワークフローを無側で活用できます。誰かの頭の中にロックされるのではなく、リポジトリに組み込まれているからです。

---

## ボーナス: 本番環境パターン

プロフェッショナルな環境で役立つオプションパターンです。

### PR説明文生成機

```bash
# 包括的なPR説明文を生成
BRANCH=$(git branch --show-current)
COMMITS=$(git log main..$BRANCH --oneline)

copilot -p "以下のPRの説明文を生成して:
Branch: $BRANCH
Commits:
$COMMITS

内容: 概要、変更内容、テスト実施内容、スクリーンショットの必要性"
```

### CI/CD統合

既存のCI/CDパイプラインを持つチーム向けに、GitHub Actionsを使ってPRごとにCopilotレビューを自動実行できます。レビューコメントの自動投稿や重要問題のフィルタリングなども可能です。

> 📖 **詳細は**: 完全なGitHub Actionsワークフロー、設定オプション、トラブルシューティングは[CI/CD統合](../appendices/ci-cd-integration.md)を参照してください。

---

# 実践

<img src="../images/practice.png" alt="Warm desk setup with monitor showing code, lamp, coffee cup, and headphones ready for hands-on practice" width="800"/>

完全なワークフローを実践してみましょう。

---

## ▶️ 実践してみよう

デモを完了したら、次のバリエーションを試してみましょう：

1. **エンドツーエンドチャレンジ**: 小さな機能（例: 「未読書籍の一覧表示」または「CSVエクスポート」）を選んで、完全なワークフローで実装する:
   - `/plan`で計画を立てる
   - Agents（python-reviewer、pytest-helper）で設計する
   - 実装する
   - テストを生成する
   - PRを作成する

2. **自動化チャレンジ**: コードレビュー自動化ワークフローのpre-commitフックを設定する。意図的なファイルパス脆弱性があるファイルでコミットを試す。ブロックされるでしょうか？

3. **自分の本番ワークフロー**: 自分がよく行うタスクのワークフローを設計する。チェックリストとして書き出す。Skills、Agents、フックで自動化できる部分はどこでしょうか？

**確認テスト**: Agents、Skills、MCPがどのように連携するか―そしてそれぞれをいつ使うか―を同僚に説明できれば、コース完了です。

---

## 📝 課題

### メインチャレンジ: エンドツーエンド機能開発

ハンズオンの例では「未読書籍の一覧表示」機能の構築を歩みました。今度は違う機能で完全なワークフローを実践してみましょう: **年単による書籍検索**

1. Copilotを起動しコンテキストを収集: `@samples/book-app-project/books.py`
2. `/plan Add a "search by year" command that lets users find books published between two years`で計画
3. `BookCollection`に`find_by_year_range(start_year, end_year)`メソッドを実装
4. `book_app.py`に開始年と終了年を入力させる`handle_search_year()`関数を追加
5. テスト生成: `@samples/book-app-project/books.py @samples/book-app-project/tests/test_books.py find_by_year_range()のテストを、無効年、逆順範囲、結果0件などのエッジケースを含めて作成して`
6. `/review`でレビュー
7. READMEを更新: `@samples/book-app-project/README.md 新しい"search by year"コマンドのドキュメントを追加して`
8. コミットメッセージを生成

ワークフローを進めながら記録してください。

**成功基準**: Copilot CLIを使って、計画、実装、テスト、ドキュメント、レビューを包括するアイデアからコミットまでの機能を完成させたこと。

> 💡 **ボーナス**: Chapter 04でAgentsを設定した場合、カスタムAgentsの作成と利用を試してみましょう。例えば実装レビュー用のエラーハンドラーAgentやREADME更新用のドキュメントライターAgentなどが考えられます。

<details>
<summary>💡 ヒント (クリックで展開)</summary>

**この章冠頭の["アイデアからマージ済みPRまで"](#idea-to-merged-pr-in-one-session)の例に従ってください。キーステップ:

1. `@samples/book-app-project/books.py`でコンテキストを収集
2. `/plan Add a "search by year" command`で計画
3. メソッドとコマンドハンドラーを実装
4. エッジケースを含むテストを生成（無効入力、結果0件、逆からの範囲）
5. `/review`でレビュー
6. `@samples/book-app-project/README.md`でREADMEを更新
7. `-p`でコミットメッセージを生成

**考えるべきエッジケース:**
- ユーザーが「2000」と「1990」を逆順入力した場合は？
- 範囲内の書籍が0件の場合は？
- 数字以外の入力の場合は？

**重要なのは完全なワークフローを練習することです**: アイデア → コンテキスト → 計画 → 実装 → テスト → ドキュメント化 → コミット。

</details>

---

<details>
<summary>🔧 <strong>よくあるミス</strong> (クリックで展開)</summary>

| ミス | 結果 | 修正方法 |
|---------|---------|----------|
| いきなり実装に着手する | 設計上の問題を足ませる | まず`/plan`でアプローチを検討 |
| 一つのツールしか使わない | 遅く、不彻底な結果 | Agentで分析 → Skillで実行 → MCPで統合を組み合わせる |
| コミット前にレビューしない | セキュリティ問題やバグが漏れる | `/review`を常に実行するか[pre-commitフック](#workflow-2-code-review-automation-optional)を利用 |
| ワークフローをチームと共有しない | 各人が車輪の再発明を繰り返す | 共有Agents、Skills、指示にパターンを文書化する |

</details>

---

# まとめ

## 🔑 重要なポイント

1. **統合 > 孤立**: ツールを組み合わせて最大の効果を発揮する
2. **まずコンテキストを収集する**: 分析の前に必要な情報を必ず集める
3. **Agentsは分析、Skillsは実行**: 仕事に適したツールを使う
4. **繰り返しを自動化**: フックとスクリプトで効率を乗算する
5. **ワークフローを文書化する**: 共有可能なパターンはチーム全体に利益をもたらす

> 📋 **クイックリファレンス**: [GitHub Copilot CLIコマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference)で全コマンドとショートカットを確認できます。

---

## 🎓 コース完了！

おめでとうございます！学んだ内容：

| 章 | 学んだ内容 |
|---------|------------------|
| 00 | Copilot CLIのインストールとクイックスタート |
| 01 | 3つのインタラクションモード |
| 02 | @構文によるコンテキスト管理 |
| 03 | 開発ワークフロー |
| 04 | 専門化されたAgents |
| 05 | 拡張性の高いSkills |
| 06 | MCPによる外部接続 |
| 07 | 統合された本番ワークフロー |

GitHub Copilot CLIを開発ワークフローの真のフォースマルチプライヤーとして使いこなす力が備わりました。

## ➡️ 次のステップ

学びはここで終わりません:

1. **毎日実践する**: 実驛の仕事でCopilot CLIを使う
2. **カスタムツールを作る**: 自分のニーズに合ったAgentsとSkillsを作成する
3. **知識を共有する**: チームがこれらのワークフローを導入するよう支援する
4. **最新情報を追う**: GitHub Copilotのアップデートで新機能を確認する

### 参考リソース

- [GitHub Copilot CLIドキュメント](https://docs.github.com/copilot/concepts/agents/about-copilot-cli)
- [MCP Serverレジストリ](https://github.com/modelcontextprotocol/servers)
- [コミュニティSkills](https://github.com/topics/copilot-skill)

---

**お疲れさまでした！さあ、素晴らしいものを作りましょう。**

**[← Chapter 06に戻る](../06-mcp-servers/README.md)** | **[コースホームに戻る →](../README.md)**
