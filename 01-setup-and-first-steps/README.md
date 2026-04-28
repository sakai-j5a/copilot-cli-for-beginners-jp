![Chapter 01: First Steps](images/chapter-header.png)

> **AIがバグを瞬時に発見し、難解なコードを解説し、動作するスクリプトを生成する様子をご覧ください。その後、GitHub Copilot CLIの3種類の使い方を習得します。**

この章からが本番の始まりです！開発者がGitHub Copilot CLIを「シニアエンジニアがすぐそこにいるよう」と表現する理由を体感してください。AIがセキュリティバグを数秒で発見し、複雑なコードを平易な英語で解説し、動作するスクリプトを即座に生成する様子を目撃します。その後、3つのインタラクションモード（Interactive、Plan、Programmatic）をマスターして、どのタスクにどのモードを使うかを理解します。

> ⚠️ **前提条件**: **[第00章: クイックスタート](../00-quick-start/README.md)**を完了してください。以下のデモを実行する前に、GitHub Copilot CLIがインストールされ、認証されている必要があります。

## 🎯 学習目標

この章を終えると、以下のことができるようになります：

- ハンズオンデモを通じてGitHub Copilot CLIが提供する生産性向上を体感する
- タスクに応じて適切なモード（Interactive、Plan、Programmatic）を選択する
- スラッシュコマンドでセッションを制御する

> ⏱️ **所要時間の目安**: 約45分（15分読む + 30分ハンズオン）

---

# 最初のCopilot CLI体験

<img src="images/first-copilot-experience.png" alt="Developer sitting at a desk with code on the monitor and glowing particles representing AI assistance" width="800"/>

さっそく飛び込んで、Copilot CLIの力を体感しましょう。

---

## 気軽に始める: 最初のプロンプト

本格的なデモに入る前に、今すぐ試せるシンプルなプロンプトから始めましょう。**コードリポジトリは必要ありません！** ターミナルを開いてCopilot CLIを起動するだけです：

```bash
copilot
```

これらの初心者向けプロンプトを試してみてください：

```
> Explain what a dataclass is in Python in simple terms

> Write a function that sorts a list of dictionaries by a specific key

> What's the difference between a list and a tuple in Python?

> Give me 5 best practices for writing clean Python code
```

Pythonを使わない方でも大丈夫です！お好みの言語について質問してください。

自然に感じるはずです。同僚に話しかけるように質問してください。探索が終わったら、`/exit`と入力してセッションを終了します。

**重要なポイント**: GitHub Copilot CLIは会話型です。始めるのに特別な構文は必要ありません。英語で自然に質問するだけです。

## 実際に見てみる

なぜ開発者がこれを「シニアエンジニアがすぐそこにいるよう」と呼ぶのか見てみましょう。

> 📖 **例の読み方**: `>`で始まる行はCopilot CLIのインタラクティブセッション内でタイプするプロンプトです。`>`のない行はターミナルで実行するシェルコマンドです。

> 💡 **サンプル出力について**: コース全体で示されているサンプル出力はあくまで参考例です。Copilot CLIの応答は毎回異なるため、内容の表現やフォーマット、詳細度はここで示したものと異なります。表示されている正確なテキストではなく、返ってくる*情報の種類*に注目してください。

### デモ1: 数秒でコードレビュー

このコースにはコード品質の問題を意図的に含むサンプルファイルが含まれています。ローカルマシンで作業していてリポジトリをまだクローンしていない場合は、下記の`git clone`コマンドを実行し、`copilot-cli-for-beginners`フォルダに移動してから`copilot`コマンドを実行してください。

```bash
# ローカルで作業中でまだクローンしていない場合はリポジトリをクローン
git clone https://github.com/github/copilot-cli-for-beginners
cd copilot-cli-for-beginners

# Copilotを起動
copilot
```

Copilot CLIのインタラクティブセッション内に入ったら、次を実行してください：

```
> @samples/book-app-project/book_app.py のコード品質の問題をレビューして、改善点を提案してください
```

> 💡 **`@`記号は何に使いますか？** `@`記号はCopilot CLIにファイルを読み込むよう指示します。詳細はChapter 02で学びます。今はコマンドをそのままコピーしてください。

---

<details>
<summary>🎬 動作を確認！</summary>

![Code Review Demo](images/code-review-demo.gif)

