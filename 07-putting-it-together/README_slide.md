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

# Chapter 07: 全てをまとめる

**これまで学んだ全てがここで結合します**

アイデアからマージ済みPRまで、一度のセッションで完了させましょう

> ⏱️ 所要時間の目安: 約75分（15分読む + 60分ハンズオン）

---

## 🎯 学習目標

この章を終えると：

- Agents、Skills、MCPを統合したワークフローを構築できる
- マルチツールアプローチで完全な機能を開発できる
- フックで基本的な自動化を設定できる
- プロフェッショナルな開発のベストプラクティスを実践できる

> 💡 Agents・Skills・MCPは必須ではありません。基本ワークフローだけでも十分です

---

## 🧩 アナロジー: オーケストラ

<img src="images/orchestra-analogy.png" alt="Orchestra Analogy" width="60%"/>

- **弦楽器** → コアワークフロー（基盤）
- **金管楽器** → Agents（専門知識を追加）
- **木管楽器** → Skills（機能を拡張）
- **打楽器** → MCP（外部システムに接続）

**指揮者がオーケストラを導くように、全ツールを統合したワークフローを指揮する**

---

## 統合パターン

<img src="images/integration-pattern.png" alt="Integration Pattern" width="60%"/>

4つのフェーズ：

1. **コンテキスト収集**（MCP） — Issue確認、ドキュメント取得
2. **分析と計画**（Agents） — 専門家の分析、実装計画
3. **実行**（Skills + 手動） — コード実装、テスト生成
4. **完了**（MCP） — コミット、PR作成

---

## 一セッションでアイデアからPRまで

```bash
copilot

> 書籍アプリに「未読の一覧表示」コマンドを追加したい。
  どのファイルを変更する必要がありますか？
```

--

## Python-Reviewerで設計

```bash
# PYTHON-REVIEWER AGENTに切り替え
> /agent
# "python-reviewer"を選択

> @samples/book-app-project/books.py
  get_unread_booksメソッドを設計してください。
  最善のアプローチは何ですか？
```

Python-reviewer agentが生成：
- メソッドシグネチャと戻り値の型
- リスト内包表記を使ったフィルター実装
- エッジケース処理

--

## Pytest-Helperでテスト設計

```bash
# PYTEST-HELPER AGENTに切り替え
> /agent
# "pytest-helper"を選択

> @samples/book-app-project/tests/test_books.py
  未読書籍のフィルタリングのテストケースを設計して
```

生成されるテスト：
- ハッピーパス（3テスト）
- エッジケース（4テスト）
- パラメータライズ（5ケース）
- 統合テスト（4テスト）

--

## 実装・テスト・レビュー・PR

```bash
# 実装
> books.pyにget_unread_booksメソッドを追加
> book_app.pyに"list unread"コマンドを追加

# テスト生成
> 新機能の包括的なテストを生成

# レビュー
> /review

# PR作成
> /pr create
# または自然言語で
> "機能: 未読書籍の一覧表示コマンド"でPR作成
```

**重要な洞察**: アーキテクトのように専門家を指揮した

---

## ワークフロー1: バグ調査と修正

```bash
copilot

# フェーズ1: GitHub MCPでバグを理解
> Issue #1の詳細を教えて

# フェーズ2: ベストプラクティスを調査
> /research Pythonの大文字小文字を無視した
  文字列マッチングのベストプラクティス

# フェーズ3: python-reviewerで分析
> /agent     # "python-reviewer"を選択
> 部分名マッチングの問題を分析して

# フェーズ4: 修正を実装
> 小文字比較とin演算子で修正を実装

# フェーズ5: テスト生成
> /agent     # "pytest-helper"を選択
> find_by_authorのpytestテストを生成

# フェーズ6: PR作成
> Issue #1にリンクするPRを作成
```

---

## ワークフロー2: コードレビュー自動化（オプション）

コミット前に自動セキュリティレビューを設定：

