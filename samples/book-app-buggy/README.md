# 書籍アプリ - バグありバージョン

このディレクトリには、第03章のデバッグ演習用に、意図的にバグを入れた書籍コレクションアプリが含まれています。

**これらのバグを直接修正しないでください。** 学習者がGitHub Copilot CLIを使って問題を特定・デバッグする練習のために存在します。

---

## 意図的なバグ

### books_buggy.py

| # | バグ | 症状 |
|---|-----|------|
| 1 | `find_book_by_title()`が大文字小文字を区別して一致させる | 「the hobbit」で検索しても「The Hobbit」が存在するのに結果が返らない |
| 2 | `save_books()`がコンテキストマネージャを使わない | ファイルハンドルのリーク；パーミッションエラーのハンドリングなし |
| 3 | `add_book()`に年のバリデーションがない | 負の年、年0、将来の年を受け付けてしまう |
| 4 | `remove_book()`が`in`で部分一致する | 「Dune」を削除すると「Dune Messiah」も一致して削除される |
| 5 | `mark_as_read()`が全所の本を既読にする | ループ変数のバグ - 一致した本だけでなく全本をイテレートしてしまう |
| 6 | `find_by_author()`が完全一致を要求する | 「Tolkien」で「J.R.R. Tolkien」が見つからない（部分一致なし） |

### book_app_buggy.py

| # | バグ | 症状 |
|---|-----|------|
| 7 | `show_books()`の番号が0始まり | ブックが「0. ...」「1. ...」と表示され、「1. ...」「2. ...」にならない |
| 8 | `handle_add()`が空のタイトル/著者を受け付ける | タイトル・著者が空白のまま本を登録できてしまう |
| 9 | `handle_remove()`が常に成功と言う | 本が見つからなくても「本を削除しました」と表示する |

---

## 第03章での使い方

```bash
copilot

> @samples/book-app-buggy/books_buggy.py Users report that searching for
> "The Hobbit" returns no results even though it's in the data. Debug why.

> @samples/book-app-buggy/book_app_buggy.py When I remove a book that
> doesn't exist, the app says it was removed. Help me find why.
```