*デモの出力は異なります。実際のモデル、ツール、回答はここで示したものと異なる場合があります。*

</details>

---

**要点**: 数秒でプロのコードレビュー完了。手動レビューはもっと時間がかかります！

---

### デモ2: 難解なコードの解説

コードを見て何をしているのか悩んだことは？Copilot CLIセッションでこれを試してみてください：

```
> @samples/book-app-project/books.py が何をするか、わかりやすく説明してください
```

---

<details>
<summary>🎬 動作を確認！</summary>

![Explain Code Demo](images/explain-code-demo.gif)

*デモの出力は異なります。実際のモデル、ツール、回答はここで示したものと異なる場合があります。*

</details>

---

**出力例**（実際の出力は異なります）: Copilot CLIがファイルを読み込み、コードを理解して平易な日本語で解説します。

```
これはPythonのdataclassを使った書籍コレクション管理モジュールです。

デジタルの本棚をイメージしてください：
- Book はdataclass — タイトル・著者・年・既読状態などの書籍情報を格納するシンプルな構造
- BookCollection はコレクション全体を管理し、保存・読み込みを処理する

主なコンポーネント：

1. @dataclass デコレーター（8行目）
   - __init__、__repr__ などを自動生成する
   - Pythonでデータ構造を定義するクリーンな方法

2. BookCollection クラス（16行目）
   - Book オブジェクトのリストを保持する
   - JSONファイルI/Oで永続化を処理する
   - load_books() は data.json から読み込む
   - save_books() は asdict() を使って data.json に書き込む

3. 書籍操作：
   - add_book() — 新しい書籍を作成して保存する
   - find_book_by_title() — コレクションを検索する
   - mark_as_read() — 既読状態を更新する
   - find_by_author() — 著者でフィルタリングする

一般的なパターン: JSON から読み込む → Python オブジェクトで処理する → JSON に書き戻す
```

**要点**: 忍耐強いメンターが解説してくれるような感覚で、複雑なコードが理解できます。

---

### デモ3: 動作するコードの生成

15分ほどかけてGoogleで調べるような関数が必要ですか？セッションでこれを試してみてください：

```
> 書籍のリストを受け取って、統計情報（合計冊数・既読数・未読数・最古と最新の書籍）を返すPython関数を書いてください
```

---

<details>
<summary>🎬 動作を確認！</summary>

![Generate Code Demo](images/generate-code-demo.gif)

*デモの出力は異なります。実際のモデル、ツール、回答はここで示したものと異なる場合があります。*

</details>

---

**要点**: 数秒で完成するコピペ実行可能な関数。そしてずっと同じセッションで作業を続けていました。

探索が終わったら、セッションを終了します：

```
> /exit
```

**要点**: 即座に結果が得られ、しかも一度もコマンドラインから離れずに済みました。

---

# モードとコマンド

<img src="images/modes-and-commands.png" alt="Futuristic control panel with glowing screens, dials, and equalizers representing Copilot CLI modes and commands" width="800"/>

Copilot CLIの使い方がわかりました。次にこれらの機能を効果的に活用する方法を理解しましょう。鍵は、異なる状況に応じてどの3つのインタラクションモードを使うか知ることです。

> 💡 **注記**: Copilot CLIには**Autopilot**モードもあり、入力を待たずにタスクを進めます。機能は強力ですが、全権限の許可が必要で、プレミアムリクエストを自律的に消費します。このコースでは下記の3つのモードに焦点を当てます。基本に慣れたらAutopilotについても視野に入れてみてください。

---

## 🧩 現実世界のアナロジー: 外食

レストランに行くような感覚で、考えてみてください。計画から注文まで、状況に応じて適切なアプローチがあります：

| モード | 外食のアナロジー | 使い所 |
|------|----------------|-------------|
| **Plan** | レストランまでのGPSナビ | 複雑なタスク - ルートを確認し、ストップをレビューし、同意してから走る |
| **Interactive** | ウェイターと話す | 探索と反復 - 質問し、カスタマイズし、リアルタイムのフィードバックを得る |
| **Programmatic** | ドライブスルーで注文 | スピーディーな特定タスク - 環境にいながら結果だけを得る |

外食と同じように、どのアプローチが自然に感じるかは使いながら覚えていけます。

