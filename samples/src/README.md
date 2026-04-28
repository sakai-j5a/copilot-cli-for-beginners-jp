# サンプルソースコード（レガシー - オプション参考）

> **注記**: このコースの主要サンプルは `../book-app-project/` の **Python書籍コレクションアプリ** です。こゎのJS/Reactファイルはコースの旧バージョンのもので、JS例を希望する学習者向けのオプション参考資料として残してあります。

このフォルダにはサンプルのソースファイルが含まれています。これらはサンプルのみであり、完全に動作するアプリケーションとして設計されていません。

## 構成

```
src/
├── api/           # API route handlers
│   ├── auth.js    # Authentication endpoints
│   └── users.js   # User CRUD endpoints
├── auth/          # Client-side auth handlers
│   ├── login.js   # Login form logic
│   └── register.js # Registration form logic
├── components/    # React components
│   ├── Button.jsx # Reusable button
│   └── Header.jsx # App header with nav
├── models/        # Data models
│   └── User.js    # User model
├── services/      # Business logic
│   ├── productService.js
│   └── userService.js
├── utils/         # Helper functions
│   └── helpers.js
├── index.js       # App entry point
└── refactor-me.js # Beginner refactoring practice (Chapter 03)
```

## 使い方

これらのファイルは、`@`構文を使ってコースの例に示されます：

```bash
copilot

> Explain what @samples/src/utils/helpers.js does
> Review @samples/src/api/ for security issues
> Compare @samples/src/auth/login.js and @samples/src/auth/register.js
```

## リファクタリング練習

`refactor-me.js`ファイルは、第03章のリファクタリング演習用に特別に用意されています：

```bash
copilot

> @samples/src/refactor-me.js Rename the variable 'x' to something more descriptive
> @samples/src/refactor-me.js This function is too long. Split it into smaller functions.
> @samples/src/refactor-me.js Remove any unused variables
```

## メモ

- ファイルには、レビュー時にCopilotが見つけるための意図的なTODOや小さな問題が含まれている
- 実際に動作する設計ではないデモコード。本番環境向けではない
- `@`ファイル参照構文の学習に使用
