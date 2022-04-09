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

## 2. centos01からデータベースを作成する(CREATE DATABASE)
1. PostgreSQLのpostgresデータベースに接続する。
2. 以下の条件のデータベースを作成する。
   - データベース名は**mydb**とする。

## 3. centos01からテーブルを作成する(CREATE TABLE)
以下の手順を実行する。
1. PostgreSQLの**mydb**データベースに接続する。
2. 以下の条件のテーブルを作成する。  
   - テーブル名は**user_tbl**とする。  
   - **id**と**name**というカラムを持つ。  
   - カラムの型については、idは **integer** 、nameは **character varying(20)** とする。  
   - idは**プライマリキー**とする。

## 4. centos01からuser_tblテーブルにデータを挿入する(INSERT)
以下のようなデータを挿入する。

id | name
--- | ---
1 | taro.yamada
2 | toshio.suzuki
3 | hanako.sato

## 5. centos01からuser_tblテーブルを参照する(SELECT)
参照結果が3のような内容であることを確認する。

## 6. centos01からuser_tblテーブルのidが「2」のnameの値を以下のように更新する(UPDATE)
参照結果が以下のような内容であることを確認する。

id | name
--- | ---
1 | taro.yamada
2 | takuya.kato
3 | hanako.sato

## 7. centos01からuser_tblテーブルのnameが「taro.yamada」のレコードを削除する(DELETE)
参照結果が以下のような内容であることを確認する。

id | name
--- | ---
2 | takuya.kato
3 | hanako.sato

## 8. centos01からデータベースのユーザーを作成する(CREATE USER)
ユーザーの情報は以下です。
1. ユーザー名は**t_user**とする。
2. **ログイン権限のみ**付与されている。
3. パスワードは「**TestCentOS**」とする。

## 9. centos01からuser_tblテーブルにt_userの参照を許可する(GRANT SELECT)

## 10. centos01からt_userでデータベースに再接続してuser_tblテーブルを参照する
参照結果が以下のような内容であることを確認する。

id | name
--- | ---
2 | takuya.kato
3 | hanako.sato

## 11. centos01でpostgresql.confのlisten_addressesの値を以下のように変更する

```
listen_addresses = '*'
```
## 12. centos01でpg_hba.confの一番下の行に以下を追加する

```
host mydb t_user 0.0.0.0/0 md5
```
## 13. centos01でPostgreSQLを再起動する

## 14. centos02にPostgreSQL14クライアントをインストールする
1. PostgreSQL14のリポジトリを追加
2. 以下のパッケージをインストール  
   - postgresql14 

## 15. centos02からt_userでcentos01内のmydbデータベースに接続してuser_tblテーブルを参照する
参照結果が以下のような内容であることを確認する。

id | name
--- | ---
2 | takuya.kato
3 | hanako.sato

## 16. centos01でmydbデータベースのバックアップを取得する
以下の条件でバックアップを取得する。
 - pg_dumpコマンドを使用する
 - フォーマットは平文のSQLスクリプトファイルとする
 - バックアップファイル名は**test.dump**とする

## 17. centos01からt_userテーブルを削除する(DROP DATABASE)

## 18. centos01からmydbをリストアする
以下の条件でリストアする
   - test.dumpを使用する
   - postgresユーザーでリストアコマンドを実行する

## 19. centos02からt_userでデータベースに接続してuser_tblテーブルを参照する
参照結果が以下のような内容であることを確認する。

id | name
--- | ---
2 | takuya.kato
3 | hanako.sato