<img src="images/ordering-food-analogy.png" alt="Three Ways to Use GitHub Copilot CLI - Plan Mode (GPS route to restaurant), Interactive Mode (talking to waiter), Programmatic Mode (drive-through)" width="800"/>

*タスクに応じてモードを選択: Planは事前に全体像を把握、Interactiveは対話的なコラボレーション、Programmaticはすばやい一発実行*

### どのモードから始めるか？

**Interactiveモードから始めましょう。**
- 試行錯誤したり、フォローアップ質問をしたりできる
- 会話を通じて自然にコンテキストが深まる
- `/clear`で間違いを簡単に修正できる

慣れてきたら試してみましょう：
- **Programmaticモード** (`copilot -p "<プロンプト>"`)：スピーディーな一回限りの質問向け
- **Planモード** (`/plan`)：コードを書く前に詳細な計画が必要なとき

---

## 3つのモード

### モード1: Interactiveモード（まずここから）

<img src="images/interactive-mode.png" alt="Interactive Mode - Like talking to a waiter who can answer questions and adjust the order" width="250"/>

**最適な用途**: 探索、反復、マルチターンの会話。貨を問いながら調整してくれるウェイターと話すような感覚です。

インタラクティブセッションを開始する：

```bash
copilot
```

ここまで見てきたように、自然にタイプできるプロンプトが表示されます。利用可能なコマンドのヘルプを表示するには、入力するだけです：

```
> /help
```

**重要なポイント**: Interactiveモードはコンテキストを維持します。各メッセージは前のメッセージに基づき、本物の会話のように展開します。

#### Interactiveモードの例

```bash
copilot

> @samples/book-app-project/utils.py をレビューして改善点を提案してください

> すべての関数に型ヒントを追加してください

> エラー処理をより堅牢にしてください

> /exit
```

各プロンプトが前の回答を引き継いでいることに注目してください。毎回最初からやり直すのではなく、会話を続けています。

---

### モード2: Planモード

<img src="images/plan-mode.png" alt="Plan Mode - Like planning a route before a trip using GPS" width="250"/>

**最適な用途**: 実行前にアプローチを確認したい複雑なタスク。GPSで旅行ルートを計画してから出発するような感覚です。

Planモードはコードを書く前に段階的な計画を作るのに役立ちます。`/plan`コマンドを使うか、**Shift+Tab**でPlanモードに切り替えます：

```bash
copilot

> /plan 書籍アプリに「既読にする」コマンドを追加する
```

> 💡 **ヒント**: **Shift+Tab**でモードを切り替えます: Interactive → Plan → Autopilot。インタラクティブセッション中にいつでも押してモードを切り替えられます。

`--plan`フラグを使ってPlanモードで直接起動することもできます：

```bash
copilot --plan
```

**Planモードの出力例**（実際の出力は異なります）:

```
📋 実装計画

ステップ1: book_app.py のコマンドハンドラーを更新する
  - "mark" コマンド用の新しい elif ブランチを追加する
  - handle_mark_as_read() 関数を作成する

ステップ2: ハンドラー関数を実装する
  - ユーザーに書籍タイトルを入力させる
  - collection.mark_as_read(title) を呼び出す
  - 成功・失敗メッセージを表示する

ステップ3: ヘルプテキストを更新する
  - 利用可能なコマンド一覧に "mark" を追加する
  - コマンドの使い方を説明する

ステップ4: フローをテストする
  - 書籍を追加する
  - 既読にする
  - 一覧表示でステータスが変わったことを確認する

実装を進めますか？ [Y/n]
```

**重要なポイント**: Planモードではコードが書かれる前にアプローチを確認・修正できます。計画が完成したら、ファイルに保存するよう指示することもできます。例えば「この計画を`mark_as_read_plan.md`に保存して」と言うと、計画の詳細をMarkdownファイルに書き出してくれます。

> 💡 **もっと複雑なことを試したい方へ**: `/plan 書籍アプリに検索・フィルタリング機能を追加する`を試してみてください。Planモードはシンプルな機能からフルアプリケーションまでスケールします。

