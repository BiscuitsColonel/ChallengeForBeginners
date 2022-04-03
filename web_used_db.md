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
   - 使用した依存関係について
 - データベース
   - アプリとの関係性
   - 基本的な運用
   - シーケンス

## 前提
 - [データベース初心者向け課題](db.md)と[Webアプリ初心者向け課題](web.md)を行っていること。

## 1. Spring Bootのプロジェクトを作成
以下の要件でSpring Bootのプロジェクトを作成する。
 - Spring スターター・プロジェクトから作成
 - 名前: BaseBallApp
 - タイプ: Gradle Project
 - Javaバージョン: 17
 - グループ: jp.co.baseball
 - 成果物: BaseBallApp
 - パッケージ: jp.co.baseball
 - Spring Boot バージョン: 2.6.4
 - 依存関係: Spring Web, Spring Boot DevTools, Thymeleaf, Lombok, MyBatis Framework, PostgreSQL Driver, Validate

## 2. データベースを作成
以下の要件でデータベースを作成する。  
※ここで言うユーザーはデータベースユーザーを指すことに注意してください
 - centos01のデータベースにpostgresユーザーでbaseballというデータベースを作成する
 - postgresユーザーでid(int)(プライマリキー), name(text), age(int), team(text), position(text)のカラムを持つplayers_tblテーブルを作成する
 - postgresユーザーでplayers_seqシーケンスを作成する
 - t-userにplayers_tblテーブルとplayers_seqシーケンスへ全ての操作権限を付与する

## 3. Spring BootにDtoを作成する
1. 「src/main/java/」配下にjp.co.baseball.dtoパッケージを作成する(パッケージは「/」を「.」と扱う)。
2. 1で作成したパッケージ配下にPlayersDto.java(クラス)を以下の条件で作成する。  
  a. ゲッターとセッターはlombokのアノテーションを使用する  
  b. name(String), age(Integer), team(String), position(String)を定義する  
  c. バリデーション用のアノテーションとして全てのNotNull
