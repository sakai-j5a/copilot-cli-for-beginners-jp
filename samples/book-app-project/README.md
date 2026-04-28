# 書籍コレクションアプリ

*（このREADMEは意図的に雑に書かれています。GitHub Copilot CLIを使って改善してみてください）*

読んだ本・読みたい本を管理するためのPythonアプリです。
本の追加・削除・一覧表示、既読のマーク付けができます。

---

## 現在の機能

* JSONファイルから書籍データを読み込む（データベース代わり）
* 一部の入力チェックが不十分
* テストはあるが、おそらく十分ではない

---

## ファイル構成

* `book_app.py` - CLIのメインエントリーポイント
* `books.py` - データロジックを持つBookCollectionクラス
* `utils.py` - UIと入力用のヘルパー関数
* `data.json` - サンプルの書籍データ
* `tests/test_books.py` - pytestの初期テスト

---

## アプリの実行

```bash
python book_app.py list
python book_app.py add
python book_app.py find
python book_app.py remove
python book_app.py help
```

## テストの実行

```bash
python -m pytest tests/
```

---

## メモ

* 本番環境向けではない（当然）
* 改善できる箇所がある
* 後でコマンドを追加できる