> 📚 **Autopilotモード**: Shift+Tabが**Autopilot**という3つ目のモードを経由することに気づいた方もいるでしょう。Autopilotモードでは、各ステップで入力を待たずにCopilotが計画全体を実行します。まるで同僚にタスクを渡して「終わったら教えて」と言うような感覚です。一般的なワークフローはplan → accept → autopilotで、まず良い計画を書けるようになることが前提です。`copilot --autopilot`でautopilotに直接起動することもできます。まずInteractiveとPlanモードに慣れてから、準備ができたら[公式ドキュメント](https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot)を参照してください。

---

### モード3: Programmaticモード

<img src="images/programmatic-mode.png" alt="Programmatic Mode - Like using a drive-through for a quick order" width="250"/>

**最適な用途**: 自動化、スクリプト、CI/CD、一回限りのコマンド。ウェイターなしですぐ注文するドライブスルーのような感覚です。

`-p`フラグでインタラクション不要の一回限りコマンドを実行する：

```bash
# コード生成
copilot -p "数が偶数か奇数かを判定する関数を書いてください"

# クイックヘルプ
copilot -p "PythonでJSONファイルを読み込む方法を教えてください"
```

**重要なポイント**: Programmaticモードは迅速に回答して終了します。会話はなく、入力 → 出力のみです。

<details>
<summary>📚 <strong>応用編: スクリプトでProgrammaticモードを活用する</strong>（クリックで展開）</summary>

慣れてきたら、`-p`をシェルスクリプトで使えます：

```bash
#!/bin/bash

# コミットメッセージを自動生成
COMMIT_MSG=$(copilot -p "以下の変更内容のコミットメッセージを生成してください: $(git diff --staged)")
git commit -m "$COMMIT_MSG"

# ファイルをレビュー
copilot --allow-all -p "@myfile.py の問題をレビューしてください"
```
> ⚠️ **`--allow-all`について**: このフラグは全権限プロンプトをスキップして、Copilot CLIが事前確認なしにファイル読み込み、コマンド実行、URL アクセスを行えるようにします。Programmaticモード（`-p`）ではインタラクティブセッションでの承認ができないため必要です。自分で書いたプロンプトと信頼できるディレクトリでのみ使用してください。信頼できない入力や機密ディレクトリでは使わないでください。

</details>

---

## 主要なスラッシュコマンド

Copilot CLIを始める際に最初に覚えておくと便利なコマンドです：

| コマンド | 機能 | 使い所 |
|---------|--------------|-------------|
| `/ask` | 会話履歴に影響せず、クイックに質問する | 現在の作業を逃さずにサッと答えが欲しいとき |
| `/clear` | 会話をクリアしてリセット | トピックを切り替えるとき |
| `/help` | 利用可能な全コマンドを表示 | コマンドを忘れたとき |
| `/model` | AIモデルの表示または切り替え | AIモデルを変えたいとき |
| `/plan` | コーディング前に作業を計画する | 複雑な機能向け |
| `/research` | GitHubとWebソースを使った深い調査 | コードを書く前にトピックを調査するとき |
| `/exit` | セッション終了 | 作業完了時 |

> 💡 **`/ask`と途中の会話の違い**: 通常、送信したメッセージは続く会話の一部になり、以後の応答に影響します。`/ask`は「オフレコード」のショートカットで、`/ask What does YAML mean?`のような一回限りの質問に最適です。セッションコンテキストを汚染することなくクイックに質問できます。

最初はこれだけで充分です！慣れてきたら、追加コマンドを探索できます。

