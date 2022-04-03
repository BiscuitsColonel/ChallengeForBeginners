# DBを使用したWebアプリ初心者向け課題
DBを扱う理由とWebアプリの基本を学ぶ

## 学んでほしいこと
 - CRUD
 - Api
 - Spring Boot
   - 基本構成
     - プロパティファイル
     - Controller
     - Service
     - Dto
     - Log
     - Mapper
     - Exception
   - エラー制御
   - バリデーション
 - データベース
   - アプリとの関係性

## 前提
 - [データベース初心者向け課題](db.md)と[Webアプリ初心者向け課題](web.md)を行っていること。

## 1. **centos01**にPostgreSQL14のインストールをして起動する
以下の手順を実行する。
1. PostgreSQL14のリポジトリを追加
2. 以下のパッケージをインストール  
  a. postgresql14  
  b. postgresql14-server
3. PostgreSQLの起動コマンドをpostgresユーザーで実行