```bash
#!/bin/bash
STAGED=$(git diff --cached --name-only --diff-filter=ACM \
  | grep -E '\.py$')

if [ -n "$STAGED" ]; then
  for file in $STAGED; do
    REVIEW=$(timeout 60 copilot --allow-all -p \
      "Quick security review of @$file - critical issues only")
    if echo "$REVIEW" | grep -qi "CRITICAL"; then
      echo "Critical issues found in $file:"
      echo "$REVIEW"
      exit 1
    fi
  done
  echo "Review passed"
fi
```

> ⚠️ `--allow-all`は信頼済みプロジェクトのみで使用

---

## ワークフロー3: 新しいコードベースへのオンボーディング

```bash
copilot

# フェーズ1: 全体像を把握
> @samples/book-app-project/
  このコードベースのアーキテクチャを説明して

# フェーズ2: 特定フローを理解
> @book_app.py "python book_app.py add"の流れは？

# フェーズ3: 専門家の分析
> /agent     # python-reviewerを選択
> 設計上の問題や改善箇所は？

# フェーズ4: 作業イシューを発見
> "good first issue"ラベルのIssueを一覧表示

# フェーズ5: 貢献を始める
> 最もシンプルなIssueの修正計画を作成
```

---

## ベストプラクティス

### 1. 分析前にコンテキストを集める

```bash
# 良い例
> Issue #42の詳細を教えて
> /agent  # python-reviewerを選択
> このIssueを分析して

# 効果が低い例
> /agent  # python-reviewerを選択
> ログインバグを修正して   # Issueのコンテキストなし
```

--

### 2. ツールの使い分け

| ツール | 得意な場面 | 例 |
|--------|---------|------|
| **@コンテキスト** | ファイル分析 | `@books.py をレビュー` |
| **Agents** | 専門知識 | `/agent python-reviewer` |
| **Skills** | 繰り返しタスク | 自動品質チェック |
| **MCP** | 外部データ | Issue確認、ドキュメント取得 |

--

### 3. セッション管理

```bash
# 機能ごとにセッションを分ける
> /rename feature-unread-books

# 長い会話はcompactで最適化
> /compact

# 翌日に続きから再開
copilot --continue
```

---

## ▶️ 最終実践

<img src="../images/practice.png" alt="Practice" width="60%"/>

--

## 実践: フル機能開発ワークフロー

1. 書籍アプリに新機能を計画（`/plan`）
2. python-reviewerでコード設計
3. pytest-helperでテスト設計
4. 実装してテスト実行
5. `/review`でコードレビュー
6. コミットメッセージを自動生成

--

## 実践: オンボーディング体験

```bash
copilot

> @samples/book-app-project/
  このプロジェクトの全体像、主要な改善点、
  最初に取り組むべきタスクを提案して
```

---

## 📝 最終課題

### 統合ワークフロー実践

1. GitHub MCPでIssueを確認
2. `@`コンテキストで関連コードを分析
3. Agentで設計・テスト計画を立てる
4. 実装してテスト
5. `/review`でレビュー
6. コミット＆PR作成

--

## チャレンジ

- **フルサイクル**: アイデアからPRまでを15分以内に完了。タイマーを設定して挑戦

- **自動化**: pre-commitフックを設定して、コミット時に自動レビューを体験

- **チーム標準**: `AGENTS.md`、カスタムAgent、Skillを組み合わせたチーム開発環境を構築

---

## 🎉 コース完了！

全7章を通じて学んだこと：

- Ch00-01: インストール、3つのモード、スラッシュコマンド
- Ch02: `@`コンテキスト、セッション管理
- Ch03: コードレビュー、デバッグ、テスト、Git連携
- Ch04: Agents（組み込み＋カスタム）
- Ch05: Skills（自動トリガー＋管理）
- Ch06: MCPサーバー（外部接続）
- Ch07: 全てを統合したプロワークフロー

> 📚 [公式ドキュメント](https://docs.github.com/copilot/concepts/agents/about-copilot-cli)で最新機能を継続的にチェック！
