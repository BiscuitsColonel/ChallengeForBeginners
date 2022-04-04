# データベース初心者向け課題
代表的なRDBMSであるPostgreSQLを学んでみる。

## 学んでほしいこと
 - DB(RDBMS)
 - 標準SQL
 - 設定ファイル
 - バックアップとリストア

## 前提
 - [Linux初心者向け課題](linux.md)で構築したcentos01とcentos02が存在していること。

## 1. centos01にPostgreSQL14のインストールをして起動する
以下の手順を実行する。
1. PostgreSQL14のリポジトリを追加
2. 以下のパッケージをインストール  
   - postgresql14  
   - postgresql14-server
3. PostgreSQLの起動コマンドをpostgresユーザーで実行

## 2. centos01からテーブルを作成する(CREATE TABLE)
以下の手順を実行する。
1. PostgreSQLのpostgresデータベースに接続する。
2. 以下の条件のテーブルを作成する。  
   - テーブル名は**user**とする。  
   - **id**と**name**というカラムを持つ。  
   - カラムの型については、idは**integer**、nameは**character varying(20)**とする。  
   - idは**プライマリキー**とする。

## 3. centos01からuserテーブルにデータを挿入する(INSERT)
以下のようなデータを挿入する。

id | name
--- | ---
1 | taro.yamada
2 | toshio.suzuki
3 | hanako.sato

## 4. centos01からuserテーブルを参照する(SELECT)
参照結果が3のような内容であることを確認する。

## 5. centos01からuserテーブルのidが「2」のnameの値を以下のように更新する(UPDATE)
参照結果が以下のような内容であることを確認する。

id | name
--- | ---
1 | taro.yamada
2 | takuya.kato
3 | hanako.sato

## 6. centos01からuserテーブルのnameが「taro.yamada」のレコードを削除する(DELETE)
参照結果が以下のような内容であることを確認する。

id | name
--- | ---
2 | takuya.kato
3 | hanako.sato

## 7. centos01からデータベースのユーザーを作成する(CREATE USER)
ユーザーの情報は以下です。
1. ユーザー名は**t-user**とする。
2. **ログイン権限のみ**付与されている。
3. パスワードは「**TestCentOS**」とする。

## 8. centos01からuserテーブルにt-userの参照を許可する(GRANT SELECT)

## 9. centos01からt-userでデータベースに再接続してuserテーブルを参照する
参照結果が以下のような内容であることを確認する。

id | name
--- | ---
2 | takuya.kato
3 | hanako.sato

## 10. centos01でpostgresql.confのlisten_addressesの値を以下のように変更する

```
listen_addresses = '*'
```
## 11. centos01でpg_hba.confの一番下の行に以下を追加する

```
host postgres t-user 0.0.0.0/0 md5
```
## 12. centos01でPostgreSQLを再起動する

## 13. centos02にPostgreSQL14クライアントをインストールする
1. PostgreSQL14のリポジトリを追加
2. 以下のパッケージをインストール  
   - postgresql14 

## 14. centos02からt-userでcentos01内のpostgresデータベースに接続してuserテーブルを参照する
参照結果が以下のような内容であることを確認する。

id | name
--- | ---
2 | takuya.kato
3 | hanako.sato

## 12. centos01でpostgresデータベースのバックアップを取得する
以下の条件でバックアップを取得する。
 - pg_dumpコマンドを使用する
 - フォーマットは平文のSQLスクリプトファイルとする
 - バックアップファイル名は**test.dump**とする

## 11. centos01からuserテーブルを削除する(DROP TABLE)

## 12. centos01からtest.dumpを使用してpostgresデータベースをリストアする

## 13. centos02からt-userでデータベースに接続してuserテーブルを参照する
参照結果が以下のような内容であることを確認する。

id | name
--- | ---
2 | takuya.kato
3 | hanako.sato
