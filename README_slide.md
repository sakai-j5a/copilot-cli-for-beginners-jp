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

# GitHub Copilot CLI for Beginners

**✨ AIサポートのコマンドライン支援で開発ワークフローを激変させる方法を学びましょう**

24時間365日常に備えている知識豊富な同僚がいるような感覚です

---

## 🎯 学ぶこと

GitHub Copilot CLIのゼロから実戦までを習得できます

- AI支援でコードをレビュー、テスト生成、バグデバッグ
- ターミナルからワークフローを自動化
- Pythonの書籍コレクションアプリを題材に段階的に学習

**AI経験は一切不要。** ターミナルが使えれば学べます

---

## ✅ 前提条件

- **GitHubアカウント**: [無料で作成](https://github.com/signup)
- **GitHub Copilotアクセス**: [無料プラン](https://github.com/features/copilot/plans) / [学生・教職員無料](https://education.github.com/pack)
- **ターミナルの基礎**: `cd`、`ls`、コマンド実行の基本操作

---

## 🤖 GitHub Copilotファミリー

| 製品 | 動作環境 | 説明 |
|------|---------|------|
| **Copilot CLI** (このコース) | ターミナル | AIコーディングアシスタント |
| **GitHub Copilot** | VS Code等 | エージェントモード、チャット |
| **Copilot on GitHub.com** | GitHub | リポジトリチャット |
| **Copilotクラウドエージェント** | GitHub | Issueを自動でPRに |

このコースでは**GitHub Copilot CLI**に焦点

---

## 📚 コース構成

| 章 | タイトル | 内容 |
|:--:|---------|------|
| 00 | 🚀 クイックスタート | インストールと動作確認 |
| 01 | 👋 最初の一歩 | デモ＋3つのモード |
| 02 | 🔍 コンテキストと会話 | マルチファイル分析 |
| 03 | ⚡ 開発ワークフロー | レビュー、デバッグ、テスト |
| 04 | 🤖 Agents | カスタムエージェント |

--

## 📚 コース構成（続き）

| 章 | タイトル | 内容 |
|:--:|---------|------|
| 05 | 🛠️ Skills | 自動読み込みスキル |
| 06 | 🔌 MCPサーバー | 外部サービス接続 |
| 07 | 🎯 全てをまとめる | 統合ワークフロー |

---

## 📖 コースの進め方

各章は同じパターンで構成：

1. **現実世界のアナロジー** — 身近な例えで概念を理解
2. **コア概念** — 必要な知識を学ぶ
3. **ハンズオンの例** — 実際にコマンドを実行
4. **課題** — 学んだことを練習
5. **次のステップ** — 次章のプレビュー

**コード例はすべてコピペ実行可能です**

---

## 📋 コマンドリファレンス

| コマンド | 機能 |
|---------|------|
| `copilot` | インタラクティブモード起動 |
| `copilot -p "<プロンプト>"` | プログラマティックモード |
| `/plan` | 実装計画を作成 |
| `/review` | コードレビュー |
| `/agent` | Agent選択 |
| `/skills list` | Skill一覧 |
| `/mcp show` | MCPサーバー状態 |

--

## その他のコマンド

| コマンド | 機能 |
|---------|------|
| `/clear` | セッションリセット |
| `/compact` | コンテキスト圧縮 |
| `/context` | トークン使用量確認 |
| `/rename` | セッション命名 |
| `/exit` | セッション終了 |
| `!command` | シェルコマンド直接実行 |

> 📚 [公式コマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference)

---

## 🚀 さあ始めましょう！

**[Chapter 00: クイックスタート →](./00-quick-start/README.md)**

インストールは約10分。本番の楽しさはChapter 01から！
