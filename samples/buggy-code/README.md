# バグありコードサンプル

このフォルダには、GitHub Copilot CLIを使ったコードレビューとデバッグの練習用に、意図的にバグを入れたコードが含まれています。

## フォルダ構成

```
buggy-code/
├── js/                    # JavaScriptの例
│   ├── userService.js     # ユーザー管理（8バグ）
│   └── paymentProcessor.js # 決済処理（8バグ）
└── python/                # Pythonの例
    ├── user_service.py    # ユーザー管理（10バグ）
    └── payment_processor.py # 決済処理（12バグ）
```

## クイックスタート

### JavaScript

```bash
copilot

# セキュリティ監査
> Review @samples/buggy-code/js/userService.js for security issues

# 全バグ橋出し
> Find all bugs in @samples/buggy-code/js/paymentProcessor.js
```

### Python

```bash
copilot

# セキュリティ監査
> Review @samples/buggy-code/python/user_service.py for security issues

# 全バグ橋出し
> Find all bugs in @samples/buggy-code/python/payment_processor.py
```

## バグの種類

### 共通のバグ（両言語）

| バグの種類 | 説明 |
|----------|--------|
| SQLインジェクション | ユーザー入力が直接SQLクエリに埋め込まれる |
| ハードコードされたシークレット | APIキーやパスワードがソースコード内に含まれる |
| 競合状態 | 適切な同期なしの共有状態 |
| 機密情報のログ | パスワードやカード番号がログに記録される |
| 入力バリデーションなし | ユーザー入力にチェックがない |
| エラーハンドリングなし | try/catchまたはtt/exceptがない |
| 脆弱なパスワード比較 | 平文またはタイミングに脆弱な比較 |
| 認証チェックなし | 認可検証なしの操作 |

### Python固有のバグ

| バグの種類 | 説明 |
|----------|--------|
| Pickleデシリアライズ | 信頼できないデータに`pickle.loads()`を使用 |
| eval()インジェクション | ユーザー入力を`eval()`に渡す |
| 安全でないYAML読み込み | `yaml.load()`をsafeローダーなしで使用 |
| シェルインジェクション | `os.system()`にユーザー入力を渡す |
| 脆弱なハッシュ | パスワードのハッシュ化にMD5を使用 |
| 不安全な乱数 | セキュリティ目的に`random`モジュールを使用 |

## 練習問題

1. **セキュリティ監査**: 包括的なセキュリティレビューを実行し、重大度別に脆弱性を列挙する
2. **バグ1件を修正**: 重大なバグを選び、Copilotから修正方法を得て、なぜそれが有効か理解する
3. **テストの生成**: デプロイ前にこれらのバグを捕捉するテストを作成する
4. **安全なリファクタリング**: SQLインジェクションバグを機能を維持しながら修正する