> 📚 **公式ドキュメント**: 全コマンドとフラグの完全な一覧は[CLIコマンドリファレンス](https://docs.github.com/copilot/reference/cli-command-reference)を参照。

<details>
<summary>📚 <strong>追加コマンド</strong>（クリックで展開）</summary>

> 💡 上記の必須コマンドは日常利用の多くをカバーしています。さらに探索したくなったときのリファレンスです。

### エージェント環境

| コマンド | 機能 |
|---------|------|
| `/agent` | 利用可能なAgentを参照・選択 |
| `/env` | 読み込み済み環境の詳細を表示 — 有効な指示、MCPサーバー、Skills、Agents、Pluginsの一覧 |
| `/init` | リポジトリ用のCopilot指示を初期化 |
| `/mcp` | MCPサーバーの設定を管理 |
| `/skills` | 拡張機能のためのSkillsを管理 |

> 💡 Agentsは[Chapter 04](../04-agents-custom-instructions/README.md)、Skillsは[Chapter 05](../05-skills/README.md)、MCPサーバーは[Chapter 06](../06-mcp-servers/README.md)でカバーしています。

### モデルとサブエージェント

| コマンド | 機能 |
|---------|------|
| `/delegate` | タスクをGitHub Copilotクラウドエージェントに引き渡す |
| `/fleet` | 複雑なタスクを並列サブタスクに分割して高速完了 |
| `/model` | AIモデルの表示または切り替え |
| `/tasks` | バックグラウンドのサブエージェントとデタッチされたシェルセッションを表示 |

### コード

| コマンド | 機能 |
|---------|------|
| `/diff` | 現在のディレクトリの変更をレビュー |
| `/pr` | 現在のブランチのPRを操作 |
| `/research` | GitHubとWebソースを使った深い調査 |
| `/review` | コードレビューエージェントを実行して変更を分析 |
| `/terminal-setup` | 複数行入力サポートを有効化（shift+enterとctrl+enter） |

### 権限

| コマンド | 機能 |
|---------|------|
| `/add-dir <directory>` | ディレクトリを許可リストに追加 |
| `/allow-all [on\|off\|show]` | 全権限プロンプトを自動承認。`on`で有効化、`off`で無効化、`show`で現在状態確認 |
| `/yolo` | `/allow-all on`のクイックエイリアス — 全権限プロンプトを自動承認 |
| `/cwd`, `/cd [directory]` | 作業ディレクトリの表示または変更 |
| `/list-dirs` | 許可済みディレクトリを全表示 |

> ⚠️ **使用には注意**: `/allow-all`と`/yolo`は確認プロンプトをスキップします。信頼済みプロジェクトには便利ですが、未知のコードには注意してください。

### セッション

| コマンド | 機能 |
|---------|------|
| `/clear` | 現在のセッションを破棄（履歴保存なし）して新しい会話を開始 |
| `/compact` | コンテキスト使用量を減らすために会話を要約 |
| `/context` | コンテキストウィンドウのトークン使用量と可視化を表示 |
| `/new` | 現在のセッションを終了（履歴に保存）して新しい会話を開始 |
| `/resume` | 別のセッションに切り替え（セッションIDを指定可） |
| `/rename` | 現在のセッションをリネーム（名前省略で自動生成） |
| `/rewind` | タイムラインピッカーを開いて以前の任意のポイントに巻き戻す |
| `/usage` | セッション使用状況メトリクスと統計を表示 |
| `/session` | セッション情報とワークスペースサマリーを表示 |
| `/share` | セッションをMarkdownファイル、GitHub Gist、スタンドアロンHTMLとしてエクスポート |

### 表示

| コマンド | 機能 |
|---------|------|
| `/statusline`（または`/footer`） | ステータスバーに表示する項目をカスタマイズ（ディレクトリ、ブランチ、努力度、コンテキストウィンドウ、クォータ） |
| `/theme` | ターミナルテーマの表示または設定 |

### ヘルプとフィードバック

| コマンド | 機能 |
|---------|------|
| `/changelog` | CLIバージョンの変更履歴を表示 |
| `/feedback` | GitHubにフィードバックを送信 |
| `/help` | 利用可能な全コマンドを表示 |

### クイックシェルコマンド

`!`をプレフィックスにすることでAIを通さずシェルコマンドを直接実行：

```bash
copilot

> !git status
# AIをバイパスしてgit statusを直接実行

> !python -m pytest tests/
# pytestを直接実行
```

### モデルの切り替え

Copilot CLIはOpenAI、Anthropic、Googleなど複数のAIモデルをサポートしています。利用可能なモデルはサブスクリプションレベルや地域によって異なります。`/model`で選択肢を確認して切り替えられます：

```bash
copilot
> /model

# 利用可能なモデルを表示して選択できます。Claude Sonnet 4.5を選んでみてください。
```

> 💡 **ヒント**: モデルによって消費する「プレミアムリクエスト」の量が異なります。**1x**表示のモデル（Claude Sonnet 4.5など）は優れたデフォルト選択です。高倍率モデルはプレミアムリクエストのクォータを早く消費するため、本当に必要なときのために残しておきましょう。

</details>

---

# 実践

<img src="../images/practice.png" alt="Warm desk setup with monitor showing code, lamp, coffee cup, and headphones ready for hands-on practice" width="800"/>

学んだことを実際に試してみましょう。

---

## ▶️ 実践してみよう

### インタラクティブ探索

Copilotを起動して、フォローアッププロンプトを使いながら書籍アプリを繰り返し改善してみましょう：

```bash
copilot

> @samples/book-app-project/book_app.py を改善できる点は何ですか？

> if/elif チェーンをより保守しやすい構造にリファクタリングしてください

> すべてのハンドラー関数に型ヒントを追加してください

> /exit
```

### 機能の計画

`/plan`を使って、コードを書く前にCopilot CLIに実装の全体像を立案させてみましょう：

```bash
copilot

> /plan タイトルまたは著者で書籍を検索できる機能を書籍アプリに追加する

# 計画を確認する
# 承認または修正する
# ステップバイステップで実装を見守る
```

### Programmaticモードで自動化

`-p`フラグを使えばインタラクティブモードに入らずにターミナルから直接Copilot CLIを実行できます。リポジトリルートから以下のスクリプトをターミナルに貼り付けて（Copilot内ではなく）書籍アプリの全Pythonファイルをレビューしてみましょう。

```bash
# 書籍アプリの全Pythonファイルをレビュー
for file in samples/book-app-project/*.py; do
  echo "Reviewing $file..."
  copilot --allow-all -p "@$file のコード品質を簡易レビューしてください。重要な問題のみ"
done
```

**PowerShell (Windows):**

```powershell
# 書籍アプリの全Pythonファイルをレビュー
Get-ChildItem samples/book-app-project/*.py | ForEach-Object {
  $relativePath = "samples/book-app-project/$($_.Name)";
  Write-Host "Reviewing $relativePath...";
  copilot --allow-all -p "@$relativePath のコード品質を簡易レビューしてください。重要な問題のみ" 
}
```

---

デモを完了したら、以下のバリエーションも試してみましょう：

1. **インタラクティブチャレンジ**: `copilot`を起動して書籍アプリを探索。`@samples/book-app-project/books.py`について質問して、改善を3回続けて依頼してみましょう。

2. **Planモードチャレンジ**: `/plan 書籍アプリに評価・レビュー機能を追加する`を実行。計画をよく読んでください。理にかなっていますか？

3. **Programmaticチャレンジ**: `copilot --allow-all -p "@samples/book-app-project/book_app.py のすべての関数を列挙して、それぞれが何をするか説明してください"`を実行。一発で機能しましたか？

---

## 💡 Tip: Control Your CLI Session from Web or Mobile

GitHub Copilot CLIは**リモートセッション**をサポートしており、実際にターミナルの前にいなくても、Webブラウザ（デスクトップ・モバイル）またはGitHub Mobileアプリから実行中のCLIセッションを監視・操作できます。

`--remote`フラグでリモートセッションを開始する：

```bash
copilot --remote
```

Copilot CLIがリンクとQRコードを表示します。スマートフォンまたはデスクトップのブラウザタブでリンクを開くと、リアルタイムでセッションを確認したり、フォローアッププロンプトを送ったり、計画を確認したり、リモートでエージェントを指示したりできます。セッションはユーザー固有なので、自分のCopilot CLIセッションにのみアクセスできます。

アクティブなセッション中にいつでもリモートアクセスを有効にできます：

```
> /remote
```

リモートセッションの詳細は[Copilot CLIドキュメント](https://docs.github.com/copilot/how-tos/copilot-cli/steer-remotely)を参照してください。

---

## 📝 課題

### メインチャレンジ: 書籍アプリのutilitiesを改善する

ハンズオンの例では`book_app.py`のレビューとリファクタリングを行いました。今度は`utils.py`で同じスキルを練習しましょう：

1. インタラクティブセッションを開始: `copilot`
2. ファイルの概要を訊く: `@samples/book-app-project/utils.py このファイルの各関数は何をしていますか？`
3. 入力バリデーションを追加する: 「`get_user_choice()`に空入力と数字以外の入力を処理するバリデーションを追加して」
4. エラーハンドリングを改善する: 「`get_book_details()`にタイトルとして空文字列が渡されたらどうなるか？そのガードを追加して」
5. docstringを追加する: 「`get_book_details()`にパラメータの説明と戻り値を含む包括的なdocstringを追加して」
6. プロンプト間でコンテキストが引き継がれることを確認する
7. `/exit`で終了する

**成功基準**: 入力バリデーション、エラーハンドリング、docstringが追加された改善済み`utils.py`が、マルチターンの会話を通じて完成すること。

<details>
<summary>💡 ヒント（クリックで展開）</summary>

**試してみるプロンプト例:**
```bash
> @samples/book-app-project/utils.py このファイルの各関数は何をしていますか？
> get_user_choice() に空入力と数字以外の入力を処理するバリデーションを追加してください
> get_book_details() にタイトルとして空文字列が渡されたらどうなりますか？そのガードを追加してください
> get_book_details() にパラメータの説明と戻り値を含む包括的なdocstringを追加してください
```

**よくある問題:**
- Copilot CLIが確認質問をしてきたら、自然に答えましょう
- コンテキストは引き継がれるので、各プロンプトは前の内容を基に展開する
- 最初からやり直したい場合は`/clear`を使用

</details>

### ボーナスチャレンジ: モードを比較する

例では検索機能に`/plan`を、バッチレビューに`-p`を使いました。今度は1つの新しいタスク「`BookCollection`クラスへの`list_by_year()`メソッド追加」を3つのモードすべてで試してみましょう：

1. **Interactive**: `copilot` → ステップバイステップで設計・構築を依頼
2. **Plan**: `/plan 出版年の範囲でフィルタリングできる list_by_year(start, end) メソッドを BookCollection に追加する`
3. **Programmatic**: `copilot --allow-all -p "@samples/book-app-project/books.py に、start から end 年（両端を含む）に出版された書籍を返す list_by_year(start, end) メソッドを追加してください"`

**振り返り**: どのモードが最も自然に感じましたか？それぞれをいつ使いますか？

---

<details>
<summary>🔧 <strong>よくあるミスとトラブルシューティング</strong>（クリックで展開）</summary>

### よくあるミス

| ミス | 起こること | 修正方法 |
|---------|--------------|-----|
| `/exit`の代わりに`exit`と入力 | Copilot CLIが「exit」をコマンドではなくプロンプトとして扱う | スラッシュコマンドは常に`/`から始まる |
| マルチターン会話に`-p`を使う | 各`-p`呼び出しは独立していて前の呼び出しを記憶しない | コンテキストを引き継ぐ会話にはInteractiveモード（`copilot`）を使う |
| `$`や`!`を含むプロンプトをクォートで囲まない | Copilot CLIに届く前にシェルが特殊文字を解釈する | プロンプトをクォートで囲む: `copilot -p "What does $HOME mean?"` |

### トラブルシューティング

**「Model not available」** - サブスクリプションに全モデルが含まれていない可能性があります。`/model`で利用可能なモデルを確認してください。

**「Context too long」** - 会話がコンテキストウィンドウの上限に達しました。`/clear`でリセットするか、新しいセッションを開始してください。

**「Rate limit exceeded」** - 数分待ってから再試行してください。バッチ処理にはProgrammaticモードを遅延を入れながら使うことを検討してください。

</details>

---

# まとめ

## 🔑 重要なポイント

1. **Interactiveモード**は探索と反復向け — コンテキストが引き継がれます。これまでの会話を覚えている相手と話すような感覚です。
2. **Planモード**は本格的なタスク向け。実装前に計画を確認できます。
3. **Programmaticモード**は自動化向け。インタラクションは不要です。
4. **必須コマンド**（`/ask`、`/help`、`/clear`、`/plan`、`/research`、`/model`、`/exit`）で日常利用の大半をカバーできます。

> 📋 **クイックリファレンス**: コマンドとショートカットの完全な一覧は[GitHub Copilot CLIコマンドリファレンス](https://docs.github.com/en/copilot/reference/cli-command-reference)を参照してください。

---

## ➡️ 次のステップ

3つのモードを理解したところで、Copilot CLIにコードのコンテキストを与える方法を学びましょう。

**[Chapter 02: コンテキストと会話](../02-context-conversations/README.md)**では以下を学びます：

- ファイルやディレクトリを参照する`@`構文
- `--resume`と`--continue`によるセッション管理
- コンテキスト管理がCopilot CLIを真に強力にする理由

---

**[← コースホームに戻る](../README.md)** | **[Chapter 02へ進む →](../02-context-conversations/README.md)**
