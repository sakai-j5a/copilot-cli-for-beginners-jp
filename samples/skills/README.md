# サンプルスキル

すぐに使えるスキルテンプレートです。各スキルフォルダをコピーするだけですぐに利用できます。

## クイックスタート

```bash
# Copy a skill to your personal skills folder
cp -r hello-world ~/.copilot/skills/

# Or copy to your project for team sharing
cp -r code-checklist .github/skills/
```

## 利用可能なスキル

| スキル | 説明 | 最適な用途 |
|-------|-------------|----------|
| `hello-world` | 最小構成の例（フォーマット学習用） | 初めてスキルを作る方 |
| `code-checklist` | Pythonコード品質チェックリスト（PEP 8、型ヒント、バリデーション） | 一貫した品質チェック |
| `pytest-gen` | 包拪pytestテストの生成 | 構造化されたテスト生成 |
| `commit-message` | Conventional Commitメッセージ | 標準化されたgit履歴 |

## スキルの仕組み

スキルは、プロンプトがスキルの`description`フィールドと一致すると**自動でトリガーされます**。手動で呼び出す必要はありません。

```bash
copilot

> Check this code for quality issues
# Copilot detects this matches "code-checklist" skill and loads it automatically

> Generate a commit message
# Copilot loads the "commit-message" skill
```

You can also invoke skills directly:
```bash
> /code-checklist Check books.py
> /pytest-gen Generate tests for BookCollection
> /commit-message
```

## スキルの構成

各スキルは`SKILL.md`ファイルを含むフォルダです：

```
skill-name/
└── SKILL.md    # 必須：フロントマターと指示を含む
```

`SKILL.md`ファイルには`name`と`description`の両方が必須なYAMLフロントマターが必要です：

```markdown
---
name: my-skill
description: このスキルの機能と使うタイミング
---

# スキルの指示

指示内容をここに記載...
```

## コミュニティスキルを見つける

- **[github/awesome-copilot](https://github.com/github/awesome-copilot)** - スキルのドキュメントと例を含むGitHub Copilot公式リソース
- **`/plugin marketplace`** - Copilot CLI内からスキルを参照・インストール

## 独自スキルを作る

1. フォルダを作成: `mkdir ~/.copilot/skills/my-skill`
2. `SKILL.md`をフロントマター付きで作成
3. 指示を追加
4. 自分のdescriptionと一致するプロンプトでCopilotに問いかけてテスト

詳細は[Chapter 05: Skills](../../05-skills/README.md)を参照してください。
