# 書籍コレクションアプリ

*（このREADMEは意図的に雑に書かれています。GitHub Copilot CLIを使って改善してみてください）*

読んだ本・読みたい本を管理するためのJavaScriptアプリです。
本の追加・削除・一覧表示、既読のマーク付けができます。

---

## 現在の機能

* JSONファイルから書籍データを読み込む（データベース代わり）
* 一部の入力チェックが不十分
* テストはあるが、おそらく十分ではない

---

## ファイル構成

* `book_app.js` - CLIのメインエントリーポイント
* `books.js` - データロジックを持つBookCollectionクラス
* `utils.js` - UIと入力用のヘルパー関数
* `data.json` - サンプルの書籍データ
* `tests/test_books.js` - Nodeの組み込みテストランナーを使った初期テスト

---

## アプリの実行

```bash
node book_app.js list
node book_app.js add
node book_app.js find
node book_app.js remove
node book_app.js help
```

## テストの実行

```bash
npm test
```

---

## メモ

* 本番環境向けではない（当然）
* 改善できる箇所がある
* 後でコマンドを追加できる
