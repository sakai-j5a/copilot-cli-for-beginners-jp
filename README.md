![GitHub Copilot CLI for Beginners](./images/copilot-banner.png)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)&ensp;
[![Open project in GitHub Codespaces](https://img.shields.io/badge/Codespaces-Open-blue?style=flat-square&logo=github)](https://codespaces.new/github/copilot-cli-for-beginners?hide_repo_select=true&ref=main&quickstart=true)&ensp;
[![Official Copilot CLI documentation](https://img.shields.io/badge/GitHub-CLI_Documentation-00a3ee?style=flat-square&logo=github)](https://docs.github.com/en/copilot/how-tos/copilot-cli)&ensp;
[![Join AI Foundry Discord](https://img.shields.io/badge/Discord-AI_Community-blue?style=flat-square&logo=discord&color=5865f2&logoColor=fff)](https://aka.ms/foundry/discord)

🎯 [What You'll Learn](#what-youll-learn) &ensp; ✅ [Prerequisites](#prerequisites) &ensp; 🤖 [Copilot Family](#understanding-the-github-copilot-family) &ensp; 📚 [Course Structure](#course-structure) &ensp; 📋 [Command Reference](#-github-copilot-cli-command-reference)

# GitHub Copilot CLI for Beginners

> **✨ AIサポートのコマンドライン支援で開発ワークフローを激変させる方法を学びましょう。**

GitHub Copilot CLIは、AIサポートをターミナルに直接届けます。ブラウザやコードエディタに切り替えることなく、コマンドラインから山ることなく、質問したり、フル機能のアプリを生成したり、コードをレビューしたり、テストを生成したり、問題をデバッグしたりできます。

24時間365日常に備えている知識豊富な同僚がいるような感覚です。コードを読んで、混乱するパターンを説明し、作業を楽にしてくれます！

> 📘 **Web画面の方がよかったら？** このコースはGitHub上でそのまま追えます。または[Awesome Copilot](https://awesome-copilot.github.com/learning-hub/cli-for-beginners/)でより使い慣れたブラウズ体験で閲覧できます。

このコースの対象：

- **ソフトウェア開発者**: コマンドラインかAIを活用したい方
- **ターミナルユーザー**: IDE連携よりキーボード骆使のワークフローを好む方
- **チームで標準化したい方**: AI支援のコードレビューと開発プラクティスを統一したいチーム

<a href="https://aka.ms/githubcopilotdevdays" target="_blank">
  <picture>
    <img src="./images/copilot-dev-days.png" alt="GitHub Copilot Dev Days - Find or host an event" width="100%" />
  </picture>
</a>

## 🎯 学ぶこと

このハンズオンコースでは、GitHub Copilot CLIのゼロから実戦辺までを習得できます。全章を通じてPythonの書籍コレクションアプリに取り組み、AI支援ワークフローを使って段階的に改善していきます。最終的には、ターミナルからAIを使ってコードをレビューし、テストを生成し、バグをデバッグし、ワークフローを自動化する自信が身につきます。

**AI経験は一切不要。** ターミナルが使えれば、これを学べます。

**ベストな対象者:** 開発者、学生、そしてソフトウェア開発の経験がある方なら誰でも。

## ✅ 前提条件

開始前に以下を用意してください：

- **GitHubアカウント**: [無料で作成](https://github.com/signup)<br>
- **GitHub Copilotアクセス**: [無料プラン](https://github.com/features/copilot/plans)、[月額サブスクリプション](https://github.com/features/copilot/plans)、または[学生・教職員無料](https://education.github.com/pack)<br>
- **ターミナルの基础**: `cd`、`ls`、コマンド実行の基本操作

## 🤖 GitHub Copilotファミリーを理解する

GitHub CopilotはAIツールのファミリーへと進化しました。各ツールの動作環境は以下のとおりです：

| 製品 | 動作環境 | 説明 |
|---------|---------------|----------|
| [**GitHub Copilot CLI**](https://docs.github.com/copilot/how-tos/copilot-cli/cli-getting-started)<br>(このコース) | ターミナル | ターミナルネイティブなAIコーディングアシスタント |
| [**GitHub Copilot**](https://docs.github.com/copilot) | VS Code、Visual Studio、JetBrainsなど | エージェントモード、チャット、インライン提案 |
| [**Copilot on GitHub.com**](https://github.com/copilot) | GitHub | リポジトリに関する汎用チャット、エージェント作成など |
| [**GitHub Copilotクラウドエージェント**](https://docs.github.com/copilot/using-github-copilot/using-copilot-coding-agent-to-work-on-tasks) | GitHub | Issueをエージェントに割り当ててPRを受け取る |

このコースでは**GitHub Copilot CLI**に焦点を当て、AIサポートをターミナルに直接届けます。

## 📚 コース構成

![GitHub Copilot CLI Learning Path](images/learning-path.png)

| 章 | タイトル | 作るもの |
|:-------:|-------|-------------------|
| 00 | 🚀 [クイックスタート](./00-quick-start/README.md) | インストールと動作確認 |
| 01 | 👋 [最初の一歩](./01-setup-and-first-steps/README.md) | ライブデモ＋3つのインタラクションモード |
| 02 | 🔍 [コンテキストと会話](./02-context-conversations/README.md) | マルチファイルプロジェクト分析 |
| 03 | ⚡ [開発ワークフロー](./03-development-workflows/README.md) | コードレビュー、デバッグ、テスト生成 |
| 04 | 🤖 [専門AIアシスタントの作成](./04-agents-custom-instructions/README.md) | ワークフロー用カスタムエージェント |
| 05 | 🛠️ [繰り返しタスクの自動化](./05-skills/README.md) | 自動読み込みスキル |
| 06 | 🔌 [GitHub・データベース・APIへの接続](./06-mcp-servers/README.md) | MCPサーバー統合 |
| 07 | 🎯 [全てをまとめる](./07-putting-it-together/README.md) | 完全な機能開発ワークフロー |

## 📖 コースの進め方

各章は同じパターンで構成されています：

1. **現実世界のアナロジー**: 襋身近な例えで概念を理解する
2. **コア概念**: 必要な知識を学ぶ
3. **ハンズオンの例**: 実際にコマンドを実行して結果を確認する
4. **課題**: 学んだことを練習する
5. **次のステップ**: 次章のプレビュー

**コード例は実行可能です。** このコース内のcopilotコードブロックはすべてターミナルでコピペして実行できます。

## 📋 GitHub Copilot CLI コマンドリファレンス

**[GitHub Copilot CLIコマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference)**では、Copilot CLIを効果的に使うためのコマンドとキーボードショートカットを確認できます。

## 🙋 ヘルプ

- 🐛 **バグを見つけたら？** [Issueを開く](https://github.com/github/copilot-cli-for-beginners/issues)
- 🤝 **貢献したい？** PRを歓迎します！
- 📚 **公式ドキュメント:** [GitHub Copilot CLIドキュメント](https://docs.github.com/copilot/concepts/agents/about-copilot-cli)

## ライセンス

このプロジェクトはMITオープンソースライセンスの条件下でライセンスされています。詳細は[LICENSE](./LICENSE)ファイルを参照してください。

