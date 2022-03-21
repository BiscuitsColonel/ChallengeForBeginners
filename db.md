# DB初心者向け課題
代表的なRDBMSであるPostgreSQLを学んでみる。

## 学んでほしいこと
 - DB(RDBMS)とは
 - 一般的な運用SQL
 - バックアップ・リストア

## 前提
 - [Linux初心者向け課題](linux.md)で構築したcentos01とcentos02が存在していること。

## 1. PostgreSQL14のインストールして起動する
以下の手順を実行する。
1. PostgreSQL14のリポジトリを追加
2. 以下のパッケージをインストール  
  a. postgresql14  
  b. postgresql14-server
3. PostgreSQLの起動コマンドをpostgresユーザーで実行

## 2. テーブルを作成する(CREATE TABLE)
以下の手順を実行する。
1. PostgreSQLのpostgresデータベースに接続する。
2. 以下の条件のテーブルを作成する。  
  a. テーブル名は**user**とする。  
  b. **id**と**name**というカラムを持つ。  
  c. カラムの型については、idは**integer**、nameは**character varying(10)**とする。  
  d. idは**プライマリキー**とする。

## 3. 2で作成したテーブルにデータを挿入する(INSERT)
以下のようなデータを挿入する。

id | name
--- | ---
1 | taro.yamada
2 | toshio.suzuki
3 | hanako.sato

## 4. userテーブルを参照する(SELECT)
参照結果が3のような内容であることを確認する。

## 5. userテーブルのidが「**2**」のnameの値を以下のように更新する(UPDATE)
参照結果が以下のような内容であることを確認する。

id | name
--- | ---
1 | taro.yamada
2 | takuya.kato
3 | hanako.sato

## 6. userテーブルのnameが「**taro.yamada**」のレコードを削除する(DELETE)
参照結果が以下のような内容であることを確認する。

id | name
--- | ---
2 | takuya.kato
3 | hanako.sato

## 7. データベースのユーザーを作成する(CREATE USER)
ユーザーの情報は以下です。
1. ユーザー名はt-userとする。
2. ログイン権限のみ付与されている。

## 8. userテーブルにt-userの参照を許可する(GRANT SELECT)

## 9. t-userでデータベースに再接続してuserテーブルを参照する
参照結果が以下のような内容であることを確認する。

id | name
--- | ---
2 | takuya.kato
3 | hanako.sato

## 10. postgresデータベースのバックアップを取得する
以下の条件でバックアップを取得する。
 - pg_dumpコマンドを使用する
 - フォーマットは平文のSQLスクリプトファイルとする
 - バックアップファイル名は**test.dump**とする

## 11. userテーブルを削除する(DROP TABLE)

## 12. test.dumpを使用してpostgresデータベースをリストアする

## 13. t-userでデータベースに再接続してuserテーブルを参照する
参照結果が以下のような内容であることを確認する。

id | name
--- | ---
2 | takuya.kato
3 | hanako.sato
