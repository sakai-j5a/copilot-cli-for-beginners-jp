# 書籍コレクションアプリ

*（このREADMEは意図的に雑に書かれています。GitHub Copilot CLIを使って改善してみてください）*

読んだ本・読みたい本を管理するためのC#コンソールアプリです。
本の追加・削除・一覧表示、既読のマーク付けができます。

---

## 現在の機能

* JSONファイルから書籍データを読み込む（データベース代わり）
* 一部の入力チェックが不十分
* テストはあるが、おそらく十分ではない

---

## ファイル構成

* `Program.cs` - CLIのメインエントリーポイント
* `Models/Book.cs` - 書籍のモデルクラス
* `Services/BookCollection.cs` - データロジックを持つBookCollectionクラス
* `data.json` - サンプルの書籍データ
* `Tests/BookCollectionTests.cs` - xUnitテスト

---

## アプリの実行

```bash
dotnet run -- list
dotnet run -- add
dotnet run -- find
dotnet run -- remove
dotnet run -- help
```

## テストの実行

```bash
cd Tests
dotnet test
```

---

## メモ

* 本番環境向けではない（当然）
* 改善できる箇所がある
* 後でコマンドを追加できる
