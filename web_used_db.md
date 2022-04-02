# DBを使用したWebアプリ初心者向け課題
DBを扱う理由とWebアプリの基本を学ぶ

## 学んでほしいこと
 - DB(RDBMS)
 - 標準SQL
 - 設定ファイル
 - バックアップとリストア

## 前提
 - [Linux初心者向け課題](linux.md)で構築したcentos01とcentos02が存在していること。

## 1. **centos01**にPostgreSQL14のインストールをして起動する
以下の手順を実行する。
1. PostgreSQL14のリポジトリを追加
2. 以下のパッケージをインストール  
  a. postgresql14  
  b. postgresql14-server
3. PostgreSQLの起動コマンドをpostgresユーザーで実行
